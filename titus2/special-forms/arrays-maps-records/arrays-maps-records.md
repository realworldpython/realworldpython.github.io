## Retrieving nested values (attr)

Arrays, maps, and records of arbitrary depth have a unified dereferencing special form. It can extract substructures of arbitrary depth in one pass, so that the equivalent of `x.y[2].z["hello"][4]` is a one form, not five. Its structure is:

`{"attr": EXPRESSION, "path": [INDEX1, INDEX2, ...]}`

where `EXPRESSION` is an expression and the `INDEX` are one or more expressions. At each level, if the substructure is an array, the corresponding `INDEX` must resolve to `"int"` type, if it is a map, the `INDEX` must resolve to `"string"` type, and if it is a record, the `INDEX` must be a literal string.

Arrays are zero-indexed, so `0` is the first element.

Variables that contain dots are a shortcut for record dereference. That is, a string with a form like:

`"FIRST.SECOND.THIRD"`

gets expanded as

`{"attr": "FIRST", "path": [{"string": "SECOND"}, {"string": "THIRD"}]}`

### Example

The `x.y[2].z["hello"][4]` example would look like the following in PFA:

```json
{"attr": "x", "path": [
    {"string": "y"},
    2,
    {"string": "z"},
    {"s.lower": "HELLO"},
    {"+": [2, 2]}
]}
```

First, `x` (a record) is dereferenced to extract field `y` (an array), which is dereferenced to extract item `2` (a record), which is dereferenced to extract field `z` (a map), which is dereferenced to extract key `"hello"` (an array), which is dereferenced to extract item `4`.

The same could be done in stages:

```json
{"attr":
  {"attr":
    {"attr":
      {"attr":
        {"attr": "x",
         "path": [{"string": "y"}]},
       "path": [2]},
     "path": [{"string": "z"}]},
   "path": [{"s.lower": "HELLO"}]},
 "path": [{"+": [2, 2]}]}
```

The dot shortcut can only be used to simplify the innermost

`{"attr": "x", "path": [{"string": "y"}]}`

to

`"x.y"`

## Creating a copy with different nested values (attr-to)

A similar structure can be used to "modify" substructure. By "modify', we mean create a copy with that substructure altered.

`{"attr": EXPRESSION, "path": [INDEX1, INDEX2, ...],
 "to": VALUE-OR-FUNCTION}`

where `EXPRESSION` is an expression and the `INDEX` are one or more expressions. As above, the types of the `INDEX` must match the corresponding substructures.

The `VALUE-OR-FUNCTION` could be an expression with the right type to replace the substructure or it could be a function from that type to that type. If a function, the original value is passed to the function and the result of the function is taken to be the replacement.

### Example
Both of the following update a counter, but the first accesses it twice.

```json
{"attr": "counters", "path": ["whichCounter"], "to":
  {"+": [{"attr": "counters", "path": ["whichCounter"]}, 1]}}

{"attr": "counters", "path": ["whichCounter"], "to":
  {"params": [{"old": "int"}],
   "ret": "int",
   "do": {"+": ["old", 1]}}}
```

!!! Source
    [Data Mining Group Website](http://dmg.org/pfa/){: target=_blank }