The "cell", "cell-to", "pool", and "pool-to" special forms have nearly the same structure as "attr" and "attr-to", but they operate on global data (cells and pools). All access to global data must go through these special forms; there are no others.

The path extraction and function updates have more relevance for global data, which may be remote and shared. If the data structure is large and remote, specifying the full path to the substructure of interest reduces the necessary network bandwidth, since only the substructure needs to be returned, not the whole second level of the object.

If the data structure is shared, the update function defines a transaction in which one scoring engine gets an exclusive lock on the object, which is necessary for correct concurrency.

## Retrieving cell values (cell)
Cells may be accessed with one of the following structures:

`{"cell": NAME}`

or

`{"cell": NAME, "path": [INDEX1, INDEX2, ...]}`

where NAME is a string, the name of the cell (not an expression). The INDEX are expressions that follow the same rules as for “attr”.

### Example
Model parameters are often stored in cells.

`{"model.cluster.closest": ["input", {"cell": "clusters"}]}`

## Changing cell values (cell-to)

Cells may be updated with one of the following structures:

`{"cell": NAME, "to": VALUE-OR-FUNCTION}`

or

`{"cell": NAME, "path": [INDEX1, INDEX2, ...], "to": VALUE-OR-FUNCTION}`

where `NAME` is a string (as above) and `VALUE-OR-FUNCTION` is either a new value or an update function (as above).

Unlike "attr-to", "cell-to" changes the value of the cell. Subsequent requests for the cell will see the new value.

The return value of this form is the new cell value, which can be used to avoid a second request.

### Example

Both of the following are intended to update a counter, but the first can lead to data corruption if the cell is shared. Another scoring engine that shares the cell might update the counter value before this one is finished.

```json
{"cell": "counter", "to": {"+": [{"cell": "counter"}, 1]}}
```

```json
{"cell": "counter", "to":
    {"params": [{"old": "int"}],
     "ret": "int",
     "do": {"+": ["old", 1]}}}
```

## Retrieving pool values (pool)

Pools may be accessed with the following structure:

`{"pool": NAME, "path": [INDEX1, INDEX2, ...]}`

where `NAME` is a string, the name of the pool (not an expression). The `INDEX` are expressions that follow the same rules as for "attr".

### Example

Segmented model parameters are often stored in pools.

```json
{"model.cluster.closest": [
     "input.vector",
     {"pool": "segmentsOfClusters",
      "path": ["input.segment"]}]}
```

## Changing pool values (pool-to)

Pool items may be updated with the following structure:

```json
{"pool": NAME, "path": [INDEX1, INDEX2, ...],
 "to": VALUE-OR-FUNCTION, "init": VALUE}
```

where `NAME` is a string (as above) and `VALUE-OR-FUNCTION` is either a new value or an update function (as above). The initialization `VALUE` is an expression, which is used if a particular pool item does not yet exist.

Unlike "attr-to", "pool-to" changes the value of the pool item. Subsequent requests for that pool item will see the new value.

The return value of this form is the new pool item (not the whole pool).

### Example

The following updates a segmented exponentially weighted moving average (EWMA) or creates a new segment if one does not exist.

```json
{"pool": "segmentedEWMAs", "path": ["input.segment"],
 "to":
     {"params": [{"old": "EWMARecord"}],
      "ret": "EWMARecord",
      "do":
          {"stat.sample.updateEWMA":
               ["input.value", 0.1, "old"]}},
 "init":
     {"type": "EWMARecord", "new": {"mean": "input.value"}}
}
```

## Removing pool values (pool-del)

Pool items may be deleted with the following structure:

`{"pool": NAME, "del": EXPRESSION}`

where `NAME` is a string (as above) and `EXPRESSION` evaluates to the name of the pool item to delete. Its return type is `"null"`.

### Example

The following deletes a segment from a segmented model.

`{"pool": "segments", "del": "input.segmentToDelete"}`

!!! Source
    [Data Mining Group Website](http://dmg.org/pfa/){: target=_blank }