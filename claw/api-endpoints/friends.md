# Friends

Send, accept, reject and cancel friend requests, remove friends, and list your friends and pending requests. Friendship is mutual: both users appear in each other's friend lists.

> **Auth:** Every endpoint requires a token. Sub-tokens need the permission listed on each endpoint.

Usernames in paths are case-insensitive. Endpoints that take a username return `404` with `Account Does Not Exist` if there is no such account.

## GET `/friends`

Lists the usernames you are friends with.

**Auth:** Required. Sub-tokens need `friends:view`.

### Example

```http
GET /friends
Authorization: Bearer <token>
```

**Response `200`:**

```json
{
  "friends": ["mist", "rm", "temp"]
}
```

## GET `/friends/requests`

Lists the usernames that have sent you a friend request.

**Auth:** Required. Sub-tokens need `friends:view`.

### Example

```http
GET /friends/requests
Authorization: Bearer <token>
```

**Response `200`:**

```json
{
  "requests": ["rm"]
}
```

## GET `/friends/requests_out`

Lists the usernames you have sent a friend request to.

**Auth:** Required. Sub-tokens need `friends:view`.

### Example

```http
GET /friends/requests_out
Authorization: Bearer <token>
```

**Response `200`:**

```json
{
  "requests_out": ["temp"]
}
```

## POST `/friends/request/:username`

Sends a friend request. If that user has already sent you one, you become friends straight away and the message is `Friend request accepted automatically`.

**Auth:** Required. Sub-tokens need `friends:request`. Your account needs `good` standing.

### Parameters

| Name | In | Type | Required | Description |
| --- | --- | --- | --- | --- |
| `username` | path | string | Yes | User to send the request to |

### Example

```http
POST /friends/request/mist
Authorization: Bearer <token>
```

**Response `200`:**

```json
{
  "message": "Friend request sent successfully"
}
```

### Errors

| Status | When |
| --- | --- |
| `400` | `You need other friends` (you tried to friend yourself) |
| `400` | `Already Friends` |
| `400` | `Already Requested` |
| `400` | `You cant send friend requests to this user` (they have blocked you) |
| `400` | `Unblock this user before sending a friend request` |
| `404` | `Account Does Not Exist` |

## POST `/friends/accept/:username`

Accepts a pending friend request.

**Auth:** Required. Sub-tokens need `friends:accept`.

### Parameters

| Name | In | Type | Required | Description |
| --- | --- | --- | --- | --- |
| `username` | path | string | Yes | User whose request you are accepting |

### Example

```http
POST /friends/accept/rm
Authorization: Bearer <token>
```

**Response `200`:**

```json
{
  "message": "Friend request accepted"
}
```

### Errors

| Status | When |
| --- | --- |
| `400` | `Invalid Operation` (the username is your own) |
| `400` | `No Pending Request` |
| `404` | `Account Does Not Exist` |

## POST `/friends/reject/:username`

Declines a pending friend request.

**Auth:** Required. Sub-tokens need `friends:accept`.

### Parameters

| Name | In | Type | Required | Description |
| --- | --- | --- | --- | --- |
| `username` | path | string | Yes | User whose request you are declining |

### Example

```http
POST /friends/reject/rm
Authorization: Bearer <token>
```

**Response `200`:**

```json
{
  "message": "Friend request rejected"
}
```

### Errors

| Status | When |
| --- | --- |
| `400` | `No Pending Request` |

## POST `/friends/cancel/:username`

Cancels a friend request you sent.

**Auth:** Required. Sub-tokens need `friends:cancel`. Your account needs `good` standing.

### Parameters

| Name | In | Type | Required | Description |
| --- | --- | --- | --- | --- |
| `username` | path | string | Yes | User you sent the request to |

### Example

```http
POST /friends/cancel/temp
Authorization: Bearer <token>
```

**Response `200`:**

```json
{
  "message": "Friend request cancelled"
}
```

### Errors

| Status | When |
| --- | --- |
| `400` | `Invalid Operation` (the username is your own) |
| `400` | `No Pending Request` |
| `404` | `Account Does Not Exist` |

## POST `/friends/remove/:username`

Removes a friend from both friend lists and clears any pending requests between you.

**Auth:** Required. Sub-tokens need `friends:remove`.

### Parameters

| Name | In | Type | Required | Description |
| --- | --- | --- | --- | --- |
| `username` | path | string | Yes | Friend to remove |

### Example

```http
POST /friends/remove/rm
Authorization: Bearer <token>
```

**Response `200`:**

```json
{
  "message": "Friend removed"
}
```

### Errors

| Status | When |
| --- | --- |
| `400` | `Cannot Remove Yourself` |
| `400` | `Not Friends` |
| `404` | `Account Does Not Exist` |
