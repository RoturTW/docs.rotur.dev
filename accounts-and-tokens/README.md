---
description: Rotur account tokens, for Rotur's own clients, tools on your own account, and devices that link with a code.
---

# Act on a user's account

Apps for other people don't need this section. [Sign in with Rotur](../build-an-app/sign-people-in.md) lets an app post for someone, read their friends and so on: the SDK asks the person for each permission the app uses.

**Rotur account tokens** are for Rotur's own clients, for tools you run on your own account, and for devices that link with a code. A token holds only the permissions the person chooses.

{% hint style="warning" %}
Rotur's ban, age and parental checks for apps, and your app's users, bans and reports, only work with Sign in with Rotur.
{% endhint %}

## Sign in with Rotur or an account token?

| | Sign in with Rotur | Rotur account token |
| --- | --- | --- |
| What your app can do | Whatever permissions the person gives it | Whatever permissions the person gives it |
| How long it lasts | An hour, renewed by the SDK until the person signs out or leaves your app | Until the person revokes it, it expires, or it goes unused for 30 days |
| Needs a Rotur App | Yes | No |
| Rotur enforces your bans and its age and parental checks | Yes | No |
| Standard | OAuth 2.0 | Rotur's own hand-off |

## Ways to get an account token

| Your app | Use |
| --- | --- |
| A website | [Get a token with rotur.dev/auth](rotur-dev-auth.md). The person signs in on rotur.dev, picks permissions, and the token comes back to your page |
| A JavaScript app | The [Rotur SDK](../rotur-sdk/account/authentication.md)'s `rotur.login()`, which wraps rotur.dev/auth |
| A desktop app, game or device | [Link with a code](linking.md). Your app shows a code, and the person enters it at rotur.dev/link |
| A TurboWarp or MistWarp project | [The Rotur Extension](../the-rotur-extension/connecting-to-rotur.md) |

Once you have a token, send it as `Authorization: Bearer <token>` to the endpoints in the [API reference](../api-reference/README.md). Each endpoint lists the permission it needs.

## Related

* [Permissions](tokens/permissions.md): every permission a token can hold.
* [Manage tokens](tokens/README.md): list, create, edit and revoke tokens on your own account.
* [Validators](validators.md): prove to your server that someone owns a Rotur account, without sending it their token.
