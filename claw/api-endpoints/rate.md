# GET `/rate`

Likes or unlikes a post.

**Auth:** Required. Sub-tokens need `posts:like`.

### Parameters

| Name | In | Type | Required | Description |
| --- | --- | --- | --- | --- |
| `id` | query | string | Yes | ID of the post |
| `rating` | query | integer | Yes | `1` to like, `0` to remove your like |

### Example

```http
GET /rate?id=abc123&rating=1
Authorization: Bearer <token>
```

**Response `200`:**

```json
{
  "message": "Post rated successfully",
  "likes": ["user_id_1", "user_id_2"]
}
```

{% hint style="info" %}
`likes` here holds user IDs. Post objects from the feed endpoints list usernames instead.
{% endhint %}

### Errors

| Status | When |
| --- | --- |
| `400` | `Post ID is required` or `Rating is required` |
| `400` | `Rating must be 1 (like) or 0 (unlike)` |
| `400` | `You cant like this post` (the author has blocked you) |
| `404` | `Post not found` |
