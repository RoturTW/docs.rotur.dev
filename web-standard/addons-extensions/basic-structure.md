# Basic structure

An extension is an object with two keys: `metadata`, which describes the extension, and `events`, which maps event names to handlers.

```json5
{
    "metadata": {
        "id": "myext",
        "name": "My Extension",
        "description": "A short description", // optional
        "author": "flufi",
        "language": "osl"
    },
    "events": {
        "onload": ...,
        "anotherevent": ...
    }
}
```

## Metadata

| Key | Required | Description |
| --- | --- | --- |
| `id` | Yes | Unique identifier for the extension |
| `name` | Yes | Display name |
| `description` | No | Short description |
| `author` | Yes | Author's name |
| `language` | Yes | Language the handlers are written in, for example `osl` |

## Events

Each key in `events` is an event name and each value is the handler to run. See [Events](events/README.md) for the events a browser sends.
