# List My Groups

Get every group you're a member of.

### GET `/v2/groups/mine`

**Auth:** required. Token permission: `groups:view`.

**Example request:**

```bash
curl "https://api.rotur.dev/v2/groups/mine?auth=YOUR_TOKEN"
```

**Example response (200):**

```json
[
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
    "member_count": 5
  }
]
```

Returns an empty array `[]` if you aren't in any groups.
