# List your groups

List every group you're a member of, including groups you own.

## GET `/v2/groups/mine`

**Auth:** Required. Sub-tokens need `groups:view`.

### Example

```http
GET /v2/groups/mine
Authorization: Bearer YOUR_TOKEN
```

**Response `200`:** an array of [group objects](README.md#group), or `[]` if you aren't in any groups.

```json
[
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
    "member_count": 5
  }
]
```
