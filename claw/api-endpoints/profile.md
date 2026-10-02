# GET `/profile`

Returns a user's public profile, including their posts.

**Auth:** Optional. With your main token or a sub-token, the response also says whether you follow each other, and you can see private profiles you have access to. An OAuth access token from "Sign in with Rotur" sees the profile as a signed-out visitor does. Uses the profile rate limit.

You can also put the username in the path: `GET /profile/<username>`.

### Parameters

| Name | In | Type | Required | Description |
| --- | --- | --- | --- | --- |
| `username` | query | string | Yes* | Username to look up. `name` also works |
| `id` | query | string | No* | Look up by user ID instead |
| `discord_id` | query | string | No* | Look up by linked Discord ID instead. Only finds accounts that are discoverable in their privacy settings |
| `include_posts` | query | string | No | `true` (or `1`) includes the user's posts. Any other value, such as `false` or `0`, leaves them out. Default `true` |

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
  "discord_verified": false,
  "posts": []
}
```

Posts are listed pinned first, then the rest, each group newest first. The response can also include `display_name`, `theme`, `group_tag`, `status`, `connections`, `background`, `profile_video`, `account_type` and `owner` when set.

If the user has turned off showing their balance, `currency` is `0` and `subscription` is empty unless you are that user. `status` follows their presence privacy setting.

With a token, `followed` (you follow them) and `follows_me` (they follow you) are included when `true`.

If the profile is private and you are not the owner or one of their friends, you get only `username`, `id`, `pfp`, `private: true` and `restricted: true`, plus `account_type` when set and `discord_id` and `discord_verified` if the account is discoverable. Accounts under 18 are private by default. A sub-token reading its own account's private profile needs `account:view`. Banned users return a placeholder profile with `sys.banned: true`.

### Errors

| Status | When |
| --- | --- |
| `400` | `Name, Discord ID, or ID is required` |
| `404` | `User not found` |
