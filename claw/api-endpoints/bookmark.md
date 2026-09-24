# GET `/bookmark`

Saves a post to your bookmarks.

**Auth:** Required. Your account needs a Plus subscription or higher.

You can keep up to 200 bookmarks. New bookmarks go to the front of the list, and when you go over 200 the oldest is dropped.

### Parameters

| Name | In | Type | Required | Description |
| --- | --- | --- | --- | --- |
| `id` | query | string | Yes | ID of the post to save |

### Example

```http
GET /bookmark?id=abc123
Authorization: Bearer <token>
```

**Response `200`:**

```json
{
  "message": "Saved"
}
```

If the post is already bookmarked, `message` is `Already saved`.

### Errors

| Status | When |
| --- | --- |
| `400` | `Post ID is required` |
| `403` | `Saving posts requires a Plus subscription or higher` |
| `404` | `Post not found` |
