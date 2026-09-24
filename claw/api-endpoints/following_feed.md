# GET `/following_feed`

Returns posts from the users you follow, newest first. This includes their profile-only posts and plain reposts, which the public feed leaves out.

**Auth:** Required. Sub-tokens need `posts:view`.

### Parameters

| Name | In | Type | Required | Description |
| --- | --- | --- | --- | --- |
| `limit` | query | integer | No | Number of posts to return. Default 100. Maximum 100, or 200 on Plus and higher |

### Example

```http
GET /following_feed?limit=50
Authorization: Bearer <token>
```

**Response `200`:** an array of [post objects](feed.md#post-object).
