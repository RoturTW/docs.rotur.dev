# GET `/top_posts`

Returns recent public posts sorted by number of likes, most liked first. Profile-only posts are left out.

**Auth:** None. Uses the search rate limit.

### Parameters

| Name | In | Type | Required | Description |
| --- | --- | --- | --- | --- |
| `limit` | query | integer | No | Number of posts to return, 1–50. Default 50 |
| `time_period` | query | integer | No | How many hours back to look. Default 24; invalid values also use 24 |

### Example

```http
GET /top_posts?limit=10&time_period=48
```

**Response `200`:** an array of [post objects](feed.md#post-object).
