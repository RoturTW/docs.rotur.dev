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

## Choose your path

{% hint style="success" %}
**Most apps want Sign in with Rotur.** If you're not sure, start there.
{% endhint %}

| You want to | Use | Start with |
| --- | --- | --- |
| Add a Sign in with Rotur button to a website, with one script tag | **signin.js** | [Add Sign in with Rotur in two lines](build-an-app/sign-in-button.md) |
| Let people sign in to your website, game or app with their Rotur account | **Sign in with Rotur** (OAuth 2.0) | [Quickstart](build-an-app/quickstart.md) |
| Act on someone's account: post for them, read their friends, spend credits | A **Rotur account token** | [Act on a user's account](accounts-and-tokens/README.md) |
| Build for originOS, or in TurboWarp or MistWarp | **The Rotur Extension** | [Connect to Rotur](the-rotur-extension/connecting-to-rotur.md) |
| Write JavaScript against the whole API | **The Rotur SDK** (`npm install rotur-sdk`) | [Rotur SDK](rotur-sdk/README.md) |
| Look up an endpoint | **The API reference** | [How the API works](api-reference/README.md) |
| Move a system over to Rotur Apps | **Rotur Apps** | [Migrate from systems](build-an-app/migrate-from-systems.md) |

## Sign in with Rotur in two lines

On a website, this is all you need:

```html
<script src="https://rotur.dev/signin.js" data-client-id="app_YOUR_ID" data-on-sign-in="signedIn"></script>
<div data-rotur-signin></div>
```

You get a Sign in with Rotur button, and in Chromium browsers a "Continue as …" prompt for anyone already signed in. [Add Sign in with Rotur in two lines](build-an-app/sign-in-button.md) covers setup and options.

## Sign in with Rotur in four steps

For anything that isn't a web page, or if you'd rather not use the script:

1. Create an app at [rotur.dev/me/developer](https://rotur.dev/me/developer) and add a redirect URI. You get a client ID (`app_…`) and a secret (`rsec_…`).
2. Send people to `https://api.rotur.dev/oauth/authorize` with PKCE.
3. Swap the code they come back with for a token at `https://api.rotur.dev/oauth/token`.
4. Read who they are from `https://api.rotur.dev/oauth/userinfo`, and key them by `sub`.

The [Quickstart](build-an-app/quickstart.md) has complete code for a browser-only app, a Node.js server and curl.

## Get help

* Website: [rotur.dev](https://rotur.dev)
* Chat with us in the official originChats server: [originchats.mistium.com](https://originchats.mistium.com?server=chats.mistium.com)
* Security problems: security@rotur.dev

Rotur started on 29 June 2024 to connect TurboWarp and MistWarp based operating systems. It now also runs services such as originChats and the Rotur suite.
