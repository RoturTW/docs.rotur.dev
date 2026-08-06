# Delete a Group

Permanently delete a group and all its data. Only the group owner can do this.

### DELETE `/v2/groups/{tag}`

**Auth:** required. Token permission: `groups:manage`.

**Path Parameters:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `tag` | string | Yes | The group tag |

**Example request:**

```bash
curl -X DELETE -H "Authorization: Bearer YOUR_TOKEN" "https://api.rotur.dev/v2/groups/mygroup"
```

**Example response (200):**

```json
{
  "message": "Group deleted successfully"
}
```

{% hint style="danger" %}
Deletion is permanent. Members, roles, announcements, events, tips, and the group's credits balance are all removed.
{% endhint %}

**Common errors:**

| Status | Error | Cause |
|--------|-------|-------|
| 403 | `You are not authorized to delete this group` | You are not the owner |
| 404 | `Group not found` | Group doesn't exist |
