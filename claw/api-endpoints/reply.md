# GET `/reply`

Adds a reply to a post. The post's author gets a `reply` notification, and anyone you @mention gets a `mention` notification.

**Auth:** Required. Sub-tokens need `posts:reply`. Your account needs `good` standing.

### Parameters

| Name | In | Type | Required | Description |
| --- | --- | --- | --- | --- |
| `id` | query | string | Yes | ID of the post you are replying to |
| `content` | query | string | Yes | Reply text. Same length limit as [`/post`](post.md) for your tier |

### Example

```http
GET /reply?id=abc123&content=Nice%20post
Authorization: Bearer <token>
```

**Response `201`:**

```json
{
  "id": "def456",
  "content": "Nice post",
  "user": "mist",
  "timestamp": 1715054400000
}
```

### Errors

| Status | When |
| --- | --- |
| `400` | `Post ID is required` or `Content is required` |
| `400` | `Content exceeds <n> character limit` |
| `400` | `You cant reply to this post` (the author has blocked you) |
| `404` | `Post not found` |
