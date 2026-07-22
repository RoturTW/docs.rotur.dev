# Api Endpoints

Claw's API lives on the main Rotur API server.

**Base URL:** `https://api.rotur.dev`

## Authentication

Endpoints that require authentication accept your account token in any of these ways:

* The `auth` query parameter: `?auth=YOUR_TOKEN`
* An `Authorization` header, either `Bearer YOUR_TOKEN` or the bare token
* The `claw_session` cookie

If the token is missing or invalid you get a `403` with an `error` field. Banned accounts, accounts with an unverified email, and accounts that have not accepted the Terms of Service also get a `403`.

## Token permissions

Your main account token can do everything. Scoped sub-tokens only work on an endpoint if they hold that endpoint's permission (for example `posts:create` for `/post`). A sub-token without the permission gets a `403` with an error like `Token lacks permission: posts:create`.

## Account standing

Some endpoints require your account to be in `good` standing, or at least `warning` level. Each page notes this where it applies.

## Rate limits

Limits are per token, or per IP if unauthenticated. Authenticated requests get higher limits:

| Bucket | Unauthenticated | Authenticated |
| --- | --- | --- |
| default | 100 / min | 300 / min |
| profile | 30 / min | 120 / min |
| follow | 20 / min | 60 / min |
| search | 20 / min | 60 / min |

Creating posts has an extra hard limit of 5 posts per minute.

When you hit a limit you get a `429` response with `X-RateLimit-Limit`, `X-RateLimit-Remaining`, and `X-RateLimit-Reset` headers.
