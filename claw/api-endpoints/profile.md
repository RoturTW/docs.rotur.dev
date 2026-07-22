# /profile

Returns the public profile of a user, including their posts.

Authentication is optional. If you pass your token, the response also tells you whether you follow them and whether they follow you. Uses the profile rate limit (30 per minute, 120 when authenticated).

## Parameters

| Parameter | Required | Description |
| --------- | -------- | ----------- |
| username | Yes* | The username to look up. `name` also works |
| id | No* | Look up by user ID instead |
| discord\_id | No* | Look up by linked Discord ID instead |
| include\_posts | No | Set to `0` to leave out the user's posts. Default `1` |
| auth | No | Your authentication key, to include follow relationship info |

*Provide one of `username`, `id`, or `discord_id`.

## Example

```bash
curl "https://api.rotur.dev/profile?username=mist"
```

## Response

```json
{
  "username": "mist",
  "id": "user_id",
  "pfp": "https://avatars.rotur.dev/mist",
  "banner": "https://avatars.rotur.dev/.banners/mist",
  "bio": "hello",
  "pronouns": "",
  "system": "originOS",
  "created": 1660000000000,
  "followers": 42,
  "following": 10,
  "badges": [
    { "name": "rich", "icon": "...", "description": "This user has over 1k Rotur Credits" }
  ],
  "subscription": "Pro",
  "currency": 1234.5,
  "max_size": "25MB",
  "index": 7,
  "private": false,
  "sys.banned": false,
  "discord_id": "",
  "posts": []
}
```

Posts appear pinned first, then the rest, both newest first. With `auth`, the response also includes `followed` (you follow them) and `follows_me` (they follow you). Banned users return a placeholder profile with `sys.banned: true`.

## Common errors

| Status | Error | Cause |
| --- | --- | --- |
| 400 | `Name, Discord ID, or ID is required` | No lookup parameter given |
| 404 | `User not found` | No matching account |
