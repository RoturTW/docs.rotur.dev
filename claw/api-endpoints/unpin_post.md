# /unpin\_post

Unpins one of your pinned posts.

Requires authentication and the `posts:manage` permission.

## Parameters

| Parameter | Required | Description |
| --------- | -------- | ----------- |
| auth | Yes | Your authentication key. Use the `Authorization` header with `Bearer <token>` (preferred). The `auth` query parameter is still accepted as fallback. |
| id | Yes | The ID of the post to unpin |

## Example

```bash
curl -H "Authorization: Bearer YOUR_AUTH_KEY" "https://api.rotur.dev/unpin_post?id=POST_ID"
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
