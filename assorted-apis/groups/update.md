# Update a Group

### PATCH `/groups/{tag}`

Updates group settings. **Owner only.**

**Path Parameters:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `tag` | string | Yes | The group tag |

**Query Parameters:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `auth` | string | Yes | Your Rotur user token |

**Body (JSON):**

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `description` | string | No | New description (max 500 chars) |
| `readme` | string | No | New readme content (max 10,000 chars) |
| `rules` | string | No | New rules content (max 5,000 chars) |
| `icon` | string | No | Icon URL string (for URL-based icons; use the `/icon` upload endpoint for image uploads) |
| `banner_url` | string | No | Banner URL (for URL-based banners; use the `/banner` endpoint to set with credit charge) |
| `public` | bool | No | Whether the group is public |
| `join_policy` | string | No | `"OPEN"`, `"REQUEST"`, or `"INVITE"` |
| `entry_fee` | float | No | Credits required to join (set to `0` to remove) |

Only included fields are updated.

**Response (200):**

```json
{
  "id": "550e8400-...",
  "tag": "mygroup",
  "name": "My Group",
  "description": "Updated description",
  "readme": "# Welcome",
  "rules": "1. Be kind",
  "icon_url": "",
  "banner_url": "",
  "owner_user_id": "alice",
  "public": true,
  "join_policy": "OPEN",
  "entry_fee": 5.0,
  "created_at": 1717000000,
  "credits_balance": 0,
  "member_count": 5
}
```

**Error Responses:**

| Status | Error | Cause |
|--------|-------|-------|
| 400 | `Invalid request body` | Malformed JSON |
| 400 | `Readme length exceeded` | Readme longer than 10,000 chars |
| 400 | `Rules length exceeded` | Rules longer than 5,000 chars |
| 400 | `Entry fee cannot be negative` | Entry fee is a negative number |
| 403 | `You are not authorized to update this group` | Not the group owner |
| 404 | `Group not found` | Group doesn't exist |
