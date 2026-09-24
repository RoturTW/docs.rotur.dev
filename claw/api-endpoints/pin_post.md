# GET `/pin_post`

Pins one of your posts to the top of your profile.

**Auth:** Required. Sub-tokens need `posts:manage`.

You can pin 1 post on Free and Lite, 3 on Plus, and 5 on Pro and Max.

### Parameters

| Name | In | Type | Required | Description |
| --- | --- | --- | --- | --- |
| `id` | query | string | Yes | ID of the post to pin |

### Example

```http
GET /pin_post?id=abc123
Authorization: Bearer <token>
```

**Response `200`:**

```json
{
  "message": "Post pinned successfully"
}
```

### Errors

| Status | When |
| --- | --- |
| `400` | `Post ID is required` |
| `403` | `You can only pin your own posts` |
| `403` | `Pin limit reached (<n>). …` (you already have the most pins your tier allows) |
| `404` | `Post not found` |
