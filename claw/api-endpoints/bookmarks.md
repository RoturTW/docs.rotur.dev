# GET `/bookmarks`

Lists the posts you have bookmarked, most recently saved first. Bookmarks of deleted posts are skipped.

**Auth:** Required.

### Example

```http
GET /bookmarks
Authorization: Bearer <token>
```

**Response `200`:** an array of [post objects](feed.md#post-object).
