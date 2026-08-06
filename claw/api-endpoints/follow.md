# /follow

Follows another user on Claw.

Requires authentication, the `following:follow` permission, and at least `warning` account standing. Rate limited to 20 follows per minute (60 when authenticated).

## Parameters

| Parameter | Required | Description |
| --------- | -------- | ----------- |
| auth | Yes | Your authentication key. Use the `Authorization` header with `Bearer <token>` (preferred). The `auth` query parameter is still accepted as fallback. |
| username | Yes | The user to follow. `name` also works |

## Example

```bash
curl -H "Authorization: Bearer YOUR_AUTH_KEY" "https://api.rotur.dev/follow?username=mist"
```

## Response

```json
{
  "message": "You are now following mist"
}
```

## Common errors

| Status | Error | Cause |
| --- | --- | --- |
| 400 | `You cannot follow yourself` | Target is your own account |
| 400 | `You are already following NAME` | Duplicate follow |
| 400 | `You cant follow this user` | The target has blocked you |
| 400 | `Unblock this user before following them` | You have blocked the target |
| 404 | `User not found` | No account with that username |
