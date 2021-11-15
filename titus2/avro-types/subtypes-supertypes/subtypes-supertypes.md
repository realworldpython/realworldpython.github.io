Although this type system has no classes, some types are subtypes of others. If `X` is a subtype of `Y`, a value of type `X` would be accepted where type `Y` is expected. In Avro, this is known as [schema resolution].

The four number types form a chain of subtypes in which an `"int"` can be used where a `"double"` is expected, etc. A supertype can always be constructed using unions, since an instance of type `X` can be used where an `[X, Y, Z]` is expected.

A type describes a set of possible values; its subtype describes a subset of those values and its supertype describes a superset of those values. If a type `X` accepts values of type `Y` and `Y` accepts values of type `X`, then `X` and `Y` are exactly the same type.

Some special forms result in the "narrowest possible supertype" of their arguments, the smallest superset of possible values that can be described in the type system, to allow for return values from all code branches. For example, the return type of

```json
{"if": "predicate",
 "then": 3.14,
 "else": {"string": "hello"}}
```

is `["double", "string"]` since it might return a `"double"` and it might return a `"string"`. On the other hand,

```json
{"if": "predicate",
 "then": 3.14,
 "else": {"int": 3}}
```

returns a `"double"` since the `"int"` is already a subtype of `"double"`.

| Expected | Accepts |
|---|---|
| null | `"null"` or `["null"]` |
| boolean | `"boolean"` or `["boolean"]` |
| int | `"int"` or `["int"]` |
| long | `"long"` or a union of any subset of `{"int", "long"}` |
| float | `"float"` or a union of any subset of `{"int", "long", "float"}` |
| double | `"double"` or a union of any subset of `{"int", "long", "float", "double"}` |
| string | `"string"` or `["string"]` |
| bytes | `"bytes"` or `["bytes"]` |
| an array of X | an array of `Y` for which `X` accepts `Y` (arrays are covariant) |
| a map of X | a map of `Y` for which `X` accepts `Y` (maps are covariant) |
| a record type | the same record type, by name |
| an enum type | the same enum type, by name |
| a fixed type | the same fixed type, by name |
| a union of types T | either a union of types `T'` such that for all `t'` in `T'`, there exists a `t` in `T` for which `t` accepts `t'`, or a single type `t''` such that there exists a `t` in `T` for which `t` accepts `t''`. |

!!! Source
    [Data Mining Group Website](http://dmg.org/pfa/){: target=_blank }