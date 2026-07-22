# Get a Group

Fetch a group's info by tag. No authentication needed.

### GET `/v2/groups/{tag}`

**Path Parameters:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `tag` | string | Yes | The group tag |

**Example request:**

```bash
curl "https://api.rotur.dev/v2/groups/mygroup"
```

**Example response (200):**

```json
{
  "id": "550e8400-e29b-41d4-a716-446655440000",
  "tag": "mygroup",
  "name": "My Group",
  "description": "A cool group",
  "readme": "# About\nWelcome to our group!",
  "rules": "1. Be respectful\n2. No spam",
  "icon_url": "https://api.rotur.dev/groups/mygroup/icon.jpg",
  "banner_url": "https://api.rotur.dev/groups/mygroup/banner",
  "owner_user_id": "alice",
  "public": true,
  "join_policy": "OPEN",
  "entry_fee": 5.0,
  "created_at": 1717000000,
  "credits_balance": 75.5,
  "member_count": 12
}
```

**Common errors:**

| Status | Error | Cause |
|--------|-------|-------|
| 404 | `Group not found` | Group doesn't exist |
