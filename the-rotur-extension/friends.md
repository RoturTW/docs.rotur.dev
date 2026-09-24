# Friends

The friend blocks read the logged-in user's friend list and incoming friend requests, and send, accept and decline requests. A friendship applies to both users: accepting a request adds each user to the other's list.

The extension loads the friend list and incoming requests when you log in, and refreshes them every 15 seconds. There's no block for the requests you have sent.

The `[FRIEND]` dropdowns list your friends or your incoming requests. Before you log in they show `Not Authenticated`, and when the list is empty they show `No Friends` or `No Requests`. You can also drop a reporter into them.

## Friend list

| Block | Type | Returns |
| --- | --- | --- |
| `(get friend list)` | Reporter | JSON array of your friends' usernames |
| `(get friend count)` | Reporter | How many friends you have |
| `(get friend status of [friend])` | Reporter | `Friend`, `Requested` or `Not Friend` (see below) |
| `(remove friend [FRIEND])` | Reporter | Removes the friendship for both users. Returns `Friend Removed`. |

Example `get friend list` result:

```json
["wow"]
```

`get friend status of` returns:

| Value | When |
| --- | --- |
| `Friend` | You're friends with the user |
| `Requested` | The user has sent you a friend request |
| `Not Friend` | Anything else |

## Send a request

```
(send friend request to [friend])
```

Reporter.

| Returns | When |
| --- | --- |
| `Sent Successfully` | The request was sent. If that user had already sent you a request, you become friends straight away. |
| `Already Friends` | You're already friends |
| `Already Requested` | You've already sent this user a request |
| `Account Does Not Exist` | No account has that username |
| `You Need Other Friends :/` | You sent a request to yourself |
| `You cant send friend requests to this user` | The user has blocked you |
| `Unblock this user before sending a friend request` | You have blocked the user |

## Incoming requests

| Block | Type | Returns |
| --- | --- | --- |
| `(get friend requests)` | Reporter | JSON array of usernames that sent you a request |
| `(accept friend request from [FRIEND])` | Reporter | Adds you to each other's friend lists. Returns `Request Accepted`. |
| `(decline friend request from [FRIEND])` | Reporter | Removes the request. Returns `Request Declined`. |

Example `get friend requests` result:

```json
["constellinux"]
```

## Events

These hats are driven by the 15-second refresh, so they can fire up to 15 seconds late.

| Block | Fires when |
| --- | --- |
| `when friend request received` | Your number of incoming requests goes up |
| `when friend request accepted` | Your number of incoming requests goes down between two refreshes, for example when a request is accepted or declined in another app, or the sender cancels it |

{% hint style="info" %}
`when friend request accepted` is about your incoming requests. It doesn't fire when someone accepts a request you sent, or when you accept or decline a request with the blocks on this page.
{% endhint %}
