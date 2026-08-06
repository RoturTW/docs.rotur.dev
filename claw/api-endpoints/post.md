# /post

Creates a new post as the authenticated user.

Requires authentication, the `posts:create` permission, and `good` account standing. You can make at most 5 posts per minute.

## Parameters

| Parameter | Required | Description |
| --------- | -------- | ----------- |
| auth | Yes | Your authentication key. Use the `Authorization` header with `Bearer <token>` (preferred). The `auth` query parameter is still accepted as fallback. |
| content | No* | The post text. Max length depends on your subscription: 300 (Free), 400 (Lite), 600 (Plus), 800 (Pro), 1000 (Max) |
| attachment | No | A URL to an image or video (PNG, JPEG, GIF, MP4, WEBM). Max 200 characters |
| attachments | No | Comma-separated list of attachment URLs. Max count per tier: 1 (Free/Lite), 2 (Plus), 4 (Pro and up) |
| poll | No | A JSON array of 2 to 6 option strings, each up to 80 characters. Requires Plus or higher |
| scheduled\_for | No | Unix milliseconds to publish the post at. Must be more than 30 seconds in the future. Requires Plus or higher |
| profile\_only | No | Set to `1` to keep the post off the public feed and only on your profile |
| os | No | The name of a registered system to tag the post with |

*A post needs at least one of `content`, an attachment, or a poll.

## Example

```bash
curl -H "Authorization: Bearer YOUR_AUTH_KEY" "https://api.rotur.dev/post?content=Hello%20Claw"
```

## Response

Returns `201` with the created post object (same shape as posts in [/feed](feed.md)).

If you scheduled the post instead:

```json
{
  "scheduled": true,
  "publish_at": 1715054321000,
  "id": "abc123"
}
```

## Common errors

| Status | Error | Cause |
| --- | --- | --- |
| 400 | `Content exceeds N character limit` | Post text too long for your tier |
| 400 | `Post needs text, an attachment, or a poll` | Empty post |
| 400 | `OS is invalid` | Unknown `os` value |
| 400 | `Too many attachments (max N for your subscription tier)` | Attachment count over your tier's limit |
| 403 | `Polls require a Plus subscription or higher` | Poll from a Free or Lite account |
| 403 | `Scheduling posts requires a Plus subscription or higher` | `scheduled_for` from a Free or Lite account |
| 429 | `Rate limit exceeded. Try again later.` | More than 5 posts in a minute |
