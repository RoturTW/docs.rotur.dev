# GET `/following`

Lists the usernames a user follows.

**Auth:** Optional. Send a token to see lists that are only shown to the user's friends. Uses the profile rate limit.

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

If the user only shows their follow lists to friends (or to nobody) and you cannot see them, you get an empty list and `hidden: true`:

**Response `200`:**

```json
{
  "following": [],
  "hidden": true
}
```

Accounts that are not discoverable, and whose profile you cannot see, are left out of the list.

### Errors

| Status | When |
| --- | --- |
| `400` | `Username is required` |
| `404` | `User not found` |
