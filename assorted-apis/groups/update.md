# Update a Group

Change a group's settings. Only fields you include are updated.

### PATCH `/v2/groups/{tag}`

**Auth:** required. Token permission: `groups:manage`. You must be the group owner or hold the `groups.group.edit` or `groups.manage` group permission.

**Path Parameters:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `tag` | string | Yes | The group tag |

**Body (JSON):**

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `name` | string | No | New display name (max 50 chars, cannot be empty) |
| `tag` | string | No | New tag (alphanumeric, max 10 chars, must be unused). Renames the group everywhere |
| `description` | string | No | New description |
| `readme` | string | No | New readme (max 10,000 chars) |
| `rules` | string | No | New rules (max 5,000 chars) |
| `icon` or `icon_url` | string | No | Icon URL (use the [icon upload endpoint](icon.md) for image uploads) |
| `banner_url` | string | No | Banner URL (use the [banner upload endpoint](banner.md) for image uploads) |
| `public` | bool | No | Whether the group is public |
| `join_policy` | string | No | `"OPEN"`, `"REQUEST"`, or `"INVITE"` |
| `entry_fee` | float | No | Credits required to join (set `0` to remove) |

**Example request:**

```bash
curl -X PATCH -H "Authorization: Bearer YOUR_TOKEN" "https://api.rotur.dev/v2/groups/mygroup" \  -H "Content-Type: application/json" \
  -d '{"description": "Updated description", "entry_fee": 5}'
```

**Example response (200):**

```json
{
  "id": "550e8400-e29b-41d4-a716-446655440000",
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

{% hint style="warning" %}
Changing `tag` renames the group. All future API calls must use the new tag, and old links to the group stop working.
{% endhint %}

**Common errors:**

| Status | Error | Cause |
|--------|-------|-------|
| 400 | `Invalid request body` | Malformed JSON |
| 400 | `Name cannot be empty` | Empty `name` |
| 400 | `Readme length exceeded` | Readme longer than 10,000 chars |
| 400 | `Rules length exceeded` | Rules longer than 5,000 chars |
| 400 | `Entry fee cannot be negative` | Negative entry fee |
| 400 | `Invalid join policy` | Not one of the valid values |
| 400 | `Group with this tag already exists` | New tag is taken |
| 403 | `You are not authorized to update this group` | Not the owner and no edit permission |
| 404 | `Group not found` | Group doesn't exist |
