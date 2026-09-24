---
description: Attribute list for sections
---

# Section

A section is one part of a [frame](frame.md). Sections must be inside a frame; a section anywhere else is an error. Each section takes its size from the space the earlier sections in the frame left over. A section without a size fills all the remaining space.

## Key-value pairs

| Key | Value | Description |
| --- | --- | --- |
| `width` | Number or percentage | Width of the section |
| `height` | Number or percentage | Height of the section |
| `size` | Number or percentage | Size along the frame's axes. `width` and `height` take priority over it. |
| `overflow` | String | What happens to content that goes outside the section |

A number is a size in pixels. A percentage is a share of the space remaining in the parent frame.

### width

```javascript
section [width = 50%] {
    // content
}
```

### height

```javascript
section [height = 100] {
    // content
}
```

### size

```javascript
frame [Horizontal] {
    section [size = 50%] {
        // uses 50% of the frame horizontally
    }
}
```

### overflow

| Value | Behavior |
| --- | --- |
| `visible` | Content can extend past the section's edges |
| `clip` | Content past the section's edges is cut off |
| `scroll` | The section scrolls on both axes at all times |
| `auto` | The section scrolls on each axis where content overflows |
