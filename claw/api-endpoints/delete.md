# /delete

Deletes one of your posts.

Requires authentication and the `posts:delete` permission. You can only delete your own posts.

## Parameters

| Parameter | Required | Description |
| --------- | -------- | ----------- |
| auth | Yes | Your authentication key |
| id | Yes | The ID of the post to delete |

## Example

```bash
curl "https://api.rotur.dev/delete?auth=YOUR_AUTH_KEY&id=POST_ID"
```

## Response

```json
{
  "message": "Post deleted successfully"
}
```

## Common errors

| Status | Error | Cause |
| --- | --- | --- |
| 403 | `You cannot delete this post` | The post belongs to someone else |
| 404 | `Post not found` | No post with that ID |
