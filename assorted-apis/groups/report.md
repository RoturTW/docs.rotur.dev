# Report a Group

Report a group to the Rotur moderation team. Reports go to a moderation channel for review.

### POST `/v2/groups/{tag}/report`

**Auth:** required. Token permission: `groups:view`.

**Path Parameters:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `tag` | string | Yes | The group tag |

**Example request:**

```bash
curl -X POST "https://api.rotur.dev/v2/groups/mygroup/report?auth=YOUR_TOKEN"
```

**Example response (200):**

```json
{
  "message": "Report sent successfully"
}
```

**Common errors:**

| Status | Error | Cause |
|--------|-------|-------|
| 404 | `Group not found` | Group doesn't exist |
