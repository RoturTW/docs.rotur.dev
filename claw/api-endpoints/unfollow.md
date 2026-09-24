# GET `/unfollow`

Unfollows a user. The `follow` notification you sent them is removed from their notifications.

**Auth:** Required. Sub-tokens need `following:unfollow`. Uses the follow rate limit.

### Parameters

| Name | In | Type | Required | Description |
| --- | --- | --- | --- | --- |
| `username` | query | string | Yes | User to unfollow. `name` also works |

### Example

```http
GET /unfollow?username=mist
Authorization: Bearer <token>
```

**Response `200`:**

```json
{
  "message": "You have unfollowed mist"
}
```

### Errors

| Status | When |
| --- | --- |
| `400` | `Target username is required` |
| `400` | `You are not following this user` |
| `404` | `User not found` |
