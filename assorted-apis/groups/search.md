# Search Groups

### GET `/groups/search`

Search public groups by name or description.

**Query Parameters:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `auth` | string | Yes | Your Rotur user token |
| `query` | string | Yes | Search term (matches against name and description, case-insensitive) |

**Response (200):**

```json
[
  {
    "tag": "gamedev",
    "name": "Game Developers",
    "description": "A group for game devs",
    "readme": "",
    "rules": "",
    "icon_url": "",
    "banner_url": "",
    "owner_user_id": "bob",
    "public": true,
    "join_policy": "OPEN",
    "entry_fee": 0,
    "created_at": 1717000000,
    "credits_balance": 30.5,
    "member_count": 12
  }
]
```

Only **public** groups are included in results. Returns an empty array if no groups match.
