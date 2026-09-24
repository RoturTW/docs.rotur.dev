# GET `/get_post`

Returns one post by its ID.

**Auth:** Optional. Send your main account token to get `poll.voted` if the post has a poll; sub-tokens are ignored here.

### Parameters

| Name | In | Type | Required | Description |
| --- | --- | --- | --- | --- |
| `id` | query | string | Yes | ID of the post |

### Example

```http
GET /get_post?id=abc123
```

**Response `200`:** a [post object](feed.md#post-object).

### Errors

| Status | When |
| --- | --- |
| `400` | `Post ID is required` |
| `404` | `Post not found` |
