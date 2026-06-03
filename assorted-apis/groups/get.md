# Get a Group

### GET `/groups/{tag}`

Returns a group's public information.

**Path Parameters:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `tag` | string | Yes | The group tag |

**Query Parameters:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `auth` | string | Yes | Your Rotur user token |

**Response (200):**

```json
{
  "tag": "mygroup",
  "name": "My Group",
  "description": "A cool group",
  "readme": "# About\nWelcome to our group!",
  "rules": "1. Be respectful\n2. No spam",
  "icon_url": "https://api.rotur.dev/groups/mygroup/icon.jpg",
  "banner_url": "",
  "owner_user_id": "alice",
  "public": true,
  "join_policy": "OPEN",
  "entry_fee": 5.0,
  "created_at": 1717000000,
  "credits_balance": 75.5,
  "member_count": 12
}
```

**Error Responses:**

| Status | Error | Cause |
|--------|-------|-------|
| 400 | `Group tag is required` | No tag provided |
| 404 | `Group not found` | Group doesn't exist |
