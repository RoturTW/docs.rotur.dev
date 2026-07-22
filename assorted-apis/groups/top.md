# Top Groups

Get the 10 biggest public groups by member count. No authentication needed.

### GET `/v2/groups/top`

**Example request:**

```bash
curl "https://api.rotur.dev/v2/groups/top"
```

**Example response (200):**

```json
[
  {
    "id": "550e8400-e29b-41d4-a716-446655440000",
    "tag": "biggroup",
    "name": "Big Group",
    "description": "The biggest group",
    "readme": "",
    "rules": "",
    "icon_url": "https://api.rotur.dev/groups/biggroup/icon.jpg",
    "banner_url": "",
    "owner_user_id": "alice",
    "public": true,
    "join_policy": "OPEN",
    "entry_fee": 0,
    "created_at": 1717000000,
    "credits_balance": 500.0,
    "member_count": 150
  }
]
```

Returns up to 10 groups, largest first. Private groups are never included.
