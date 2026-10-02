---
description: Every Sign in with Rotur and Rotur Apps endpoint, with links to the full docs.
---

# Apps and OAuth endpoints

An index of the endpoints for [building an app](../build-an-app/README.md). Each row links to the page that documents it.

## Sign in with Rotur

| Method | Path | Auth | Docs |
| --- | --- | --- | --- |
| GET | `/oauth/authorize` | None (a browser redirect) | [Send the person to Rotur](../advanced-oauth/authorize.md#1-send-the-person-to-rotur) |
| POST | `/oauth/token` | Client credentials, or `client_id` for public clients | [Swap the code for tokens](../advanced-oauth/authorize.md#3-swap-the-code-for-tokens), [Refresh](../advanced-oauth/authorize.md#4-refresh) |
| GET | `/oauth/userinfo` | Access token | [Find out who signed in](../advanced-oauth/authorize.md#5-find-out-who-signed-in) |
| GET | `/.well-known/oauth-authorization-server` | None | [Endpoints](../advanced-oauth/README.md#endpoints) |

`/oauth/request`, `/oauth/approve` and `/oauth/deny` are used by Rotur's own consent screen. Don't call them from your app.

## Your app

All under `https://api.rotur.dev`. "App" means HTTP Basic with your client ID and secret.

| Method | Path | Auth | Docs |
| --- | --- | --- | --- |
| GET | `/v2/apps/<app>/public` | None | [Public details](../build-an-app/apps-api.md#public-details) |
| GET | `/v2/apps/<app>` | App | [App settings](../build-an-app/apps-api.md#app-settings) |
| GET | `/v2/apps/<app>/users` | App | [See who uses your app](../build-an-app/users.md) |
| GET | `/v2/apps/<app>/badges` | None | [Give badges](../build-an-app/badges.md#list-your-badges) |
| POST | `/v2/apps/<app>/badges` | App | [Create a badge](../build-an-app/badges.md#create-a-badge) |
| PUT, DELETE | `/v2/apps/<app>/badges/<badge>` | App | [Update or delete a badge](../build-an-app/badges.md#update-or-delete-a-badge) |
| PUT, PATCH | `/v2/apps/<app>/badges/<badge>/users/<user>` | App | [Give a badge](../build-an-app/badges.md#give-a-badge) |
| DELETE | `/v2/apps/<app>/badges/<badge>/users/<user>` | App | [Take a badge away](../build-an-app/badges.md#take-a-badge-away) |
| GET | `/v2/apps/<app>/bans` | App | [List bans](../build-an-app/bans.md#list-bans) |
| PUT | `/v2/apps/<app>/bans/<user>` | App | [Ban someone](../build-an-app/bans.md#ban-someone) |
| DELETE | `/v2/apps/<app>/bans/<user>` | App | [Lift a ban](../build-an-app/bans.md#lift-a-ban) |
| POST | `/v2/apps/<app>/reports` | App, or a token with `reports:create` | [File a report](../build-an-app/handle-reports.md#file-a-report-from-your-app) |
| GET | `/v2/apps/<app>/reports` | App | [List reports](../build-an-app/handle-reports.md#list-reports) |
| POST | `/v2/apps/<app>/reports/<id>/resolve` | App | [Close a report](../build-an-app/handle-reports.md#close-a-report) |
| POST | `/v2/apps/<app>/reports/<id>/escalate` | App | [Send a report to Rotur](../build-an-app/handle-reports.md#send-a-report-to-rotur) |
| POST | `/v2/apps/<app>/signals/message` | App only | [May they message?](../build-an-app/safety.md#may-they-message) |
| POST | `/v2/apps/<app>/signals/purchase` | App only | [May they spend?](../build-an-app/safety.md#may-they-spend) |
| POST | `/v2/apps/<app>/payment-requests` | App, or the payer's Sign in with Rotur token | [Take payments](../build-an-app/payments.md) |
| GET | `/v2/apps/<app>/payment-requests/<id>` | App, or the payer's token | [Take payments](../build-an-app/payments.md) |
| POST | `/v2/apps/<app>/payment-requests/<id>/cancel` | App, or the payer's token | [Take payments](../build-an-app/payments.md) |

The endpoints for editing your app, its secrets, managers, declarations and webhook are only for its owner and managers signed in on rotur.dev. See [Call the apps API](../build-an-app/apps-api.md#endpoints).

## For the people who use apps

These are for Rotur's own pages, signed in with the account itself. Apps can't call them.

| Method | Path | What |
| --- | --- | --- |
| GET | `/v2/me/apps` | The apps the account uses |
| DELETE | `/v2/me/apps/<app>` | Leave an app. Revokes its tokens and sends it `user.left` |
| POST | `/v2/me/apps/<app>/privacy-request` | Ask an app for a copy of your data or to delete it (`{"kind": "access"}` or `{"kind": "erasure"}`) |
| GET | `/v2/apps/mine` | The apps the account owns or manages |
| POST | `/v2/apps` | Create an app |
| GET | `/v2/developer-terms` | The current Developer Terms version, and whether the account accepted it |
