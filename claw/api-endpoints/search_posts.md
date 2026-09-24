# GET `/search_posts`

Searches the text of public posts, ignoring case, and returns matches newest first. Profile-only posts are not searched.

**Auth:** None. Uses the search rate limit.

### Parameters

| Name | In | Type | Required | Description |
| --- | --- | --- | --- | --- |
| `q` | query | string | Yes | Text to search for |
| `limit` | query | integer | No | Number of posts to return, 1–20. Default 20 |

### Example

```http
GET /search_posts?q=mist&limit=10
```

**Response `200`:** an array of [post objects](feed.md#post-object).

### Errors

| Status | When |
| --- | --- |
| `400` | `Search query is required` |
