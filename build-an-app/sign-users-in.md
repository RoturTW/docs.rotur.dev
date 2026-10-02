---
description: Every request, parameter and response in Sign in with Rotur.
---

# Sign users in

This page covers each part of Sign in with Rotur in detail. If you want working code first, start with the [Quickstart](quickstart.md).

Sign in with Rotur is OAuth 2.0 with:

* the authorisation code grant only;
* PKCE with `S256`, required for every app;
* the `profile` and `email` scopes;
* access tokens that last one hour, with no refresh tokens.

It is not OpenID Connect. There is no ID token and no `openid` scope, so set your library's scope to `profile` (many add `openid` by default, which Rotur refuses).

## Endpoints

| What | Address |
| --- | --- |
| Authorisation | `GET https://api.rotur.dev/oauth/authorize` |
| Token | `POST https://api.rotur.dev/oauth/token` |
| User info | `GET https://api.rotur.dev/oauth/userinfo` |
| Server metadata ([RFC 8414](https://www.rfc-editor.org/rfc/rfc8414)) | `GET https://api.rotur.dev/.well-known/oauth-authorization-server` |

The issuer is `https://api.rotur.dev`. Libraries that read server metadata can use the last address to configure themselves.

## How it fits together

1. Your app makes a PKCE verifier (a random string) and its challenge (the SHA-256 hash of the verifier, base64url-encoded).
2. Your app sends the person's browser to `/oauth/authorize` with the challenge.
3. Rotur signs them in and shows a consent screen naming your app.
4. Rotur sends the browser back to your redirect URI with a `code`, or with `error=access_denied` if they said no.
5. Your app posts the `code` and the verifier to `/oauth/token` and gets an access token.
6. Your app calls `/oauth/userinfo` with the token to learn who signed in.

## 1. Send the person to Rotur

Send the browser to `https://api.rotur.dev/oauth/authorize` with these query parameters:

| Parameter | Required | Value |
| --- | --- | --- |
| `response_type` | Yes | `code` |
| `client_id` | Yes | Your app's ID, `app_…` |
| `redirect_uri` | Yes | One of your app's redirect URIs, exactly as registered |
| `code_challenge` | Yes | Base64url SHA-256 of your verifier, without `=` padding. 43 to 128 characters |
| `code_challenge_method` | Yes | `S256`. Plain PKCE isn't supported |
| `scope` | No | Space-separated: `profile`, or `profile email`. Defaults to `profile`. See [Scopes](scopes.md) |
| `state` | Recommended | A random value you check when the person comes back. Rotur returns it unchanged |

The verifier itself must be 43 to 128 characters. A good recipe is 32 random bytes, base64url-encoded without padding, which gives 43 characters.

```
https://api.rotur.dev/oauth/authorize
  ?response_type=code
  &client_id=app_0123456789abcdef
  &redirect_uri=https%3A%2F%2Fexample.com%2Fcallback
  &scope=profile
  &state=Zt1o3rWq8Jx0Vb2Kc5mN
  &code_challenge=E9Melhoa2OwvFrEMTJguCHaoeK1t8URWbuGJSstw-cM
  &code_challenge_method=S256
```

Rotur first checks `client_id` and `redirect_uri`. If either is wrong, it can't safely send the person back, so it shows a JSON error instead:

| Status | `error_description` | Fix |
| --- | --- | --- |
| `400` | `Unknown client_id` | Check the ID. Your app also needs at least one redirect URI, and mustn't be suspended |
| `400` | `redirect_uri is not registered for this client` | Add the address to your app, or fix the typo. It must match exactly |

Any other problem sends the person back to your redirect URI with an error (see below).

If all is well, Rotur sends the person to its sign-in and consent screen at `rotur.dev/auth`. They have 10 minutes to finish.

## 2. Handle the person coming back

Rotur sends the browser to your redirect URI. Any query parameters already in your redirect URI are kept.

**If they approved:**

```
https://example.com/callback?code=SplxlOBeZQQYbYS6WxSbIA&state=Zt1o3rWq8Jx0Vb2Kc5mN&iss=https%3A%2F%2Fapi.rotur.dev
```

**If they said no, or something was wrong with the request:**

```
https://example.com/callback?error=access_denied&state=Zt1o3rWq8Jx0Vb2Kc5mN&iss=https%3A%2F%2Fapi.rotur.dev
```

| `error` | Why |
| --- | --- |
| `access_denied` | The person chose not to sign in |
| `unsupported_response_type` | `response_type` wasn't `code` |
| `invalid_request` | PKCE was missing, wasn't `S256`, or the challenge wasn't valid base64url of the right length. `error_description` says which |
| `invalid_scope` | You asked for a scope other than `profile` or `email` |

Before you do anything else:

* Check that `state` matches the value you stored for this browser. If it doesn't, stop.
* Optionally check that `iss` is `https://api.rotur.dev`.

People Rotur won't let use your app (for example, people you have banned) see the reason on Rotur's consent screen and can't approve. If they choose **Back to** your app, you get `error=access_denied`, the same as a refusal. Rotur doesn't tell your app why. See [Handle errors and denials](errors.md).

## 3. Swap the code for a token

From your server (or from the browser, for a public client), post the code to the token endpoint. The body must be form-encoded (`application/x-www-form-urlencoded`), not JSON.

`POST https://api.rotur.dev/oauth/token`

| Parameter | Required | Value |
| --- | --- | --- |
| `grant_type` | Yes | `authorization_code` |
| `code` | Yes | The `code` from the redirect |
| `redirect_uri` | Yes | The same `redirect_uri` you sent in step 1 |
| `code_verifier` | Yes | The verifier you made in step 1 |
| `client_id` | Public clients, or with `client_secret` | Your app's ID |
| `client_secret` | Only if you don't use Basic | One of your app's secrets |

How your app proves who it is depends on its type:

| App | How to authenticate |
| --- | --- |
| Confidential (has a server) | HTTP Basic: `Authorization: Basic base64(client_id:client_secret)`. Or send `client_id` and `client_secret` in the body |
| Public (can't keep a secret) | Send only `client_id` in the body |

```http
POST /oauth/token HTTP/1.1
Host: api.rotur.dev
Authorization: Basic <base64 of client_id:client_secret>
Content-Type: application/x-www-form-urlencoded

grant_type=authorization_code&code=SplxlOBeZQQYbYS6WxSbIA&redirect_uri=https%3A%2F%2Fexample.com%2Fcallback&code_verifier=dBjftJeZ4CVP-mB92K27uhbUJU1p1r_wW1gFWFOEjXk
```

**Response `200`:**

```json
{
  "access_token": "rotur_st_…",
  "token_type": "Bearer",
  "expires_in": 3600,
  "scope": "profile"
}
```

`scope` lists what you were actually given. It can be less than you asked for: `email` is left out for anyone not known to be 18 or over. There is no `refresh_token` and no `id_token`.

A code works once, and only for 5 minutes. Using it again, or after that, fails.

### Errors

Errors are JSON with `error` and `error_description`.

| Status | `error` | `error_description` | Why |
| --- | --- | --- | --- |
| `400` | `unsupported_grant_type` | `Only authorization_code is supported` | `grant_type` was something else, such as `refresh_token` or `client_credentials` |
| `401` | `invalid_client` | `Client authentication failed` | Unknown client ID, wrong secret, or a confidential app that sent no secret |
| `400` | `invalid_grant` | `Authorization code is invalid or expired` | The code was already used, is over 5 minutes old, belongs to another app, or `redirect_uri` doesn't match |
| `400` | `invalid_grant` | `PKCE verification failed` | The verifier doesn't match the challenge, or isn't 43 to 128 characters |
| `400` | `invalid_grant` | `This account can't use this app` | Since approving, the person was banned from your app, or otherwise can't use it |
| `400` | `invalid_grant` | `account has reached its active token limit` | The person has too many active app tokens. Rare |

The token endpoint allows 5 requests every 10 seconds from each IP address. Over that you get `429` with `{"error": "Rate limit exceeded."}`.

## 4. Find out who signed in

`GET https://api.rotur.dev/oauth/userinfo`

```http
GET /oauth/userinfo HTTP/1.1
Host: api.rotur.dev
Authorization: Bearer rotur_st_…
```

**Response `200`:**

```json
{
  "sub": "7ebdf483-5b9f-4b70-9edc-a1f2827391f9",
  "username": "kit",
  "name": "Kit",
  "picture": "https://avatars.rotur.dev/7ebdf483-5b9f-4b70-9edc-a1f2827391f9",
  "profile": "https://rotur.dev/@kit"
}
```

With the `email` scope, for people known to be 18 or over who agreed to share it:

```json
{
  "sub": "7ebdf483-5b9f-4b70-9edc-a1f2827391f9",
  "username": "kit",
  "name": "Kit",
  "picture": "https://avatars.rotur.dev/7ebdf483-5b9f-4b70-9edc-a1f2827391f9",
  "profile": "https://rotur.dev/@kit",
  "email": "kit@example.com",
  "email_verified": true
}
```

Key your users by `sub`. Usernames and display names can change; `sub` never does.

**Errors:**

| Status | `error` | Why |
| --- | --- | --- |
| `401` | `invalid_token` | The token is missing, wrong, over an hour old, or was revoked. Also when the person can no longer use your app. The response has a `WWW-Authenticate: Bearer` header |

Calling `/oauth/userinfo` also updates when the person last used your app, which you see in your [users list](users.md).

## What an access token can do

An access token from Sign in with Rotur only tells you who signed in. It works with `/oauth/userinfo`. Other Rotur endpoints that need an account answer `403` with `OAuth access tokens can only read the public profile`.

If your app needs to act on someone's account, for example to post or read their friends, that's a different kind of token. See [Accounts and tokens](../accounts-and-tokens/README.md).

## Sessions and signing out

Access tokens last one hour and can't be refreshed. Most apps:

1. Sign the person in with Rotur.
2. Read `/oauth/userinfo` once and create or update their account in your own database, keyed by `sub`.
3. Start their own session (a cookie, for example) and stop using the Rotur token.
4. Send them through Sign in with Rotur again when their session ends. If they're still signed in to Rotur and have approved your app before, this is quick.

There's no endpoint for your app to revoke a token. To sign someone out, end your own session and throw the token away. It stops working within the hour.

Tokens also stop working straight away when:

* the person leaves your app from [rotur.dev/me/apps](https://rotur.dev/me/apps);
* you [ban them](bans.md), or Rotur bans or suspends their account;
* Rotur suspends your app, or you delete it.

## When someone signs up during sign-in

If someone makes a new Rotur account in the middle of signing in to your app, your app is recorded as the app they joined through. Their profile shows your app's [origin badge](badges.md#the-origin-badge), which they can hide.

## Using an OAuth library

Most OAuth 2.0 client libraries work. Configure them with:

| Setting | Value |
| --- | --- |
| Authorisation URL | `https://api.rotur.dev/oauth/authorize` |
| Token URL | `https://api.rotur.dev/oauth/token` |
| User info URL | `https://api.rotur.dev/oauth/userinfo` |
| Scope | `profile` (or `profile email`) |
| PKCE | On, `S256` |
| Client authentication | `client_secret_basic` or `client_secret_post` for confidential apps, `none` for public apps |
| User ID field | `sub` |
