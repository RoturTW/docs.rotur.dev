# Member roles and permissions

Look up the roles, permissions, and benefits of one member, and assign or remove their roles.

On these endpoints, `{userid}` accepts a username or a user ID.

## GET `/v2/groups/{tag}/members/{userid}/roles`

Get every role the member holds. The Owner role is returned with the full permission list.

**Auth:** Required. Sub-tokens need `groups:view`.

### Example

```http
GET /v2/groups/mygroup/members/alice/roles
Authorization: Bearer YOUR_TOKEN
```

**Response `200`:** `roles` holds [role objects](README.md#role).

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

### Errors

| Status | When |
| --- | --- |
| `404` | The user isn't a member (`User is not a member of this group`) |

## GET `/v2/groups/{tag}/members/{userid}/permissions`

Get the combined permissions from all the member's roles. A member with the Owner role gets the full list.

**Auth:** Required. Sub-tokens need `groups:view`.

### Example

```http
GET /v2/groups/mygroup/members/alice/permissions
Authorization: Bearer YOUR_TOKEN
```

**Response `200`:**

```json
{
  "permissions": ["groups.announcements.send", "groups.events.manage"]
}
```

### Errors

| Status | When |
| --- | --- |
| `404` | The user isn't a member (`User is not a member of this group`) |

## GET `/v2/groups/{tag}/members/{userid}/benefits`

Get the combined benefits from all the member's roles.

**Auth:** Required. Sub-tokens need `groups:view`.

### Example

```http
GET /v2/groups/mygroup/members/alice/benefits
Authorization: Bearer YOUR_TOKEN
```

**Response `200`:**

```json
{
  "benefits": ["custom_color", "priority_access"]
}
```

### Errors

| Status | When |
| --- | --- |
| `404` | The user isn't a member (`User is not a member of this group`) |

## PUT `/v2/groups/{tag}/members/{userid}/roles/{roleid}`

Give a member a role.

**Auth:** Required. Sub-tokens need `groups:manage`. You need `groups.roles.assign`, unless the role is `self_assignable` and you're giving it to yourself.

### Parameters

| Name | In | Type | Required | Description |
| --- | --- | --- | --- | --- |
| `roleid` | path | string | Yes | The role's `id` |

### Example

```http
PUT /v2/groups/mygroup/members/alice/roles/role-3
Authorization: Bearer YOUR_TOKEN
```

**Response `200`:**

```json
{
  "message": "Role assigned"
}
```

### Errors

| Status | When |
| --- | --- |
| `400` | The member already has the role (`User already has this role`) |
| `403` | You lack `groups.roles.assign` and aren't self-assigning a self-assignable role (`You don't have permission to assign roles`) |
| `404` | No role has that ID in this group (`Role not found`) |
| `404` | The user isn't a member (`User is not a member of this group`) |

## DELETE `/v2/groups/{tag}/members/{userid}/roles/{roleid}`

Remove a role from a member. The Owner role can't be removed this way; [transfer ownership](transfer.md) instead.

**Auth:** Required. Sub-tokens need `groups:manage`. You need `groups.roles.assign`, including to remove a role from yourself.

### Parameters

| Name | In | Type | Required | Description |
| --- | --- | --- | --- | --- |
| `roleid` | path | string | Yes | The role's `id` |

### Example

```http
DELETE /v2/groups/mygroup/members/alice/roles/role-3
Authorization: Bearer YOUR_TOKEN
```

**Response `200`:**

```json
{
  "message": "Role removed"
}
```

### Errors

| Status | When |
| --- | --- |
| `400` | The role is the Owner role (`Cannot remove Owner role`) |
| `400` | The member doesn't have the role (`User doesn't have this role`) |
| `403` | You lack `groups.roles.assign` (`You don't have permission to remove roles`) |
| `404` | No role has that ID in this group (`Role not found`) |
| `404` | The user isn't a member (`User is not a member of this group`) |
