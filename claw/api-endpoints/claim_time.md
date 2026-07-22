# /claim\_time

Tells you how long until you can claim your next daily credit reward.

Requires authentication and the `credits:daily` permission.

## Parameters

| Parameter | Required | Description |
| --------- | -------- | ----------- |
| auth | Yes | Your authentication key |

## Example

```bash
curl "https://api.rotur.dev/claim_time?auth=YOUR_AUTH_KEY"
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
