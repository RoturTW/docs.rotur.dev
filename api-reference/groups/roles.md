# Roles

Roles give members group permissions and benefits. Every group starts with an **Owner** role, which always has every permission and can't be deleted, and a **Member** role, which is given to new members. See [group permissions](README.md#roles-and-group-permissions) for what each permission allows.

## GET `/v2/groups/{tag}/roles`

List a group's roles. The Owner role is always returned with the full permission list, whatever is stored.

**Auth:** Required. Sub-tokens need `groups:view`.

### Example

```http
GET /v2/groups/mygroup/roles
Authorization: Bearer YOUR_TOKEN
```

**Response `200`:** an array of [role objects](README.md#role).

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

## POST `/v2/groups/{tag}/roles`

Create a role. New roles have no permissions or benefits; set them with the update endpoint.

**Auth:** Required. Sub-tokens need `groups:manage`. You need `groups.roles.manage`.

### Parameters

| Name | In | Type | Required | Description |
| --- | --- | --- | --- | --- |
| `name` | query | string | Yes | Up to 50 characters |
| `description` | query | string | No | Up to 200 characters |
| `assign_on_join` | query | string | No | `true` to give the role to new members automatically. Default `false` |
| `self_assignable` | query | string | No | `true` to let members give the role to themselves. Default `false` |

### Example

```http
POST /v2/groups/mygroup/roles?name=Moderator
Authorization: Bearer YOUR_TOKEN
```

**Response `201`:** the new [role object](README.md#role).

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

### Errors

| Status | When |
| --- | --- |
| `400` | `name` is missing (`Name is required`) or over 50 characters (`Name length exceeded`) |
| `400` | `description` is over 200 characters (`Description length exceeded`) |
| `403` | You lack `groups.roles.manage` (`You don't have permission to manage roles`) |

## PATCH `/v2/groups/{tag}/roles/{roleid}`

Update a role. Only the fields you send are changed. Lists you send replace the existing lists.

**Auth:** Required. Sub-tokens need `groups:manage`. You need `groups.roles.manage`.

### Parameters

| Name | In | Type | Required | Description |
| --- | --- | --- | --- | --- |
| `roleid` | path | string | Yes | The role's `id` |
| `name` | body | string | No | New name |
| `description` | body | string | No | New description |
| `assign_on_join` | body | boolean | No | Give the role to new members automatically |
| `self_assignable` | body | boolean | No | Let members give the role to themselves |
| `permissions` | body | string[] | No | New permission list |
| `benefits` | body | string[] | No | New benefit list |

### Example

```http
PATCH /v2/groups/mygroup/roles/role-3
Authorization: Bearer YOUR_TOKEN
Content-Type: application/json

{ "permissions": ["groups.announcements.send"] }
```

**Response `200`:**

```json
{
  "message": "Role updated"
}
```

### Errors

| Status | When |
| --- | --- |
| `400` | The body isn't valid JSON (`Invalid request body`) |
| `400` | `permissions` or `benefits` isn't an array of strings (`Invalid permissions`, `Invalid benefits`) |
| `403` | You lack `groups.roles.manage` (`You don't have permission to manage roles`) |
| `404` | No role has that ID in this group (`Role not found`) |

## DELETE `/v2/groups/{tag}/roles/{roleid}`

Delete a role. The Owner role can't be deleted.

**Auth:** Required. Sub-tokens need `groups:manage`. You need `groups.roles.manage`.

### Parameters

| Name | In | Type | Required | Description |
| --- | --- | --- | --- | --- |
| `roleid` | path | string | Yes | The role's `id` |

### Example

```http
DELETE /v2/groups/mygroup/roles/role-3
Authorization: Bearer YOUR_TOKEN
```

**Response `200`:**

```json
{
  "message": "Role deleted"
}
```

### Errors

| Status | When |
| --- | --- |
| `400` | The role is named `Owner` or `Everyone` (`Cannot delete default roles`) |
| `403` | You lack `groups.roles.manage` (`You don't have permission to manage roles`) |
| `404` | No role has that ID in this group (`Role not found`) |
