# Leave a Group

### POST `/groups/{tag}/leave`

Leave a group you are a member of. You cannot leave a group you own.

**Path Parameters:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `tag` | string | Yes | The group tag |

**Query Parameters:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `auth` | string | Yes | Your Rotur user token |

**Response (200):**

Returns the updated group info with decremented `member_count`:

```json
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
  "member_count": 4
}
```

**Error Responses:**

| Status | Error | Cause |
|--------|-------|-------|
| 400 | `You cannot leave the group you own` | You are the owner |
| 400 | `You are not a member of this group` | Not a member |
| 404 | `Group not found` | Group doesn't exist |
