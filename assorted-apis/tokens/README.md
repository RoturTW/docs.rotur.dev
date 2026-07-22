# Tokens

Sub-tokens let you grant limited, scoped access to your Rotur account. Instead of sharing your main account token (which has full access), you create a sub-token with only the permissions an application needs.

> **Base URL:** `https://api.rotur.dev/`
>
> The same endpoints are also available under `https://api.rotur.dev/v2/tokens`. The only path difference is that creating a token is `POST /v2/tokens` instead of `POST /tokens/create`.

{% hint style="info" %}
You can authenticate in three ways: an `auth` query parameter, an `Authorization: Bearer <token>` header, or a session cookie. The examples on these pages use the query parameter.
{% endhint %}

***

## Core Concepts

### Main Token vs Sub-Token

| | Main Token | Sub-Token |
|---|---|---|
| **Format** | Base64-encoded, 64 bytes of random entropy | Prefixed `rotur_st_`, 32 bytes of random entropy |
| **Scope** | Full access to everything on the account | Limited to the permissions you assign |
| **Who uses it** | You, the account owner | Third-party apps and services you authorize |
| **Can create sub-tokens?** | Yes | No, only the main token can manage sub-tokens |
| **Can be revoked?** | By refreshing the token via `/me/refresh_token` | By revoking or deleting via the tokens API |

### Token Lifecycle

1. **Created**: you create a sub-token with a name, permissions, and optional expiry.
2. **Active**: the sub-token can authenticate to any endpoint that its permissions allow.
3. **Expired**: if an expiry was set, the token automatically becomes invalid after that time.
4. **Revoked**: you can manually revoke a token at any time. This is reversible only by creating a new token.
5. **Deleted**: you can permanently delete a token from your store.

### Limits

- Maximum of **25 active sub-tokens** per account.
- Token names must be **1-50 characters**.
- Maximum expiry is **1 year (8760 hours)**.
- The `tokens:manage` permission **cannot** be granted to sub-tokens. Because of this, the listing and activity endpoints below are effectively main-token only.
- The `full` permission group excludes `account:delete` and `tokens:manage`.

***

## Endpoints

| Endpoint | Method | Auth | Main Token | Description |
|---|---|---|---|---|
| [`/tokens/permissions`](permissions.md) | GET | No | No | List all available permissions and groups |
| [`/tokens`](list-tokens.md) | GET | Yes | Yes | List all sub-tokens |
| [`/tokens/active`](list-active-tokens.md) | GET | Yes | Yes | List only active (non-expired, non-revoked) sub-tokens |
| [`/tokens/create`](create-token.md) | POST | Yes | Yes | Create a new sub-token |
| [`/tokens/:id`](get-token.md) | GET | Yes | No | Get a single sub-token by ID |
| [`/tokens/:id/activity`](token-activity.md) | GET | Yes | Yes | Get activity/status for a sub-token |
| [`/tokens/:id`](update-token.md) | PATCH | Yes | Yes | Update a sub-token's permissions, name, etc. |
| [`/tokens/:id/rename`](rename-token.md) | POST | Yes | Yes | Rename a sub-token |
| [`/tokens/:id/revoke`](revoke-token.md) | POST | Yes | Yes | Revoke a sub-token |
| [`/tokens/:id`](delete-token.md) | DELETE | Yes | Yes | Permanently delete a sub-token |

"Main Token: Yes" means the endpoint either requires the main account token outright, or requires the `tokens:manage` permission, which sub-tokens can never hold.
