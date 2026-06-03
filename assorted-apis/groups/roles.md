# Roles

Roles control member permissions and benefits within a group. Every group starts with an **Owner** role (all permissions, cannot be deleted) and a **Member** role (auto-assigned on join).

## List Roles

### GET `/groups/{tag}/roles`

Returns all roles for a group.

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
[
  {
    "id": "role-1",
    "group_tag": "mygroup",
    "name": "Owner",
    "description": "Group owner",
    "assign_on_join": false,
    "self_assignable": false,
    "benefits": [],
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
```

**Error Responses:**

| Status | Error | Cause |
|--------|-------|-------|
| 404 | `Group not found` | Group doesn't exist |

---

## Create a Role

### POST `/groups/{tag}/roles`

Create a new custom role. Requires `groups.roles.manage` permission.

**Path Parameters:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `tag` | string | Yes | The group tag |

**Query Parameters:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `auth` | string | Yes | Your Rotur user token |
| `name` | string | Yes | Role name (max 50 chars) |
| `description` | string | No | Role description (max 200 chars) |
| `assign_on_join` | string | No | `"true"` to auto-assign to new members (default: `"false"`) |
| `self_assignable` | string | No | `"true"` to let members assign to themselves (default: `"false"`) |

New roles start with empty `benefits` and `permissions`. Use the update endpoint to add them.

**Response (201):**

```json
{
  "id": "role-3",
  "group_tag": "mygroup",
  "name": "Moderator",
  "description": "Can manage announcements",
  "assign_on_join": false,
  "self_assignable": true,
  "benefits": [],
  "permissions": []
}
```

**Error Responses:**

| Status | Error | Cause |
|--------|-------|-------|
| 400 | `Name is required` | No name provided |
| 400 | `Name length exceeded` | Name longer than 50 chars |
| 400 | `Description length exceeded` | Description longer than 200 chars |
| 403 | `You don't have permission to manage roles` | Missing `groups.roles.manage` |
| 404 | `Group not found` | Group doesn't exist |

---

## Update a Role

### PATCH `/groups/{tag}/roles/{roleid}`

Update a role's properties. Requires `groups.roles.manage` permission.

**Path Parameters:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `tag` | string | Yes | The group tag |
| `roleid` | string | Yes | The role ID |

**Query Parameters:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `auth` | string | Yes | Your Rotur user token |

**Body (JSON):**

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `name` | string | No | New role name |
| `description` | string | No | New description |
| `assign_on_join` | bool | No | Auto-assign to new members |
| `self_assignable` | bool | No | Members can self-assign |
| `permissions` | string[] | No | New permissions list |
| `benefits` | string[] | No | New benefits list |

**Response (200):**

```json
{
  "message": "Role updated"
}
```

**Error Responses:**

| Status | Error | Cause |
|--------|-------|-------|
| 400 | `Invalid request body` | Malformed JSON |
| 403 | `You don't have permission to manage roles` | Missing permission |
| 404 | `Role not found` | Role ID doesn't exist in this group |
| 404 | `Group not found` | Group doesn't exist |

---

## Delete a Role

### DELETE `/groups/{tag}/roles/{roleid}`

Delete a custom role. Requires `groups.roles.manage` permission. Cannot delete the **Owner** or **Everyone** default roles.

**Path Parameters:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `tag` | string | Yes | The group tag |
| `roleid` | string | Yes | The role ID |

**Query Parameters:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `auth` | string | Yes | Your Rotur user token |

**Response (200):**

```json
{
  "message": "Role deleted"
}
```

**Error Responses:**

| Status | Error | Cause |
|--------|-------|-------|
| 400 | `Cannot delete default roles` | Attempting to delete Owner or Everyone role |
| 403 | `You don't have permission to manage roles` | Missing permission |
| 404 | `Role not found` | Role ID doesn't exist in this group |
| 404 | `Group not found` | Group doesn't exist |
