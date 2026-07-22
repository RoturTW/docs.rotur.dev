# GET `/cosmetics/overlays/:file`

Fetch a raw overlay GIF asset. Use this to preview an overlay in the shop, for example.

**Authentication:** Not required.

**Path Parameter:**

| Parameter | Description |
|---|---|
| `:file` | The overlay's file name: the cosmetic ID followed by `.gif` |

**Example request:**

```http
GET /cosmetics/overlays/cat_ears.gif
```

**Example response (200):** the GIF file itself (`image/gif`).

{% hint style="info" %}
The `raw_url` field returned by the [profile cosmetics endpoint](profile-cosmetics.md) points here.
{% endhint %}

**Common errors:**

| Status | Cause |
|---|---|
| `400` | Missing file name, path traversal attempt, or a name that does not end in `.gif` |
| `404` | No overlay asset with that name |

Errors from this endpoint are plain status codes with no JSON body.
