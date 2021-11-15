## Mini-program (do)

The `"do"` special form is for limiting scope in mini-programs. It is often used by automated code builders. The structure is:

`{"do": [EXPR1, EXPR2, ...]}`

where `EXPR` may be any expression and the return value is the value of the last expression.

### Example 

The intermediate variables in these blocks do not conflict.

```json
{"do": [
    {"do": [
        {"let": {"x": 1}},
        {"+": ["x", 1]},
    ],
    {"do": [
        {"let": {"x": 2}},
        {"+": ["x", 1]},
    ],
    {"do": [
        {"let": {"x": 3}},
        {"+": ["x", 1]},
    ]
}
```

## Conditional (if)
PFA’s "if" statement has two forms:

```json
{"if": CONDITION, "then": EXPRESSION-OR-EXPRESSIONS}
```

and

```json
{"if": CONDITION, "then": EXPRESSION-OR-EXPRESSIONS,
                  "else": EXPRESSION-OR-EXPRESSIONS}
```

where `CONDITION` is an expression whose type resolves to `"boolean"` and `EXPRESSION-OR-EXPRESSIONS` is either a single expression or a JSON array of expressions.

The return value of the first form is `null`, so it acts as a statement: its influence comes from changing variables inside the `"then"` clause.

The return value of the second form is either the last expression in the `"then"` clause or the last expression in the `"else"` clause, so it can be used as an inline expression. It can influence the program through its return value, rather than changing variables.

The return type is the narrowest possible supertype of the `"then"` clause and the `"else"` clause.

### Example
Both of the following set `y` to `"yes"` if `x` is greater than `0` and `"no"` otherwise, but the first uses `"if"` as a statement, while the second uses it as an expression.

```json
{"do": [
    {"let": {"y": "no"}},
    {"if": {">": ["x", 0]},
     "then": {"set": {"y": "yes"}}}
]}

{"let": {"y":
    {"if": {">": ["x", 0]},
     "then": "yes",
     "else": "no"}
}}
```

## Conditional with many cases (cond)

Long "else if" chains could be constructed by nesting the simple "if" form above, but for flatter PFA files, an explicit chain is provided. Its structure is:

```json
{"cond": [IF-THEN-1, IF-THEN-2, ...]}
```

or

```json
{"cond": [IF-THEN-1, IF-THEN-2, ...],
 "else": EXPRESSION-OR-EXPRESSIONS}
```

where the `IF-THEN` are `"if"` forms with no `"else"` clause and the `EXPRESSION-OR-EXPRESSIONS` is either a single expression or a JSON array of expressions.

Just like the simple `"if"` form, `"cond"` without `"else"` returns `null` and `"cond"` with `"else"` returns the smallest possible supertype of the `"then"` clauses and the `"else"` clause.

### Example
The following turns small numbers into English words.

```json
{"cond": [
    {"if": {"==": ["x", 1]}, "then": {"string": "one"}},
    {"if": {"==": ["x", 2]}, "then": {"string": "two"}},
    {"if": {"==": ["x", 3]}, "then": {"string": "three"}},
    {"if": {"==": ["x", 4]}, "then": {"string": "four"}},
    {"if": {"==": ["x", 5]}, "then": {"string": "five"}},
    {"if": {"==": ["x", 6]}, "then": {"string": "six"}},
    {"if": {"==": ["x", 7]}, "then": {"string": "seven"}},
    {"if": {"==": ["x", 8]}, "then": {"string": "eight"}},
    {"if": {"==": ["x", 9]}, "then": {"string": "nine"}},
    {"if": {"==": ["x", 10]}, "then": {"string": "ten"}}],
 "else": {"string": "unknown"}}
```

(For large look-up tables, it’s better to fill a cell with a map because data can be arbitrarily large, while code is limited on some systems.)

!!! Source
    [Data Mining Group Website](http://dmg.org/pfa/){: target=_blank }