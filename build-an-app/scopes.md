---
description: What each scope gives you, and what people see when they sign in.
---

# Scopes and the consent screen

Sign in with Rotur has two scopes. Send them space-separated in the `scope` parameter of `/oauth/authorize`.

| Scope | You get | Who it works for |
| --- | --- | --- |
| `profile` | `sub`, `username`, `name`, `picture` and `profile` from [`/oauth/userinfo`](sign-users-in.md#4-find-out-who-signed-in) | Everyone. Always included, even if you leave it out |
| `email` | `email` and `email_verified` as well | Only people known to be 18 or over |

There are no other scopes. Rotur has no `openid`, `offline_access` or permission scopes, and asking for any of them sends the person back with `error=invalid_scope`.

## The email scope

Ask for `email` only if you need it. Rotur gives you an email address only when:

* the person approves it on the consent screen, and
* Rotur knows they are 18 or over.

For everyone else, Rotur quietly drops the scope. The sign-in still works, the token response's `scope` is just `profile`, and `/oauth/userinfo` has no `email` field. Your app must work without an email address.

Check `email_verified` before you trust an address. Never use the email address to identify someone: use `sub`.

## What people see

When your app sends someone to Rotur, they:

1. Sign in to Rotur, or confirm the account they're already signed in with. They can switch accounts or create one.
2. See your app's name and what it will receive:
   * with `profile`: "*Your app* will receive your Rotur username and public profile details."
   * with `email`: "*Your app* will receive your Rotur username, profile details, and email address."
3. Approve, or go back to your app without signing in.

Your app's name, icon and description come from your [app settings](set-up-your-app.md#fill-in-your-details). Fill them in, so people know what they are signing in to.

## When someone is refused

Rotur checks whether the person may use your app before showing **Approve**. If they can't, it tells them why instead, in words meant for them. Your app is never told why.

| Why | What they see |
| --- | --- |
| You banned them | "You've been banned from *your app*." with the reason you gave and when the ban ends |
| Your app declares mature content, and they aren't known to be 18 or over | "*Your app* is for adults only." |
| A parent has turned off apps for them | "A parent has turned off signing in to apps on your account." |
| A parent only allows apps they approve, and hasn't approved yours | "A parent needs to approve *your app* first." |
| Their Rotur account is banned or suspended | "Your Rotur account can't sign in to apps right now." |
| Rotur has suspended your app | "*Your app* has been suspended by Rotur." |

From there they can go back to your app, which sends you `error=access_denied`.
