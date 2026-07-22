# /edit\_post

Edits the text of one of your posts. Editing requires a Plus subscription or higher.

Requires authentication, the `posts:manage` permission, and `good` account standing. Reposts cannot be edited.

## Parameters

| Parameter | Required | Description |
| --------- | -------- | ----------- |
| auth | Yes | Your authentication key |
| id | Yes | The ID of the post to edit |
| content | Yes | The new post text. Same length limit as posts for your tier |

## Example

```bash
curl "https://api.rotur.dev/edit_post?auth=YOUR_AUTH_KEY&id=POST_ID&content=Updated+text"
```

## Response

```json
{
  "message": "Post edited successfully",
  "edited_at": 1715054500000
}
```

Edited posts carry an `edited_at` timestamp in feed responses.

## Common errors

| Status | Error | Cause |
| --- | --- | --- |
| 400 | `Content exceeds N character limit` | New text too long for your tier |
| 400 | `Reposts cannot be edited` | The post is a repost |
| 403 | `Editing posts requires a Plus subscription or higher` | Free or Lite account |
| 403 | `You can only edit your own posts` | The post belongs to someone else |
| 404 | `Post not found` | No post with that ID |
