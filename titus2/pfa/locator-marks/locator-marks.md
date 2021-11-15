Any JSON object in a PFA document may include `"@"` as a string-valued field. This string is used to provide a line number from the original source file so that errors can be traced back to their source. Consumers may read the PFA file progressively (interpreting it while reading it), so the `"@"` field is only useful if it comes first in the JSON object.

## Example
A function call with locator marks.

```json
{
    "@": "PrettyPFA line 1",
    "a.append": [
        {
            "@": "PrettyPFA line 2",
            "type": {"type": "array", "items": "string"},
            "value": []
        },
        {
            "@": "PrettyPFA line 3",
            "string": "mytext"
        }
    ]
}
```

!!! Source
    [Data Mining Group Website](http://dmg.org/pfa/docs/document_structure/){: target=_blank }