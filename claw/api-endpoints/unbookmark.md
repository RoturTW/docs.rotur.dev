# GET `/unbookmark`

Removes a post from your bookmarks. It succeeds even if the post was not bookmarked.

**Auth:** Required.

### Parameters

| Name | In | Type | Required | Description |
| --- | --- | --- | --- | --- |
| `id` | query | string | Yes | ID of the post to remove |

### Example

```http
GET /unbookmark?id=abc123
Authorization: Bearer <token>
```

**Response `200`:**

```json
{
  "message": "Removed"
}
```
