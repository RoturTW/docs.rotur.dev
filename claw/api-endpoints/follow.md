# GET `/follow`

Follows another user. They get a `follow` notification.

**Auth:** Required. Sub-tokens need `following:follow`. Your account needs at least `warning` standing. Uses the follow rate limit.

### Parameters

| Name | In | Type | Required | Description |
| --- | --- | --- | --- | --- |
| `username` | query | string | Yes | User to follow. `name` also works |

### Example

```http
GET /follow?username=mist
Authorization: Bearer <token>
```

**Response `200`:**

```json
{
  "message": "You are now following mist"
}
```

### Errors

| Status | When |
| --- | --- |
| `400` | `Target username is required` |
| `400` | `You cannot follow yourself` |
| `400` | `You are already following <username>` |
| `400` | `You cant follow this user` (they have blocked you) |
| `400` | `Unblock this user before following them` |
| `404` | `User not found` |
