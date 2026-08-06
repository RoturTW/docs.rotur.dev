# /delete

Deletes one of your posts.

Requires authentication and the `posts:delete` permission. You can only delete your own posts.

## Parameters

| Parameter | Required | Description |
| --------- | -------- | ----------- |
| auth | Yes | Your authentication key. Use the `Authorization` header with `Bearer <token>` (preferred). The `auth` query parameter is still accepted as fallback. |
| id | Yes | The ID of the post to delete |

## Example

```bash
curl -H "Authorization: Bearer YOUR_AUTH_KEY" "https://api.rotur.dev/delete?id=POST_ID"
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
