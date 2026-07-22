# Leave a Group

Leave a group you're a member of. Owners can't leave their own group; [transfer ownership](transfer.md) or [delete the group](delete.md) instead.

### POST `/v2/groups/{tag}/leave`

**Auth:** required. Token permission: `groups:leave`.

**Path Parameters:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `tag` | string | Yes | The group tag |

**Example request:**

```bash
curl -X POST "https://api.rotur.dev/v2/groups/mygroup/leave?auth=YOUR_TOKEN"
```

**Example response (200):**

Returns the updated group info:

```json
{
  "id": "550e8400-e29b-41d4-a716-446655440000",
  "tag": "mygroup",
  "name": "My Group",
  "description": "A cool group",
  "readme": "",
  "rules": "",
  "icon_url": "",
  "banner_url": "",
  "owner_user_id": "alice",
  "public": true,
  "join_policy": "OPEN",
  "entry_fee": 0,
  "created_at": 1717000000,
  "credits_balance": 0,
  "member_count": 4
}
```

**Common errors:**

| Status | Error | Cause |
|--------|-------|-------|
| 400 | `You cannot leave the group you own` | You are the owner |
| 400 | `You are not a member of this group` | Not a member |
| 404 | `Group not found` | Group doesn't exist |
