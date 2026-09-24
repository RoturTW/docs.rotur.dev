# Delete a group

Permanently delete a group and everything in it. Only the owner can do this.

## DELETE `/v2/groups/{tag}`

**Auth:** Required. Sub-tokens need `groups:manage`. You must be the group owner.

{% hint style="danger" %}
Deletion can't be undone. Members, roles, announcements, events, tips, products, and the group's `credits_balance` are all removed. Withdraw any credits you want to keep first.
{% endhint %}

### Example

```http
DELETE /v2/groups/mygroup
Authorization: Bearer YOUR_TOKEN
```

**Response `200`:**

```json
{
  "message": "Group deleted successfully"
}
```

### Errors

| Status | When |
| --- | --- |
| `403` | You aren't the owner (`You are not authorized to delete this group`) |
