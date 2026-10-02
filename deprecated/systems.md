---
description: The systems endpoints, which Rotur Apps replaced on 2 October 2026.
---

# Systems

{% hint style="warning" %}
Systems are deprecated. Every system is now a [Rotur App](../build-an-app/migrate-from-systems.md). These endpoints still work, and every response carries `Deprecation: true` and a `Link` header to the migration notes.
{% endhint %}

A system was a platform registered on Rotur, such as originOS, with its own badges and the accounts made on it. Each became an app with the same name, icon, badges and owner.

| Method | Path | Auth | What it did | Use instead |
| --- | --- | --- | --- | --- |
| GET | `/systems`, `/v2/systems` | None | List systems | Still works. Apps have [public details](../build-an-app/apps-api.md#public-details) |
| GET | `/system/users`, `/v2/systems/users` | Token with `groups:view`, from the app's owner or a manager. Takes `?system=` | List the usernames of a system's accounts | [`GET /v2/apps/<app>/users`](../build-an-app/users.md) |
| PATCH | `/v2/systems` (v1: `POST /update_system`) | Token with `account:settings`, from the app's owner or a manager. Body `{system, key, value}` | Edit a system | Your app's settings on rotur.dev |
| POST | `/v2/systems/reload` (v1: `GET /reload_systems`) | Rotur staff only | Reload the list | Not needed |
| GET | `/v2/systems/<system>/badges` | None | List badges | [`GET /v2/apps/<app>/badges`](../build-an-app/badges.md#list-your-badges) |
| POST, PUT, DELETE | `/v2/systems/<system>/badges[/<badge>]` | Token with `account:settings`, from the app's owner or a manager | Manage badges | [Badge endpoints](../build-an-app/badges.md) with app credentials |
| PUT, PATCH, DELETE | `/v2/systems/<system>/badges/<badge>/users/<username>` | Token with `account:settings`, from the app's owner or a manager | Give or take badges | [Give a badge](../build-an-app/badges.md#give-a-badge) with app credentials |

The `system` field when creating an account, and the `system` parameter of [rotur.dev/auth](../accounts-and-tokens/rotur-dev-auth.md), still work. New code should sign people in with [Sign in with Rotur](../build-an-app/sign-people-in.md), which records the app they joined through.
