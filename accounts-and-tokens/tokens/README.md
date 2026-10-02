# Tokens

Sub-tokens give an app limited access to your Rotur account. Instead of sharing your main account token, which can do everything, you create a sub-token that holds only the permissions the app needs.

{% hint style="info" %}
These are Rotur account tokens, for acting on a user's account. If you only want people to sign in to your app with their Rotur account, use [Sign in with Rotur](../../build-an-app/quickstart.md) instead.
{% endhint %}

> **Base URL:** `https://api.rotur.dev`
>
> **Auth:** Send your token in the `Authorization: Bearer <token>` header. The legacy `auth` query parameter and the rotur.dev session cookie are also accepted. Managing sub-tokens needs the main account token; see each endpoint's **Auth** line.

Every endpoint is also available under `/v2/tokens`. The paths are the same except for creating a token, which is `POST /v2/tokens` instead of `POST /tokens/create`.

## Concepts

### Main token and sub-tokens

| | Main token | Sub-token |
| --- | --- | --- |
| **Format** | URL-safe Base64 of 64 random bytes | `rotur_st_` followed by URL-safe Base64 of 32 random bytes |
| **Scope** | Full access to the account | Only the permissions you assign |
| **Used by** | You, the account owner | Apps and services you authorize |
| **Can manage sub-tokens** | Yes | No |
| **How to invalidate it** | Refresh it with `POST /me/refresh_token` | Revoke or delete it through this API |

Each sub-token also has an ID of the form `st_…`. You use the ID, not the token value, in the paths below.

### OAuth access tokens

When someone signs in to an app with [Sign in with Rotur](../../build-an-app/quickstart.md), the access token the app gets is stored as a sub-token on their account. It shows up in [List tokens](list-tokens.md) like any other, with these values:

| Field | Value |
| --- | --- |
| `name` | The app's name |
| `origin` | `oauth:<client_id>` |
| `description` | `OAuth access for <app name>` |
| `websites` | The app's redirect URIs |
| `permissions` | `[]`, or `["account:email"]` if the `email` scope was granted |
| `expires_at` | One hour after it was issued |

An OAuth access token can read the user's public profile (and email, if granted) through `GET /me` and `GET /oauth/userinfo`. Of the endpoints that need a token, it can only call [Get a token](get-token.md) with its own ID, `GET /me/able` and `GET /v2/me/abilities`. Every other one turns it away with `OAuth access tokens can only read the public profile`. You can rename, revoke or delete it like any sub-token. Editing its permissions has no effect, because it always holds only what its scopes give.

### Lifecycle

1. **Active:** the sub-token works on every endpoint its permissions allow.
2. **Expired:** if you set an expiry, the sub-token stops working once it passes. The server checks every hour and marks expired sub-tokens revoked.
3. **Revoked:** you revoked it, or it expired. A revoked sub-token cannot be restored; create a new one instead. Revoked sub-tokens stay on your account as history until you delete them.
4. **Deleted:** the record is removed from your account entirely.

Sub-tokens nobody has used for 30 days are deleted automatically, so forgotten apps lose access. A sub-token that has never been used is deleted 30 days after it was created. This runs in the same hourly check, and again whenever you create a token.

### Limits

- Up to 250 active (not revoked and not expired) sub-tokens per account. This counts app tokens, linked devices and OAuth access tokens together.
- Names are 1–50 characters (counted in bytes, so fewer for non-Latin text).
- Expiry is at most 8760 hours (1 year).
- `tokens:manage` cannot be granted to a sub-token, so the endpoints that require it only work with the main token.
- `account:email` and `account:signins` are only kept for adults. If the account is under 18, or has no date of birth, they are dropped when the token is created or updated.
- Giving a token a permission that moves money or reads secrets asks you to confirm it's you. See [Create a token](create-token.md).

## Endpoints

| Method | Endpoint | Auth | Description |
| --- | --- | --- | --- |
| GET | [`/tokens/permissions`](permissions.md) | None | List every permission and permission group |
| GET | [`/tokens`](list-tokens.md) | Main token | List all sub-tokens |
| GET | [`/tokens/active`](list-active-tokens.md) | Main token | List sub-tokens that are not revoked or expired |
| POST | [`/tokens/create`](create-token.md) | Main token | Create a sub-token |
| GET | [`/tokens/:id`](get-token.md) | Main token, or the sub-token itself | Get one sub-token |
| GET | [`/tokens/:id/activity`](token-activity.md) | Main token | Get a sub-token's status |
| PATCH | [`/tokens/:id`](update-token.md) | Main token | Change a sub-token's name, permissions, description or websites |
| POST | [`/tokens/:id/rename`](rename-token.md) | Main token | Rename a sub-token |
| POST | [`/tokens/:id/revoke`](revoke-token.md) | Main token | Revoke a sub-token |
| DELETE | [`/tokens/:id`](delete-token.md) | Main token | Delete a sub-token |

## Errors on every authenticated endpoint

| Status | When |
| --- | --- |
| `403` | No token was sent (`auth key is required`) |
| `403` | The token is invalid, revoked or expired (`Invalid authentication key`) |
| `403` | The account is banned (`User is banned`) |
| `403` | The account's email is not verified, or it has not accepted the current terms |
| `403` | The endpoint needs the main token and you sent a sub-token (`This action requires the main account token`) |
| `403` | The sub-token lacks the required permission (`Token lacks permission: <permission>`) |
| `403` | You sent an OAuth access token to an endpoint it can't use (`OAuth access tokens can only read the public profile`) |
