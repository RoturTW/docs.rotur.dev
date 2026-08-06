# /claim\_time

Tells you how long until you can claim your next daily credit reward.

Requires authentication and the `credits:daily` permission.

## Parameters

| Parameter | Required | Description |
| --------- | -------- | ----------- |
| auth | Yes | Your authentication key. Use the `Authorization` header with `Bearer <token>` (preferred). The `auth` query parameter is still accepted as fallback. |

## Example

```bash
curl -H "Authorization: Bearer YOUR_AUTH_KEY" "https://api.rotur.dev/claim_time"
```

## Response

`wait_time` is the number of seconds until you can claim again. `0` means you can claim now.

```json
{
  "wait_time": 43200
}
```

## Common errors

| Status | Error | Cause |
| --- | --- | --- |
| 400 | `No daily claim found` | You have never claimed a daily reward, so there is no cooldown to report. You can claim right away with [/claim\_daily](claim_daily.md) |
