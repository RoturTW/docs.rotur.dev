# Report a Group

### POST `/groups/{tag}/report`

Report a group to the Rotur moderation team. Reports are sent to a Discord channel for review.

**Path Parameters:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `tag` | string | Yes | The group tag |

**Query Parameters:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `auth` | string | Yes | Your Rotur user token |

**Response (200):**

```json
{
  "message": "Report sent successfully"
}
```

**Error Responses:**

| Status | Error | Cause |
|--------|-------|-------|
| 400 | `Group tag is required` | No tag provided |
| 404 | `Group not found` | Group doesn't exist |
