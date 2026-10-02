---
description: Create a Rotur App, add redirect URIs, manage secrets and add people to your team.
---

# Set up your app

A Rotur App is how your website, game or program connects to Rotur. It gives you:

* A **client ID** (`app_` and 16 characters), which is also your app's ID.
* **Secrets** (`rsec_…`) for your server.
* **Redirect URIs**, the pages people sign in from.
* A dashboard for your app's users, bans, reports, badges, webhooks and safety settings.

Anyone with a Rotur account can create apps. You can own up to 10.

## Create an app

1. Go to [rotur.dev/me/developer](https://rotur.dev/me/developer) and choose **Create app**.
2. Fill in:
   * **Name**: 1 to 40 characters. People see it when they sign in.
   * **Slug**: filled in from the name. 3 to 32 lowercase letters, numbers, `-` and `_`, starting with a letter or number. It appears in links (such as your [report page](handle-reports.md#use-the-hosted-report-page)) and in your badge IDs.
   * **Redirect URI**: optional here, but you need at least one before anyone can sign in.
3. The first time, accept the [Developer Terms](developer-terms.md).
4. Choose **Create app**. Copy the secret Rotur shows you. It won't be shown again.

## Fill in your details

Open your app from [rotur.dev/me/developer](https://rotur.dev/me/developer) and go to its settings. People see these on the consent screen, on profiles and on your report page.

| Field | Rules |
| --- | --- |
| Name | 1 to 40 characters |
| Slug | As above. Must be unique |
| Icon | An `https://` image URL or an ICN glyph, up to 2000 characters |
| Description | Up to 300 characters |
| Website | An `https://` address, or empty |

Anyone can read these through [`GET /v2/apps/<app>/public`](apps-api.md#public-details).

## Add redirect URIs

A redirect URI is where Rotur sends people back to after they approve or deny your app. You can add up to 10, one per line.

* It must use `https://`. The only exception is `http://localhost` and `http://127.0.0.1`, for testing.
* It can't contain a fragment (`#…`) or a username and password.
* The `redirect_uri` you send when signing someone in must match one of them **exactly**. `https://example.com/callback` and `https://example.com/callback/` are different.
* With the [JavaScript SDK](sign-people-in.md), add the address of each page people sign in from. The sign-in window only needs the page's origin to match, but if the browser blocks the window, the page itself goes to Rotur and comes back, and then its address must match exactly.

Add one for each place your app runs, for example your live site and `http://localhost:3000/callback` for development.

## Choose public or confidential

Apps that sign people in with the [JavaScript SDK](sign-people-in.md), or that run entirely on someone's computer, tick **This app can't keep a secret**. Sign-in then finishes without a secret. Your server can still use a secret for the [apps API](app-secret.md). See [Public and confidential clients](../advanced-oauth/client-types.md).

## Manage secrets

Your server uses a secret to swap sign-in codes for tokens and to call the [apps API](apps-api.md). Only the owner can make and revoke secrets.

* Every app starts with one secret, shown when you create the app.
* You can have up to 5. Choose **New secret** to make another. It is shown once: Rotur only keeps a hash and the last four characters.
* To rotate a secret without downtime: make a new one, move your server to it, then revoke the old one.
* Revoking a secret stops it working straight away.

{% hint style="danger" %}
Keep secrets on your server. Never put one in a web page, a downloadable app or a public repository. If one leaks, revoke it at once.
{% endhint %}

## Add managers

The owner can add up to 3 managers by username. Managers can:

* edit the app's details and redirect URIs;
* see users, give badges, ban people and handle reports.

Only the owner can manage secrets, the webhook, the team and the safety declarations, or delete the app. A manager can remove themselves.

If the owner deletes their Rotur account, the first manager becomes the owner.

## Delete an app

The owner can delete an app from its settings by typing its slug to confirm. This can't be undone: its secrets, users list, bans, badges and its own copies of reports go with it. Reports already sent to Rotur stay with Rotur.

## If Rotur suspends your app

Rotur can suspend an app that breaks the [Developer Terms](developer-terms.md). While it is suspended, nobody can sign in, existing tokens stop working, the apps API answers `403` with `app_suspended`, and its badges are hidden. The owner sees why on the app's page.
