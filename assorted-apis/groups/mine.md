# List My Groups

### GET `/groups/mine`

Returns all groups the authenticated user is a member of.

**Query Parameters:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `auth` | string | Yes | Your Rotur user token |

**Response (200):**

```json
[
  {
    "tag": "mygroup",
    "name": "My Group",
    "description": "A cool group",
    "icon_url": "",
    "banner_url": "",
    "owner_user_id": "alice",
    "public": true,
    "join_policy": "OPEN",
    "created_at": 1717000000,
    "credits_balance": 0,
    "member_count": 5
  }
]
```

Returns an empty array `[]` if you aren't in any groups.
