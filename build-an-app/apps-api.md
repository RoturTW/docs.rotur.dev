---
description: Call Rotur from your server with your app's client ID and secret.
---

# Call the apps API

The apps API lets your server manage your app: give badges, ban people, file and handle reports, list your users and ask safety signals.

**Base URL:** `https://api.rotur.dev/v2/apps/<app>`, where `<app>` is your app's ID (`app_…`) or slug.

**Auth:** HTTP Basic, with your client ID as the username and one of your secrets as the password.

```sh
curl https://api.rotur.dev/v2/apps/app_0123456789abcdef \
  -u "app_0123456789abcdef:$ROTUR_CLIENT_SECRET"
```

```js
const auth = "Basic " + Buffer.from(`${CLIENT_ID}:${CLIENT_SECRET}`).toString("base64");
const app = await fetch(`https://api.rotur.dev/v2/apps/${CLIENT_ID}`, {
  headers: { Authorization: auth },
}).then((response) => response.json());
```

Your credentials only work on your own app, and only about your own users. Call the apps API from your server: it needs a secret, so it can't run in a browser.

{% hint style="info" %}
The apps API is separate from Sign in with Rotur. You don't need a signed-in user to call it, and an access token from Sign in with Rotur won't work here, except to make and check payment requests for the person it belongs to. [Use your app secret on the server](app-secret.md) has examples in several languages.
{% endhint %}

## Endpoints

"App" means your client ID and secret. "Dashboard" means the owner or a manager, signed in on rotur.dev: those actions are done from [rotur.dev/me/developer](https://rotur.dev/me/developer), not from your server.

| Method | Path | Who | What |
| --- | --- | --- | --- |
| GET | `/v2/apps/<app>/public` | Anyone | [Your app's public details](#public-details) |
| GET | `/v2/apps/<app>` | App | [Your app's settings](#app-settings) |
| GET | `/v2/apps/<app>/users` | App | [List your users](users.md) |
| GET | `/v2/apps/<app>/badges` | Anyone | [List your badges](badges.md) |
| POST | `/v2/apps/<app>/badges` | App | [Create a badge](badges.md#create-a-badge) |
| PUT | `/v2/apps/<app>/badges/<badge>` | App | [Update a badge](badges.md#update-or-delete-a-badge) |
| DELETE | `/v2/apps/<app>/badges/<badge>` | App | [Delete a badge](badges.md#update-or-delete-a-badge) |
| PUT, PATCH | `/v2/apps/<app>/badges/<badge>/users/<user>` | App | [Give a badge or set progress](badges.md#give-a-badge) |
| DELETE | `/v2/apps/<app>/badges/<badge>/users/<user>` | App | [Take a badge away](badges.md#take-a-badge-away) |
| GET | `/v2/apps/<app>/bans` | App | [List bans](bans.md#list-bans) |
| PUT | `/v2/apps/<app>/bans/<user>` | App | [Ban someone](bans.md#ban-someone) |
| DELETE | `/v2/apps/<app>/bans/<user>` | App | [Lift a ban](bans.md#lift-a-ban) |
| POST | `/v2/apps/<app>/reports` | App, or a user's token with `reports:create` | [File a report](handle-reports.md#file-a-report-from-your-app) |
| GET | `/v2/apps/<app>/reports` | App | [List reports](handle-reports.md#list-reports) |
| POST | `/v2/apps/<app>/reports/<id>/resolve` | App | [Close a report](handle-reports.md#close-a-report) |
| POST | `/v2/apps/<app>/reports/<id>/escalate` | App | [Send a report to Rotur](handle-reports.md#send-a-report-to-rotur) |
| POST | `/v2/apps/<app>/signals/message` | App only | [May they message?](safety.md#may-they-message) |
| POST | `/v2/apps/<app>/signals/purchase` | App only | [May they spend?](safety.md#may-they-spend) |
| POST | `/v2/apps/<app>/payment-requests` | App, or the payer's Sign in with Rotur token | [Take payments](payments.md) |
| GET | `/v2/apps/<app>/payment-requests/<id>` | App, or the payer's token | [Take payments](payments.md) |
| POST | `/v2/apps/<app>/payment-requests/<id>/cancel` | App, or the payer's token | [Take payments](payments.md) |
| PATCH | `/v2/apps/<app>` | Dashboard | Edit details and redirect URIs |
| POST, DELETE | `/v2/apps/<app>/secrets` | Dashboard, owner | Make and revoke secrets |
| PUT, DELETE | `/v2/apps/<app>/managers/<username>` | Dashboard | Add and remove managers |
| PUT | `/v2/apps/<app>/declarations` | Dashboard, owner | [Safety declarations](safety.md#declare-what-your-app-does) |
| GET, PUT, DELETE | `/v2/apps/<app>/webhook` | Dashboard, owner | [Webhook settings](webhooks.md#set-up-a-webhook) |
| POST | `/v2/apps/<app>/webhook/test` | Dashboard, owner | Send a test delivery |
| DELETE | `/v2/apps/<app>` | Dashboard, owner | Delete the app |

Wherever a path takes `<user>`, you can use a username or a Rotur user ID. Prefer the ID (`sub` from sign-in): it never changes.

## Public details

`GET /v2/apps/<app>/public`

What anyone can see about an app. Rotur shows this on the consent screen, on profiles and on your report page.

**Auth:** None.

```sh
curl https://api.rotur.dev/v2/apps/originchats/public
```

**Response `200`:**

```json
{
  "id": "app_bcdcfb6d22690806",
  "slug": "originchats",
  "name": "originChats",
  "icon": "",
  "description": "",
  "website": "",
  "mature": false,
  "suspended": false
}
```

**Errors:** `404` `App not found`.

## App settings

`GET /v2/apps/<app>`

**Auth:** App credentials, or the dashboard.

**Response `200`:**

```json
{
  "id": "app_0123456789abcdef",
  "client_id": "app_0123456789abcdef",
  "slug": "sketchpad",
  "name": "Sketchpad",
  "icon": "https://example.com/icon.png",
  "description": "Draw together.",
  "website": "https://sketchpad.example",
  "redirect_uris": ["https://sketchpad.example/callback"],
  "public_client": false,
  "owner": { "id": "7ebdf483-…", "username": "kit", "avatar": "https://avatars.rotur.dev/7ebdf483-…" },
  "managers": [],
  "secrets": [{ "id": "a1b2c3d4e5f6a7b8", "hint": "x9Qz", "created_at": 1790000000000, "last_used_at": 1790003600000 }],
  "declarations": { "communication": true, "mature": false, "spending": false, "reviewed": true, "updated_at": 1790000000000 },
  "webhook": { "url": "https://sketchpad.example/rotur-webhook", "events": ["user.deleted", "user.left", "user.banned"], "created_at": 1790000000000 },
  "member_count": 42,
  "ban_count": 1,
  "open_reports": 0,
  "escalated_reports": 0,
  "badge_prefix": "sketchpad",
  "role": "app",
  "suspended": null,
  "created_at": 1790000000000,
  "updated_at": 1790000000000
}
```

Secrets show only their last four characters. The webhook never shows its signing secret. `role` is `app` when you call with your credentials, or `owner` or `manager` from the dashboard. `legacy_system` appears on apps that used to be [systems](migrate-from-systems.md). Times are Unix milliseconds.

## Errors

Errors are JSON with `error`, a sentence you can log, and usually `code`.

```json
{ "error": "App credentials are invalid", "code": "invalid_client" }
```

| Status | `code` | Why |
| --- | --- | --- |
| `400` | | The request body isn't valid JSON (`Invalid request body`), or a field is wrong. `error` says which |
| `401` | `invalid_client` | Wrong client ID or secret, or the credentials belong to another app (`App credentials are invalid`). Or you called an app-only endpoint without credentials (`This needs the app's client ID and secret`) |
| `403` | `app_team_only` | That action is only for the owner or managers on rotur.dev, not for app credentials |
| `403` | `app_suspended` | Rotur has suspended your app |
| `404` | | The user, badge, ban or report doesn't exist, or the user isn't one of your app's users. `error` says which |
| `409` | | A conflict, such as a badge that already exists or a report that is already with Rotur |
| `429` | | Too many requests. Most endpoints allow 100 a minute from each IP address |

Failed authentication never says whether an app exists.
