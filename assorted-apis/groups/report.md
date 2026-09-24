# Report a group

Report a group to the Rotur moderation team. The report, with your username and the group's details, goes to a moderation channel for review.

## POST `/v2/groups/{tag}/report`

**Auth:** Required. Sub-tokens need `groups:view`.

The endpoint takes no reason or other input.

### Example

```http
POST /v2/groups/mygroup/report
Authorization: Bearer YOUR_TOKEN
```

**Response `200`:**

```json
{
  "message": "Report sent successfully"
}
```
