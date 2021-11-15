Every programming language has an implicit or explicit type system, and most of these type systems are similar to one another. Rather than invent a new type system, PFA uses the same one as the [Avro serialization format](http://avro.apache.org){: target=_blank }. Avro describes data types using strings and JSON objects, so PFA simply includes Avro as a language subset.

The Avro/PFA type system has

- boolean, integer, floating-point, and string primitives,
- a null type, which PFA uses to represent missing data,
- arrays of homogeneous, sequential items,
- maps of homogeneous, unordered key-value pairs,
- named records of heterogeneous named fields,
- named enumeration sets,
- byte sequences (raw strings),
- named, fixed-width byte sequences,
- and tagged unions.

The same type system applies to input data, output data, persistent data structures like model parameters, and function signatures.

!!! Source
    [Data Mining Group Website](http://dmg.org/pfa/){: target=_blank }