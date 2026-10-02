---
description: Systems are now Rotur Apps. What changed, and how to move your code over.
---

# Migrate from systems

On 2 October 2026, Rotur Apps replaced systems. Every system became an app automatically. Nothing stops working today, but the systems endpoints are deprecated, so move over when you can.

## What happened to your system

Each system became an app with:

* the same name (cut to 40 characters), icon and badges;
* the same owner, now tied to the account's ID rather than its username, so renaming your account no longer affects it;
* a slug made from the system's name;
* your badges' IDs unchanged. They keep the system's name as their prefix, so badges people already have, and how they've hidden or ordered them, are untouched;
* nothing declared in its safety settings, and a prompt to review them.

Accounts made on your system count as your app's users for badges and signals. Your app is their origin app, so their profiles show your app's [origin badge](badges.md#the-origin-badge) in place of the old system badge. Unlike the old badge, they can hide it.

The share of daily credit claims that went to a system's owner now goes to the owner of the app it became.

## What to do

{% stepper %}
{% step %}
### Find your app

Open [rotur.dev/me/developer](https://rotur.dev/me/developer). Your system is listed under **Apps**.
{% endstep %}

{% step %}
### Review its safety declarations

Open the app's **Safety** page and declare whether people can talk, whether there's mature content and whether people can spend. See [Declarations and safety signals](safety.md).
{% endstep %}

{% step %}
### Make a secret

Migrated apps start without a secret. As the owner, choose **New secret** in the app's settings and store it on your server.
{% endstep %}

{% step %}
### Move sign-in to Sign in with Rotur

Add a redirect URI and follow the [Quickstart](quickstart.md). Sign in with Rotur records which app people joined through, and Rotur's ban, age and parental checks apply to it.
{% endstep %}

{% step %}
### Move your badge calls

Call the apps API with your app's credentials instead of a user token. The paths have the same shape.
{% endstep %}
{% endstepper %}

## Old and new endpoints

| Systems (deprecated) | Apps |
| --- | --- |
| `GET /v2/systems/<system>/badges` | `GET /v2/apps/<app>/badges` |
| `POST /v2/systems/<system>/badges` | `POST /v2/apps/<app>/badges` |
| `PUT /v2/systems/<system>/badges/<badge>` | `PUT /v2/apps/<app>/badges/<badge>` |
| `DELETE /v2/systems/<system>/badges/<badge>` | `DELETE /v2/apps/<app>/badges/<badge>` |
| `PUT` or `PATCH /v2/systems/<system>/badges/<badge>/users/<username>` | `PUT` or `PATCH /v2/apps/<app>/badges/<badge>/users/<user>` |
| `DELETE /v2/systems/<system>/badges/<badge>/users/<username>` | `DELETE /v2/apps/<app>/badges/<badge>/users/<user>` |
| `GET /v2/systems/users` | `GET /v2/apps/<app>/users` (only lists people once they sign in through your app) |
| `PATCH /v2/systems` | Edit your app's details on rotur.dev |
| The `system` field when creating an account | The `app` field (your app's ID or slug), or Sign in with Rotur, which records it for you |

The systems endpoints keep working for now. Every response has a `Deprecation: true` header and a `Link` header pointing to the migration notes. Managing a system through them needs the owner or a manager of the app it became.

`GET /v2/systems` (the list of systems) and the `system` parameter of [rotur.dev/auth](../accounts-and-tokens/rotur-dev-auth.md) also still work.

If you use the JavaScript SDK, `rotur.systems` calls the deprecated endpoints. The SDK has no apps methods yet, so call the apps API directly.
