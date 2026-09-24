# Members

List a group's members, look up one member, or kick a member.

## GET `/v2/groups/{tag}/members`

List members, newest first, one page at a time.

**Auth:** Required. Sub-tokens need `groups:members.view`. You must be a member with the Owner role or `groups.members.view`.

### Parameters

| Name | In | Type | Required | Description |
| --- | --- | --- | --- | --- |
| `search` | query | string | No | Case-insensitive text to match anywhere in the username or user ID |
| `page` | query | integer | No | Page number. Default `1` |
| `per_page` | query | integer | No | Members per page. Default `20`, maximum `100` |

### Example

```http
GET /v2/groups/mygroup/members?page=1&per_page=20
Authorization: Bearer YOUR_TOKEN
```

**Response `200`:** `members` holds [member objects](README.md#member). `total` counts members matching `search`, and `pages` is at least `1`.

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

### Errors

| Status | When |
| --- | --- |
| `403` | You aren't a member, or lack `groups.members.view` (`You don't have permission to view this group's members`) |

## GET `/v2/groups/{tag}/members/{userid}`

Get one member's record together with their roles, combined permissions, and combined benefits. You don't have to be a member of the group.

**Auth:** Required. Sub-tokens need `groups:members.view`.

### Parameters

| Name | In | Type | Required | Description |
| --- | --- | --- | --- | --- |
| `userid` | path | string | Yes | The member's user ID. A username doesn't work here |

### Example

```http
GET /v2/groups/mygroup/members/USER_ID
Authorization: Bearer YOUR_TOKEN
```

**Response `200`:**

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

### Errors

| Status | When |
| --- | --- |
| `404` | No member has that user ID (`User is not a member of this group`) |

## DELETE `/v2/groups/{tag}/members/{userid}`

Kick a member. They can rejoin unless you also [ban](bans.md) them. The kicked user gets a `group_kicked` event and a push notification.

**Auth:** Required. Sub-tokens need `groups:manage`. You must be the owner or hold `groups.members.remove`. Only the owner can kick a member who has the Owner role, and nobody can kick the owner.

### Parameters

| Name | In | Type | Required | Description |
| --- | --- | --- | --- | --- |
| `userid` | path | string | Yes | The member's user ID. A username doesn't work here |

### Example

```http
DELETE /v2/groups/mygroup/members/USER_ID
Authorization: Bearer YOUR_TOKEN
```

**Response `200`:** `group` is the full updated [group object](README.md#group), shortened here.

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

### Errors

| Status | When |
| --- | --- |
| `400` | The user owns the group (`Cannot kick the group owner`) |
| `403` | You lack `groups.members.remove` (`You don't have permission to kick members`) |
| `403` | The user has the Owner role and you aren't the owner (`Cannot kick a member with the Owner role`) |
| `404` | No member has that user ID (`User is not a member of this group`) |
