# GET `/me`

Returns the full account object for your account: username, credits, subscription, badges, settings and other account data. `/get_user` and `/get_user_new` are the same endpoint.

**Auth:** Required. Send a token, or sign in with a username and password instead. A sub-token without `account:view` gets a reduced, profile-only object. Uses the profile rate limit.

### Parameters

| Name | In | Type | Required | Description |
| --- | --- | --- | --- | --- |
| `username` | query | string | No* | Your username, for password sign-in |
| `password` | query | string | No* | Your password, for password sign-in |

*Only needed when you do not send a token. A JSON body with `username` and `password` also works.

### Example

```http
GET /me
Authorization: Bearer <token>
```

**Response `200`:** your account object.

### Errors

| Status | When |
| --- | --- |
| `403` | `Invalid authentication credentials` (no valid token, or wrong username or password) |
| `403` | `User is banned` |
| `403` | `Email address not verified` |
| `403` | `Terms-Of-Service are not accepted or outdated` |
| `403` | `Unable to login to this account` (your IP is blocked on this account) |
| `403` | `Tor is not allowed` |
