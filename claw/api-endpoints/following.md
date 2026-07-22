# /following

Lists the users a given user is following.

No authentication required. Uses the profile rate limit (30 per minute, 120 when authenticated).

## Parameters

| Parameter | Required | Description |
| --------- | -------- | ----------- |
| name | Yes | The username whose following list you want. `username` also works |

## Example

```bash
curl "https://api.rotur.dev/following?name=mist"
```

## Response

```json
{
  "following": ["rm", "temp"]
}
```

## Common errors

| Status | Error | Cause |
| --- | --- | --- |
| 400 | `Username is required` | Missing `name` parameter |
| 404 | `User not found` | No account with that username |
