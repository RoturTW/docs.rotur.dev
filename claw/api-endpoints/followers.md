# GET `/followers`

Lists the usernames that follow a user.

**Auth:** None. Uses the profile rate limit.

### Parameters

| Name | In | Type | Required | Description |
| --- | --- | --- | --- | --- |
| `name` | query | string | Yes | Username to look up. `username` also works |

### Example

```http
GET /followers?name=mist
```

**Response `200`:**

```json
{
  "followers": ["rm", "temp"]
}
```

### Errors

| Status | When |
| --- | --- |
| `400` | `Username is required` |
| `404` | `User not found` |
