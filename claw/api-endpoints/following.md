# GET `/following`

Lists the usernames a user follows.

**Auth:** None. Uses the profile rate limit.

### Parameters

| Name | In | Type | Required | Description |
| --- | --- | --- | --- | --- |
| `name` | query | string | Yes | Username to look up. `username` also works |

### Example

```http
GET /following?name=mist
```

**Response `200`:**

```json
{
  "following": ["rm", "temp"]
}
```

### Errors

| Status | When |
| --- | --- |
| `400` | `Username is required` |
| `404` | `User not found` |
