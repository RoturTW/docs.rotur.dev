# GET `/delete`

Deletes one of your posts. Network admins can delete any post.

Rotur staff can also remove posts that break the rules. The author gets a `content_removed` notification with the reason, which does not say who removed it.

**Auth:** Required. Sub-tokens need `posts:delete`.

{% hint style="warning" %}
Deletion is permanent. The post's replies, likes and poll votes are deleted with it.
{% endhint %}

### Parameters

| Name | In | Type | Required | Description |
| --- | --- | --- | --- | --- |
| `id` | query | string | Yes | ID of the post to delete |

### Example

```http
GET /delete?id=abc123
Authorization: Bearer <token>
```

**Response `200`:**

```json
{
  "message": "Post deleted successfully"
}
```

### Errors

| Status | When |
| --- | --- |
| `400` | `Post ID is required` |
| `403` | `You cannot delete this post` (it belongs to someone else) |
| `404` | `Post not found` |
