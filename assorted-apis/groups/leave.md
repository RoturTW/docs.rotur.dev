# Leave a group

Leave a group you're a member of. The owner can't leave; [transfer ownership](transfer.md) or [delete the group](delete.md) instead.

## POST `/v2/groups/{tag}/leave`

**Auth:** Required. Sub-tokens need `groups:leave`.

### Example

```http
POST /v2/groups/mygroup/leave
Authorization: Bearer YOUR_TOKEN
```

**Response `200`:** the updated [group object](README.md#group).

```json
{
  "id": "550e8400-e29b-41d4-a716-446655440000",
  "tag": "mygroup",
  "name": "My Group",
  "description": "A group for testing",
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

### Errors

| Status | When |
| --- | --- |
| `400` | You own the group (`You cannot leave the group you own`) |
| `400` | You aren't a member (`You are not a member of this group`) |
