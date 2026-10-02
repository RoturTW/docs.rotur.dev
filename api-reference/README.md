---
description: Base URLs, authentication, versions, errors and rate limits for the Rotur API.
---

# How the API works

These pages describe each Rotur endpoint: what it does, the auth it needs, its parameters, an example, the response and its errors.

## Base URLs

| Service | Base URL |
| --- | --- |
| The main API | `https://api.rotur.dev` |
| Avatars, banners and overlays | `https://avatars.rotur.dev` |
| Other services | Listed on their own pages, such as [rMail](rmail.md), [Gate](gate.md) and [share.rotur.dev](share.rotur.dev.md) |

## Authentication

Most endpoints need a token. Send it in the `Authorization` header:

```http
Authorization: Bearer <token>
```

The older `auth` query parameter (`?auth=<token>`) still works, but the header keeps tokens out of logs.

Which token you have decides what you can call:

| Token | Where it comes from | What it can call |
| --- | --- | --- |
| Sign in with Rotur access token | [Sign in with Rotur](../advanced-oauth/authorize.md) | `/oauth/userinfo`, `/me` for the public profile, and whatever permissions the person gave the app. With no permissions, other endpoints answer `403` with `OAuth access tokens can only read the public profile` |
| Rotur account sub-token (`rotur_st_…`) | [rotur.dev/auth](../accounts-and-tokens/rotur-dev-auth.md), [linking](../accounts-and-tokens/linking.md) or the [tokens API](../accounts-and-tokens/tokens/README.md) | Endpoints its [permissions](../accounts-and-tokens/tokens/permissions.md) allow. Each endpoint's **Auth** line names the permission |
| The account's main token | The account itself | Everything, including the endpoints marked "main token only" |
| App credentials (`app_…` and `rsec_…`) | [Your Rotur App](../build-an-app/set-up-your-app.md) | The [apps API](../build-an-app/apps-api.md), with HTTP Basic |

When a sub-token lacks a permission, the answer is `403` with `Token lacks permission: <permission>`.

## Versions

Most endpoints exist twice:

* **v1** paths at the root, such as `GET /keys/mine`. Many take their parameters in the query string, even for writes.
* **v2** paths under `/v2`, such as `GET /v2/keys/mine`, with RESTful methods. For v2 requests with `Content-Type: application/json`, top-level fields in the JSON body are read as if they were query parameters. A query parameter wins if both are sent.

Newer features, including [Rotur Apps](../build-an-app/apps-api.md), are v2 only. Pages show v1 or v2 paths as the endpoint was designed; where both exist, the page says so.

## Requests and responses

* Send JSON bodies with `Content-Type: application/json`. The exception is `POST /oauth/token`, which takes a form body.
* Responses are JSON. Errors have an `error` field with a sentence, and sometimes a `code` you can match on:

```json
{ "error": "Token lacks permission: posts:create" }
```

* Account IDs are UUIDs. Prefer them to usernames, which can change.
* Times are Unix timestamps. Most are in milliseconds; a few older fields are in seconds. Each page says which.

## Rate limits

Limits are counted per account when you send a token, and per IP address when you don't.

| Kind of endpoint | Without a token | With a token |
| --- | --- | --- |
| Most endpoints | 100 a minute | 300 a minute |
| Sign-in, sign-up and OAuth token requests | 5 every 10 seconds | 5 every 10 seconds |
| Profiles | 6000 a minute | 24000 a minute |
| Posting | 5 a minute | 20 a minute |

Some endpoints have their own limits, listed on their pages. Over the limit you get `429`:

```json
{ "error": "Rate limit exceeded.", "reset_time": 1790000060, "remaining": 0 }
```

with `X-RateLimit-Limit`, `X-RateLimit-Remaining` and `X-RateLimit-Reset` headers.

## Deprecated endpoints

Deprecated endpoints keep working for a while and send a `Deprecation: true` header, with a `Link` header to the migration notes. See [Deprecated](../deprecated/systems.md).

## Find an endpoint

| You want to | Look in |
| --- | --- |
| Read or change a profile, post, follow, manage friends or files | [Claw](../claw/api-endpoints/README.md) |
| Understand what an account holds | [Your account](account/README.md) |
| Sell access, spend credits or give cosmetics | [Credits and purchases](economy/README.md) |
| Run a community | [Groups](groups/README.md) |
| Send or receive push notifications | [Notifications](notifications/README.md) |
| See who's online | [Status WebSocket](status-websocket/README.md) |
| Show avatars and banners | [avatars.rotur.dev](avatars.rotur.dev/README.md) |
| Sign people in, or manage your app | [Apps and OAuth endpoints](apps-and-oauth.md) |
