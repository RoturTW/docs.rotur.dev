# Join Requests

Ask to join a group with the `REQUEST` join policy. Members with invite permission review and accept or decline requests.

## Request to Join

### POST `/v2/groups/{tag}/join-requests`

**Auth:** required. Token permission: `groups:join`.

The group must be public and have the `REQUEST` join policy. Members with invite permission are notified of your request.

**Query Parameters:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `message` | string | No | A message to the reviewers (max 200 chars) |

**Example request:**

```bash
curl -X POST "https://api.rotur.dev/v2/groups/mygroup/join-requests?auth=YOUR_TOKEN&message=Hi%20there"
```

**Example response (201):**

```json
{
  "id": "req-1",
  "group_tag": "mygroup",
  "user_id": "your-id",
  "username": "bob",
  "message": "Hi there",
  "status": "PENDING",
  "created_at": 1717000000
}
```

**Common errors:**

| Status | Error | Cause |
|--------|-------|-------|
| 400 | `This group does not require join requests` | Join policy isn't `REQUEST` |
| 400 | `You are already a member of this group` | Already joined |
| 400 | `You already have a pending join request` | Duplicate request |
| 400 | `You already have a pending invite to this group` | Accept the invite instead |
| 403 | `Group is private` | Group is not public |
| 403 | `You are banned from this group` | You're banned |
| 404 | `Group not found` | Group doesn't exist |

***

## List Join Requests

### GET `/v2/groups/{tag}/join-requests`

**Auth:** required. Token permission: `groups:invite`. You must be the group owner or hold the `groups.members.invite` group permission.

Returns the group's pending join requests.

**Example request:**

```bash
curl "https://api.rotur.dev/v2/groups/mygroup/join-requests?auth=YOUR_TOKEN"
```

**Example response (200):** an array of join request objects like the one above.

**Common errors:**

| Status | Error | Cause |
|--------|-------|-------|
| 403 | `You don't have permission to view join requests` | Missing `groups.members.invite` |
| 404 | `Group not found` | Group doesn't exist |

***

## Accept a Join Request

### POST `/v2/groups/{tag}/join-requests/{requestid}/accept`

**Auth:** required. Token permission: `groups:invite`. You must be the group owner or hold the `groups.members.invite` group permission.

The requester joins the group with the `assign_on_join` roles. If the group has an entry fee, it's charged to the requester at this point. They get a `group_request_accepted` event and a push notification.

**Example request:**

```bash
curl -X POST "https://api.rotur.dev/v2/groups/mygroup/join-requests/req-1/accept?auth=YOUR_TOKEN"
```

**Example response (200):**

```json
{
  "message": "Join request accepted",
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
| 400 | `User is already a member of this group` | Requester joined some other way |
| 400 | `User has insufficient funds to join this group` | Requester can't afford the entry fee |
| 403 | `You don't have permission to accept join requests` | Missing `groups.members.invite` |
| 403 | `User is banned from this group` | Requester was banned after requesting |
| 404 | `Join request not found or already handled` | Wrong ID or not pending |
| 404 | `Group not found` | Group doesn't exist |

***

## Decline a Join Request

### POST `/v2/groups/{tag}/join-requests/{requestid}/decline`

**Auth:** required. Token permission: `groups:invite`. You must be the group owner or hold the `groups.members.invite` group permission.

The requester gets a `group_request_declined` event and a push notification.

**Example request:**

```bash
curl -X POST "https://api.rotur.dev/v2/groups/mygroup/join-requests/req-1/decline?auth=YOUR_TOKEN"
```

**Example response (200):**

```json
{
  "message": "Join request declined"
}
```

**Common errors:**

| Status | Error | Cause |
|--------|-------|-------|
| 403 | `You don't have permission to decline join requests` | Missing `groups.members.invite` |
| 404 | `Join request not found or already handled` | Wrong ID or not pending |
| 404 | `Group not found` | Group doesn't exist |
