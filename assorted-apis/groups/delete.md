# Delete a Group

### DELETE `/groups/{tag}`

Permanently deletes a group and all its data. **Owner only.**

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
  "message": "Group deleted successfully"
}
```

**Error Responses:**

| Status | Error | Cause |
|--------|-------|-------|
| 400 | `Group tag is required` | No tag provided |
| 403 | `You are not authorized to delete this group` | Not the group owner |
| 404 | `Group not found` | Group doesn't exist |
