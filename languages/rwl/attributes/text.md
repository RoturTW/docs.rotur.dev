---
description: >-
  Attribute list for any non-block element, such as text or a number.
---

# Text

These attributes apply to any element that is not a block, including numbers.

Text without an `anchor` or `padding` continues from where the previous element ended. Setting either one positions the text inside its frame instead.

## Key-value pairs

| Key | Type | Default | Description |
| --- | --- | --- | --- |
| `font` | String | | Font used to render the text |
| `size` | Number | `10` | Text size |
| `spacing` | Number | `1` | Space between characters, as a multiple of the normal spacing |
| `line_height` | Number | `1` | Space between lines, as a multiple of the normal line height |
| `anchor` | String | `"c"` | Where in the frame the text is placed |
| `alignment` | String | `"c"` | Which point of the text sits at the anchor |
| `padding` | Number | `10` | Space between the text and the edge of the frame |
| `link` | String | | Makes the text a link to this address |
| `decoration` | String | | Text decoration, for example `"none"` |

`anchor` and `alignment` take the names listed in [Alignments and anchors](../alignments-and-anchors.md), in full or short form.

### font

```javascript
"Hello World" [font="llama"]
```

### size

```javascript
"Large Text" [size=20]
```

### spacing

```javascript
// twice the normal space between characters
"Wide Text" [spacing=2]
```

### line\_height

```javascript
// twice the normal space between lines
"Text With\nBig Lines" [line_height=2]
```

### anchor

```javascript
"I'm in the corner" [anchor="top left"]
```

### alignment

```javascript
"I'm centered" [alignment="center"],
"I'm centered from the left" [alignment="left"]
```

### padding

```javascript
"I've got padding" [padding=100, anchor="tl"]
```

{% hint style="info" %}
Padding has no effect when the anchor is `center`.
{% endhint %}

### link

By default, clicking a link opens the address in a new tab.

```javascript
"Funny Website" [link="flufi.web"]
```

### decoration

Use `decoration="none"` to remove the link styling:

```javascript
"This is actually a link" [link="flufi.web", decoration="none"]
```

## Flags

| Flag | Effect |
| --- | --- |
| `Redirect` | On a link, loads the address in the current tab instead of a new tab |
| `Wrapped` | Wraps the text at the edges of the current frame, or the window if there is no frame |

```javascript
"Redirecting link" [link="flufi.web", Redirect]
```

```javascript
"This is long text that will wrap at the edge of the frame" [size=50, Wrapped]
```
