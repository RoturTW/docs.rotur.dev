# Friends

The friends API lets you send, accept, reject, and cancel friend requests, remove friends, and list your friends and pending requests.

All endpoints require authentication. Sub-tokens need the matching `friends:*` permission (`friends:view`, `friends:request`, `friends:accept`, `friends:cancel`, `friends:remove`).

**Base URL:** `https://api.rotur.dev`

## List Friends

### GET `/friends`

Returns the list of usernames you are friends with.

**Response (200):**

```json
{
  "friends": ["mist", "rm", "temp"]
}
```

## Incoming Requests

### GET `/friends/requests`

Returns the usernames who have sent you a friend request.

**Response (200):**

```json
{
  "requests": ["rm"]
}
```

## Outgoing Requests

### GET `/friends/requests_out`

Returns the usernames you have sent a friend request to.

**Response (200):**

```json
{
  "requests_out": ["temp"]
}
```

## Send Friend Request

### POST `/friends/request/:username`

Sends a friend request. If they have already sent you a request, the friendship is accepted automatically and you get `Friend request accepted automatically`.

Requires `good` account standing.

**Response (200):**

```json
{
  "message": "Friend request sent successfully"
}
```

**Error responses (400 unless noted):**

| Message | Condition |
| --- | --- |
| `Already Friends` | You are already friends with this user |
| `Already Requested` | You have already sent a request to this user |
| `Account Does Not Exist` (404) | The target user does not exist |
| `You need other friends` | You tried to friend yourself |
| `You cant send friend requests to this user` | The target has blocked you |
| `Unblock this user before sending a friend request` | You have blocked the target |

## Accept Friend Request

### POST `/friends/accept/:username`

Accepts a pending friend request. Both users are added to each other's friends list.

**Response (200):**

```json
{
  "message": "Friend request accepted"
}
```

Returns `400` with `No Pending Request` if that user has not sent you a request.

## Reject Friend Request

### POST `/friends/reject/:username`

Declines a pending friend request.

**Response (200):**

```json
{
  "message": "Friend request rejected"
}
```

Returns `400` with `No Pending Request` if there is nothing to reject.

## Cancel Friend Request

### POST `/friends/cancel/:username`

Cancels a friend request you sent. Requires `good` account standing.

**Response (200):**

```json
{
  "message": "Friend request cancelled"
}
```

Returns `400` with `No Pending Request` if you have no outgoing request to that user.

## Remove Friend

### POST `/friends/remove/:username`

Removes a user from your friends list and removes you from theirs. Also cleans up any pending requests between you.

**Response (200):**

```json
{
  "message": "Friend removed"
}
```

Returns `400` with `Not Friends` if you were not friends.
