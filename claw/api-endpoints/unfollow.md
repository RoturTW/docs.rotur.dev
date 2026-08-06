# /unfollow

Unfollows a user on Claw. Unfollowing also removes your follow notification from their history.

Requires authentication and the `following:unfollow` permission. Rate limited to 20 per minute (60 when authenticated).

## Parameters

| Parameter | Required | Description |
| --------- | -------- | ----------- |
| auth | Yes | Your authentication key. Use the `Authorization` header with `Bearer <token>` (preferred). The `auth` query parameter is still accepted as fallback. |
| username | Yes | The user to unfollow. `name` also works |

## Example

```bash
curl -H "Authorization: Bearer YOUR_AUTH_KEY" "https://api.rotur.dev/unfollow?username=mist"
```

## Response

```json
{
  "message": "You have unfollowed mist"
}
```

## Common errors

| Status | Error | Cause |
| --- | --- | --- |
| 400 | `You are not following this user` | No existing follow to remove |
| 404 | `User not found` | No account with that username |
