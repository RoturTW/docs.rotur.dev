---
description: Every request, parameter, response and error in Sign in with Rotur, for apps that run OAuth themselves.
---

# Authorise, swap and refresh

This page covers each request in Sign in with Rotur. For working code, see [Complete examples](examples.md).

## How it fits together

1. Your app makes a PKCE verifier (a random string) and its challenge (the SHA-256 hash of the verifier, base64url-encoded).
2. Your app sends the person's browser to `/oauth/authorize` with the challenge.
3. Rotur signs them in and shows a consent screen naming your app.
4. Rotur sends the browser back to your redirect URI with a `code`, or with `error=access_denied` if they said no.
5. Your app posts the `code` and the verifier to `/oauth/token` and gets an access token, and a refresh token if you asked for one.
6. Your app calls `/oauth/userinfo` with the access token to learn who signed in.

## 1. Send the person to Rotur

Send the browser to `https://api.rotur.dev/oauth/authorize` with these query parameters:

| Parameter | Required | Value |
| --- | --- | --- |
| `response_type` | Yes | `code` |
| `client_id` | Yes | Your app's ID, `app_…` |
| `redirect_uri` | Yes | One of your app's redirect URIs, exactly as registered |
| `code_challenge` | Yes | Base64url SHA-256 of your verifier, without `=` padding. 43 to 128 characters |
| `code_challenge_method` | Yes | `S256`. Plain PKCE isn't supported |
| `scope` | No | Space-separated. Defaults to `profile`. See [Scopes](scopes.md) |
| `state` | Recommended | A random value you check when the person comes back. Rotur returns it unchanged |
| `response_mode` | No | `query` (the default), or `web_message` for a popup that hands the code to the page that opened it. See [signin.js](signin-js.md#without-the-script) |

The verifier itself must be 43 to 128 characters. A good recipe is 32 random bytes, base64url-encoded without padding, which gives 43 characters.

```
https://api.rotur.dev/oauth/authorize
  ?response_type=code
  &client_id=app_0123456789abcdef
  &redirect_uri=https%3A%2F%2Fexample.com%2Fcallback
  &scope=profile%20offline_access
  &state=Zt1o3rWq8Jx0Vb2Kc5mN
  &code_challenge=E9Melhoa2OwvFrEMTJguCHaoeK1t8URWbuGJSstw-cM
  &code_challenge_method=S256
```

Rotur first checks `client_id` and `redirect_uri`. If either is wrong, it can't safely send the person back, so it shows a JSON error instead:

| Status | `error_description` | Fix |
| --- | --- | --- |
| `400` | `Unknown client_id` | Check the ID. Your app also needs at least one redirect URI, and mustn't be suspended |
| `400` | `redirect_uri is not registered for this client` | Add the address to your app, or fix it. It must match exactly, including `http` or `https`, the port, the path and any trailing slash |

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

| `error` | Why | What to do |
| --- | --- | --- |
| `access_denied` | The person chose not to sign in, or Rotur told them they can't use your app and they went back | Say they didn't sign in, and offer to try again |
| `unsupported_response_type` | `response_type` wasn't `code` | Send `response_type=code` |
| `invalid_request` | PKCE was missing, wasn't `S256`, or the challenge wasn't valid base64url of the right length. `error_description` says which | Fix your code |
| `invalid_scope` | You asked for a scope Rotur doesn't have, such as `openid` | Remove it. See [Scopes](scopes.md) |

Before you do anything else:

* Check that `state` matches the value you stored for this browser. If it doesn't, stop.
* Optionally check that `iss` is `https://api.rotur.dev`.

Rotur never tells your app why someone can't use it. The person sees the reason on Rotur's own screen. See [When someone is refused](scopes.md#when-someone-is-refused).

## 3. Swap the code for tokens

Post the code to the token endpoint, from your server or, for a public client, from the browser. The body must be form-encoded (`application/x-www-form-urlencoded`), not JSON.

`POST https://api.rotur.dev/oauth/token`

| Parameter | Required | Value |
| --- | --- | --- |
| `grant_type` | Yes | `authorization_code` |
| `code` | Yes | The `code` from the redirect |
| `redirect_uri` | Yes | The same `redirect_uri` you sent in step 1 |
| `code_verifier` | Yes | The verifier you made in step 1 |
| `client_id` | Public clients, or with `client_secret` | Your app's ID |
| `client_secret` | Only if you don't use Basic | One of your app's secrets |

How your app proves who it is depends on its [type](client-types.md):

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
  "scope": "profile offline_access",
  "refresh_token": "rrt_…"
}
```

`scope` lists what you were actually given. It can be less than you asked for: people can turn permissions off on the consent screen, and Rotur leaves out `email` and some permissions for anyone not known to be 18 or over. `refresh_token` is only there if you asked for `offline_access`. There is no `id_token`.

A code works once, and only for 5 minutes.

## 4. Refresh

An access token lasts one hour. With a refresh token, get a new one without sending the person through Rotur again:

| Parameter | Required | Value |
| --- | --- | --- |
| `grant_type` | Yes | `refresh_token` |
| `refresh_token` | Yes | The refresh token |
| `client_id`, `client_secret` | As in step 3 | Your app proves who it is in the same way |

```sh
curl -s https://api.rotur.dev/oauth/token \
  -d grant_type=refresh_token \
  -d client_id=app_0123456789abcdef \
  -d refresh_token="$REFRESH_TOKEN"
```

The answer has the same shape as step 3, always with a new `refresh_token`.

* Each refresh token works once. Keep the new one, and throw the old one away.
* Refreshing stops the access token the refresh token last gave.
* A refresh token stops working after 60 days without being used, or when the person leaves your app, is banned from it, or revokes the app's token in Token Manager.
* A person can be signed in to your app this way on up to 10 devices. Signing in on an eleventh ends the oldest.

## Errors from the token endpoint

Errors are JSON with `error` and `error_description`.

| Status | `error` | `error_description` | What to do |
| --- | --- | --- | --- |
| `400` | `unsupported_grant_type` | `grant_type must be authorization_code or refresh_token` | Send one of those |
| `401` | `invalid_client` | `Client authentication failed` | Check the app ID and secret. A public client must be marked **This app can't keep a secret** |
| `400` | `invalid_grant` | `Authorization code is invalid or expired` | The code was already used, is over 5 minutes old, belongs to another app, or `redirect_uri` doesn't match. Sign the person in again |
| `400` | `invalid_grant` | `PKCE verification failed` | You sent the wrong verifier. Use the one you stored for this sign-in |
| `400` | `invalid_grant` | `Refresh token is invalid or expired` | It was already used, expired or revoked. Sign the person in again |
| `400` | `invalid_grant` | `This account can't use this app` | The person can no longer use your app, for example because you banned them. Tell them they can't sign in with that account |
| `400` | `invalid_grant` | `account has reached its active token limit` | The person has too many active tokens. They can remove some on rotur.dev |
| `429` | | | Too many requests from your IP address. Most endpoints allow 100 a minute |

## 5. Find out who signed in

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

| Field | What it is |
| --- | --- |
| `sub` | The person's Rotur ID. It never changes, so key your users by it |
| `username` | Their username. It can change |
| `name` | Their display name. It can be empty |
| `picture` | Their avatar. The address stays the same when they change it |
| `profile` | Their profile page on rotur.dev |
| `email`, `email_verified` | Only with the `email` scope, for people who share it. See [Scopes](scopes.md#the-email-scope) |

**Errors:** `401` with `error: "invalid_token"` and a `WWW-Authenticate: Bearer` header, when the token is missing, wrong, over an hour old or revoked, or the person can no longer use your app.

Calling `/oauth/userinfo` also updates when the person last used your app, which you see in your [users list](../build-an-app/users.md).

## What an access token can do

With only `profile` (and `email`), an access token reads who signed in. Other Rotur endpoints that need an account answer `403` with `OAuth access tokens can only read the public profile`, except [making validators](README.md#call-your-server-with-a-token) for your own app.

With permission scopes, it can also call each endpoint those permissions cover, as the person. Each endpoint in the [API reference](../api-reference/README.md) lists the permission it needs.

## Sessions and signing out

There's no endpoint for your app to revoke a token. To sign someone out, end your own session and throw the tokens away. An access token stops working within the hour.

Tokens also stop working straight away when:

* the person leaves your app from [rotur.dev/me/apps](https://rotur.dev/me/apps);
* you [ban them](../build-an-app/bans.md), or Rotur bans or suspends their account;
* Rotur suspends your app, or you delete it.

If you keep your own sessions, also set up [webhooks](../build-an-app/webhooks.md) so you hear when someone leaves your app, is banned from Rotur or deletes their account.

## When someone signs up during sign-in

If someone makes a new Rotur account in the middle of signing in to your app, your app is recorded as the app they joined through. Their profile shows your app's [origin badge](../build-an-app/badges.md#the-origin-badge), which they can hide.
