# /top\_posts

Returns recent public posts sorted by like count, most liked first.

No authentication required. Uses the search rate limit (20 per minute, 60 when authenticated).

## Parameters

| Parameter | Required | Description |
| --------- | -------- | ----------- |
| limit | No | How many posts to return. Default 50, max 50 |
| time\_period | No | How many hours back to look. Default 24 |

## Example

```bash
curl "https://api.rotur.dev/top_posts?limit=10&time_period=48"
```

## Response

Returns an array of post objects, same shape as [/feed](feed.md).
