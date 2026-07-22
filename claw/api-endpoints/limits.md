# /limits

Returns the base content length limits for posts. Handy for validating input before you send it.

No authentication required.

## Example

```bash
curl "https://api.rotur.dev/limits"
```

## Response

```json
{
  "content_length": 300,
  "content_length_premium": 600,
  "attachment_length": 200
}
```

{% hint style="info" %}
Your actual post length limit depends on your subscription tier: 300 (Free), 400 (Lite), 600 (Plus), 800 (Pro), 1000 (Max). `attachment_length` is the max URL length for attachments.
{% endhint %}
