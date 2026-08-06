# /reply

Replies to a post on Claw.

Requires authentication, the `posts:reply` permission, and `good` account standing.

## Parameters

| Parameter | Required | Description |
| --------- | -------- | ----------- |
| auth | Yes | Your authentication key. Use the `Authorization` header with `Bearer <token>` (preferred). The `auth` query parameter is still accepted as fallback. |
| id | Yes | The ID of the post you are replying to |
| content | Yes | The reply text. Same length limit as posts for your tier |

## Example

```bash
curl -H "Authorization: Bearer YOUR_AUTH_KEY" "https://api.rotur.dev/reply?id=POST_ID&content=Nice+post"
```

## Response

Returns `201` with the created reply:

```json
{
  "id": "def456",
  "content": "Nice post",
  "user": "mist",
  "timestamp": 1715054400000
}
```

## Common errors

| Status | Error | Cause |
| --- | --- | --- |
| 400 | `Post ID is required` / `Content is required` | Missing parameter |
| 400 | `Content exceeds N character limit` | Reply too long for your tier |
| 400 | `You cant reply to this post` | The post author has blocked you |
| 404 | `Post not found` | No post with that ID |
