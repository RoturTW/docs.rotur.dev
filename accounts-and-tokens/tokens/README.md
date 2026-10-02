# Tokens

Sub-tokens give an app limited access to your Rotur account. Instead of sharing your main account token, which can do everything, you create a sub-token that holds only the permissions the app needs.

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

### Lifecycle

1. **Active:** the sub-token works on every endpoint its permissions allow.
2. **Expired:** if you set an expiry, the sub-token stops working once it passes. The server checks for expired sub-tokens every hour and marks them revoked.
3. **Revoked:** you revoked it, or it expired. A revoked sub-token cannot be restored; create a new one instead.
4. **Deleted:** the record is removed from your account entirely.

### Limits

- Up to 25 active (not revoked and not expired) sub-tokens per account.
- Names are 1–50 characters.
- Expiry is at most 8760 hours (1 year).
- `tokens:manage` cannot be granted to a sub-token, so the endpoints that require it only work with the main token.

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
| `403` | No token was sent, the token is invalid, or the account is banned |
| `403` | The endpoint needs the main token and you sent a sub-token (`This action requires the main account token`) |
| `403` | The sub-token lacks the required permission (`Token lacks permission: <permission>`) |
