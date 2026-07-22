# /bookmarks

Lists the posts you have bookmarked, most recently saved first.

Requires authentication. Bookmarks of posts that have since been deleted are skipped.

## Parameters

| Parameter | Required | Description |
| --------- | -------- | ----------- |
| auth | Yes | Your authentication key |

## Example

```bash
curl "https://api.rotur.dev/bookmarks?auth=YOUR_AUTH_KEY"
```

## Response

Returns an array of post objects, same shape as [/feed](feed.md).
