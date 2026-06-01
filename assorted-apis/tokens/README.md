# Tokens

Sub-tokens allow you to grant limited, scoped access to your Rotur account. Instead of sharing your main account token (which has full access), you create a sub-token with only the permissions an application needs.

> **Base URL:** `https://api.rotur.dev/`
>
> **Authentication:** All endpoints require a valid `auth` query parameter (your Rotur user token). Mutating endpoints additionally require the **main account token** (not a sub-token).

***

## Core Concepts

### Main Token vs Sub-Token

| | Main Token | Sub-Token |
|---|---|---|
| **Format** | Base64-encoded, 64 bytes of random entropy | Prefixed `rotur_st_`, 32 bytes of random entropy |
| **Scope** | Full access to everything on the account | Limited to the permissions you assign |
| **Who uses it** | You, the account owner | Third-party apps and services you authorize |
| **Can create sub-tokens?** | Yes | No — only the main token can manage sub-tokens |
| **Can be revoked?** | By refreshing the token via `/me/refresh_token` | By revoking or deleting via the tokens API |

### Token Lifecycle

1. **Created** — You create a sub-token with a name, permissions, and optional expiry.
2. **Active** — The sub-token can authenticate to any endpoint that its permissions allow.
3. **Expired** — If an expiry was set, the token automatically becomes invalid after that time.
4. **Revoked** — You can manually revoke a token at any time. This is reversible only by creating a new token.
5. **Deleted** — You can permanently delete a token from your store.

### Limits

- Maximum of **25 active sub-tokens** per account.
- Token names must be **1–50 characters**.
- Maximum expiry is **1 year (8760 hours)**.
- The `tokens:manage` permission **cannot** be granted to sub-tokens.
- The `account:delete` permission **cannot** be granted via the `full` permission group.

### Storage

Sub-tokens are stored per-user at:

```
<USERDATA_PATH>/<username>/tokens.json
```

***

## Endpoints

| Endpoint | Method | Auth | Main Token | Description |
|---|---|---|---|---|
| [`/tokens/permissions`](permissions.md) | GET | No | No | List all available permissions and groups |
| [`/tokens`](list-tokens.md) | GET | Yes | No | List all sub-tokens |
| [`/tokens/active`](list-active-tokens.md) | GET | Yes | No | List only active (non-expired, non-revoked) sub-tokens |
| [`/tokens/create`](create-token.md) | POST | Yes | Yes | Create a new sub-token |
| [`/tokens/:id`](get-token.md) | GET | Yes | No | Get a single sub-token by ID |
| [`/tokens/:id/activity`](token-activity.md) | GET | Yes | No | Get activity/status for a sub-token |
| [`/tokens/:id`](update-token.md) | PATCH | Yes | Yes | Update a sub-token's permissions, name, etc. |
| [`/tokens/:id/rename`](rename-token.md) | POST | Yes | Yes | Rename a sub-token |
| [`/tokens/:id/revoke`](revoke-token.md) | POST | Yes | Yes | Revoke a sub-token |
| [`/tokens/:id`](delete-token.md) | DELETE | Yes | Yes | Permanently delete a sub-token |
