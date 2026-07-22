# /exists

Checks whether a user account exists.

No authentication required. Uses the profile rate limit (30 per minute, 120 when authenticated).

## Parameters

| Parameter | Required | Description |
| --------- | -------- | ----------- |
| username | Yes | The username to check (case-insensitive) |

## Example

```bash
curl "https://api.rotur.dev/exists?username=mist"
```

## Response

Always returns `200`:

```json
{
  "exists": true
}
```

## Common errors

| Status | Error | Cause |
| --- | --- | --- |
| 400 | `Username is required` | Missing `username` parameter |
