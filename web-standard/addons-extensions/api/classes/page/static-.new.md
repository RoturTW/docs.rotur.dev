# (static) .new()

Creates a new Page / Document and returns it.

{% hint style="info" %}
this does not create a new tab.  you can use `opentab <url>` for that.
{% endhint %}

## Parameters

### Data

a json objects that can contain any of these keys:

```json5
{
    "url": <URL class>,
    "isWeb": true / false,
    "update": <update function>
}
```

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
