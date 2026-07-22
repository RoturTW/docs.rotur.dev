# /search\_posts

Searches posts by their text content. Matching is case-insensitive.

No authentication required. Uses the search rate limit (20 per minute, 60 when authenticated).

## Parameters

| Parameter | Required | Description |
| --------- | -------- | ----------- |
| q | Yes | The text to search for |
| limit | No | How many posts to return. Default 20, max 20 |

## Example

```bash
curl "https://api.rotur.dev/search_posts?q=mist&limit=10"
```

## Response

Returns an array of matching post objects, newest first. Same shape as [/feed](feed.md).

## Common errors

| Status | Error | Cause |
| --- | --- | --- |
| 400 | `Search query is required` | Missing `q` parameter |
