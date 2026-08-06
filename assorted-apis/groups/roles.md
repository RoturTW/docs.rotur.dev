# Roles

Roles control member permissions and benefits inside a group. Every group starts with an **Owner** role (always all permissions, cannot be deleted) and a **Member** role (assigned on join).

## List Roles

### GET `/v2/groups/{tag}/roles`

**Auth:** required. Token permission: `groups:view`.

The Owner role always returns the full permission list, whatever is stored.

**Example request:**

```bash
curl -H "Authorization: Bearer YOUR_TOKEN" "https://api.rotur.dev/v2/groups/mygroup/roles"
```

**Example response (200):**

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
      "groups.members.ban",
      "groups.members.view",
      "groups.roles.manage",
      "groups.roles.assign",
      "groups.announcements.send",
      "groups.events.manage",
      "groups.events.publish",
      "groups.tips.manage",
      "groups.tips.withdraw",
      "groups.tips.deposit",
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

**Common errors:**

| Status | Error | Cause |
|--------|-------|-------|
| 404 | `Group not found` | Group doesn't exist |

***

## Create a Role

### POST `/v2/groups/{tag}/roles`

**Auth:** required. Token permission: `groups:manage`. Requires the `groups.roles.manage` group permission.

New roles start with empty `benefits` and `permissions`. Use the update endpoint to set them.

**Query Parameters:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `name` | string | Yes | Role name (max 50 chars) |
| `description` | string | No | Role description (max 200 chars) |
| `assign_on_join` | string | No | `"true"` to auto-assign to new members (default: `"false"`) |
| `self_assignable` | string | No | `"true"` to let members assign it to themselves (default: `"false"`) |

**Example request:**

```bash
curl -X POST -H "Authorization: Bearer YOUR_TOKEN" "https://api.rotur.dev/v2/groups/mygroup/roles?name=Moderator"
```

**Example response (201):**

```json
{
  "id": "role-3",
  "group_tag": "mygroup",
  "name": "Moderator",
  "description": "",
  "assign_on_join": false,
  "self_assignable": false,
  "benefits": [],
  "permissions": []
}
```

**Common errors:**

| Status | Error | Cause |
|--------|-------|-------|
| 400 | `Name is required` | No name provided |
| 400 | `Name length exceeded` | Name longer than 50 chars |
| 400 | `Description length exceeded` | Description longer than 200 chars |
| 403 | `You don't have permission to manage roles` | Missing `groups.roles.manage` |
| 404 | `Group not found` | Group doesn't exist |

***

## Update a Role

### PATCH `/v2/groups/{tag}/roles/{roleid}`

**Auth:** required. Token permission: `groups:manage`. Requires the `groups.roles.manage` group permission.

**Body (JSON):**

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `name` | string | No | New role name |
| `description` | string | No | New description |
| `assign_on_join` | bool | No | Auto-assign to new members |
| `self_assignable` | bool | No | Members can self-assign |
| `permissions` | string[] | No | New permissions list (replaces the old one) |
| `benefits` | string[] | No | New benefits list (replaces the old one) |

**Example request:**

```bash
curl -X PATCH -H "Authorization: Bearer YOUR_TOKEN" "https://api.rotur.dev/v2/groups/mygroup/roles/role-3" \  -H "Content-Type: application/json" \
  -d '{"permissions": ["groups.announcements.send"]}'
```

**Example response (200):**

```json
{
  "message": "Role updated"
}
```

**Common errors:**

| Status | Error | Cause |
|--------|-------|-------|
| 400 | `Invalid request body` | Malformed JSON |
| 400 | `Invalid permissions` / `Invalid benefits` | Not an array of strings |
| 403 | `You don't have permission to manage roles` | Missing permission |
| 404 | `Role not found` | Role ID doesn't exist in this group |
| 404 | `Group not found` | Group doesn't exist |

***

## Delete a Role

### DELETE `/v2/groups/{tag}/roles/{roleid}`

**Auth:** required. Token permission: `groups:manage`. Requires the `groups.roles.manage` group permission. The **Owner** and **Everyone** roles can't be deleted.

**Example request:**

```bash
curl -X DELETE -H "Authorization: Bearer YOUR_TOKEN" "https://api.rotur.dev/v2/groups/mygroup/roles/role-3"
```

**Example response (200):**

```json
{
  "message": "Role deleted"
}
```

**Common errors:**

| Status | Error | Cause |
|--------|-------|-------|
| 400 | `Cannot delete default roles` | Tried to delete Owner or Everyone |
| 403 | `You don't have permission to manage roles` | Missing permission |
| 404 | `Role not found` | Role ID doesn't exist in this group |
| 404 | `Group not found` | Group doesn't exist |
