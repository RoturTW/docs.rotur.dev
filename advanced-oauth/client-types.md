---
description: Whether your app uses a secret to sign people in, and how to set it up.
---

# Public and confidential clients

Every app signs people in with PKCE. What differs is whether it also proves who it is with a secret.

| | Confidential client | Public client |
| --- | --- | --- |
| What it is | An app with a server you control | An app that runs entirely in a browser or on someone's computer |
| Examples | A website with a backend, a Discord bot's web dashboard | A static site, a single-page app, a desktop app, a TurboWarp project |
| Setting | **This app can't keep a secret** unticked (the default) | **This app can't keep a secret** ticked |
| Token request | Secret in HTTP Basic, or `client_id` and `client_secret` in the body | `client_id` only, no secret |
| PKCE | Required | Required |

Change the setting in your app's settings on [rotur.dev/me/developer](https://rotur.dev/me/developer). Apps that use the [JavaScript SDK](../build-an-app/sign-people-in.md) are public clients.

## Confidential clients

The code exchange happens on your server, so the secret never reaches the browser.

```sh
curl https://api.rotur.dev/oauth/token \
  -u "app_0123456789abcdef:$ROTUR_CLIENT_SECRET" \
  -d grant_type=authorization_code \
  -d code="$CODE" \
  -d redirect_uri=https://example.com/callback \
  -d code_verifier="$VERIFIER"
```

If a confidential client leaves out the secret, the token endpoint answers `401 invalid_client`.

## Public clients

The code exchange happens in the browser or the program, with no secret:

```sh
curl https://api.rotur.dev/oauth/token \
  -d grant_type=authorization_code \
  -d client_id=app_0123456789abcdef \
  -d code="$CODE" \
  -d redirect_uri=http://127.0.0.1:8765/callback \
  -d code_verifier="$VERIFIER"
```

The token and user info endpoints allow requests from any website, so `fetch` works from the browser without a proxy. [Complete examples](examples.md) has one.

PKCE is what keeps a public client safe: only the program that started the sign-in knows the verifier, so a stolen code is useless on its own.

{% hint style="warning" %}
Never ship a secret inside a web page or a program people download. Anyone can read it. If your app can't keep a secret, make it a public client instead.
{% endhint %}

A public app can still have secrets. You need one to call the [apps API](../build-an-app/app-secret.md), which only works from a server.

## Desktop and command-line apps

Redirect URIs must be `https://`, or `http://` on `localhost` or `127.0.0.1`. Custom schemes such as `myapp://` aren't allowed. A desktop app can:

1. Start a small web server on a fixed local port, for example `http://127.0.0.1:8765/callback`, and register that exact address, port included.
2. Open the authorisation URL in the person's browser.
3. Read the `code` when the browser comes back to the local server, then swap it for a token.

The port must match the registered redirect URI exactly, so pick one and stick to it. You can register up to 10 redirect URIs if you need fallbacks.

If your program can't open a browser and listen locally, for example on a games console, [linking with a code](../accounts-and-tokens/linking.md) may suit it better.
