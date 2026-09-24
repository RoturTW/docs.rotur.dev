# GET `/exists`

Checks whether an account with a username exists.

**Auth:** None. Uses the profile rate limit.

### Parameters

| Name | In | Type | Required | Description |
| --- | --- | --- | --- | --- |
| `username` | query | string | Yes | Username to check, case-insensitive |

### Example

```http
GET /exists?username=mist
```

**Response `200`:**

```json
{
  "exists": true
}
```

### Errors

| Status | When |
| --- | --- |
| `400` | `Username is required` |
