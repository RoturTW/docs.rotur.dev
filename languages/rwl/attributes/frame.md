---
description: Attribute list for frames
---

# Frame

A frame divides its area into [sections](section.md), placed one after another along the frame's axis.

## Flags

| Flag | Effect |
| --- | --- |
| `Horizontal` | Places sections side by side along the X axis |
| `Vertical` | Places sections one after another along the Y axis |

You can combine both flags. A frame with neither flag behaves as `Horizontal`. Any other flag is an error.

```javascript
frame [Horizontal] {
    // sections, split along the X axis
}
```

```javascript
frame [Vertical] {
    // sections, split along the Y axis
}
```
