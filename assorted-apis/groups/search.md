# Search Groups

Search public groups by tag, name, or description.

### GET `/v2/groups/search`

**Auth:** required. Token permission: `groups:view`.

**Query Parameters:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `query` | string | No | Search term, case-insensitive. Matches tag, name, and description. Leave empty to list all public groups |

**Example request:**

```bash
curl "https://api.rotur.dev/v2/groups/search?auth=YOUR_TOKEN&query=gamedev"
```

**Example response (200):**

```json
[
  {
    "id": "550e8400-e29b-41d4-a716-446655440000",
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

Only public groups appear in results. If nothing matches, the response body is `null`.
