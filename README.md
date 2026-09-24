![](https://github.com/user-attachments/assets/59384c74-a8c4-4707-9931-f6a3364395dc)

# Getting started

Rotur provides accounts, authentication and social features that your app can use instead of building its own. Every Rotur user can sign in to your app with their existing account.

Rotur covers:

* Accounts and authentication, including sub-tokens with scoped permissions
* Friends, following and posts
* Profile avatars and banners, served from one place
* Live account updates over the status websocket
* An economy of credits, items, keys and cosmetics
* Groups, and a way to run Discord-style rich presence and activities from the web

## Where to start

| You are building | Start here |
| --- | --- |
| A web or JavaScript app | The [Rotur SDK](rotur-sdk/README.md): `npm install rotur-sdk` |
| Anything that calls HTTP directly | The REST API at `https://api.rotur.dev`. Endpoint docs are in the sidebar, starting with [APIs](assorted-apis/keys.md) and [Claw](claw/what-is-claw.md). |
| A TurboWarp or MistWarp project | [The Rotur Extension](the-rotur-extension/connecting-to-rotur.md) |

{% hint style="info" %}
The old websocket docs are in the [Deprecated](deprecated/what-is-a-websocket.md) section. Use the REST API or the SDK for new projects.
{% endhint %}

## Background

Rotur started on June 29, 2024, to connect TurboWarp and MistWarp based operating systems. It now also runs services such as originChats and the Rotur suite.

## Links

* Website: [rotur.dev](https://rotur.dev)
* Docs: [docs.rotur.dev](https://docs.rotur.dev)
* Chat with us in the official originChats server: [originchats.mistium.com](https://originchats.mistium.com?server=chats.mistium.com)
