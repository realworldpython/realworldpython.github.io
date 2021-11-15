## Basic Access (7)

| Short Description | Function |
|---|---| 
| Length | `a.len` | 
| Extract subsequence  | `a.subseq` |
| Extract the first item | `a.head` |
| Extract all but the first item | `a.tail` |
| Extract the last item | `a.last` |
| Extract all but the last item | `a.init` |
| Modify subsequence | `a.subseqto` |

## Search and Replace (6)

| Short Description | Function |
|---|---| 
| Contains | `a.contains` |
| Count instances | `a.count` |
| Find first index | `a.index` |
| Find last index | `a.rindex` |
| Check start  | `a.startswith` | 
| Check end | `a.endswith` |

## Manipulation (7)

| Short Description | Function |
|---|---| 
| Concatenate two arrays | `a.concat` |
| Append | `a.append` |
| Append to a circular buffer with a maximum size | `a.cycle` |
| Insert or prepend | `a.insert` |
| Replace item | `a.replace` | 
| Remove item | `a.remove` |
| Rotate an array left | `a.rotate` |

## Reordering (4)

| Short Description | Function |
|---|---| 
| Sort  | `a.sort` |
| Sort with a less-than function | `a.sortLT` |
| Randomly shuffle array | `a.shuffle` |
| Reverse order | `a.reverse` |

## Extreme Values (16)

| Short Description | Function |
|---|---| 
| Maximum of all values  | `a.max` |
| Minimum of all values | `a.min` |
| Maximum with a less-than function | `a.maxLT` |
| Minimum with a less-than function | `a.minLT` | 
| Maximum *N* items | `a.maxN` |
| Minimum *N* items | `a.minN` |
| Maximum *N* with a less-than function | `a.maxNLT` |
| Minimum *N* with a less-than function  | `a.minNLT` |
| Argument maximum | `a.argmax` |
| Argument minimum | `a.argmin` |
| Argument maximum with a less-than function  | `a.argmaxLT` |
| Argument minimum with a less-than function  | `a.argminLT` |
| Maximum *N* arguments | `a.argmaxN` |
| Minimum *N* arguments  | `a.argminN` |
| Maximum *N* arguments with a less-than function  | `a.argmaxNLT` |
| Minimum *N* arguments with a less-than function  | `a.argminNLT` |

## Numerical Combinations (9)

| Short Description | Function |
|---|---| 
| Add all array values | `a.sum` |
| Multiply all array values  | `a.product` |  
| Sum of logarithms | `a.lnsum` | 
| Log of the sum of exponentials without roundoff error  | `a.logsumexp` |
| Arithmetic mean | `a.mean` |
| Geometric mean | `a.geomean` |
| Median | `a.median` |
| Percentile in unit interval | `a.ntile` |
| Mode, or most common value | `a.mode` |

## Set or Set-like Functions (8)

| Short Description | Function |
|---|---| 
| Distinct items  | `a.distinct` |
| Set equality | `a.seteq` |
| Union | `a.union` |
| Intersection | `a.intersection` |
| Set difference | `a.diff` |
| Symmetric set difference | `a.symdiff` |
| Subset check | `a.subset` |
| Disjointness check | `a.disjoint` |

## Functional Programming (16)

| Short Description | Function |
|---|---| 
| Transform array items with function | `a.map` |
| Transform array items, providing access to the index  | `a.mapWithIndex` |
| Filter array items with a function | `a.filter` |
| Filter array items, providing access to the index  | `a.filterWithIndex` |
| Filter and map | `a.filterMap` |
| Filter and map, providing access to the index  | `a.filterMapWithIndex` |
| Map and flatten | `a.flatMap` |
| Map and flatten, providing access to the index  | `a.flatMapWithIndex` |
| Zip and map | `a.zipmap` |
| Zip and map, providing access to the index  | `a.zipmapWithIndex` |
| Reduce array items to a single value | `a.reduce` |
| Right-to-left reduce | `a.reduceRight` |
| Fold array items to another type | `a.fold` |
| Right-to-left fold | `a.foldRight` |
| Take items until predicate is false | `a.takeWhile` |
| Drop items until predicate is true | `a.dropWhile` | 

## Functional Tests (4) 

| Short Description | Function |
|---|---| 
| Existential check, `∃` | `a.any` | 
| Univeral check, `∀` | `a.all` | 
| Pairwise check of two arrays | `a.corresponds` |
| Pairwise check, providing access to the index  | `a.correspondsWithIndex` |

## Restructuring (5)

| Short Description | Function |
|---|---| 
| Sliding window | `a.slidingWindow` |
| Unique combinations of a fixed size | `a.combinations` | 
| Permutations | `a.permutations` | 
| Flatten array  | `a.flatten` |
| Group items by category | `a.groupby` |