# Member Roles, Permissions & Benefits

Inspect and manage the roles held by an individual member.

{% hint style="info" %}
The `{userid}` path segment on these endpoints accepts either a username or a user ID.
{% endhint %}

## Get a Member's Roles

### GET `/v2/groups/{tag}/members/{userid}/roles`

**Auth:** required. Token permission: `groups:view`.

Returns every role assigned to the member. The Owner role includes the full permission list.

**Example request:**

```bash
curl -H "Authorization: Bearer YOUR_TOKEN" "https://api.rotur.dev/v2/groups/mygroup/members/alice/roles"
```

**Example response (200):**

```json
{
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
  ]
}
```

**Common errors:**

| Status | Error | Cause |
|--------|-------|-------|
| 404 | `User is not a member of this group` | User not in the group |
| 404 | `Group not found` | Group doesn't exist |

***

## Get a Member's Permissions

### GET `/v2/groups/{tag}/members/{userid}/permissions`

**Auth:** required. Token permission: `groups:view`.

Returns the combined permissions from all the member's roles. Members with the Owner role always get the full list.

**Example request:**

```bash
curl -H "Authorization: Bearer YOUR_TOKEN" "https://api.rotur.dev/v2/groups/mygroup/members/alice/permissions"
```

**Example response (200):**

```json
{
  "permissions": ["groups.announcements.send", "groups.events.manage"]
}
```

**Common errors:**

| Status | Error | Cause |
|--------|-------|-------|
| 404 | `User is not a member of this group` | User not in the group |
| 404 | `Group not found` | Group doesn't exist |

***

## Get a Member's Benefits

### GET `/v2/groups/{tag}/members/{userid}/benefits`

**Auth:** required. Token permission: `groups:view`.

Returns the combined benefits from all the member's roles.

**Example request:**

```bash
curl -H "Authorization: Bearer YOUR_TOKEN" "https://api.rotur.dev/v2/groups/mygroup/members/alice/benefits"
```

**Example response (200):**

```json
{
  "benefits": ["custom_color", "priority_access"]
}
```

**Common errors:**

| Status | Error | Cause |
|--------|-------|-------|
| 404 | `User is not a member of this group` | User not in the group |
| 404 | `Group not found` | Group doesn't exist |

***

## Assign a Role

### PUT `/v2/groups/{tag}/members/{userid}/roles/{roleid}`

**Auth:** required. Token permission: `groups:manage`. Requires the `groups.roles.assign` group permission, unless the role is `self_assignable` and you're assigning it to yourself.

**Example request:**

```bash
curl -X PUT -H "Authorization: Bearer YOUR_TOKEN" "https://api.rotur.dev/v2/groups/mygroup/members/alice/roles/role-3"
```

**Example response (200):**

```json
{
  "message": "Role assigned"
}
```

**Common errors:**

| Status | Error | Cause |
|--------|-------|-------|
| 400 | `User already has this role` | Role already assigned |
| 403 | `You don't have permission to assign roles` | Missing `groups.roles.assign` and not self-assigning a self-assignable role |
| 404 | `Role not found` | Role ID doesn't exist in this group |
| 404 | `User is not a member of this group` | Target not in the group |
| 404 | `Group not found` | Group doesn't exist |

***

## Remove a Role

### DELETE `/v2/groups/{tag}/members/{userid}/roles/{roleid}`

**Auth:** required. Token permission: `groups:manage`. Requires the `groups.roles.assign` group permission. The Owner role can't be removed this way; use [ownership transfer](transfer.md) instead.

**Example request:**

```bash
curl -X DELETE -H "Authorization: Bearer YOUR_TOKEN" "https://api.rotur.dev/v2/groups/mygroup/members/alice/roles/role-3"
```

**Example response (200):**

```json
{
  "message": "Role removed"
}
```

**Common errors:**

| Status | Error | Cause |
|--------|-------|-------|
| 400 | `Cannot remove Owner role` | Tried to remove the Owner role |
| 400 | `User doesn't have this role` | Role not assigned to this member |
| 403 | `You don't have permission to remove roles` | Missing `groups.roles.assign` |
| 404 | `Role not found` | Role ID doesn't exist in this group |
| 404 | `User is not a member of this group` | Target not in the group |
| 404 | `Group not found` | Group doesn't exist |
