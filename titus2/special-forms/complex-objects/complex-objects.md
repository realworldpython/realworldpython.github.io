Simple values, like numbers and booleans, can be [inserted directly into PFA](../../pfa/document-structure/#literal-values) as JSON numbers and booleans. Strings require annotation to be distinguished from variable names. Complex objects, such as arrays, maps, records, enumeration symbols, etc., additionally require a type specification. Even an empty array has an `"items"` data type to specify what *could* be inserted into the array.

PFA has a general mechanism for making any type of object from embedded JSON whose value is known at "compile-time" (when the scoring engine is constructed). However, that does not allow you to create arrays, maps, or records from variables, so a second special form exists for that case.

When constructing an array, map, or record whose value can be specified at compile-time, the embedded JSON method is preferable because more optimizations are possible (such as constructing exactly one copy of the object and referencing it). Common examples of this are empty arrays, empty maps, and initial value records.

## Creating compile-time constants from embedded JSON (type-value)

To make any type of object from embedded JSON, use the following structure:

`{"type": TYPE, "value": JSON-VALUE}`

where `TYPE` is an Avro type and `JSON-VALUE` is a JSON representation of the object. This `JSON-VALUE` is not an expression, so variables and function calls can never appear in it.

### Example

The following example illustrates the difference between this form and the `"new"` special form (below).

```json
{"type": {"type": "array", "items": "string"},
 "value": ["one", "two", "three"]}
```
creates an array of strings whose value is `["one", "two", "three"]`.

```json
{"type": {"type": "array", "items": "string"},
 "new": ["one", "two", "three"]}
```
creates an array of strings containing the contents of the variables *one*, *two*, and *three*. If *one* is `"1"`, *two* is `"2"`, and *three* is `"3"`, the result would be `["1", "2", "3"]`.

## Creating arrays, maps, and records from runtime data (new)

The `"new"` special form creates arrays, maps, and records, taking expressions, rather than embedded JSON values. The array form has the following structure:

`{"type": ARRAY-TYPE, "new": [EXPR1, EXPR2, ...]}`

where `ARRAY-TYPE` is an array Avro type and the `EXPR` are zero or more expressions whose types can be accepted by the array's `"items"` type.

The map and record forms have the following structure:

`{"type": TYPE, "new": {KEY1: EXPR1, KEY2: EXPR2, ...}}`

where `TYPE` is a map or record Avro type, the `KEY` strings are keys for maps and field names for records, and the `EXPR` are expressions. For a record, all the fields of the record must be present. The types of the `EXPR` must be accepted by the map's `"values"` or the record’s field types.

### Example
This creates a record out of three expressions.

```json
{"type": {"type": "record",
          "name": "ExampleRecord",
          "fields": [
              {"name": "one", "type": "int"},
              {"name": "two", "type": "double"},
              {"name": "three", "type": "string"}
          ]},
 "new": {
     "one": 1,
     "two": {"+": [2, 0.2]},
     "three": {"a.lower": "THREE"}
  }}
```

!!! Source
    [Data Mining Group Website](http://dmg.org/pfa/){: target=_blank }