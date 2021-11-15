## Inline documentation (doc)
Since JSON has no comments, PFA provides an inert special form for comments. It takes a string and always returns `null`.

`{"doc": STRING}`

where `STRING` is any string.

### Example
A simple example.

`{"doc": "This is like those REM strings from BASIC."}`

## User-defined exceptions (error)
The following special form raises an exception. It has no return type.

`{"error": STRING}`

### Example
When combined with other types as a narrowest possible supertype, the "error" form is ignored. The following has type `"int"`.

```json
{"if": "condition",
 "then": 3,
 "else": {"error": "no good"}}
```

It can be used to let special forms return a non-null value without constructing unwanted unions.

## Turning exceptions into missing values (try)
PFA has a "try" special form, but unlike most languages, it does not have a "catch" case. In PFA, "try" is used to turn exceptions into missing values (`null`). Its structure is

`{"try": EXPRESSION-OR-EXPRESSION}`

or

`{"try": EXPRESSION-OR-EXPRESSION, "filter": ARRAY-OF-STRINGS}`

The `EXPRESSION-OR-EXPRESSIONS` is a single expression or a JSON array of expressions, and `ARRAY-OF-STRINGS` is a JSON array of simple strings (not expressions). If provided, the `"filter"` selects a subset of exception strings to catch.

The return type is a union of the expression's type and `"null"`. This makes it applicable for any impute function.

### Example
The following gets the first element from an array or null if the array is empty.

`{"try": {"a.head": "input"}}`

If the `"input"` array had type `{"type": "array", "items": "int"}`, then the type of the above expression is `["null", "int"]`.

A traditional try-catch block can be formed by combining "try" with "ifnotnull":

```json
{"ifnotnull": {"x": {"try": {"u.attempt": []}}},
 "then": {"u.workWithResult": "x"},
 "else": {"u.dealWithException": []}}
```

## Log messages (log)
A PFA scoring engine is connected to an input source, and output sink, and optionally a log file. The nature of this log file is outside the scope of PFA, but if it exists, the following special form writes text to it.

`{"log": EXPRESSION-OR-EXPRESSIONS}`

or

`{"log": EXPRESSION-OR-EXPRESSIONS, "namespace": NAME}`

where `EXPRESSION-OR-EXPRESSIONS` is a single expression or a JSON array of expressions, and `NAME` is a string (not an expression).

The output of each call to `"log"` is a line of text, space-delimited JSON representations of the provided expressions. The `"namespace"` is optional; the external system may use it to send logs to different files, prefix the line of text with a marker, or ignore it.

The return value of "log" is `null`.

### Example
The following writes the values of two variables, a number, and string to the logfile.

`{"log": ["x", "y", 3.14, {"string": "literal string"}]}`

!!! Source
    [Data Mining Group Website](http://dmg.org/pfa/){: target=_blank }