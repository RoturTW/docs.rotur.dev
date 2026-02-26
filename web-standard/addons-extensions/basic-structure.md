# Basic Structure

The structure of extensions should follow this:

```json5
{
    "metadata": {
        "id": "myext",
        "name": "my Extension",
        "description": "a funny extension :3", // optional
        "author": "flufi",
        "language": "osl"
    },
    "events": {
        "onload": ...,
        "anotherevent": ...
    }
}
```
