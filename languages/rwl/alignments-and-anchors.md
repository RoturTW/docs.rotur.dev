---
description: A list of alignments
---

# Alignments and anchors

[Text](attributes/text.md) uses two position attributes:

* `anchor` sets where in the frame the text is placed.
* `alignment` sets which point of the text sits at that position.

Both take the same names, in full or short form. Both default to `center`.

{% tabs %}
{% tab title="Full name" %}
| Position | Left        | Middle | Right        |
| -------- | ----------- | ------ | ------------ |
| Top      | top left    | top    | top right    |
| Middle   | left        | center | right        |
| Bottom   | bottom left | bottom | bottom right |
{% endtab %}

{% tab title="Short name" %}
| Position | Left | Middle | Right |
| -------- | ---- | ------ | ----- |
| Top      | tl   | t      | tr    |
| Middle   | l    | c      | r     |
| Bottom   | bl   | b      | br    |
{% endtab %}
{% endtabs %}

Any other name is an error.

```javascript
"I'm in the top right" [anchor="tr"],
"I start from the right" [alignment="right"]
```
