# GET `/unpin_post`

Unpins one of your posts.

**Auth:** Required. Sub-tokens need `posts:manage`.

### Parameters

| Name | In | Type | Required | Description |
| --- | --- | --- | --- | --- |
| `id` | query | string | Yes | ID of the post to unpin |

### Example

```http
GET /unpin_post?id=abc123
Authorization: Bearer <token>
```

**Response `200`:**

```json
{
  "message": "Post unpinned successfully"
}
```

### Errors

| Status | When |
| --- | --- |
| `400` | `Post ID is required` |
| `403` | `You can only unpin your own posts` |
| `404` | `Post not found` |
