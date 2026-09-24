# Top groups

Get the 10 largest public groups by member count.

## GET `/v2/groups/top`

**Auth:** None.

### Example

```http
GET /v2/groups/top
```

**Response `200`:** up to 10 [group objects](README.md#group), largest first. Private groups are never included.

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
