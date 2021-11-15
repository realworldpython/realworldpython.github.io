Special forms and regular functions impose constraints on the types they are willing to accept. For instance, the if special form requires a `"boolean"` predicate. All special forms are unique, but the types accepted by library functions follow prescribed patterns. User-defined functions are even more restrictive: only one explicit combination of types is accepted (along with their subtypes).

Library functions have one or more signatures, and these signatures include explicit types, wildcards, and function references. The explicit types found in type signatures are:

- `"boolean"`
- `"int"`
- `"long"`
- `"float"`
- `"double"`
- `"string"`
- `"bytes"`
- arrays of explicit types
- maps of explicit types
- unions of explicit types

For instance, the `m.round` function has two signatures, one takes a `"float"` and returns an `"int"`, the other takes a `"double"` and returns a `"long"`.

As another example, the `s.join` function function has one signature that takes a `{"type": "array", "items": "string"}` and a `"string"` delimiter and returns a single `"string"`.

## Wildcards in function signatures
Wildcards are parts of a function signature that could take different types. This is not like a union type, which accepts different types at runtime, because a wildcard gets resolved during the type-check when a PFA engine is being built. Once resolved, all values must conform to that resolved type.

Wildcards are labeled with letters and the same label may be repeated to indicate that whatever type one wildcard resolves to, all others with the same label must resolve to the same type.

For instance, the `==` function takes two arguments, both with the same wildcard, and returns `"boolean"`. If the first argument is a `"string"`, then the second argument must be a `"string"`. If one argument is an `"int"` and the other is a `"double"`, that is acceptable because the wildcard resolves to the supertype `"double"`.

As another example, the `a.last` function takes an array of some wildcard A and returns the last item in the array, which has type A.

## Records in function signatures
All record types in library function signatures are wildcards with an additional restriction: they must have a specified subset of fields.

For instance, the `stat.sample.updateEWMA` function takes a record that must have a field named `"mean"` of type `"double"`. The function updates an exponentially weighted moving average (EWMA) and returns a new record with a new `"mean"`.

The record type does not need to have any particular name and it is allowed to have other fields in addition to `"mean"`. (Those other fields are passed through without modification.) Thus, the library function does not exclusively "own" its input data type: other library functions can operate on the same record type, requiring it to have other fields, as long as those requirements do not conflict.

As another example, the `model.tree.simpleTest` and `model.tree.simpleWalk` functions each perform half of the job of evaluating a decision tree. The `model.tree.simpleTest` function decides how to step from one tree node to the next, and it requires the tree node to contain `"field"`, `"operator"`, and `"value"` fields. The `model.tree.simpleWalk` function repeatedly applies the test to different nodes until it gets to a leaf, and it requires the tree node to contain `"pass"` and `"fail"` fields. When the two functions are used together, they require all five fields. Alternate stepping functions and walking functions can be combined different functionality. The form of the tree data structure is determined by the set of functions that are to be used on it.

## Enumerated fields in function signatures

Some library functions require an enumeration set whose symbols are fields of another record (referenced by its wildcard label). This ensures that values passed to the function name a field.

An example of this is the `"fields"` field of `model.tree.simpleTest`, which requires the tree node to only name fields in the data records.

## Function references in function signatures

Some library functions require function references as arguments. Functions are not [first-class objects](https://en.wikipedia.org/wiki/First-class_function){: target=_blank } in PFA, in part because the Avro type system cannot express them, but also because this restriction makes PFA algorithms subject to more thorough analysis offline. Accepting a function as an argument to another function is useful for building workflows from generic algorithms, so PFA has the ability to take function references as arguments, even though they cannot be assigned to values.

The parameter types and return types of these function references are restricted, and they share in the same set of wildcard labels as the rest of the calling function’s signature. For instance, the `a.map` function takes the following arguments:

- an array of any `A`
- a function that maps `A` to any `B`

and returns:

- an array of `B`

That is, `a.map` can apply a given function to arrays of any type, but the given function has to operate on that type. Whatever the given function returns, `a.map` returns an array of that type.

The function references may refer to named user-defined functions, inline (anonymous) user-defined functions, or library functions. Named user-defined functions are defined in the [`"fcns"` top-level field](../document-structure/), and user-defined functions have the same syntax, but they can appear directly in the argument list. Both of the following examples square the elements of an array of numbers and round the results.

`{"a.map": ["arrayOfNumbers", {"fcn": "u.squareAndRound"}]}`

where `"squareAndRound"` is defined in the "fcns" top-level field.

```json
{"a.map": ["arrayOfNumbers",
           {"params": [{"x": "double"}],
            "ret": "int",
            "do":
                {"cast.int":
                    {"m.round":
                        {"**": ["x", 2]}}}}
          ]}
```

The parameter type of `"x"` has to accept the item type of `"arrayOfNumbers"` because they both resolve the same wildcard `A`. The function’s return type `"int"` resolves `B`, so the final result of this operation is an array of `"int"`.

Library functions can be used as function references, but only if the library function has exactly one signature and no wildcards. For example, the `s.len` function, which returns the length of a string, can be applied to an array of strings:

`{"a.map": "arrayOfStrings", {"fcn": "s.len"}}`

but the `a.len` function, which returns the length of an array of any type, cannot. (PFA employs a simple type inference algorithm that propagates upward from the leaves of an expression, so the type of A in the function reference would be ambiguous before it can be matched to A in the array.) However, we can always "wrap" a multi-signature or wildcarded library function in a user-defined function to resolve all the types:

```json
{"a.map": ["arrayOfArraysOfStrings",
           {"params": [{"x": {"type": "array",
                              "items": "string"}}],
            "ret": "int",
            "do": {"a.len": "x"}}]}
```

Wrapping library functions is useful for resolving types, but it isn’t needed if you only want to turn a two-argument function into a one-argument function by specifying one of the arguments ([partial application](https://en.wikipedia.org/wiki/Partial_application){: target=_blank }). PFA has a special form for this. Below, we apply `m.special.nChooseK` to an array of `"k"` values with `"n"` fixed to 100.

```json
{"a.map": ["arrayOfK", {"fcn": "m.special.nChooseK",
                        "fill": {"n": 100}}]}
```

!!! Source
    [Data Mining Group Website](http://dmg.org/pfa/){: target=_blank }