# /bookmarks

Lists the posts you have bookmarked, most recently saved first.

Requires authentication. Bookmarks of posts that have since been deleted are skipped.

## Parameters

| Parameter | Required | Description |
| --------- | -------- | ----------- |
| auth | Yes | Your authentication key. Use the `Authorization` header with `Bearer <token>` (preferred). The `auth` query parameter is still accepted as fallback. |

## Example

```bash
curl -H "Authorization: Bearer YOUR_AUTH_KEY" "https://api.rotur.dev/bookmarks"
```

## Response

Returns an array of post objects, same shape as [/feed](feed.md).
