# (static) .new()

Creates a page (document) and returns it.

{% hint style="info" %}
This does not open a tab. To open a URL in a new tab, use [`opentab`](../../../commands/opentab.md).
{% endhint %}

## Parameters

`.new()` takes one object. All keys are optional.

| Key | Type | Description |
| --- | --- | --- |
| `url` | [URL](../url/README.md) instance | The page's address. The Flufi Browser uses its "unknown page" URL if this is missing. |
| `isWeb` | boolean | Whether the page comes from the Rotur web |
| `update` | function | Function the browser calls to update the page |
| `type` | string | Page type, for example the file extension. Defaults to `"unknown"`. |
| `state` | any | Initial state for the page |

```json5
{
    "url": <URL class>,
    "isWeb": true,
    "update": <update function>
}
```

Calling it on an instance instead of the class throws `static function ran on instance`.

## Implementations

{% tabs %}
{% tab title="Flufi Browser (OSL)" %}
```javascript
if self.isInstance (
  throw "static function ran on instance"
)
local document = Document.JsonStringify().JsonParse()
document["url"] = data["url"] ?? Config.url.defaults.pages.unknown
document["isInstance"] = true
document["type"] = data["type"] ?? "unknown"
document["update"] = data["update"]
document["state"] = data["state"]
document["isWeb"] = data["isWeb"]
document["id"] = OuidNew()
return document.delete("new")
```
{% endtab %}
{% endtabs %}
