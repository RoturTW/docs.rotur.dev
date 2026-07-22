# /me

Returns the full account object for the authenticated user. This is the primary way to get your own user data.

Uses the profile rate limit (30 per minute, 120 when authenticated).

## Parameters

| Parameter | Required | Description |
| --------- | -------- | ----------- |
| auth | Yes* | Your authentication key |
| username | No* | Alternative login: your username, paired with `password` |
| password | No* | Alternative login: your password, paired with `username` |

*You can authenticate with either `auth` or a `username` and `password` pair.

## Example

```bash
curl "https://api.rotur.dev/me?auth=YOUR_AUTH_KEY"
```

## Response

Returns your account object, including your username, credits, subscription, badges, and settings.

This endpoint is functionally identical to `/get_user` and `/get_user_new`.

## Common errors

| Status | Error | Cause |
| --- | --- | --- |
| 403 | `Invalid authentication credentials` | Wrong username or password |
| 403 | `User is banned` | The account is banned |
| 403 | `Email address not verified` | The account email is not verified |
| 403 | `Terms-Of-Service are not accepted or outdated` | The account needs to accept the current TOS |
