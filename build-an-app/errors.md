---
description: What to do when sign-in fails, someone says no, or a token stops working.
---

# Handle errors and denials

Most sign-in problems fall into a few cases. This page says what each looks like and what your app should do.

## Rotur shows an error instead of the sign-in page

The browser shows JSON like `{"error": "invalid_request", "error_description": "Unknown client_id"}` on `api.rotur.dev`. This is a set-up problem, not something the person did.

| `error_description` | Fix |
| --- | --- |
| `Unknown client_id` | Check the client ID. Your app needs at least one redirect URI, and mustn't be suspended |
| `redirect_uri is not registered for this client` | The `redirect_uri` must match one of your app's redirect URIs exactly, including `http`/`https`, port, path and trailing slash |

## The person comes back with an error

Your redirect URI receives `error` instead of `code`. Always check `state` first.

| `error` | What happened | What to do |
| --- | --- | --- |
| `access_denied` | They chose not to sign in, or Rotur told them they can't use your app and they went back | Show a calm message, such as "You didn't sign in", and a button to try again |
| `invalid_request` | Your request was missing PKCE, used a method other than `S256`, or had a malformed challenge | Fix your code. `error_description` says what was wrong |
| `invalid_scope` | You asked for a scope other than `profile` or `email` | Remove it. Many libraries add `openid`; turn that off |
| `unsupported_response_type` | `response_type` wasn't `code` | Send `response_type=code` |

Rotur never tells your app *why* someone can't use it. The person sees the reason on Rotur's own screen. See [When someone is refused](scopes.md#when-someone-is-refused).

## The code exchange fails

`POST /oauth/token` answers with `error` and `error_description`.

| Status | `error` | `error_description` | What to do |
| --- | --- | --- | --- |
| `401` | `invalid_client` | `Client authentication failed` | Check the client ID and secret. Public clients must be marked **This app can't keep a secret** |
| `400` | `invalid_grant` | `Authorization code is invalid or expired` | Codes work once, for 5 minutes, and only with the same `redirect_uri`. Send the person through sign-in again |
| `400` | `invalid_grant` | `PKCE verification failed` | You sent the wrong verifier. Make sure you use the one you stored for this sign-in |
| `400` | `invalid_grant` | `This account can't use this app` | Between approving and the exchange, the person stopped being able to use your app (for example, you banned them). Tell them they can't sign in with that account |
| `400` | `invalid_grant` | `account has reached its active token limit` | The person has too many active app tokens. They can remove some from their account's token list on rotur.dev |
| `400` | `unsupported_grant_type` | `Only authorization_code is supported` | There are no refresh tokens or client credentials. Use the authorisation code grant |
| `429` | | | Too many requests from your IP address (5 every 10 seconds). Wait and try again |

## The token stops working

`/oauth/userinfo` answers `401` with `error: "invalid_token"`. This happens when:

* the token is over an hour old (the usual case);
* the person left your app from [rotur.dev/me/apps](https://rotur.dev/me/apps);
* you banned them, or Rotur banned or suspended their account;
* a parent turned off apps for them, or your app now declares mature content and they aren't known to be 18 or over;
* Rotur suspended your app.

There are no refresh tokens. Send the person through Sign in with Rotur again. If they can still use your app, they'll come straight back; if not, Rotur will tell them why.

Most apps only call `/oauth/userinfo` once, at sign-in, and keep their own session after that. If you do that, also set up [webhooks](webhooks.md) so you hear when someone leaves your app, is banned from Rotur or deletes their account.

## Errors from the apps API

Calls to `/v2/apps/…` with your client ID and secret return a JSON body with `error` (a sentence) and usually `code`. See [Call the apps API](apps-api.md#errors).
