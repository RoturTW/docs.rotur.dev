# /claim\_daily

Claims your daily credit reward. You can claim once every 24 hours.

Requires authentication, the `credits:daily` permission, and `good` account standing. The amount you receive depends on your subscription tier's daily credit multiplier.

## Parameters

| Parameter | Required | Description |
| --------- | -------- | ----------- |
| auth | Yes | Your authentication key. Use the `Authorization` header with `Bearer <token>` (preferred). The `auth` query parameter is still accepted as fallback. |

## Example

```bash
curl -H "Authorization: Bearer YOUR_AUTH_KEY" "https://api.rotur.dev/claim_daily"
```

## Response

```json
{
  "message": "Daily claim successful"
}
```

## Common errors

If you have already claimed within the last 24 hours you get a `429`:

```json
{
  "error": "Daily claim already made",
  "wait_time": 43200,
  "wait_hours": "12"
}
```

`wait_time` is in seconds.
