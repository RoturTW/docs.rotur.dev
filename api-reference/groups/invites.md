# Invites

Invite users to a group. Invites work for any group, public or private, and are the only way to join a private group or an `INVITE` group.

## GET `/v2/groups/invites/mine`

List your pending invites across all groups.

**Auth:** Required. Sub-tokens need `groups:view`.

### Example

```http
GET /v2/groups/invites/mine
Authorization: Bearer YOUR_TOKEN
```

**Response `200`:** an array of invites, or `[]` if you have none.

```json
[
  {
    "id": "inv-1",
    "group_tag": "mygroup",
    "from_user_id": "sender-id",
    "from_username": "alice",
    "to_user_id": "your-id",
    "to_username": "bob",
    "status": "PENDING",
    "created_at": 1717000000
  }
]
```

## GET `/v2/groups/{tag}/invites`

List a group's pending invites.

**Auth:** Required. Sub-tokens need `groups:invite`. You must be the owner or hold `groups.members.invite`.

### Example

```http
GET /v2/groups/mygroup/invites
Authorization: Bearer YOUR_TOKEN
```

**Response `200`:** an array of invites in the same shape as above, or `[]` if there are none.

### Errors

| Status | When |
| --- | --- |
| `403` | You lack `groups.members.invite` (`You don't have permission to view invites`) |

## POST `/v2/groups/{tag}/invites`

Invite a user. They get a `group_invite` event (with `group_tag`, `group_name`, `from`, and `invite_id`) and a push notification.

**Auth:** Required. Sub-tokens need `groups:invite`. You must be the owner or hold `groups.members.invite`.

### Parameters

| Name | In | Type | Required | Description |
| --- | --- | --- | --- | --- |
| `username` | query | string | Yes | The username to invite |

### Example

```http
POST /v2/groups/mygroup/invites?username=bob
Authorization: Bearer YOUR_TOKEN
```

**Response `201`:** the new invite, in the same shape as above, with `status` `PENDING`.

### Errors

| Status | When |
| --- | --- |
| `400` | `username` is missing (`Username is required`) |
| `400` | You invited yourself (`You cannot invite yourself`) |
| `400` | The user is already a member (`User is already a member of this group`) |
| `400` | The user already has a pending invite (`User already has a pending invite`) |
| `400` | The user is banned (`User is banned from this group`) |
| `403` | You lack `groups.members.invite` (`You don't have permission to invite members`) |
| `404` | No account has that username (`User not found`) |

## POST `/v2/groups/{tag}/invites/{inviteid}/accept`

Accept an invite sent to you and join the group. Any entry fee is charged now. You get the roles marked `assign_on_join`, or the Member role if none are.

**Auth:** Required. Sub-tokens need `groups:join`.

### Parameters

| Name | In | Type | Required | Description |
| --- | --- | --- | --- | --- |
| `inviteid` | path | string | Yes | The invite's `id` |

### Example

```http
POST /v2/groups/mygroup/invites/inv-1/accept
Authorization: Bearer YOUR_TOKEN
```

**Response `200`:** `group` is the full updated [group object](README.md#group), shortened here.

```json
{
  "message": "Invite accepted",
  "group": {
    "id": "550e8400-e29b-41d4-a716-446655440000",
    "tag": "mygroup",
    "name": "My Group",
    "member_count": 6
  }
}
```

### Errors

| Status | When |
| --- | --- |
| `400` | You're already a member (`You are already a member of this group`). The invite is marked accepted |
| `400` | You can't afford the entry fee (`Insufficient funds to join this group`). The body also has `required` and `available` |
| `403` | You're banned (`You are banned from this group`) |
| `404` | The invite doesn't exist, isn't addressed to you, or isn't pending (`Invite not found or already handled`) |

## POST `/v2/groups/{tag}/invites/{inviteid}/decline`

Decline an invite sent to you.

**Auth:** Required. Sub-tokens need `groups:join`.

### Parameters

| Name | In | Type | Required | Description |
| --- | --- | --- | --- | --- |
| `inviteid` | path | string | Yes | The invite's `id` |

### Example

```http
POST /v2/groups/mygroup/invites/inv-1/decline
Authorization: Bearer YOUR_TOKEN
```

**Response `200`:**

```json
{
  "message": "Invite declined"
}
```

### Errors

| Status | When |
| --- | --- |
| `404` | The invite doesn't exist, isn't addressed to you, or isn't pending (`Invite not found or already handled`) |

## DELETE `/v2/groups/{tag}/invites/{inviteid}`

Revoke a pending invite. It's deleted rather than marked.

**Auth:** Required. Sub-tokens need `groups:invite`. You must be the owner or hold `groups.members.invite`.

### Parameters

| Name | In | Type | Required | Description |
| --- | --- | --- | --- | --- |
| `inviteid` | path | string | Yes | The invite's `id` |

### Example

```http
DELETE /v2/groups/mygroup/invites/inv-1
Authorization: Bearer YOUR_TOKEN
```

**Response `200`:**

```json
{
  "message": "Invite revoked"
}
```

### Errors

| Status | When |
| --- | --- |
| `403` | You lack `groups.members.invite` (`You don't have permission to revoke invites`) |
| `404` | The invite doesn't exist or isn't pending (`Invite not found or already handled`) |
