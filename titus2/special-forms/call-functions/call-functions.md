## Ordinary function call

The majority of functionality in PFA is provided through functions with regular parameter lists, signatures that can be described in terms of Avro types, wildcards, and function references. An ordinary function call has the following structure:

`{FUNCTION-NAME: [EXPR1, EXPR2, ...]}`

where `FUNCTION-NAME` is the name of the function and `EXPR` are zero or more expressions. The number of expressions and their types are determined by the function's signature. User-defined functions always start with `"u."` (even though they are declared in the `"fcns"` section without the `"u."`).

Ordinary function calls are expressions and always return an Avro-typed value, even if that value is `null`.

### Example
The following illustrate zero-argument, one-argument, and multi-argument function calls.

- `{"m.pi": []}`
- `{"m.exp": "x"}`
- `{"m.exp": ["x"]}`
- `{"m.special.nChooseK": ["n", "k"]}`

## Call a user-defined function that is specified at runtime

Ordinary function calls fix the choice of function while a scoring engine is being built— there is no ability to change it at runtime. This is good because a PFA file can be statically analyzed to determine which functions can be accessed from a given scope (used to exclude the possibility of deadlock in concurrent transactions, for instance), but there are cases when you want this flexibility.

For instance, when you describe a ruleset as a data structure, you might want to associate actions with each rule in the data structure (e.g. in a cell's `"init"`) rather than in the code (e.g. in an if-then block in the code). The actions are defined as named functions in the `"fcns"` section, but we need to pick the function to run at runtime.

The following structure provides that ability:

`{"call": ENUM-EXPRESSION, "args": [EXPR1, EXPR2, ...]}`

The `ENUM-EXPRESSION` is an expression that evaluates to an enumeration type whose symbols are all names of user-defined functions (without the `"u."` prefix). The `EXPR` expressions are all supplied arguments.

The set of user-defined functions that could be called must all have signatures that accept the arguments: they must be drop-in replacements for one another. When the static analysis encounters this structure, it reports that any of the user-defined functions in the set could be called.

### Example

Here is a PFA document that shows a complete example of a model that performs different actions based on the closest cluster. The important point is that the choice of action is bundled in the model parameters.

```json
{"input": {"type": "array", "items": "double"},
 "output": "double",
 "cells": {
   "clusters": {
     "type": {"type": "array", "items":
       {"type": "record",
        "name": "Rule",
        "fields": [
          {"name": "center", "type":
            {"type": "array", "items": "double"}},
          {"name": "act", "type":
            {"type": "enum",
             "name": "Action",
             "symbols": ["add", "sub", "mul", "div"]}},
          {"name": "param", "type": "double"}
         ]}},
     "init": [
       {"center": [0, 0, 0], "act": "add", "param": 1.0},
       {"center": [1, 0, 0], "act": "add", "param": 2.0},
       {"center": [0, 1, 0], "act": "sub", "param": 3.0},
       {"center": [0, 0, 1], "act": "sub", "param": 4.0},
       {"center": [1, 1, 0], "act": "mul", "param": 5.0},
       {"center": [1, 0, 1], "act": "mul", "param": 6.0},
       {"center": [0, 1, 1], "act": "div", "param": 7.0},
       {"center": [1, 1, 1], "act": "div", "param": 8.0}
     ]}},
 "action": [
   {"let": {
      "x": {"attr": "input", "path": [0]},
      "c": {"model.cluster.closest":
              ["input", {"cell": "clusters"}]}}},
   {"call": "c.act", "args": ["x", "c.param"]}
 ],
 "fcns": {
   "add": {"params": [{"x": "double"}, {"y": "double"}],
           "ret": "double",
           "do": {"+": ["x", "y"]}},
   "sub": {"params": [{"x": "double"}, {"y": "double"}],
           "ret": "double",
           "do": {"-": ["x", "y"]}},
   "mul": {"params": [{"x": "double"}, {"y": "double"}],
           "ret": "double",
           "do": {"*": ["x", "y"]}},
   "div": {"params": [{"x": "double"}, {"y": "double"}],
           "ret": "double",
           "do": {"/": ["x", "y"]}}}
 }
```

!!! Source
    [Data Mining Group Website](http://dmg.org/pfa/){: target=_blank }