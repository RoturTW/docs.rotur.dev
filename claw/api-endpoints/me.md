# GET `/me`

Returns the full account object for your account: username, credits, subscription, badges, settings and other account data. `/get_user` and `/get_user_new` are the same endpoint.

**Auth:** Required. Send a token, or sign in with a username and password instead. Uses the profile rate limit.

What you get back depends on the token:

- **Your main account token** (or a password sign-in) gets the full account object.
- **A sub-token** only gets the parts of the account its permissions cover. With `account:view` it gets the account object less anything it may not read. Without `account:view` it gets the [public profile](profile.md) plus whatever its other permissions cover. Credits need `credits:view`, friends need `friends:view`, the email address needs `account:email`, and sign-in history needs `account:signins`. The `key` field holds the sub-token you sent, not the account token.
- **An OAuth access token** from "Sign in with Rotur" gets only the public profile, plus `email` if the person approved the `email` scope.

The email address and sign-in history are never released to apps for accounts under 18, or accounts whose age Rotur does not know. The request still succeeds, without those fields.

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
| `403` | `This account is suspended until <date>, when you reach the minimum age for your country.` (with `code: age_restricted`) |
| `403` | `This is a sub account. Open it from the account that owns it.` (password sign-in to a sub account) |
| `403` | `Unable to login to this account` (your IP is blocked on this account) |
| `403` | `Tor is not allowed` |
