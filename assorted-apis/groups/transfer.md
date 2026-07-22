# Transfer Ownership

Hand your group to another member. Only the current owner can do this.

### POST `/v2/groups/{tag}/transfer/{userid}`

**Auth:** required. Token permission: `groups:manage`.

The target must already be a member. They receive the Owner role and become the group owner; you lose the Owner role (keeping your other roles, or the default join roles if you had none). The new owner gets a `group_ownership_transferred` event and a push notification.

{% hint style="warning" %}
`{userid}` must be the user's ID, not their username. Transfers take effect immediately and can only be reversed by the new owner.
{% endhint %}

**Path Parameters:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `tag` | string | Yes | The group tag |
| `userid` | string | Yes | User ID of the new owner |

**Example request:**

```bash
curl -X POST "https://api.rotur.dev/v2/groups/mygroup/transfer/USER_ID?auth=YOUR_TOKEN"
```

**Example response (200):**

```json
{
  "message": "Ownership transferred",
  "group": {
    "id": "550e8400-e29b-41d4-a716-446655440000",
    "tag": "mygroup",
    "name": "My Group",
    "owner_user_id": "bob",
    "member_count": 5
  }
}
```

The `group` field is the full updated group object (shortened here).

**Common errors:**

| Status | Error | Cause |
|--------|-------|-------|
| 400 | `You are already the owner` | Transferring to yourself |
| 403 | `Only the group owner can transfer ownership` | You are not the owner |
| 404 | `Target user is not a member of this group` | Target not in the group |
| 404 | `Group not found` | Group doesn't exist |
