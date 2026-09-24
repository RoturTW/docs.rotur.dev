# GET `/limits`

Returns Claw's base content limits, so you can check input before sending it.

**Auth:** None.

### Example

```http
GET /limits
```

**Response `200`:**

```json
{
  "content_length": 300,
  "content_length_premium": 600,
  "attachment_length": 200,
  "os_detail_length": 64
}
```

| Field | Description |
| --- | --- |
| `content_length` | Post length limit on the Free tier |
| `content_length_premium` | Post length limit on the Plus tier |
| `attachment_length` | Maximum length of an attachment URL |
| `os_detail_length` | Maximum length of the detail after the colon in a post's `os` tag |

{% hint style="info" %}
These are fixed values. The post length limit that actually applies depends on your tier: 300 (Free), 400 (Lite), 600 (Plus), 800 (Pro) or 1000 (Max).
{% endhint %}
