---
description: Build on Rotur. Start here to pick the right path for your app.
---

![](https://github.com/user-attachments/assets/59384c74-a8c4-4707-9931-f6a3364395dc)

# Start here

Rotur is an account system and social platform you can build on. Instead of running your own sign-up, passwords, age checks and moderation, you let people use the Rotur account they already have.

When people sign in with Rotur, Rotur handles:

* **Accounts and sign-in**: passwords, passkeys, Google, GitHub and Discord sign-in, and email checks.
* **Age**: Rotur asks for a date of birth and applies each country's minimum age. Your app never sees a date of birth or an age.
* **Parental controls and teen defaults**: parents choose which apps their teen uses, who they can message and what they can spend.
* **Account safety**: people banned or suspended on Rotur can't sign in to any app.
* **Privacy requests and deletion**: Rotur tells your app when to delete someone's data.

You build your app and moderate your own content.

## Build an app

Most apps are a web page and a server:

1. **In the browser**, the JavaScript SDK signs people in and calls your server: `new Rotur({ app })`, `rotur.signIn()`, `rotur.fetch()`.
2. **On your server**, in any language, one HTTP call to Rotur tells you who sent each request. It needs no secret.
3. **With your app's secret**, your server gives badges, bans people, asks for payments and handles reports.

[Build an app](build-an-app/README.md) walks through each step, with server code in Go, Python, PHP and JavaScript.

## Choose your path

| You want to | Start with |
| --- | --- |
| Let people sign in to your website, and know who calls your server | [Build an app](build-an-app/README.md) |
| Sign people in from a desktop app, a game, or without JavaScript | [OAuth without the SDK](advanced-oauth/README.md) |
| Act on your own account, or link a device with a code | [Act on a user's account](accounts-and-tokens/README.md) |
| Build for originOS, or in TurboWarp or MistWarp | [Connect to Rotur](the-rotur-extension/connecting-to-rotur.md) with the Rotur Extension |
| Write JavaScript against the whole API | [Rotur SDK](rotur-sdk/README.md) |
| Look up an endpoint | [How the API works](api-reference/README.md) |
| Move a system over to Rotur Apps | [Migrate from systems](build-an-app/migrate-from-systems.md) |

## Get help

* Website: [rotur.dev](https://rotur.dev)
* Chat with us in the official originChats server: [originchats.mistium.com](https://originchats.mistium.com?server=chats.mistium.com)
* Security problems: security@rotur.dev

Rotur started on 29 June 2024 to connect TurboWarp and MistWarp based operating systems. It now also runs services such as originChats and the Rotur suite.
