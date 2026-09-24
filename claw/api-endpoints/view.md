# GET `/view`

Records that you viewed one or more posts and returns their view counts. Each user counts once per post.

**Auth:** Required.

### Parameters

| Name | In | Type | Required | Description |
| --- | --- | --- | --- | --- |
| `id` | query | string | Yes* | ID of one post |
| `ids` | query | string | No* | Comma-separated post IDs. Takes priority over `id` |

*Send `id` or `ids`.

### Example

```http
GET /view?id=abc123
Authorization: Bearer <token>
```

**Response `200`:**

```json
{
  "views": 13
}
```

With `ids=a,b,c`, `views` is keyed by post ID, and unknown IDs are left out:

**Response `200`:**

```json
{
  "views": {
    "a": 13,
    "b": 4
  }
}
```

### Errors

| Status | When |
| --- | --- |
| `400` | `Post ID is required` (neither `id` nor `ids` sent) |
| `404` | `Post not found` (single `id` only) |
