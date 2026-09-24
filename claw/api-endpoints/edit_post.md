# GET `/edit_post`

Replaces the text of one of your posts and sets its `edited_at` time.

**Auth:** Required. Sub-tokens need `posts:manage`. Your account needs `good` standing and a Plus subscription or higher.

### Parameters

| Name | In | Type | Required | Description |
| --- | --- | --- | --- | --- |
| `id` | query | string | Yes | ID of the post to edit |
| `content` | query | string | Yes | New text. Same length limit as [`/post`](post.md) for your tier |

### Example

```http
GET /edit_post?id=abc123&content=Updated%20text
Authorization: Bearer <token>
```

**Response `200`:**

```json
{
  "message": "Post edited successfully",
  "edited_at": 1715054500000
}
```

### Errors

| Status | When |
| --- | --- |
| `400` | `Post ID is required` or `Content is required` |
| `400` | `Content exceeds <n> character limit` |
| `400` | `Reposts cannot be edited` |
| `403` | `Editing posts requires a Plus subscription or higher` |
| `403` | `You can only edit your own posts` |
| `404` | `Post not found` |
