## Basic Access (3)

| Short Description | Function |
|---|---|
| Length | `map.len` |
| Extract the keys | `map.keys` |
| Extract the values | `map.values` |

## Search and Replace (2)

| Short Description | Function |
|---|---|
| Contains key | `map.containsKey` |  
| Contains value | `map.containsValue` | 

## Manipulation (7)

| Short Description | Function |
|---|---|
| Insert a key-value pair | `map.add` | 
| Remove a key | `map.remove` |
| Keep only certain keys | `map.only` | 
| Keep all except certain keys  | `map.except` | 
| Add or replace keys with an overlay map | `map.update` |
| Split map into an array of single-key maps | `map.split` | 
| Join an array of maps into one map | `map.join` | 

## Extreme Values by Key (8)

| Short Description | Function |
|---|---|
| Argument maximum | `map.argmax` | 
| Argument minimum | `map.argmin` | 
| Argument maximum with a less-than function  | `map.argmaxLT` | 
| Argument minimum with a less-than function  | `map.argminLT` | 
| Maximum *N* arguments | `map.argmaxN` |
| Minimum *N* arguments | `map.argminN` |
| Maximum *N* arguments with a less-than function  | `map.argmaxNLT` |
| Minimum *N* arguments with a less-than function  | `map.argminNLT` |

## Set or Set-like Functions (9)

| Short Description | Function |
|---|---|
| Convert an array to a map-set | `map.toset` |
| Convert a map to an array-set | `map.fromset` |
| Determine if an object is in the set | `map.in` |
| Union | `map.union` |
| Intersection | `map.intersection` | 
| Set difference  | `map.diff` |
| Symmetric set difference | `map.symdiff` |
| Subset check | `map.subset` |
| Disjointness check | `map.disjoint` |

## Functional Programming (10)

| Short Description | Function |
|---|---|
| Transform map items with a function | `map.map` |
| Transform map items, providing access to the key  | `map.mapWithKey` |
| Filter map items with a function | `map.filter` |
| Filter map items, providing access to the key  | `map.filterWithKey` |
| Filter and map | `map.filterMap` |
| Filter and map, providing access to the keys  | `map.filterMapWithKey` |
| Map and flatten | `map.flatMap` |
| Map and flatten, providing access to the keys  | `map.flatMapWithKey` |
| Zip and map | `map.zipmap` |
| Zip and map, providing access to the keys  | `map.zipmapWithKey` |

## Functional Tests (2)

| Short Description | Function |
|---|---|
| Pairwise check of two maps | `map.corresponds` |
| Pairwise check, providing access to the keys  | `map.correspondsWithKey` |
