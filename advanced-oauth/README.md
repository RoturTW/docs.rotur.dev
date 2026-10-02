---
description: Sign in with Rotur is OAuth 2.0 with PKCE. Use it directly from a desktop app, a game, a server, or any language without the JavaScript SDK.
---

# OAuth without the SDK

The [JavaScript SDK](../build-an-app/sign-people-in.md) runs Sign in with Rotur for you. Use these pages when you can't use it: a desktop app or game, a server that signs people in with redirects, or a language other than JavaScript.

Sign in with Rotur is OAuth 2.0 with:

* the authorisation code grant, and refresh tokens;
* PKCE with `S256`, required for every app;
* scopes for the profile, the email address and each [Token Manager permission](../accounts-and-tokens/tokens/permissions.md).

It is not OpenID Connect. There is no ID token and no `openid` scope.

## Endpoints

| What | Address |
| --- | --- |
| Authorisation | `GET https://api.rotur.dev/oauth/authorize` |
| Token | `POST https://api.rotur.dev/oauth/token` |
| User info | `GET https://api.rotur.dev/oauth/userinfo` |
| Server metadata ([RFC 8414](https://www.rfc-editor.org/rfc/rfc8414)) | `GET https://api.rotur.dev/.well-known/oauth-authorization-server` |

The issuer is `https://api.rotur.dev`. Libraries that read server metadata can configure themselves from the last address.

## Pages in this section

* [Authorise, swap and refresh](authorize.md): every request, parameter, response and error.
* [Scopes and the consent screen](scopes.md): what to ask for, and what people see.
* [Public and confidential clients](client-types.md): whether your app sends a secret, and desktop apps.
* [Complete examples](examples.md): a page with no server, a Node.js server, and curl.
* [Sign-in button with signin.js](signin-js.md): a button and the browser's "Continue as" prompt, from one script tag.

## Using an OAuth library

Most OAuth 2.0 client libraries work. Configure them with:

| Setting | Value |
| --- | --- |
| Authorisation URL | `https://api.rotur.dev/oauth/authorize` |
| Token URL | `https://api.rotur.dev/oauth/token` |
| User info URL | `https://api.rotur.dev/oauth/userinfo` |
| Scope | `profile`, plus what you need. Many libraries add `openid` by default, which Rotur refuses |
| PKCE | On, `S256` |
| Client authentication | `client_secret_basic` or `client_secret_post` for confidential apps, `none` for public apps |
| User ID field | `sub` |

## Call your server with a token

The SDK's `rotur.fetch` sends a validator, so your server never sees the person's token. You can do the same with any access token from Sign in with Rotur: make a validator for your app's ID, and send it as `Authorization: Rotur <validator>`.

```sh
curl -s -X POST "https://api.rotur.dev/v2/validators?key=app_0123456789abcdef" \
  -H "Authorization: Bearer $ACCESS_TOKEN"
```

```json
{ "validator": "7ebdf483-5b9f-4b70-9edc-a1f2827391f9,9c1f…" }
```

A token can always make validators for its own app. Validators for one 5-minute window are the same, so make one per window and reuse it. While the person can't use your app, for example because you banned them, this answers `403` with `error`, `code`, and for a ban `reason` and `until`: show them `error`. Your server checks it as in [Check who's calling](../build-an-app/check-whos-calling.md).
