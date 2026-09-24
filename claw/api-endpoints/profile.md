# GET `/profile`

Returns a user's public profile, including their posts.

**Auth:** Optional. With a token (main or sub-token) the response also says whether you follow each other, and you can see private profiles you have access to. Uses the profile rate limit.

You can also put the username in the path: `GET /profile/<username>`.

### Parameters

| Name | In | Type | Required | Description |
| --- | --- | --- | --- | --- |
| `username` | query | string | Yes* | Username to look up. `name` also works |
| `id` | query | string | No* | Look up by user ID instead |
| `discord_id` | query | string | No* | Look up by linked Discord ID instead |
| `include_posts` | query | string | No | `0` leaves out the user's posts. Default `1` |

*Send one of `username`, `id` or `discord_id`.

### Example

```http
GET /profile?username=mist
```

**Response `200`:**

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
    { "id": "pro", "name": "pro", "icon": "...", "description": "This user has a Rotur Pro subscription", "issuer": "rotur" }
  ],
  "subscription": "Pro",
  "currency": 1234.5,
  "max_size": "1000000000",
  "index": 7,
  "private": false,
  "sys.banned": false,
  "discord_id": "",
  "posts": []
}
```

Posts are listed pinned first, then the rest, each group newest first. The response can also include `display_name`, `theme`, `group_tag`, `status`, `connections`, `background` and `profile_video` when set.

With a token, `followed` (you follow them) and `follows_me` (they follow you) are included when `true`.

If the profile is private and you are not the owner or one of their friends, you get only `username`, `pfp`, `private: true` and `restricted: true`. Banned users return a placeholder profile with `sys.banned: true`.

### Errors

| Status | When |
| --- | --- |
| `400` | `Name, Discord ID, or ID is required` |
| `404` | `User not found` |
