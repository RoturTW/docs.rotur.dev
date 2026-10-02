# Bans

Ban users from a group. A banned user is removed from the group and can't join, accept an invite, or send a join request until they're unbanned.

{% hint style="warning" %}
On these endpoints `{userid}` must be a user ID, not a username. The ID isn't checked against existing accounts, so a username is stored as a ban on a user ID that doesn't exist.
{% endhint %}

## PUT `/v2/groups/{tag}/members/{userid}/ban`

Ban a user. This also removes their membership, pending invites, and pending join requests. The user gets a `group_banned` event and a push notification.

**Auth:** Required. Sub-tokens need `groups:manage`. You must be the owner or hold `groups.members.ban`.

### Parameters

| Name | In | Type | Required | Description |
| --- | --- | --- | --- | --- |
| `userid` | path | string | Yes | The user's ID. They don't have to be a member |
| `reason` | query | string | No | Up to 200 characters |

### Example

```http
PUT /v2/groups/mygroup/members/USER_ID/ban?reason=spam
Authorization: Bearer YOUR_TOKEN
```

**Response `200`:** `message` is `banned and removed from group` if the user was a member, or `banned` if not.

```json
{
  "message": "banned and removed from group",
  "ban": {
    "id": "ban-1",
    "group_tag": "mygroup",
    "user_id": "user-id-here",
    "username": "bob",
    "banned_by_id": "owner-id-here",
    "banned_by": "alice",
    "reason": "spam",
    "created_at": 1717000000
  }
}
```

### Errors

| Status | When |
| --- | --- |
| `400` | `reason` is over 200 characters (`Reason length exceeded (max 200)`) |
| `400` | The user owns the group (`Cannot ban the group owner`) |
| `400` | The user is already banned (`User is already banned from this group`) |
| `403` | You lack `groups.members.ban` (`You don't have permission to ban members`) |

## DELETE `/v2/groups/{tag}/members/{userid}/ban`

Unban a user. They aren't added back to the group.

**Auth:** Required. Sub-tokens need `groups:manage`. You must be the owner or hold `groups.members.ban`.

### Parameters

| Name | In | Type | Required | Description |
| --- | --- | --- | --- | --- |
| `userid` | path | string | Yes | The user's ID |

### Example

```http
DELETE /v2/groups/mygroup/members/USER_ID/ban
Authorization: Bearer YOUR_TOKEN
```

**Response `200`:**

```json
{
  "message": "User unbanned"
}
```

### Errors

| Status | When |
| --- | --- |
| `403` | You lack `groups.members.ban` (`You don't have permission to unban members`) |
| `404` | The user isn't banned (`User is not banned from this group`) |

## GET `/v2/groups/{tag}/bans`

List every ban in the group.

**Auth:** Required. Sub-tokens need `groups:manage`. You must be the owner or hold `groups.members.ban`.

### Example

```http
GET /v2/groups/mygroup/bans
Authorization: Bearer YOUR_TOKEN
```

**Response `200`:** an array of bans, in the same shape as `ban` above, or `[]` if there are none.

```json
[
  {
    "id": "ban-1",
    "group_tag": "mygroup",
    "user_id": "user-id-here",
    "username": "bob",
    "banned_by_id": "owner-id-here",
    "banned_by": "alice",
    "reason": "spam",
    "created_at": 1717000000
  }
]
```

### Errors

| Status | When |
| --- | --- |
| `403` | You lack `groups.members.ban` (`You don't have permission to view bans`) |

## GET `/v2/groups/{tag}/bans/{userid}`

Check whether a user is banned from the group. No group permission is needed.

**Auth:** Required. Sub-tokens need `groups:members.view`.

### Parameters

| Name | In | Type | Required | Description |
| --- | --- | --- | --- | --- |
| `userid` | path | string | Yes | The user's ID |

### Example

```http
GET /v2/groups/mygroup/bans/USER_ID
Authorization: Bearer YOUR_TOKEN
```

**Response `200`:** if the user isn't banned, the body is `{"banned": false}`.

```json
{
  "banned": true,
  "ban": {
    "id": "ban-1",
    "group_tag": "mygroup",
    "user_id": "user-id-here",
    "username": "bob",
    "banned_by_id": "owner-id-here",
    "banned_by": "alice",
    "reason": "spam",
    "created_at": 1717000000
  }
}
```
