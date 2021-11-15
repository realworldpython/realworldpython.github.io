Expressions are the closest constructs PFA has to "code". They are evaluated in the context of predefined variables and most return a value.

There are four fundamental types of expressions:

- **literal values** (constants)
- **symbol references** (variables)
- **special forms** (language constructs like `if` and `while`)
- **function calls** (including operators like `+` and `-`)

Every top-level field that can take a single expression can take a JSON array of expressions— the return value is the last expression in the array.

!!! example "Example Expressions"
    All of the following expressions return the number 3.

    - 
    ```json
    3
    ```
    - 
    ```json
    {"+": [2, 1]}
    ```
    - 
    ```json
    {"+": [{"+": [1, 1]}, 1]}
    ```
    - 
    ```json
    [{"let": {"x": 0}},
    {"set": {"x": {"+": ["x", 1]}}},
    {"set": {"x": {"+": ["x", 1]}}},
    {"x": {"+": ["x", 1]}}]
    ```

## Literal values
The following are all possible forms of literal values:

| JSON form | Examples <div style="width:8rem;"></div> | Description |
|---|---|---|
| null | `null` | Only value of type “null”. |
| boolean | `true`, `false` | Only values of type “boolean”. |
| integer | `3`, `-3` | Easy way to express integers. |
| floating-point number | `3.0`, `-3.14`, `1e8` | Easy way to express double-precision numbers. |
| bracketed string | `["hello"]` | Easy way to express a string, but it can only be used in contexts where a JSON array of expressions is illegal (otherwise, hello would be interpreted as a variable name in a single-expression array).|
|singleton JSON object | `{"int": 3}` | Explicit declaration of type. |
| | `{"long": 3}` | |	 
| | `{"float": 3}` | | 
| | `{"double": 3}` | | 
| | `{"string": "hello"}` | More explicit than ["hello"] and therefore less prone to error.|
| | `{"base64": "aGVsbG8="}` | Base-64 may be a more convenient way to express binary sequences.|
| type-value special form | `{"type": "int",`<br> `"value": 3}` | Any Avro type can be used in the "type" field; works for arrays, maps, records, unions, etc.|

The type-value special form may be used as an alternative to base-64 for binary sequences, since the JSON value of a bytes type is a raw string:   
`{"type": "bytes", "value": "hello"}`   
versus   
`{"base64": "aGVsbG8="}`

## Symbol references
A JSON string in a context where an expression is expected is always interpreted as a symbol reference.

### Example
In the following, "input" is a variable and "hello" is a string.   
`{"s.concat": ["input", {"string": "hello"}]}`  
or   
`{"s.concat": ["input", ["hello"]]}`

## Special forms
Special forms are JSON objects with specified structure. Each special form is unique: see the special forms page for details.

## Function calls
Function calls have the following forms for zero arguments, one argument, and more than one arguments.

`{"function.name": []}`   
or   
`{"function.name": "argument"}`    
or   
`{"function.name": ["arg1", "arg2", "arg3"]}`   
Library functions are built into PFA and user-defined functions are declared in the fcns section of a PFA document. See the function library for a list of all predefined functions.

In the fcns section, user-defined functions must be declared with names that have no dots (`.`), but when they are called, they are always prepended by `u.` to avoid collisions with library functions.

### Example
A function declared as
```json
"fcns": {
    "addone": {
        "params": [
            {"old": {"type": "array", "items": "string"}},
            {"newitem": "string"}
        ],
        "ret": {"type": "array", "items": "string"},
        "do": {"a.append": ["old", "newitem"]}
    }
}
```
would be accessed as `{"u.addone": ["oldarray", "newitem"]}`

!!! Source
    [Data Mining Group Website](http://dmg.org/pfa/docs/document_structure/){: target=_blank }