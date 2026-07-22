# Bans

Ban users from a group. Banned users are removed from the group and can't rejoin, be invited, or request to join until unbanned.

{% hint style="warning" %}
The `{userid}` path segment on ban and unban must be the user's ID, not their username.
{% endhint %}

## Ban a User

### PUT `/v2/groups/{tag}/members/{userid}/ban`

**Auth:** required. Token permission: `groups:manage`. You must be the group owner or hold the `groups.members.ban` group permission.

Banning removes the user's membership, pending invites, and pending join requests. The user gets a `group_banned` event and a push notification.

**Query Parameters:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `reason` | string | No | Ban reason (max 200 chars) |

**Example request:**

```bash
curl -X PUT "https://api.rotur.dev/v2/groups/mygroup/members/USER_ID/ban?auth=YOUR_TOKEN&reason=spam"
```

**Example response (200):**

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

`message` is `"banned"` if the user wasn't a member, or `"banned and removed from group"` if they were.

**Common errors:**

| Status | Error | Cause |
|--------|-------|-------|
| 400 | `Cannot ban the group owner` | Target owns the group |
| 400 | `User is already banned from this group` | Duplicate ban |
| 403 | `You don't have permission to ban members` | Missing `groups.members.ban` |
| 404 | `Group not found` | Group doesn't exist |

***

## Unban a User

### DELETE `/v2/groups/{tag}/members/{userid}/ban`

**Auth:** required. Token permission: `groups:manage`. You must be the group owner or hold the `groups.members.ban` group permission.

**Example request:**

```bash
curl -X DELETE "https://api.rotur.dev/v2/groups/mygroup/members/USER_ID/ban?auth=YOUR_TOKEN"
```

**Example response (200):**

```json
{
  "message": "User unbanned"
}
```

**Common errors:**

| Status | Error | Cause |
|--------|-------|-------|
| 403 | `You don't have permission to unban members` | Missing `groups.members.ban` |
| 404 | `User is not banned from this group` | No matching ban |
| 404 | `Group not found` | Group doesn't exist |

***

## List Bans

### GET `/v2/groups/{tag}/bans`

**Auth:** required. Token permission: `groups:manage`. You must be the group owner or hold the `groups.members.ban` group permission.

**Example request:**

```bash
curl "https://api.rotur.dev/v2/groups/mygroup/bans?auth=YOUR_TOKEN"
```

**Example response (200):**

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

**Common errors:**

| Status | Error | Cause |
|--------|-------|-------|
| 403 | `You don't have permission to view bans` | Missing `groups.members.ban` |
| 404 | `Group not found` | Group doesn't exist |

***

## Check a Ban

### GET `/v2/groups/{tag}/bans/{userid}`

**Auth:** required. Token permission: `groups:members.view`.

Checks whether a user ID is banned from the group.

**Example request:**

```bash
curl "https://api.rotur.dev/v2/groups/mygroup/bans/USER_ID?auth=YOUR_TOKEN"
```

**Example response (200):**

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

If the user isn't banned, the response is `{"banned": false}`.

**Common errors:**

| Status | Error | Cause |
|--------|-------|-------|
| 404 | `Group not found` | Group doesn't exist |
