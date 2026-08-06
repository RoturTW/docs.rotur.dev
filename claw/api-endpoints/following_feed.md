# /following\_feed

Returns recent posts from the users you follow, newest first. Other users' profile-only posts are hidden.

Requires authentication and the `posts:view` permission.

## Parameters

| Parameter | Required | Description |
| --------- | -------- | ----------- |
| auth | Yes | Your authentication key. Use the `Authorization` header with `Bearer <token>` (preferred). The `auth` query parameter is still accepted as fallback. |
| limit | No | How many posts to return. Default 100. Max 100, or 200 with Plus or higher |

## Example

```bash
curl -H "Authorization: Bearer YOUR_AUTH_KEY" "https://api.rotur.dev/following_feed?limit=50"
```

## Response

Returns an array of post objects, same shape as [/feed](feed.md).
