# Get a group

Fetch a group's details by tag. This works for private groups too.

## GET `/v2/groups/{tag}`

**Auth:** None.

### Example

```http
GET /v2/groups/mygroup
```

**Response `200`:** a [group object](README.md#group).

```json
{
  "id": "550e8400-e29b-41d4-a716-446655440000",
  "tag": "mygroup",
  "name": "My Group",
  "description": "A group for testing",
  "readme": "# About\nWelcome to our group.",
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
