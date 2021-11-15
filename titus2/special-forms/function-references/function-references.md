## Function definition (fcndef)

Functions may be defined and given names in the "fcns" top-level field and they may be defined without names within the argument list of some library functions and special forms. Technically, a function definition is not an expression because it does not return an Avro-typed value. A function cannot be defined and assigned to a variable, but it can be defined and passed to a generic algorithm that takes a callback.

The structure of a function definition is

```json
{"params": [{ARG1: TYPE1}, {ARG2: TYPE2}, ...],
 "ret": RETURN-TYPE,
 "do": EXPRESSION-OR-EXPRESSIONS}
```

The `"params"` can have any number of parameters, including zero. Each ARG is a string defining a variable that can be used in the `"do"` expressions. The `RETURN-TYPE` must be an Avro type, though `"null"` can be used if the function doesn’t return anything useful. `EXPRESSION-OR-EXPRESSIONS` may be a single expression or a JSON array of them.

### Example
The following computes an absolute difference:
```json
{"params": [{"x": "double"}, {"y": "double"}],
 "ret": "double",
 "do": {"m.abs": {"-": ["x", "y"]}}}
```
which could be passed as an argument to the `a.zipmap` function, for instance.

When used to create anonymous functions, those functions can access but not modify variables in the containing scope ([captured by value](https://en.wikipedia.org/wiki/Closure_(computer_programming)){: target=_blank}).

## Function reference (fcnref)

Named user-defined functions and library functions can be referenced in the argument list of some library functions and special forms. Technically, a function reference is not an expression because it does not return an Avro-typed value. A function cannot be assigned to a variable, but it can be passed to a generic algorithm that takes a callback.

The structure of a function reference is

`{"fcn": FUNCTION-NAME}`

where `FUNCTION-NAME` is a string naming the function. User-defined functions always start with `"u."` (even though they are declared in the `"fcns"` section without the `"u."`).

Library functions can only be referenced if they have exactly one signature and no wildcards. For instance, `s.len` can be referenced because its only signature takes one `"string"` argument, but `a.len` cannot because its signature takes an array of any A (array of strings, array of integers, array of records, etc.). A function reference must resolve the function’s signature to a fixed sequence of Avro types.

### Example
The following references `"s.len"`, which returns the length of a string.

`{"fcn": "s.len"}`

Below is an example of wrapping `"a.len"` with a user-defined function so that it returns the length of an array of numbers.

```json
{"params": ["x": {"type": "array", "items": "double"}]
 "ret": "int",
 "do": {"a.len": "x"}}
```

The wrapped form can be used anywhere that a direct function reference can be used.

## Function reference with partial application (fcnref-fill)

Function references can also reduce the number of arguments in the function by [partially applying](https://en.wikipedia.org/wiki/Partial_application){: target=_blank} some of the arguments.

The structure of a partially applied function reference is

`{"fcn": FUNCTION-NAME, "fill": {ARG1: EXPR1, ARG2: EXPR2, ...}}`

where `FUNCTION-NAME` is a string naming the function and the `ARG` keys are a subset of the function's parameter names. The expressions `EXPR` provide values.

### Example
To turn the two-argument `m.special.nChooseK` function into a one-argument function reference that computes 100 choose `k`, we can reference it like this:

`{"fcn": "m.special.nChooseK", "fill": {"n": 100}}`

!!! Source
    [Data Mining Group Website](http://dmg.org/pfa/){: target=_blank }
