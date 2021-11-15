Cells and pools are both persistent storage, but cells are global variables that cannot be created or destroyed at runtime (only reassigned) and pools are like environments in R: collections of key-value pairs that can be created and destroyed at runtime, and the granularity of concurrent access is at the level of a single pool item.

Cells and pools are both specified as JSON objects with the same fields, though `init` is required for cells and not for pools.

| Name | Default | Constraints | Description |
|---|---|---|---|
| type | *required* | Avro type | The type of the value in the cell or the type of a single pool item.|
| init | *required* for cells, `{}` for pools | JSON matching `type` or string | If source is embedded, the contents are the initial value of the cell or pool. Otherwise, the contents are a string URL pointing to a resource containing the initial value.|
| shared | `false` | boolean | If true, all scoring engines derived from this PFA file share a common, consistent value in the cell or pool.|
| rollback | `false` | boolean | If true, the value in the cell or pool reverts to the value it had at the beginning of an action when an action raises an exception. Incompatible with shared.|
| source | `"embedded"` | `"embedded"`, `"json"`, or `"avro"` | If "embedded", the initial value is located within this PFA file (in init). Otherwise, init is a URL pointing to the data (including "file://" prefix for local files) with "json" or "avro" format.|

## Example 1

The following is repeated from example 2 above: a global variable that stores an array of strings. The initial value is `[]`, but it could also be `["one", "two", "three"]`.

```json
"cells": {"accumulate": {"type": {"type": "array", "items": "string"},
                        "init": []}}
```

## Example 2

Pool types are implicitly maps— the following maps a counter name to an integer. Counters can be created or destroyed at runtime.

```json
"pools": {"counters": {"type": "int", "shared": "true"}}
```

!!! Source
    [Data Mining Group Website](http://dmg.org/pfa/docs/document_structure/){: target=_blank }