# Structure

An RWL file is made of elements, blocks, segments, attributes, and values. Comments use `//` for a single line and `/* */` for several lines.

## Elements

An element is a single item on the page, such as a piece of text. Attributes go in square brackets after the value:

```js
"hi" [id = "myID"]
```

Separate several attributes with commas:

```js
"hi" [id = "myID", font = "llama"]
```

## Blocks

A block is an element that contains other elements. It has a name, optional attributes, and contents in curly braces:

```js
name {
    content
}
```

```js
name [attributes] {
    content
}
```

| Block | Purpose |
| --- | --- |
| `root` | Top-level block of an element based page |
| `frame` | Splits its area into [sections](attributes/section.md) |
| `section` | One part of a frame. Must be inside a `frame`. |
| `script` | Code in another language, named by the `type` attribute |

## Segments

A segment is a comma-separated list of elements. The contents of a block are a segment:

```js
"hi",
frame {}
```

## Attributes

An attribute is a key-value pair or a flag, written inside the square brackets of an element or block. See [Attributes](attributes/README.md).

```js
id = "myID"   // key-value pair
Horizontal    // flag
```

## Values

| Type | Written as | Examples |
| --- | --- | --- |
| String | Text in `""`, `''`, or ` `` ` | `"Hello World!"`, `'wow'`, `` `:3` `` |
| Number | Any number | `4`, `-7`, `3.14`, `1e-18` |
| Percentage | A number followed by `%` | `50%` |
| Color | `#` and 3 or 6 hex digits | `#f00`, `#ff0000` |
| Property | `source:name` | `theme:text` |

In strings, a backslash escapes the next character, and `\n` is a new line.
