# Invites

Invite users to a group. Invites are required to join groups with the `INVITE` join policy, but work for any group.

## List My Invites

### GET `/v2/groups/invites/mine`

**Auth:** required. Token permission: `groups:view`.

Returns your pending invites across all groups.

**Example request:**

```bash
curl "https://api.rotur.dev/v2/groups/invites/mine?auth=YOUR_TOKEN"
```

**Example response (200):**

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

***

## List a Group's Invites

### GET `/v2/groups/{tag}/invites`

**Auth:** required. Token permission: `groups:invite`. You must be the group owner or hold the `groups.members.invite` group permission.

Returns the group's pending invites.

**Example request:**

```bash
curl "https://api.rotur.dev/v2/groups/mygroup/invites?auth=YOUR_TOKEN"
```

**Example response (200):** same invite objects as above, in an array.

**Common errors:**

| Status | Error | Cause |
|--------|-------|-------|
| 403 | `You don't have permission to view invites` | Missing `groups.members.invite` |
| 404 | `Group not found` | Group doesn't exist |

***

## Send an Invite

### POST `/v2/groups/{tag}/invites`

**Auth:** required. Token permission: `groups:invite`. You must be the group owner or hold the `groups.members.invite` group permission.

The invited user gets a `group_invite` event and a push notification.

**Query Parameters:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `username` | string | Yes | Username of the user to invite |

**Example request:**

```bash
curl -X POST "https://api.rotur.dev/v2/groups/mygroup/invites?auth=YOUR_TOKEN&username=bob"
```

**Example response (201):** the created invite object with `status: "PENDING"`.

**Common errors:**

| Status | Error | Cause |
|--------|-------|-------|
| 400 | `You cannot invite yourself` | Self-invite |
| 400 | `User is already a member of this group` | Already joined |
| 400 | `User already has a pending invite` | Duplicate invite |
| 400 | `User is banned from this group` | Target is banned |
| 403 | `You don't have permission to invite members` | Missing `groups.members.invite` |
| 404 | `User not found` | Username doesn't exist |
| 404 | `Group not found` | Group doesn't exist |

***

## Accept an Invite

### POST `/v2/groups/{tag}/invites/{inviteid}/accept`

**Auth:** required. Token permission: `groups:join`.

Accepts an invite addressed to you and joins the group. If the group has an entry fee, it's charged when you accept. You get the roles marked `assign_on_join`.

**Example request:**

```bash
curl -X POST "https://api.rotur.dev/v2/groups/mygroup/invites/inv-1/accept?auth=YOUR_TOKEN"
```

**Example response (200):**

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

The `group` field is the full updated group object (shortened here).

**Common errors:**

| Status | Error | Cause |
|--------|-------|-------|
| 400 | `You are already a member of this group` | Already joined |
| 400 | `Insufficient funds to join this group` | Can't afford the entry fee (response includes `required` and `available`) |
| 403 | `You are banned from this group` | You're banned |
| 404 | `Invite not found or already handled` | Wrong ID, not yours, or not pending |
| 404 | `Group not found` | Group doesn't exist |

***

## Decline an Invite

### POST `/v2/groups/{tag}/invites/{inviteid}/decline`

**Auth:** required. Token permission: `groups:join`.

**Example request:**

```bash
curl -X POST "https://api.rotur.dev/v2/groups/mygroup/invites/inv-1/decline?auth=YOUR_TOKEN"
```

**Example response (200):**

```json
{
  "message": "Invite declined"
}
```

**Common errors:**

| Status | Error | Cause |
|--------|-------|-------|
| 404 | `Invite not found or already handled` | Wrong ID, not yours, or not pending |
| 404 | `Group not found` | Group doesn't exist |

***

## Revoke an Invite

### DELETE `/v2/groups/{tag}/invites/{inviteid}`

**Auth:** required. Token permission: `groups:invite`. You must be the group owner or hold the `groups.members.invite` group permission.

Removes a pending invite.

**Example request:**

```bash
curl -X DELETE "https://api.rotur.dev/v2/groups/mygroup/invites/inv-1?auth=YOUR_TOKEN"
```

**Example response (200):**

```json
{
  "message": "Invite revoked"
}
```

**Common errors:**

| Status | Error | Cause |
|--------|-------|-------|
| 403 | `You don't have permission to revoke invites` | Missing `groups.members.invite` |
| 404 | `Invite not found or already handled` | Wrong ID or not pending |
| 404 | `Group not found` | Group doesn't exist |
