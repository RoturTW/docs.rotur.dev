# /scheduled

Lists your posts that are scheduled but not yet published.

Requires authentication. You schedule a post with the `scheduled_for` parameter on [/post](post.md), which needs a Plus subscription or higher. The server publishes due posts roughly every 15 seconds.

## Parameters

| Parameter | Required | Description |
| --------- | -------- | ----------- |
| auth | Yes | Your authentication key |

## Example

```bash
curl "https://api.rotur.dev/scheduled?auth=YOUR_AUTH_KEY"
```

## Response

Returns an array of your pending posts, same shape as [/feed](feed.md).
