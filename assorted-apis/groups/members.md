# Group Members

## List Group Members

### GET `/groups/{tag}/members`

Returns a paginated list of all members in a group. Only the group owner or members with the `groups.members.view` permission can access this endpoint.

**Path Parameters:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `tag` | string | Yes | The group tag |

**Query Parameters:**

| Parameter | Type | Required | Default | Description |
|-----------|------|----------|---------|-------------|
| `auth` | string | Yes | — | Your Rotur user token |
| `page` | int | No | 1 | Page number (1-indexed) |
| `per_page` | int | No | 20 | Results per page (max 100) |
| `search` | string | No | — | Filter members by username or user ID (case-insensitive partial match) |

**Response (200):**

```json
{
  "members": [
    {
      "id": "abc-123",
      "group_tag": "mygroup",
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

Members are sorted by join date (newest first). The `total` and `pages` fields reflect the filtered count when using the `search` parameter.

**Error Responses:**

| Status | Error | Cause |
|--------|-------|-------|
| 403 | `You don't have permission to view this group's members` | Not a member, or missing `groups.members.view` permission and not the owner |
| 404 | `Group not found` | Group doesn't exist |

---

## Get Member Info

### GET `/groups/{tag}/members/{userid}`

Returns detailed info about a specific member, including their roles, permissions, and benefits.

**Path Parameters:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `tag` | string | Yes | The group tag |
| `userid` | string | Yes | The user's ID |

**Query Parameters:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `auth` | string | Yes | Your Rotur user token |

**Response (200):**

```json
{
  "member": {
    "id": "abc-123",
    "group_tag": "mygroup",
    "username": "alice",
    "role_ids": ["role-1"],
    "joined_at": 1717000000,
    "muted_announcements": false
  },
  "roles": [...],
  "permissions": [...],
  "benefits": [...]
}
```

**Error Responses:**

| Status | Error | Cause |
|--------|-------|-------|
| 404 | `User is not a member of this group` | User not in the group |
| 404 | `Group not found` | Group doesn't exist |

---

## Kick a Member

### DELETE `/groups/{tag}/members/{userid}`

Removes a member from the group. Requires `groups.members.remove` permission or owner status. Cannot kick the group owner.

**Path Parameters:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `tag` | string | Yes | The group tag |
| `userid` | string | Yes | The user ID to kick |

**Query Parameters:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `auth` | string | Yes | Your Rotur user token |

**Response (200):**

```json
{
  "message": "Member kicked",
  "group": { ... }
}
```

**Error Responses:**

| Status | Error | Cause |
|--------|-------|-------|
| 400 | `Cannot kick the group owner` | Attempting to kick the owner |
| 403 | `You don't have permission to kick members` | Missing permission |
| 404 | `User is not a member of this group` | User not in the group |

---

## Ban a Member

### POST `/groups/{tag}/members/{userid}/ban`

Bans a user from the group, removing them as a member and cancelling any pending invites or join requests. Requires `groups.members.ban` permission or owner status.

**Path Parameters:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `tag` | string | Yes | The group tag |
| `userid` | string | Yes | The user ID to ban |

**Query Parameters:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `auth` | string | Yes | Your Rotur user token |
| `reason` | string | No | Ban reason (max 200 characters) |

**Response (200):**

```json
{
  "message": "banned and removed from group",
  "ban": {
    "id": "ban-1",
    "group_tag": "mygroup",
    "username": "bob",
    "banned_by": "alice",
    "reason": "Spamming",
    "created_at": 1717200000
  }
}
```

---

## Unban a Member

### DELETE `/groups/{tag}/members/{userid}/ban`

Removes a ban for a user. Requires `groups.members.ban` permission or owner status.

**Response (200):**

```json
{
  "message": "User unbanned"
}
```

---

## List Bans

### GET `/groups/{tag}/bans`

Returns all bans for a group. Requires `groups.members.ban` permission or owner status.

**Response (200):**

```json
[
  {
    "id": "ban-1",
    "group_tag": "mygroup",
    "username": "bob",
    "banned_by": "alice",
    "reason": "Spamming",
    "created_at": 1717200000
  }
]
```

---

## Check Ban Status

### GET `/groups/{tag}/bans/{userid}`

Checks whether a specific user is banned from the group.

**Response (200):**

```json
{
  "banned": true,
  "ban": { ... }
}
```

Or if not banned:

```json
{
  "banned": false
}
```

---

## Invite System

### Send an Invite

#### POST `/groups/{tag}/invites`

Sends an invite to a user. Requires `groups.members.invite` permission or owner status.

**Query Parameters:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `auth` | string | Yes | Your Rotur user token |
| `username` | string | Yes | The username to invite |

**Response (201):**

```json
{
  "id": "inv-1",
  "group_tag": "mygroup",
  "from_username": "alice",
  "to_username": "bob",
  "status": "PENDING",
  "created_at": 1717000000
}
```

### List Group Invites

#### GET `/groups/{tag}/invites`

Returns all pending invites for a group. Requires `groups.members.invite` permission or owner status.

### Accept an Invite

#### POST `/groups/{tag}/invites/{inviteid}/accept`

Accepts a pending group invite. Only the invited user can accept.

### Decline an Invite

#### POST `/groups/{tag}/invites/{inviteid}/decline`

Declines a pending group invite. Only the invited user can decline.

### Revoke an Invite

#### DELETE `/groups/{tag}/invites/{inviteid}`

Revokes a pending invite. Requires `groups.members.invite` permission or owner status.

### List Your Invites

#### GET `/groups/invites/mine`

Returns all pending group invites for the authenticated user.

---

## Join Request System

### Request to Join

#### POST `/groups/{tag}/join_requests`

Submits a join request for a group with `REQUEST` join policy.

**Query Parameters:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `auth` | string | Yes | Your Rotur user token |
| `message` | string | No | Optional message (max 200 characters) |

**Response (201):**

```json
{
  "id": "req-1",
  "group_tag": "mygroup",
  "username": "bob",
  "message": "I'd love to join!",
  "status": "PENDING",
  "created_at": 1717000000
}
```

### List Join Requests

#### GET `/groups/{tag}/join_requests`

Returns all pending join requests. Requires `groups.members.invite` permission or owner status.

### Accept a Join Request

#### POST `/groups/{tag}/join_requests/{requestid}/accept`

Accepts a pending join request. Requires `groups.members.invite` permission or owner status.

### Decline a Join Request

#### POST `/groups/{tag}/join_requests/{requestid}/decline`

Declines a pending join request. Requires `groups.members.invite` permission or owner status.

---

## Transfer Ownership

### POST `/groups/{tag}/transfer/{userid}`

Transfers group ownership to another member. Only the current owner can do this. The new owner receives the Owner role; the previous owner loses it but remains a member.

**Path Parameters:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `tag` | string | Yes | The group tag |
| `userid` | string | Yes | The user ID to transfer ownership to |

**Response (200):**

```json
{
  "message": "Ownership transferred",
  "group": { ... }
}
```

**Error Responses:**

| Status | Error | Cause |
|--------|-------|-------|
| 400 | `You are already the owner` | Attempting to transfer to self |
| 403 | `Only the group owner can transfer ownership` | Not the owner |
| 404 | `Target user is not a member of this group` | Target not in the group |

---

## Authorization Summary

| Endpoint | Required Permission |
|----------|-------------------|
| List members | `groups.members.view` or owner |
| Get member info | `groups.members.view` or owner |
| Kick member | `groups.members.remove` or owner |
| Ban/unban | `groups.members.ban` or owner |
| View bans | `groups.members.ban` or owner |
| Send/revoke invites | `groups.members.invite` or owner |
| View invites | `groups.members.invite` or owner |
| View/handle join requests | `groups.members.invite` or owner |
| Transfer ownership | Owner only |
