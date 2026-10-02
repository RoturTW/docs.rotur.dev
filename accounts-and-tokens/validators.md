---
description: Short-lived proofs that someone owns a Rotur account, which a server checks without ever seeing their token.
---

# Validators

A validator is a short string that proves who made it, without the server that checks it seeing their token or password. Someone signed in to Rotur makes one for a **key**, and gives it to a service that knows the key. The service asks Rotur who made it.

Apps use their app ID as the key. The JavaScript SDK's `rotur.fetch` sends one to your server with each request, and [Check who's calling](../build-an-app/check-whos-calling.md) shows your server checking it in several languages. This page is the reference for both endpoints.

* **Base URL:** `https://api.rotur.dev`
* **Make one:** `POST /v2/validators?key=<key>`, with the person's token. The older `GET /generate_validator` does the same.
* **Check one:** `GET /v2/validators/verify?v=<validator>&key=<key>`, with no auth. The older `GET /validate` does the same.

## Make a validator

`POST /v2/validators?key=<key>`

**Auth:** The person's token, as `Authorization: Bearer <token>`. A Sign in with Rotur token can always make validators for its own app's ID. For any other key, a token from Token Manager or an app needs the `validators:generate` permission.

| Name | In | Required | Description |
| --- | --- | --- | --- |
| `key` | query | Yes | The key the validator is for: your app's ID, or any string your service and its users agree on |

```sh
curl -s -X POST "https://api.rotur.dev/v2/validators?key=app_0123456789abcdef" \
  -H "Authorization: Bearer $TOKEN"
```

**Response `200`:**

```json
{ "validator": "7ebdf483-5b9f-4b70-9edc-a1f2827391f9,9c1f0e…" }
```

`validator` is the person's Rotur ID and a SHA-256 hash, separated by a comma.

| Status | When |
| --- | --- |
| `400` | `key` is missing (`key is required`) |
| `403` | The token is missing or invalid, or a token without the permission asked for a key that isn't its own app (`code: "permission_missing"`) |
| `403` | The account is restricted or banned. The body has `code: "account_blocked"`, `standing`, `recover_at`, `reason` and `redirect_url` |
| `403` | The key is for an originChats server that a parent or carer has blocked. The body has `code: "parental_block"` and `server` |

## Check a validator

`GET /v2/validators/verify?v=<validator>&key=<key>`

**Auth:** None.

| Name | In | Required | Description |
| --- | --- | --- | --- |
| `v` | query | Yes | The whole validator, URL-encoded |
| `key` | query | Yes | The key it was made for |

**Response `200` (valid):**

```json
{ "valid": true, "id": "7ebdf483-5b9f-4b70-9edc-a1f2827391f9", "username": "kit", "minor": false }
```

| Field | Description |
| --- | --- |
| `minor` | `true` if the account isn't known to be 18 or over |
| `account_type` | Only for sub-accounts: `bot` or `org` |
| `owner` | Only for sub-accounts whose owner is discoverable: the owner's username |
| `restrictions` | Only for originChats keys when parental controls limit direct messages. Holds `direct_messages` and the person's `friends`, so a DM server can enforce the rule |

**Not valid:** an expired, unknown or wrong-key validator still gets `200`, with `valid: false` and an `error`. Always check `valid`.

```json
{ "valid": false, "error": "Validator expired or not found" }
```

When the key is an app ID, Rotur also checks the person may use that app, and refuses with a `code` if not: `app_banned`, `app_suspended`, `app_adults_only`, `parent_blocked_apps`, `parent_approval_needed` or `account_unavailable`. [Check who's calling](../build-an-app/check-whos-calling.md#the-call) says what each means. A successful check also counts as the person using the app, for your [users list](../build-an-app/users.md).

For an originChats key, a server that a parent or carer has blocked gets `valid: false` with `code: "parental_block"`.

| Status | When |
| --- | --- |
| `400` | `v` or `key` is missing (`Validator is required`, `Key is required`), or `v` has no comma (`Invalid validator format`). No `valid` field |
| `403` | The account is restricted or banned. The body has `valid: false`, `code: "account_blocked"`, `standing`, `recover_at`, `reason`, `redirect_url`, `username` and `id` |
| `404` | No account has the ID in the validator (`User not found`). No `valid` field |

## How it works

### Hash

```
SHA-256(key + accountToken + windowStart)
```

* `key`: the key it was made for.
* `accountToken`: the account's main token. It never leaves Rotur.
* `windowStart`: when it was made, in Unix seconds, rounded down to a multiple of 300, as a decimal string.

The result is a 64-character lowercase hex string. Every validator one account makes for one key in the same 5-minute window is the same string.

### Lifetime

Each validator works for 300 seconds from when it was last made. It can be checked any number of times until then. A service can keep the answer for a validator until the end of its 5-minute window.

### Storage

Rotur holds validators in memory only. A restart of Rotur ends every validator, and so does the account's main token changing.

## Security

* A validator expires 5 minutes after it's made, which limits replay.
* The hash covers both the key and the account's token, so a validator can't be used for another key or account.
* The account's token is never sent anywhere. It is only mixed into the hash, on Rotur's side.
* Anyone can check a validator. Forging one needs the account's token.
