PFA has four types of [expressions](../pfa/document-structure/#expressions): literal values, symbol references, function calls, and special forms. Special forms could be thought of as function calls with irregular constraints on their arguments and return values. The analogy in ordinary programming languages would be keywords like `if` and `while`.

Special forms are all represented by JSON objects with one or more key-value pairs. Other than that, they are all unique.

!!! Source
    [Data Mining Group Website](http://dmg.org/pfa/){: target=_blank }