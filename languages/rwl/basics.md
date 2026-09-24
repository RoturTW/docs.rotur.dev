# Basics

RWL (Rotur Web Language) structures Rotur websites. This page shows a minimal page and the two ways to build one.

## Display text

To show text on a page, put a string inside a `root` block:

```js
root {
  "Hello World!"
}
```

Strings go in double quotes (`""`), single quotes (`''`), or backticks (` `` `). The quotes tell RWL the value is text.

Separate several elements with commas:

```js
root {
  "Hello World!",
  ":D",
  6
}
```

## Site types

RWL sites come in two types.

### Element based

Elements describe the layout and content of the page. This suits static, simple sites.

```js
// element based sites need a root block
root {
  // content
}
```

### Script based

A script draws the page, usually written in [RTR](../rtr/README.md). This suits dynamic sites that need custom graphics.

```js
script [type="rtr"] {
  // RTR code
}
```

RWL does not parse the contents of a `script` block; the browser passes them to the language named in `type`.

{% hint style="info" %}
Element based sites can also include scripts that draw with RTR.
{% endhint %}
