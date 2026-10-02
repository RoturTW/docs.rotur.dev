---
description: Sign people in with Rotur in the browser, check who is calling on your server in any language, and manage your app with its secret.
---

# Build an app

A Rotur app has three parts. A page with no server only needs the first.

1. **In the browser**, the JavaScript SDK signs people in and calls your server with `rotur.fetch`. See [Sign people in](sign-people-in.md) and [Call your server](call-your-server.md).
2. **On your server**, in any language, one HTTP call to Rotur tells you who sent each request. It needs no secret. See [Check who's calling](check-whos-calling.md).
3. **Also on your server**, your app's secret lets you give badges, ban people, ask for payments and handle reports. Rotur sends your server webhooks too. See [Use your app secret on the server](app-secret.md) and [Receive webhooks](webhooks.md).

## Create your app

1. Go to [rotur.dev/me/developer](https://rotur.dev/me/developer) and choose **Create app**. The first time, accept the [Developer Terms](developer-terms.md).
2. Under **Redirect URIs**, add the address of each page people sign in from, such as `https://example.com/`. For testing, `http://localhost:3000/` works too.
3. In the app's settings, tick **This app can't keep a secret**. Sign-in then finishes in the browser. Your server still has a secret for step 3.
4. Copy the app ID (`app_…`) and the secret (`rsec_…`). The secret is shown once.

[Set up your app](set-up-your-app.md) covers the other settings: icon, description, more secrets and your team.

## The whole thing, in short

In the browser:

```js
import { Rotur } from "rotur-sdk";

const rotur = new Rotur({ app: "app_0123456789abcdef" });

signInButton.onclick = () => rotur.signIn();
rotur.onChange((user) => showUser(user)); // { id, username, name, avatar }, or null

const scores = await rotur.fetch("/api/scores").then((res) => res.json());
```

On your server, read `Authorization: Rotur <validator>` from the request and ask Rotur who made it:

```
GET https://api.rotur.dev/v2/validators/verify?v=<validator>&key=app_0123456789abcdef
→ { "valid": true, "id": "7ebdf483-…", "username": "kit", "minor": false }
```

[Check who's calling](check-whos-calling.md) has this in Go, Python, PHP and JavaScript.

## What Rotur does for you

People who can't use your app never get through: people you've banned, people banned or suspended on Rotur, under-18s on an app that declares mature content, and teens whose parent hasn't allowed it. Rotur tells them why on its own screens. Your server gets a refusal, with a `code`, when it checks their validator.

## Other ways in

* [OAuth without the SDK](../advanced-oauth/README.md): for desktop apps, games and servers that run Sign in with Rotur themselves.
* [Migrate from systems](migrate-from-systems.md): if your app used to be a Rotur system.
