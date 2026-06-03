# Member Roles, Permissions & Benefits

These endpoints let you inspect and manage role assignments for individual members.

## Get a Member's Roles

### GET `/groups/{tag}/members/{userid}/roles`

Returns all roles assigned to a specific member.

**Path Parameters:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `tag` | string | Yes | The group tag |
| `userid` | string | Yes | The user's ID (username) |

**Query Parameters:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `auth` | string | Yes | Your Rotur user token |

**Response (200):**

```json
{
  "roles": [
    {
      "id": "role-1",
      "group_tag": "mygroup",
      "name": "Owner",
      "description": "Group owner",
      "assign_on_join": false,
      "self_assignable": false,
      "benefits": [],
      "permissions": ["groups.manage", "..."]
    },
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
  ]
}
```

**Error Responses:**

| Status | Error | Cause |
|--------|-------|-------|
| 404 | `User is not a member of this group` | User not in the group |
| 404 | `Group not found` | Group doesn't exist |

---

## Get a Member's Permissions

### GET `/groups/{tag}/members/{userid}/permissions`

Returns the aggregated list of permissions a member has across all their roles.

**Path Parameters:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `tag` | string | Yes | The group tag |
| `userid` | string | Yes | The user's ID (username) |

**Query Parameters:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `auth` | string | Yes | Your Rotur user token |

**Response (200):**

```json
{
  "permissions": [
    "groups.manage",
    "groups.members.invite",
    "groups.members.remove",
    "groups.roles.manage",
    "groups.roles.assign",
    "groups.announcements.send",
    "groups.events.manage",
    "groups.events.publish",
    "groups.tips.manage",
    "groups.group.edit"
  ]
}
```

The **Owner** role automatically grants all permissions, regardless of what's listed in the `permissions` array.

**Error Responses:**

| Status | Error | Cause |
|--------|-------|-------|
| 404 | `User is not a member of this group` | User not in the group |
| 404 | `Group not found` | Group doesn't exist |

---

## Get a Member's Benefits

### GET `/groups/{tag}/members/{userid}/benefits`

Returns the aggregated list of benefits a member has across all their roles.

**Path Parameters:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `tag` | string | Yes | The group tag |
| `userid` | string | Yes | The user's ID (username) |

**Query Parameters:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `auth` | string | Yes | Your Rotur user token |

**Response (200):**

```json
{
  "benefits": ["custom_color", "priority_access"]
}
```

**Error Responses:**

| Status | Error | Cause |
|--------|-------|-------|
| 404 | `User is not a member of this group` | User not in the group |
| 404 | `Group not found` | Group doesn't exist |

---

## Assign a Role to a Member

### POST `/groups/{tag}/members/{userid}/roles/{roleid}`

Assign a role to a member. Requires `groups.roles.assign` permission, unless the role is `self_assignable` and the user is assigning it to themselves.

**Path Parameters:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `tag` | string | Yes | The group tag |
| `userid` | string | Yes | The target user's ID (username) |
| `roleid` | string | Yes | The role ID to assign |

**Query Parameters:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `auth` | string | Yes | Your Rotur user token |

**Response (200):**

```json
{
  "message": "Role assigned"
}
```

**Error Responses:**

| Status | Error | Cause |
|--------|-------|-------|
| 400 | `User already has this role` | Role already assigned |
| 403 | `You don't have permission to assign roles` | Missing `groups.roles.assign` and role is not self-assignable |
| 404 | `Role not found` | Role ID doesn't exist in this group |
| 404 | `User is not a member of this group` | Target user not in the group |

---

## Remove a Role from a Member

### DELETE `/groups/{tag}/members/{userid}/roles/{roleid}`

Remove a role from a member. Requires `groups.roles.assign` permission. Cannot remove the **Owner** role.

**Path Parameters:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `tag` | string | Yes | The group tag |
| `userid` | string | Yes | The target user's ID (username) |
| `roleid` | string | Yes | The role ID to remove |

**Query Parameters:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `auth` | string | Yes | Your Rotur user token |

**Response (200):**

```json
{
  "message": "Role removed"
}
```

**Error Responses:**

| Status | Error | Cause |
|--------|-------|-------|
| 400 | `Cannot remove Owner role` | Attempting to remove the Owner role |
| 400 | `User doesn't have this role` | Role not assigned to this member |
| 403 | `You don't have permission to remove roles` | Missing `groups.roles.assign` |
| 404 | `Role not found` | Role ID doesn't exist in this group |
| 404 | `User is not a member of this group` | Target user not in the group |
