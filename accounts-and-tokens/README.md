---
description: When your app needs to act on someone's Rotur account, not just know who they are.
---

# Act on a user's account

[Sign in with Rotur](../build-an-app/quickstart.md) tells your app who someone is. That's all it does: its tokens can only read the public profile.

Some apps need more. A Claw client posts for you, a file manager reads your files, a game spends your credits. For that, the person gives your app a **Rotur account token**: a sub-token holding only the permissions they choose.

{% hint style="warning" %}
Use account tokens only when your app really acts on the account. To let people sign in, use [Sign in with Rotur](../build-an-app/quickstart.md). Rotur's ban, age and parental checks for apps, and your app's users, bans and reports, only work with Sign in with Rotur.
{% endhint %}

## Sign in with Rotur or an account token?

| | Sign in with Rotur | Rotur account token |
| --- | --- | --- |
| What your app learns | Who signed in: ID, username, display name, avatar, and email if allowed | Whatever its permissions allow |
| What your app can do | Nothing on the account | Post, read friends, spend, manage files and so on, as permitted |
| How long it lasts | One hour | Until the person revokes it, it expires, or it goes unused for 30 days |
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
