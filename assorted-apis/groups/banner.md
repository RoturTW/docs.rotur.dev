# Set a Group Banner

### POST `/groups/{tag}/banner`

Set the group's banner URL. **Owner only. Costs 10 credits.**

**Path Parameters:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `tag` | string | Yes | The group tag |

**Query Parameters:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `auth` | string | Yes | Your Rotur user token |
| `banner_url` | string | Yes | URL for the banner image (must start with `http://` or `https://`) |

**Response (200):**

```json
{
  "id": "550e8400-...",
  "tag": "mygroup",
  "name": "My Group",
  "description": "A cool group",
  "icon_url": "",
  "banner_url": "https://example.com/banner.png",
  "owner_user_id": "alice",
  "public": true,
  "join_policy": "OPEN",
  "created_at": 1717000000,
  "credits_balance": 0,
  "member_count": 5
}
```

**Transaction:** A `group_banner` transaction for 10 credits is recorded on your account.

**Error Responses:**

| Status | Error | Cause |
|--------|-------|-------|
| 400 | `Banner must be a valid URL` | URL doesn't start with `http://` or `https://` |
| 400 | `Insufficient funds to set a banner (10 credits required)` | Not enough credits |
| 403 | `You are not authorized to update this group` | Not the group owner |
| 404 | `Group not found` | Group doesn't exist |
