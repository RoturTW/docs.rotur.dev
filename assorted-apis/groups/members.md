# Members

List a group's members, look up a single member, and kick members.

## List Members

### GET `/v2/groups/{tag}/members`

**Auth:** required. Token permission: `groups:members.view`. You must be a member of the group, and either hold the Owner role or the `groups.members.view` group permission.

Members are returned newest first and paginated.

**Query Parameters:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `search` | string | No | Filter by username or user ID (case-insensitive substring) |
| `page` | int | No | Page number (default: 1) |
| `per_page` | int | No | Results per page (default: 20, max: 100) |

**Example request:**

```bash
curl "https://api.rotur.dev/v2/groups/mygroup/members?auth=YOUR_TOKEN&page=1&per_page=20"
```

**Example response (200):**

```json
{
  "members": [
    {
      "id": "abc-123",
      "group_tag": "mygroup",
      "user_id": "user-id-here",
      "username": "alice",
      "role_ids": ["role-1", "role-2"],
      "joined_at": 1717000000,
      "muted_announcements": false
    }
  ],
  "page": 1,
  "per_page": 20,
  "total": 42,
  "pages": 3
}
```

**Common errors:**

| Status | Error | Cause |
|--------|-------|-------|
| 403 | `You don't have permission to view this group's members` | Not a member, or missing `groups.members.view` |
| 404 | `Group not found` | Group doesn't exist |

***

## Get a Member

### GET `/v2/groups/{tag}/members/{userid}`

**Auth:** required. Token permission: `groups:members.view`.

Returns a member's record plus their resolved roles, permissions, and benefits in one call.

{% hint style="warning" %}
`{userid}` here must be the user's ID, not their username.
{% endhint %}

**Example request:**

```bash
curl "https://api.rotur.dev/v2/groups/mygroup/members/USER_ID?auth=YOUR_TOKEN"
```

**Example response (200):**

```json
{
  "member": {
    "id": "abc-123",
    "group_tag": "mygroup",
    "user_id": "user-id-here",
    "username": "alice",
    "role_ids": ["role-2"],
    "joined_at": 1717000000,
    "muted_announcements": false
  },
  "roles": [
    {
      "id": "role-2",
      "group_tag": "mygroup",
      "name": "Member",
      "description": "Regular group member",
      "assign_on_join": true,
      "self_assignable": false,
      "benefits": [],
      "permissions": []
    }
  ],
  "permissions": [],
  "benefits": []
}
```

**Common errors:**

| Status | Error | Cause |
|--------|-------|-------|
| 404 | `User is not a member of this group` | User ID not in the group |
| 404 | `Group not found` | Group doesn't exist |

***

## Kick a Member

### DELETE `/v2/groups/{tag}/members/{userid}`

**Auth:** required. Token permission: `groups:manage`. You must be the group owner or hold the `groups.members.remove` group permission.

The group owner can't be kicked. Members holding the Owner role can only be kicked by the actual owner. The kicked user gets a `group_kicked` event and a push notification.

{% hint style="warning" %}
`{userid}` here must be the user's ID, not their username.
{% endhint %}

**Example request:**

```bash
curl -X DELETE "https://api.rotur.dev/v2/groups/mygroup/members/USER_ID?auth=YOUR_TOKEN"
```

**Example response (200):**

```json
{
  "message": "Member kicked",
  "group": {
    "id": "550e8400-e29b-41d4-a716-446655440000",
    "tag": "mygroup",
    "name": "My Group",
    "member_count": 4
  }
}
```

The `group` field is the full updated group object (shortened here).

**Common errors:**

| Status | Error | Cause |
|--------|-------|-------|
| 400 | `Cannot kick the group owner` | Target owns the group |
| 403 | `You don't have permission to kick members` | Missing `groups.members.remove` |
| 403 | `Cannot kick a member with the Owner role` | Only the owner can kick Owner-role members |
| 404 | `User is not a member of this group` | Target not in the group |
| 404 | `Group not found` | Group doesn't exist |
