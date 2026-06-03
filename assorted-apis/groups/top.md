# Top Groups

### GET `/groups/top`

Returns the top 10 public groups sorted by member count (descending).

**Query Parameters:** None (no auth required).

**Response (200):**

```json
[
  {
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

Returns up to 10 groups. Only public groups are included.
