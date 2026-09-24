# GET `/scheduled`

Lists your posts that are scheduled but not yet published.

**Auth:** Required.

You schedule a post with the `scheduled_for` parameter on [`/post`](post.md), which needs a Plus subscription or higher. The server checks for due posts every 15 seconds, so a post can go out up to 15 seconds after its scheduled time. Its `timestamp` is set to the moment it is published.

### Example

```http
GET /scheduled
Authorization: Bearer <token>
```

**Response `200`:** an array of [post objects](feed.md#post-object).
