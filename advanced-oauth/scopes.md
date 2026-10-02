---
description: What each scope gives you, how auto works, and what people see when they sign in.
---

# Scopes and the consent screen

Send scopes space-separated in the `scope` parameter of [`/oauth/authorize`](authorize.md#1-send-the-person-to-rotur).

| Scope | You get | Who it works for |
| --- | --- | --- |
| `profile` | `sub`, `username`, `name`, `picture` and `profile` from [`/oauth/userinfo`](authorize.md#5-find-out-who-signed-in) | Everyone. Always included, even if you leave it out |
| `email` | `email` and `email_verified` as well | Only people known to be 18 or over |
| `offline_access` | A [refresh token](authorize.md#4-refresh) | Everyone |
| A permission, such as `posts:create` | The access token can do what that permission allows, as the person | Everyone, except `account:email` and `account:signins`, which are only honoured for adults |
| `auto` | Every scope your app is known to use (see below) | |

The permissions are the same as for Token Manager tokens: see [Permissions](../accounts-and-tokens/tokens/permissions.md). Apps can't ask for `tokens:manage` or `account:delete`. Rotur has no `openid` scope, and asking for any scope it doesn't have sends the person back with `error=invalid_scope`.

## auto

Rotur keeps a list of the scopes each app uses. When the app's owner or a manager approves signing in to it, Rotur adds the scopes the app asked for to the list. `auto` stands for the whole list, so an app that asks for `auto` asks everyone else for everything at once. The JavaScript SDK asks for `auto offline_access`.

`auto` can sit alongside other scopes: `auto posts:create` asks for the list and `posts:create`. The list is in [your app's settings](../build-an-app/apps-api.md#app-settings) as `scopes`. Your team can take scopes off it with `PATCH /v2/apps/<app>` and `{"scopes": [...]}`, keeping only those listed, but only asking for a scope adds it back.

## The email scope

Ask for `email` only if you need it. Rotur gives you an email address only when:

* the person allows it on the consent screen, and
* Rotur knows they are 18 or over.

For everyone else, Rotur drops the scope. The sign-in still works, the token response's `scope` leaves out `email`, and `/oauth/userinfo` has no `email` field. Your app must work without an email address.

Check `email_verified` before you trust an address. Never use the email address to identify someone: use `sub`.

## What people see

When your app sends someone to Rotur, they:

1. Sign in to Rotur, or confirm the account they're already signed in with. They can switch accounts or create one.
2. See your app's name and what it will get. They can turn off anything except the profile.
3. Confirm with their password if your app asks for a permission that moves credits or reads secrets, such as `credits:transfer`.
4. Approve, or go back to your app without signing in.

Your app's name, icon and description come from your [app settings](../build-an-app/set-up-your-app.md#fill-in-your-details). Fill them in, so people know what they are signing in to.

## When someone is refused

Rotur checks whether the person may use your app before showing **Approve**. If they can't, it tells them why instead, in words meant for them. Your app is never told why, but your server learns the `code` when it [checks a validator](../build-an-app/check-whos-calling.md).

| Why | What they see |
| --- | --- |
| You banned them | "You've been banned from *your app*." with the reason you gave and when the ban ends |
| Your app declares mature content, and they aren't known to be 18 or over | "*Your app* is for adults only." |
| A parent has turned off apps for them | "A parent has turned off signing in to apps on your account." |
| A parent only allows apps they approve, and hasn't approved yours | "A parent needs to approve *your app* first." |
| Their Rotur account can't be used right now | "Your Rotur account can't sign in to apps right now." |
| Rotur has suspended your app | "*Your app* has been suspended by Rotur." |

From there they can go back to your app, which sends you `error=access_denied`.
