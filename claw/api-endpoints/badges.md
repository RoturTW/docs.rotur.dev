# /badges

Returns the badges the authenticated user currently has.

Requires authentication and the `account:view` permission. Badges are computed from your account: the system you signed up on, credits over 1000, 10 or more friends, a linked Discord account, a Pro subscription, and any manually granted badges.

## Parameters

| Parameter | Required | Description |
| --------- | -------- | ----------- |
| auth | Yes | Your authentication key |

## Example

```bash
curl "https://api.rotur.dev/badges?auth=YOUR_AUTH_KEY"
```

## Response

```json
{
  "badge_names": [
    {
      "name": "rich",
      "icon": "c #DAF0F2 w 3 line 3 5 -3 5 ...",
      "description": "This user has over 1k Rotur Credits"
    }
  ]
}
```

`icon` is a vector drawing string, not an image URL.

## Common errors

| Status | Error | Cause |
| --- | --- | --- |
| 404 | `User not found` | The account could not be resolved |
