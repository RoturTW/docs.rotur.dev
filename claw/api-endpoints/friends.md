# Friends

The friends API provides full friend management — sending, accepting, rejecting, and removing friends, as well as listing your current friends.

All endpoints require authentication.

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

## Send Friend Request

### POST `/friends/request/:username`

Sends a friend request to the specified user. If they have already sent you a request, the friendship is accepted automatically.

**Path Parameters:**

| Parameter | Description |
| --- | --- |
| `username` | The Rotur username to send a request to |

**Response (200):**

```json
{
  "message": "Friend request sent successfully"
}
```

**Error Responses:**

| Message | Condition |
| --- | --- |
| `Already Friends` | You are already friends with this user |
| `Already Requested` | You have already sent a request to this user |
| `Account Does Not Exist` | The target user does not exist |
| `You need other friends` | You tried to friend yourself |

Requires `good` account standing.

## Accept Friend Request

### POST `/friends/accept/:username`

Accepts a pending friend request from the specified user. This adds both users to each other's friends list.

**Path Parameters:**

| Parameter | Description |
| --- | --- |
| `username` | The username who sent the original request |

**Response (200):**

```json
{
  "message": "Friend request accepted"
}
```

## Reject Friend Request

### POST `/friends/reject/:username`

Declines a pending friend request.

**Path Parameters:**

| Parameter | Description |
| --- | --- |
| `username` | The username whose request to reject |

**Response (200):**

```json
{
  "message": "Friend request rejected"
}
```

## Remove Friend

### POST `/friends/remove/:username`

Removes a user from your friends list and removes you from theirs. Also cleans up any pending requests between both users.

**Path Parameters:**

| Parameter | Description |
| --- | --- |
| `username` | The Rotur username to unfriend |

**Response (200):**

```json
{
  "message": "Friend removed"
}
```
