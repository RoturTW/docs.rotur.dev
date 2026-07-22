# /unpin\_post

Unpins one of your pinned posts.

Requires authentication and the `posts:manage` permission.

## Parameters

| Parameter | Required | Description |
| --------- | -------- | ----------- |
| auth | Yes | Your authentication key |
| id | Yes | The ID of the post to unpin |

## Example

```bash
curl "https://api.rotur.dev/unpin_post?auth=YOUR_AUTH_KEY&id=POST_ID"
```

## Response

```json
{
  "message": "Post unpinned successfully"
}
```

## Common errors

| Status | Error | Cause |
| --- | --- | --- |
| 403 | `You can only unpin your own posts` | The post belongs to someone else |
| 404 | `Post not found` | No post with that ID |
