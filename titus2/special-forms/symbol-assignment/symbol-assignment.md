PFA has lexical, expression-level scope. You can create variables and access them in any contained expression, including inline functions. You can only modify them at the same level of scope or inside expressions that do not seal their scope from above, such as inline functions (this allows callbacks to be distributed or run in parallel).

All PFA values are immutable: you cannot change an element of an array in-place; you must replace the array. PFA variables are mutable: you can replace the value stored in a variable as long as it has the same type. Pure immutability for PFA values allows them to share structure and have non-blocking read access during shared data updates. Mutability of variable storage allows for straightforward implementation of iterative algorithms.

## Creating variables (let)

Variables are created with `"let"`, which has the following structure:

`{"let": {VAR1: EXPR1, VAR2: EXPR2, ...}}`

where the `VAR` are new variable names and the `EXPR` are their initial values. The initial values set the type. More than one variable can be created at a time, but none of the variables are in scope inside the `"let"` block. In principle, they could be computed in parallel.

Variables can only be created once and variables in deeper scopes cannot shadow other variables of the same name.

### Example

The following creates a union of "null" and "double" with an initial value of null.

`{"let": {"x": {"type": ["null", "double"], "value": null}}}`

`x` can be set to a number later.

## Changing variable bindings (set)

Variables are changed with `"set"`, which has the following structure:

`{"set": {VAR1: EXPR1, VAR2: EXPR2, ...}}`

where the `VAR` are existing variable names and the `EXPR` are their new values. The variables must accept the types of the `EXPR`. More than one variable can be changed at a time, but none of the changes are bound to the variables inside the `"set"` block (which makes swaps possible; see example). Taking advantage of immutability, these updates could be computed in parallel.

### Example
The following swaps the values in a and b.

`{"set": {"a": "b", "b": "a"}}`

!!! Source
    [Data Mining Group Website](http://dmg.org/pfa/){: target=_blank }