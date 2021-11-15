The [Avro specification page](http://avro.apache.org/docs/1.7.7/spec.html){: target=_blank } fully describes this type system, but it is reproduced here with PFA-specific notes.

Since Avro is a serialization format, it does not raise the issue of whether its values are mutable (can be changed in-place) or immutable. All values in PFA are immutable.

In both Avro and PFA, values cannot contain circular references: all data structures are trees.

In both Avro and PFA, all values have a well-defined sort order: PFA inerits Avro sort order and equivalence when performing comparisons in functions such as `"<"`, `"max"`, and `"=="`.

## Null type
The null type is referred to as `"null"` (in quotes, as a string), and it has only one possible value (also `null`, but without quotes).

PFA variables cannot change type, so a variable of null type is not useful: it can only ever be null. The null type is used in tagged unions and as a return value for functions and special forms that have nothing to return (instead of introducing "void").

A union of any type, such as string, and null is represented as

`["null", "string"]`

and indicates that the string value may be missing. Since this type is distinct from `"string"`, the rigor of the type system enforces an action to be defined for the missing-string case.

## Boolean
The boolean type is referred to as `"boolean"` and it has only two possible values, `true` and `false` (without quotes).

Boolean is not a subclass of integers, so turning a boolean variable x into zero or one requires an expression like

`{"if": "x", "then": 1, "else": 0}`

## Integer types
There are two integer types, `"int"` for 32-bit, signed integers and `"long"` for 64-bit, signed integers. The `"int"` form is default for most functions, and literal integers in JSON and [PFA expressions](../document-structure/#expressions) are interpreted as `"int"`.

## Floating point types
There are two floating-point types, `"float"` for 32-bit, [IEEE floating-point numbers](https://en.wikipedia.org/wiki/IEEE_754){: target=_blank } and `"double"` for 64-bit. The `"double"` form is default for most functions, and literal floating-point numbers in JSON and [PFA expressions](../document-structure/#expressions) are interpreted as `"double"`.

The `"int"`, `"long"`, `"float"`, and `"double"` types form a chain of subtypes:

| This type | Can be used where this is expected |
|---|---|
|"int" | "int", "long", "float", "double"|
|"long" | "long", "float", "double"|
|"float" | "float", "double"|
|"double" | "double"|

## String type
The string type is referred to as `"string"` and it can accept any valid Unicode sequence.

Strings are not arrays and they cannot be changed in-place (all PFA values are immutable).

There is no character type: characters are represented by strings of length 1.

In JSON, a value of type `"string"` is represented as a simple JSON string:

`"hello"`

but in a [PFA expression](../document-structure/#expressions), it has to be qualified to avoid confusion with variable names:

`{"string": "hello"}`

The appropriate form to use depends on context within the PFA document. A cell or pool’s `"init"` section takes a value as JSON, while the `"action"` top-level field takes expressions. This allows large sets of model parameters to be expressed more concisely in a cell or pool while expressions that apply the model can be more expressive.

## Bytes type
The bytes type is referred to as `"bytes"` and it can accept any byte sequence. It is therefore a generalization of a string (though neither is a subtype of the other).

Values of type `"bytes"` are not arrays and cannot be changed in-place (all PFA values are immutable).

In JSON, a value of type `"bytes"` is a raw string:

`"hello"`

but in a [PFA expression](../document-structure/#expressions), it has to be qualified, either using base-64 or as a wrapped string:

`{"type": "bytes", "value": "hello"}`

or

`{"base64": "aGVsbG8="}`

## Arrays
Arrays are homogeneous, ordered collections of items. For any type X, an array of X can be constructed by

`{"type": "array", "items": X}`

For instance, an array of strings is

`{"type": "array", "items": "string"}`

For an array to contain items of mixed type, they must be explicitly declared as a union. For instance, an array of nullable, floating-point numbers is

`{"type": "array", "items": ["null", "double"]}`

When extracted from the array, these items would have to be further unpacked to handle both `"null"` and `"double"` cases.

The `"items"` may contain any type, no matter how complex. Two-dimensional arrays are formed by constructing an array of arrays:

```json
{"type": "array", "items":
    {"type": "array", "items": "double"}
}
```

Since arrays, like all PFA values, are immutable, they have fixed length. To grow an array, you must replace short versions with longer versions using something like the `a.append` function. Functional programming with `a.map`, `a.filter`, `a.reduce`, etc., is a better match to array's immutability.

In JSON, an array object is represented by square brakets:

`["one", "two", "three"]`

The type of a JSON array is ambiguous without reference to the schema. (The above could be an array of strings, an array of bytes, or an array of enumeration symbols.)

In a [PFA expression](../document-structure/#expressions), a type must be given:

```json
{"type": {"type": "array", "items": "string"},
 "value": ["one", "two", "three"]}
```

## Maps
Maps are homogeneous, unordered key-value pairs in which the keys are strings. They may be thought of as arrays in which the index (key) is a string, rather than an integer (position). For any type `X`, a map from string to `X` can be constructed by

`{"type": "map", "values": X}`

For instance, a map from strings to floating-point numbers is

`{"type": "map", "values": "double"}`

Maps follow the same composition rules as arrays, and they are similarly immutable. Maps may be grown using `map.add` or transformed with `map.map`, `map.filter`, etc., in direct analogy with arrays.

In JSON, a map object is represented by curly brackets:

`{"one": 1, "two": 2, "three": 3}`

Like arrays, the type of a JSON map is ambiguous without reference to the schema. (The above could be a map of integers or a map of floating-point numbers.)

In a [PFA expression](../document-structure/#expressions), a type must be given:

```json
{"type": {"type": "map", "values": "int"},
 "value": {"one": 1, "two": 2, "three": 3}]}
```

## Records

Records are named, heterogeneous collections of a fixed set of named fields. Below is an example of a record with three fields: one (an int), two (a double), and three (a string).

```json
{"type": "record",
 "name": "MyRecord",
 "fields": [
     {"name": "one", "type": "int"},
     {"name": "two", "type": "double"},
     {"name": "three", "type": "string"}
 ]}
``` 

Records are similar to maps, but with three exceptions:

- Maps can have any string-valued keys; records have specific fields that must always be present, and those field names are restricted to the following regular expression: `[A-Za-z_][A-Za-z0-9_]*`.
- Map values must all have the same type; record field values can each be different.
- Maps are unnamed; records have names to distinguish records with different sets of keys and to allow records to be recursively nested (see example below).

Like a map, a record in JSON is represented by curly brakets:

`{"one": 1, "two": 2.2, "three": "THREE"}`

Again, the JSON is ambiguous without reference to the schema, since the above could be a record or a `{"type": "map", "values": ["double", "string"]}`.

As with all three named types (record, enum, and fixed), a `"name"` field must be present and a `"namespace"` is optional. Also, a specific record type can only be defined once per PFA document; all other references must be by name (though the full declaration does not need to be first). The name of a record is fully-qualified: if it has a namespace, that namespace must precede the name with a dot.

Here is an example of a recursively defined record type: a binary tree with strings as leaves.

```json
{"type": "record",
 "name": "Node",
 "namespace": "tree",
 "fields": [
     {"name": "left", "type": ["tree.Node", "string"]},
     {"name": "right", "type": ["tree.Node", "string"]}
 ]}
```

The types of this record's fields refer to the record by reference: `"tree.Node"` is its fully-qualified name. The fields are unions of `"tree.Node"` with `"string"` so that the tree can terminate on values of a different type (and not be infinite). Here’s what an example of such a tree would look like in JSON:

```json
{"left": {"tree.Node":
             {"left":  {"string": "L-L"},
              "right": {"string": "L-R"}}},
 "right": {"tree.Node":
             {"left":  {"string": "R-L"},
              "right": {"string": "R-R"}}}}
```

The `{"tree.Node": ...}` and `{"string": ...}` qualifiers are for tagging the union values in JSON.

Another way to form a recursive record is to give it array or map subfields, since these containers may be empty, making it possible to terminate the tree.

```json
{"type": "record",
 "name": "Tree",
 "fields": [
     {"name": "children", "type": {"type": "array", "items": "Tree"}}
 ]}
```

Here is an example of such a tree in JSON:

`{"children": [{"children": []}, {"children": []}]}`

As with all PFA values, records are immutable. To change one field in a record, use the attr-to special form. To change multiple fields, create a new record with the new special form.

Records have additional features that are primarily relevant for serialization and deserialization. See the [Avro specification](http://avro.apache.org/docs/1.7.7/spec.html#schema_record){: target=_blank } for details. Only the sort order property affects processing in PFA (for functions such as `"<"`, `"max"`, and `"=="`).

## Enumeration sets
Enumeration sets or “enums” are small, finite sets of strings. The strings must be Avro names (`[A-Za-z_][A-Za-z0-9_]*`). Below is an example of an enum with five symbol values.

```json
{"type": "enum",
 "name": "SmallNumbers",
 "symbols": ["one", "two", "three", "four", "five"]}
```

As with all three named types (record, enum, and fixed), a `"name"` field must be present and a `"namespace"` is optional. Also, a specific enum type can only be defined *once* per PFA document; all other references must be by name (though the full declaration does not need to be first). The name of an enum is fully-qualified: if it has a namespace, that namespace must precede the name with a dot.

To refer to an enumeration value in JSON, simply reference its symbol name:

`"three"`

This is, of course, ambiguous, since it could also be a string (or a bytes).

PFA usually uses enumeration sets to specify categorical variables with a fixed set of categories. If sorted or compared using PFA’s inequalities, the order is given by the order of the symbols in the type declaration. For example, the five symbols above would be sorted as

`"one", "two", "three", "four", "five"`

not as

`"five", "four", "one", "three", "two"`

## Fixed-width byte sequences

Avro also provides for named raw byte sequences with fixed length. A 6-byte MAC address type would be defined as

```json
{"type": "fixed",
 "name": "MACAddress",
 "size": 6}
```

As with all three named types (record, enum, and fixed), a `"name"` field must be present and a `"namespace"` is optional. Also, a specific enum type can only be defined *once* per PFA document; all other references must be by name (though the full declaration does not need to be first). The name of an enum is fully-qualified: if it has a namespace, that namespace must precede the name with a dot.

Fixed-type values are not arrays and cannot be changed in-place (all PFA values are immutable).

They are primarily intended for tighter packing in Avro serialization and are not particularly useful in PFA.

## Tagged unions

To allow for variables that can take one of several types, Avro and PFA have tagged unions. They are "tagged" in the sense that a value’s specific type is always available. A union of `X`, `Y`, and `Z` is represented as a list of these three types:

`[X, Y, Z]`

For instance, a variable that could be `"null"`, `"string"`, or a map of `"int"` would be expressed as

`["null", "string", {"type": "map", "values": "int"}]`

At least two types must be provided, types may not be repeated, and directly nested unions are not allowed.

With the exception of `"null"`, union values in JSON are singleton JSON objects whose key is the type name and whose value is the value.

|Type | Name | Example value in JSON|
|---|---|---|
|null | *none* | `null` (without quotes)|
|boolean | `"boolean"` | `{"boolean": true}`|
|int | `"int"` | `{"int": 3}`|
|long |`"long"` | `{"long": 3}`|
|float | `"float"` | `{"float": 3.14}`|
|double | `"double"` | `{"double": 3.14}`|
|string | `"string"` | `{"string": "hello"}`|
|bytes | `"bytes"` | `{"bytes": "hello"}`|
|array | `"array"` | `{"array": [1, 2, 3]}`|
|map | `"map"` | `{"map": {"one": 1, "two": 2, "three": 3}}`|
|record | *fully-qualified name* | `{"MyRecord": {"one": 1, "two": 2, "three": 3}}`|
|enum | *fully-qualified name* | `{"SmallNumbers": "three"}`|
|fixed | *fully-qualified name* | `{"MACAddress": "^)=;T{"}`|
|union | *not allowed* | |

(Note that a union containing two types of arrays, such as an array of `"int"` and an array of `"string"`, cannot be disambiguated. An array of `["int", "string"]`, however, is unambiguous and less restrictive.)

PFA primarily uses unions to express the possibility of missing data. For instance, values of type `"double"` are never missing, but values of type `["double", "null"]` are either floating-point numbers or `null` (missing). The non-nullable and nullable types are not interchangeable. For example, you cannot add nullable numbers:

```json
[{"let": {"xornull": {"type": ["double", "null"],
                      "value": null},
          "yornull": {"type": ["double", "null"],
                      "value": {"double": 3.14}}}},
 {"+": ["xornull", "yornull"]}]
```

The above is invalid because the `"+"` function can only add numbers, not nullable numbers. Even if `xornull` and `yornull` are *usually* numbers, a PFA consumer will not accept their sum because one of them might, at runtime, be `null`. To add them, you must provide for the null case, which is known as a type-safe null.

For example, the sum could be replaced with the ifnotnull special form:

```json
{"ifnotnull": {"x": "xornull", "y": "yornull"},
 "then": {"+": ["x", "y"]},
 "else": 0}
```

Some functions, such as the ones in the impute library, three-state logic, and missing value variants of the tree model, take nullable types directly.

Of course, null is not the only type that can be included in a union, so PFA has the cast-cases special form for unpacking general unions.

!!! Source
    [Data Mining Group Website](http://dmg.org/pfa/){: target=_blank }