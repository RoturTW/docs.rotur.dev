# GET `/vote_poll`

Votes in a post's poll. Voting again replaces your earlier vote.

**Auth:** Required.

### Parameters

| Name | In | Type | Required | Description |
| --- | --- | --- | --- | --- |
| `id` | query | string | Yes | ID of the post with the poll |
| `option` | query | integer | Yes | Index of the option, starting at 0 |

### Example

```http
GET /vote_poll?id=abc123&option=1
Authorization: Bearer <token>
```

**Response `200`:**

```json
{
  "poll": {
    "options": [
      { "text": "Yes", "count": 4 },
      { "text": "No", "count": 7 }
    ],
    "total": 11,
    "voted": 1
  }
}
```

`voted` is the option index you voted for.

### Errors

| Status | When |
| --- | --- |
| `400` | `Post ID and option are required` |
| `400` | `Invalid option` (`option` is not a number) |
| `400` | `Option out of range` |
| `404` | `Poll not found` (the post does not exist or has no poll) |
