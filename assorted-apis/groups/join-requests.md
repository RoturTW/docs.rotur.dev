# Join requests

Ask to join a public group whose join policy is `REQUEST`. The owner and members with `groups.members.invite` review requests and accept or decline them.

## POST `/v2/groups/{tag}/join-requests`

Send a join request. The owner and every member with `groups.members.invite` get a push notification.

**Auth:** Required. Sub-tokens need `groups:join`.

### Parameters

| Name | In | Type | Required | Description |
| --- | --- | --- | --- | --- |
| `message` | query | string | No | A note to the reviewers, up to 200 characters |

### Example

```http
POST /v2/groups/mygroup/join-requests?message=Hi%20there
Authorization: Bearer YOUR_TOKEN
```

**Response `201`:**

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

### Errors

| Status | When |
| --- | --- |
| `400` | The join policy isn't `REQUEST` (`This group does not require join requests`) |
| `400` | You're already a member (`You are already a member of this group`) |
| `400` | You already have a pending request (`You already have a pending join request`) |
| `400` | You have a pending invite; accept it instead (`You already have a pending invite to this group`) |
| `400` | `message` is over 200 characters (`Message length exceeded (max 200)`) |
| `403` | The group is private (`Group is private`) |
| `403` | You're banned (`You are banned from this group`) |

## GET `/v2/groups/{tag}/join-requests`

List the group's pending join requests.

**Auth:** Required. Sub-tokens need `groups:invite`. You must be the owner or hold `groups.members.invite`.

### Example

```http
GET /v2/groups/mygroup/join-requests
Authorization: Bearer YOUR_TOKEN
```

**Response `200`:** an array of join requests in the same shape as above, or `[]` if there are none.

### Errors

| Status | When |
| --- | --- |
| `403` | You lack `groups.members.invite` (`You don't have permission to view join requests`) |

## POST `/v2/groups/{tag}/join-requests/{requestid}/accept`

Accept a join request. The requester joins with the roles marked `assign_on_join` (or the Member role), and any entry fee is charged to them now. They get a `group_request_accepted` event and a push notification.

**Auth:** Required. Sub-tokens need `groups:invite`. You must be the owner or hold `groups.members.invite`.

### Parameters

| Name | In | Type | Required | Description |
| --- | --- | --- | --- | --- |
| `requestid` | path | string | Yes | The join request's `id` |

### Example

```http
POST /v2/groups/mygroup/join-requests/req-1/accept
Authorization: Bearer YOUR_TOKEN
```

**Response `200`:** `group` is the full updated [group object](README.md#group), shortened here.

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

### Errors

| Status | When |
| --- | --- |
| `400` | The requester is already a member (`User is already a member of this group`). The request is marked accepted |
| `400` | The requester can't afford the entry fee (`User has insufficient funds to join this group`). The body also has `required` and `available` |
| `403` | You lack `groups.members.invite` (`You don't have permission to accept join requests`) |
| `403` | The requester has been banned since asking (`User is banned from this group`) |
| `404` | The request doesn't exist or isn't pending (`Join request not found or already handled`) |

## POST `/v2/groups/{tag}/join-requests/{requestid}/decline`

Decline a join request. The requester gets a `group_request_declined` event and a push notification.

**Auth:** Required. Sub-tokens need `groups:invite`. You must be the owner or hold `groups.members.invite`.

### Parameters

| Name | In | Type | Required | Description |
| --- | --- | --- | --- | --- |
| `requestid` | path | string | Yes | The join request's `id` |

### Example

```http
POST /v2/groups/mygroup/join-requests/req-1/decline
Authorization: Bearer YOUR_TOKEN
```

**Response `200`:**

```json
{
  "message": "Join request declined"
}
```

### Errors

| Status | When |
| --- | --- |
| `403` | You lack `groups.members.invite` (`You don't have permission to decline join requests`) |
| `404` | The request doesn't exist or isn't pending (`Join request not found or already handled`) |
