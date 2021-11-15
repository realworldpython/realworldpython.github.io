## Generic pre-test loop (while)
PFA has an ordinary `"while"` loop, which has the usual danger of not terminating. (The timeout option can prevent that, however.) Its structure is:

`{"while": CONDITION, "do": EXPRESSION-OR-EXPRESSIONS}`

where `CONDITION` is an expression that resolves to `"boolean"` and `EXPRESSION-OR-EXPRESSIONS` is either a single expression or a JSON array of expressions.

PFA has no "break", "continue", or "return" statement, so a while loop is often not the best way to do things.

### Example
An infinite loop.

`{"while": true, "do": {"log": {"string": "Can't stop!"}}}`

## Generic post-test loop (do-until)
The `"while"` loop tests its condition before executing its `"do"` expressions, and sometimes it’s necessary to test the condition afterward (especially because there is no “break” statement). Its structure is:

`{"do": EXPRESSION-OR-EXPRESSIONS, "until": CONDITION}`

where `CONDITION` is an expression that resolves to `"boolean"` and `EXPRESSION-OR-EXPRESSIONS` is either a single expression or a JSON array of expressions.

### Example
An iterative procedure that has a non-trivial stop condition.

```json
{"do": [
    {"set": {"state": {"u.doOneIteration": "state"}}},
    {"let": {
        "cond1": {"u.checkCondition1": "state"},
        "cond2": {"u.checkCondition2": "state"}}},
    ],
 "until": {"&&": ["cond1", "cond2"]}}
```

## Iteration with dummy variables (for)

PFA's `"for"` loop takes a `"let"`-like structure for its initializer and a `"set"`-like structure for its updator, but is otherwise like a for loop in C. Its structure is:

```json
{"for": {VAR1: EXPR1, VAR2: EXPR2, ...},
 "while": CONDITION,
 "step": {VAR1: EXPR1, VAR2: EXPR2, ...},
 "do": EXPRESSION-OR-EXPRESSIONS}
```

where the `VAR` are variable names and the `EXPR` are their initial or updated values. The initial values set the type and the updated values have to conform to that type. The `CONDITION` is an expression that resolves to `"boolean"`, and `EXPRESSION-OR-EXPRESSIONS` is either a single expression or a JSON array of expressions.

### Example
A for loop that applies a procedure to numbers from 1 to 10 (inclusive).

```json
{"for": {"i": 1},
 "while": {"<=": ["i", 10]},
 "step": {"i": {"+": ["i", 1]}},
 "do":
     {"u.procedure": "i"}}
```

## Iteration over arrays (foreach)
PFA has a `"foreach"` loop for iteration over arrays. This version should be familiar to users of Python or R. Its structure is:

```json
{"foreach": VAR, "in": ARRAY, "do": EXPRESSION-OR-EXPRESSIONS}
```

or

```json
{"foreach": VAR, "in": ARRAY, "do": EXPRESSION-OR-EXPRESSIONS,
 "seq": TRUE-OR-FALSE}
```

where `VAR` is a new variable name, `ARRAY` is an expression that resolves to an array type, and `EXPRESSION-OR-EXPRESSIONS` is either a single expression or a JSON array of expressions.

The `"seq"` parameter, if provided must be a JSON `true` or `false` (default of true). If `true`, it indicates that the loop must be processed sequentially. If `false`, the PFA implementation may parallelize the loop. The consequence for the PFA author is that variables defined outside the `"foreach"` can only be *changed* if elements are processed sequentially. If you get an error saying that a variable cannot be modified inside the scope of a `"foreach"` loop, make sure the `"seq"` parameter is `true` (although the cause could be another sealed scope between the `"foreach"` and the variable, such as an inline function declaration.)

### Example
Apply a procedure to each element of an array.

```json
{"foreach": "x", "in": "input.myArray", "do":
    {"u.procedure": "x"}}
```

Allow the PFA consumer to perform the steps in parallel, which makes it impossible to modify any variables defined outside the `"foreach"`.

```json
{"foreach": "x", "in": "input.myArray", "do":
    {"u.procedure": "x"},
 "seq": false}
```

## Iteration over maps (forkey-forval)
PFA's `"forkey-forval"` is an extension of the `"foreach"` idea to iterating over the key-value pairs of a map. Its structure is:

```json
{"forkey": VAR1, "forval": VAR2, "in": MAP,
 "do": EXPRESSION-OR-EXPRESSIONS}
```

where `VAR1` is the name of a new variable for each key of the map, `VAR2` is the name of a new variable for each value of the map, `MAP` is an expression that resolves to a map type, and `EXPRESSION-OR-EXPRESSIONS` is either a single expression or a JSON array of expressions.

The order of a loop over a map is never guaranteed.

### Example
Apply a procedure to each key, value pair of a map.

```json
{"forkey": "k", "forval": "v", "in": "input.myMap", "do":
    {"u.procedure": ["k", "v"]}
```

!!! Source
    [Data Mining Group Website](http://dmg.org/pfa/){: target=_blank }