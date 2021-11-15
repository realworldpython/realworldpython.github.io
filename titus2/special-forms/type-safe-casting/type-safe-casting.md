## Narrowing a type (cast-cases)
If an expression has a type with subtypes, such as a union, you sometimes need to cast it as one of those subtypes. In most languages, like C and Java, casting bypasses type-safety. Incorrect casts either lead to wrong results or raise exceptions. Some languages, like PFA, provide type-safe casts, which require the author to provide a contingency for every possible subtype. The structure for a type-safe cast in PFA is:

```json
{"cast": EXPRESSION, "cases": [
    {"as": TYPE1,
     "named": VAR1,
     "do": EXPRESSION-OR-EXPRESSIONS},
    {"as": TYPE2,
     "named": VAR2,
     "do": EXPRESSION-OR-EXPRESSIONS},
    ...
 ]}
```

or

```json
{"cast": EXPRESSION, "cases": [
    {"as": TYPE1,
     "named": VAR1,
     "do": EXPRESSION-OR-EXPRESSIONS},
    {"as": TYPE2,
     "named": VAR2,
     "do": EXPRESSION-OR-EXPRESSIONS},
    ...],
 "partial": TRUE-OR-FALSE}
```

where `EXPRESSION` is an expression, the `TYPE` are subtypes of the `EXPRESSION` type, the `VAR` are new variable names, and `EXPRESSION-OR-EXPRESSIONS` is either a single expression or a JSON array of expressions.

If the type of `EXPRESSION` is `TYPE1`, then the first `"do"` block is evaluated with `VAR1` defined as a variable with `TYPE1`. If, instead, it is `TYPE2`, then the second `"do"` block is evaluated.

If `"partial"` is not provided or `"partial"` is `false`, then the `TYPE` subtypes must cover all possibilities and the `"cast"` form returns the last expression of whichever block is evaluated. (The return type is the narrowest possible supertype of the `"do"` blocks.)

If `"partial"` is `true`, then the `TYPE` subtypes do not have to cover all possibilities and the `"cast"` form returns `null`.

### Example
Given a variable `x` that could be `"double"`, `"string"`, or `"null"`, the following expression returns the name of the type.

```json
{"cast": "x", "cases": [
    {"as": "double", "named": "y", "do": {"string": "double"}},
    {"as": "string", "named": "y", "do": {"string": "string"}},
    {"as": "null", "named": "y", "do": {"string": "null"}}]}
```

The following writes a log message for the first two cases, including the value of the variable.

```json
{"cast": "x", "cases": [
    {"double", "named": "y": "do":
         {"log": ["y", {"string": "double"}]}},
    {"string", "named": "y": "do":
         {"log": ["y", {"string": "string"}]}}],
 "partial": true}
```

## Widening a type (upcast)
The "cast-cases" form (above) is used for the usual case of casting a supertype to its subtypes. The other direction is rarely needed and always safe. Its structure is simple:

`{"upcast": EXPRESSION, "as": TYPE}`

where `EXPRESSION` is an expression and `TYPE` is a supertype of the `EXPRESSION` type.

### Example
Casting an integer as a double.

`{"upcast": 3, "as": "double"}`

## Checking for missing values (ifnotnull)

Checking for `null` is a special case of narrowing a type, so the "cast-cases" form could be used to handle it. However, PFA uses `null` to represent missing values, so it is a frequent special case and "cast-cases" is cumbersome. Moreover, "cast-cases" can only cast one expression at a time, and we frequently need to check for `null` in several expressions.

The "ifnotnull" special form handles this case, and its structure is:

```json
{"ifnotnull": {VAR1: EXPR1, VAR2: EXPR2, ...},
 "then": EXPRESSION-OR-EXPRESSIONS}
```

or

```json
{"ifnotnull": {VAR1: EXPR1, VAR2: EXPR2, ...},
 "then": EXPRESSION-OR-EXPRESSIONS,
 "else": EXPRESSION-OR-EXPRESSIONS}
```

where the `VAR` are new variable names that are in the scope of the `"then"` clause, the `EXPR` are single expressions or JSON arrays of expressions to check. If all of them are not `null`, the `"then"` clause is evaluated. If any one is `null` and an `"else"` clause is provided, it is evaluated.

If an `"else"` clause is provided, the return value is the last expression of `"then"` or `"else"`. Its type is the narrowest possible supertype of both clauses.

If an "else" is not provided, the return value is `null`.

### Example
The following performs a procedure if all variables are present, and returns a substitute otherwise.

```json
{"ifnotnull": {"x": "input.x", "y": "input.y", "z": "input.z"},
 "then": {"u.procedure": ["x", "y", "z"]},
 "else": 0}
```

## Extracting values from binary (unpack)
Sometimes, it is necessary to extract data from a serialized `"bytes"` object. This is done with a special form, rather than a library function, because the return type depends on the interpretation of the `"bytes"` object. It has the following structure.

```json
{"unpack": BYTES,
 "format": [{VAR1: FORMAT1}, {VAR1: FORMAT1}, ...],
 "then": EXPRESSION-OR-EXPRESSIONS}
```

or

```json
{"unpack": BYTES,
 "format": [{VAR1: FORMAT1}, {VAR1: FORMAT1}, ...],
 "then": EXPRESSION-OR-EXPRESSIONS,
 "else": EXPRESSION-OR-EXPRESSIONS}
```

where `BYTES` is an expression that resolves to `"bytes"` type, the `VAR` are new variable names that are in the scope of the `"then"` clause, the `FORMAT` are format strings (described below), and the `"then"` and optional `"else"` clauses are single expresions or JSON arrays of expressions.

If the `"bytes"` content conforms to the format, the `"then"` clause is evaluated. If not, and if an `"else"` clause is provided, the `"else"` clause is evaluated. With an `"else"` clause, the result is the last expression in either `"then"` or `"else"` whose type is the narrowest possible supertype of the two. Without an `"else"`, the return value is `null`.

The format strings have the following values.

| Format | Result | PFA type |
|---|---|---|
| `pad` | skips one byte | `"null"` |
| `boolean` | one byte as true if nonzero | `"boolean"` |
| `int8` | one byte as signed integer | `"int"` |
| `int16` | two bytes as signed integer | `"int"` |
| `int32` | four bytes as signed integer | `"int"` |
| `int64` | eight bytes as signed integer | `"long"` |
| `float32` | four bytes as a floating-point number | `"float"` |
| `float64` | eight bytes as a floating-point number | `"double"` |
| `raw` ## | extract a fixed number of bytes | `"bytes"` |
| `null terminated` | extract bytes until terminated by zero (excluding terminus) | `"bytes"` |
| `length prefixed` | interpret first byte as a size, then extract that many bytes (excluding size byte) | `"bytes"` |

The `int8`, `int16`, `int32`, and `int64` formats can also be preceded by `unsigned` to interpret the bytes as an unsigned integer.

The `int16`, `int32`, `int64`, `float32`, and `float64` formats can be preceded by `little` to interpret the bytes as little-endian. (The `unsigned` modifier comes first.)

### Example
Extract three fields and use them to fill a record, with a default if the bytes are corrupted.

```json
{"unpack": "input", "format": [
    {"x": "int32"},
    {"y": "float64"},
    {"z": "null terminated"}],
 "then":
    {"type": {"type": "record",
              "name": "MyRecord",
              "fields": [
                  {"name": "one", "type": "int"},
                  {"name": "two", "type": "double"},
                  {"name": "three", "type": "string"}]},
     "new": {
         "one": "x",
         "two": "y",
         "three": {"bytes.decodeUtf8": "z"}}
    },
 "else":
    {"type": "MyRecord",
     "new": {"one": 0, "two": 0.0, "three": ""}}}
```

## Encoding values in binary (pack)
The opposite of the above is "pack", which serializes variables as a "bytes" object. Its structure is:

`{"pack": [{FORMAT1: EXPR1}, {FORMAT2: EXPR2}, ...]}`

where `FORMAT` are format strings (described below), and `EXPR` are expressions. The return type of this form is a `"bytes"` object.

The format strings are the same as the `"unpack"` case, with one additional format.

| Format | Result | PFA type |
|---|---|---|
| raw | any number of bytes | `"bytes"` |

### Example
This serializes three variables.

```json
{"pack": [{"int32": "input.x"},
          {"float64": "input.y"},
          {"null terminated": "input.z"}]}
```

!!! Source
    [Data Mining Group Website](http://dmg.org/pfa/){: target=_blank }