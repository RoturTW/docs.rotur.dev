# /following\_feed

Returns recent posts from the users you follow, newest first. Other users' profile-only posts are hidden.

Requires authentication and the `posts:view` permission.

## Parameters

| Parameter | Required | Description |
| --------- | -------- | ----------- |
| auth | Yes | Your authentication key |
| limit | No | How many posts to return. Default 100. Max 100, or 200 with Plus or higher |

## Example

```bash
curl "https://api.rotur.dev/following_feed?auth=YOUR_AUTH_KEY&limit=50"
```

## Response

Returns an array of post objects, same shape as [/feed](feed.md).
