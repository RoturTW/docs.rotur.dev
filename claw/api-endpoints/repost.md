# GET `/repost`

Reposts a post. Without `content` it is a plain repost that only shows on your profile. With `content` it is a quote post that also goes on the public feed.

**Auth:** Required. Sub-tokens need `posts:repost`. Your account needs `good` standing.

### Parameters

| Name | In | Type | Required | Description |
| --- | --- | --- | --- | --- |
| `id` | query | string | Yes | ID of the post to repost |
| `content` | query | string | No | Quote text. Same length limit as [`/post`](post.md) for your tier |

### Example

```http
GET /repost?id=abc123&content=Look%20at%20this
Authorization: Bearer <token>
```

**Response `201`:** the new [post object](feed.md#post-object), with `is_repost: true` and the reposted post in `original_post`.

### Errors

| Status | When |
| --- | --- |
| `400` | `Post ID is required` |
| `400` | `You cant repost this post` (the author has blocked you) |
| `400` | `Content exceeds <n> character limit` |
| `403` | `Cannot repost a profile-only post` |
| `403` | `Cannot repost a repost` |
| `404` | `Original post not found` |
