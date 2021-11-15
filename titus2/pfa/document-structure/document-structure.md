A PFA document is a JSON document with additional constraints. The JSON content describes algorithms, data types, model parameters, and other aspects of the scoring engine. Some structures have no effect on the scoring procedure and are only intended for archival purposes.

The outermost structure of a PFA document is a JSON object, a collection of key-value pairs surrounded by curly brackets. We refer to these as the top-level fields: the field name (key) determines the allowed content (value). Some of those contents are recursively defined, so a PFA document may have arbitrary depth.

The following are all of the allowed top-level fields.

| Name | Default | Constraints | Description |
|---|---|---|---|
| `input` | *required* | Avro type | Input data type. Input data that do not conform to this schema should be rejected before sending it to PFA.|
| `output` | *required* | Avro type | Output data type. A valid PFA file will always produce data of this type (or an exception).|
| `begin` | [] | expressions | Algorithm to be executed before evaluating any data. See tutorial.|
| `action` | *required* | expressions | Algorithm to be executed for each input datum. See tutorial.|
| `end` | [] | expressions | Algorithm to be executed after evaluating all data (if such a time exists). See tutorial.|
| `fcns` | {} | JSON object of functions | Named user-defined functions that can be called in any expression (including other functions).|
| `cells` | {} | JSON object of cells | Persistent storage containing model parameters, intermediate results, etc. See tutorial.|
| `pools` | {} | JSON object of pools | Like cells, but items can be created and destroyed at runtime and concurrent access extends only to a pool item, not the entire pool. See tutorial.|
| `method` | "map" | "map", "emit", or "fold" | How results from the scoring engine will be served to the environment. See tutorial.|
| `zero` | *required* when method is "fold" | JSON matching type output | The starting value for a "fold" tally. See tutorial.|
| `merge` | *required* when method is "fold" | expressions | Algorithm to combine two partial tallies.|
| `randseed` | *optional* | integer | Global seed used to generate all random numbers. Multiple scoring engines derived from the same PFA file have different seeds generated from the global one.|
| `name` | *none* | name | An optional name for the scoring engine.|
| `version` | *optional* | integer | Sequential version number for the model.|
| `doc` | *optional* | string | Documentation string for archival purposes.|
| `metadata` | {} | JSON object of strings | Computer-readable documentation for archival purposes.|
| `options` | {} | JSON object; value types depend on option name | Initialization or runtime options to customize implementation (e.g. optimization switches). May be overridden or ignored by PFA consumer.|

## Example 1
The smallest possible PFA document (by number of bytes) is
```json
{
    "input":"int",
    "output":"int",
    "action":"input"
}
```
It returns the integer you provide it.

## Example 2
The following example uses every kind of top-level field except pools, zero, and merge (which are similar to other fields and/or are only applicable to “fold” engines).

```json
{
    "input": "string",
    "output": {"type": "array", "items": "string"},
    "cells": {
        "accumulate": {
            "type": {"type": "array", "items": "string"},
            "init": []
        }
    },
    "method": "map",
    "begin": {
        "log": {"rand.gaussian": [0.0, 1.0]}
    },
    "action": {
        "cell": "accumulate",
        "to": {"fcn": "u.addone", "fill": {"newitem": "input"}}
    },
    "end": {
        "log": {"rand.choice": {"cell": "accumulate"}}
    },
    "fcns": {
        "addone": {
            "params": [
                {"old": {"type": "array", "items": "string"}},
                {"newitem": "string"}
            ],
            "ret": {"type": "array", "items": "string"},
            "do": {"a.append": ["old", "newitem"]}
        }
    },
    "randseed": 12345,
    "name": "ExampleScoringEngine",
    "version": 1,
    "doc": "Doesn't do much.",
    "metadata": {"does": "notmuch"},
    "options": {"timeout": 1000}
}
```

!!! Source
    [Data Mining Group Website](http://dmg.org/pfa/docs/document_structure/){: target=_blank }