---
description: Let people sign in to your website or app with their Rotur account, in about 15 minutes.
---

# Quickstart: Sign in with Rotur

By the end of this page, people can click **Sign in with Rotur** on your site and you get their Rotur ID, username, display name and avatar.

Sign in with Rotur is standard OAuth 2.0: the authorisation code flow with PKCE. If you have added "Sign in with Google" or "Sign in with GitHub" before, this works the same way. Any OAuth 2.0 library that supports PKCE will work too.

You need:

* A Rotur account.
* A page on your site for Rotur to send people back to. For testing, `http://localhost` is fine.

## Set up

{% stepper %}
{% step %}
### Create an app

1. Go to [rotur.dev/me/developer](https://rotur.dev/me/developer) and choose **Create app**.
2. Give it a name.
3. Under **Redirect URI**, enter the address Rotur should send people back to. For the examples below, use `http://localhost:3000/` for the browser example or `http://localhost:3000/callback` for the server example.
4. Accept the [Developer Terms](developer-terms.md) (only the first time) and choose **Create app**.

Rotur shows your app's **secret** (it starts with `rsec_`). Copy it now: it is only shown once. Your **client ID** is your app's ID, which starts with `app_`.
{% endstep %}

{% step %}
### Decide where the sign-in code runs

* **Your app has a server** (Node, Python, PHP and so on): keep the secret on the server. This is a *confidential client*. Use the server example.
* **Your app runs only in the browser or on someone's computer** (a static site, a desktop app, a TurboWarp project): it can't keep a secret. Open your app's settings and tick **This app can't keep a secret**. This is a *public client*. Use the browser example.

[Public and confidential clients](client-types.md) explains the difference.
{% endstep %}

{% step %}
### Send people to Rotur

Send the person's browser to `https://api.rotur.dev/oauth/authorize` with your client ID, your redirect URI and a PKCE challenge. Rotur asks them to sign in and approve your app.
{% endstep %}

{% step %}
### Swap the code for a token

Rotur sends them back to your redirect URI with a `code`. Post it to `https://api.rotur.dev/oauth/token` with the PKCE verifier to get an access token.
{% endstep %}

{% step %}
### Read who they are

Call `https://api.rotur.dev/oauth/userinfo` with the access token. Store people by `sub`, their Rotur user ID, which never changes. Usernames can change.
{% endstep %}
{% endstepper %}

## Copy a working example

Pick the one that matches your app. Each is complete: paste it, put in your client ID (and secret for the server), and run it.

{% tabs %}
{% tab title="Browser (no server)" %}
This is one HTML file. It sends people to Rotur, and handles them coming back to the same page.

In your app's settings, add the redirect URI `http://localhost:3000/` (with the slash) and tick **This app can't keep a secret**.

{% code title="index.html" %}
```html
<!doctype html>
<meta charset="utf-8">
<title>Sign in with Rotur</title>
<button id="sign-in">Sign in with Rotur</button>
<pre id="result"></pre>

<script type="module">
  const CLIENT_ID = "app_0123456789abcdef"; // your client ID
  const REDIRECT_URI = "http://localhost:3000/"; // exactly as you registered it
  const API = "https://api.rotur.dev";

  function base64url(bytes) {
    return btoa(String.fromCharCode(...new Uint8Array(bytes)))
      .replace(/\+/g, "-").replace(/\//g, "_").replace(/=+$/, "");
  }

  async function signIn() {
    // PKCE: a random verifier, and its SHA-256 hash as the challenge.
    const verifier = base64url(crypto.getRandomValues(new Uint8Array(32)));
    const challenge = base64url(
      await crypto.subtle.digest("SHA-256", new TextEncoder().encode(verifier)),
    );
    const state = base64url(crypto.getRandomValues(new Uint8Array(16)));
    sessionStorage.setItem("rotur_sign_in", JSON.stringify({ verifier, state }));

    const url = new URL(`${API}/oauth/authorize`);
    url.search = new URLSearchParams({
      response_type: "code",
      client_id: CLIENT_ID,
      redirect_uri: REDIRECT_URI,
      scope: "profile",
      state,
      code_challenge: challenge,
      code_challenge_method: "S256",
    });
    location.assign(url);
  }

  async function finishSignIn(params) {
    const saved = JSON.parse(sessionStorage.getItem("rotur_sign_in") ?? "null");
    sessionStorage.removeItem("rotur_sign_in");
    history.replaceState(null, "", location.pathname); // tidy the address bar

    if (!saved || params.get("state") !== saved.state) {
      throw new Error("This sign-in didn't start here. Try again.");
    }
    if (params.has("error")) {
      throw new Error(`You didn't sign in (${params.get("error")}).`);
    }

    const tokenResponse = await fetch(`${API}/oauth/token`, {
      method: "POST",
      body: new URLSearchParams({
        grant_type: "authorization_code",
        code: params.get("code"),
        redirect_uri: REDIRECT_URI,
        client_id: CLIENT_ID,
        code_verifier: saved.verifier,
      }),
    });
    const token = await tokenResponse.json();
    if (!tokenResponse.ok) throw new Error(token.error_description ?? token.error);

    const userResponse = await fetch(`${API}/oauth/userinfo`, {
      headers: { Authorization: `Bearer ${token.access_token}` },
    });
    return userResponse.json();
  }

  const result = document.getElementById("result");
  document.getElementById("sign-in").addEventListener("click", signIn);

  const params = new URLSearchParams(location.search);
  if (params.has("code") || params.has("error")) {
    finishSignIn(params).then(
      (user) => (result.textContent = `Signed in as ${user.username} (${user.sub})`),
      (error) => (result.textContent = error.message),
    );
  }
</script>
```
{% endcode %}

Serve the folder on port 3000, for example with `python3 -m http.server 3000`, and open `http://localhost:3000/`.
{% endtab %}

{% tab title="Node.js server" %}
This needs Node.js 18 or later and nothing else. The secret stays on the server and never reaches the browser.

In your app's settings, add the redirect URI `http://localhost:3000/callback`. Leave **This app can't keep a secret** unticked.

{% code title="server.mjs" %}
```js
import http from "node:http";
import crypto from "node:crypto";

const API = "https://api.rotur.dev";
const CLIENT_ID = process.env.ROTUR_CLIENT_ID; // app_…
const CLIENT_SECRET = process.env.ROTUR_CLIENT_SECRET; // rsec_…
const REDIRECT_URI = "http://localhost:3000/callback";

// Sign-ins in progress, by state. In a real app, keep this in the session.
const pending = new Map();

http.createServer(async (req, res) => {
  const url = new URL(req.url, "http://localhost:3000");

  if (url.pathname === "/login") {
    // PKCE: a random verifier, and its SHA-256 hash as the challenge.
    const verifier = crypto.randomBytes(32).toString("base64url");
    const challenge = crypto.createHash("sha256").update(verifier).digest("base64url");
    const state = crypto.randomBytes(16).toString("base64url");
    pending.set(state, verifier);

    const authorize = new URL(`${API}/oauth/authorize`);
    authorize.search = new URLSearchParams({
      response_type: "code",
      client_id: CLIENT_ID,
      redirect_uri: REDIRECT_URI,
      scope: "profile",
      state,
      code_challenge: challenge,
      code_challenge_method: "S256",
    });
    res.writeHead(302, { Location: authorize.toString() }).end();
    return;
  }

  if (url.pathname === "/callback") {
    const state = url.searchParams.get("state");
    const verifier = pending.get(state);
    pending.delete(state);
    if (!verifier) {
      res.writeHead(400).end("This sign-in didn't start here. Go to /login.");
      return;
    }
    if (url.searchParams.has("error")) {
      res.writeHead(200).end(`You didn't sign in (${url.searchParams.get("error")}).`);
      return;
    }

    const tokenResponse = await fetch(`${API}/oauth/token`, {
      method: "POST",
      headers: {
        Authorization: "Basic " + Buffer.from(`${CLIENT_ID}:${CLIENT_SECRET}`).toString("base64"),
      },
      body: new URLSearchParams({
        grant_type: "authorization_code",
        code: url.searchParams.get("code"),
        redirect_uri: REDIRECT_URI,
        code_verifier: verifier,
      }),
    });
    const token = await tokenResponse.json();
    if (!tokenResponse.ok) {
      res.writeHead(400).end(`Sign-in failed: ${token.error_description ?? token.error}`);
      return;
    }

    const user = await fetch(`${API}/oauth/userinfo`, {
      headers: { Authorization: `Bearer ${token.access_token}` },
    }).then((response) => response.json());

    // Find or create your own account for user.sub here, then start your own session.
    res.writeHead(200, { "Content-Type": "text/plain; charset=utf-8" });
    res.end(`Signed in as ${user.username} (${user.sub})`);
    return;
  }

  res.writeHead(200, { "Content-Type": "text/html" });
  res.end('<a href="/login">Sign in with Rotur</a>');
}).listen(3000, () => console.log("Open http://localhost:3000"));
```
{% endcode %}

Run it with your credentials and open `http://localhost:3000`:

```sh
ROTUR_CLIENT_ID=app_0123456789abcdef ROTUR_CLIENT_SECRET=rsec_… node server.mjs
```
{% endtab %}

{% tab title="curl" %}
Walk through the flow by hand to see each request. Use an app with the redirect URI `http://localhost:3000/callback`. Nothing has to be running on that address.

**1. Make a PKCE verifier and challenge.**

```sh
CLIENT_ID=app_0123456789abcdef
CLIENT_SECRET=rsec_...
VERIFIER=$(openssl rand -base64 48 | tr -d '\n=' | tr '+/' '-_')
CHALLENGE=$(printf '%s' "$VERIFIER" | openssl dgst -sha256 -binary | openssl base64 | tr -d '\n=' | tr '+/' '-_')
```

**2. Open the sign-in page.** Print the address, open it in your browser and approve the app.

```sh
echo "https://api.rotur.dev/oauth/authorize?response_type=code&client_id=$CLIENT_ID&redirect_uri=http%3A%2F%2Flocalhost%3A3000%2Fcallback&scope=profile&state=test&code_challenge=$CHALLENGE&code_challenge_method=S256"
```

Your browser ends up on `http://localhost:3000/callback?code=…&state=test&iss=https%3A%2F%2Fapi.rotur.dev`. The page won't load, and that's fine. Copy the `code` from the address bar. It works once, for 5 minutes.

**3. Swap the code for a token.**

```sh
CODE=paste-the-code-here
curl -s https://api.rotur.dev/oauth/token \
  -u "$CLIENT_ID:$CLIENT_SECRET" \
  -d grant_type=authorization_code \
  -d code="$CODE" \
  -d redirect_uri=http://localhost:3000/callback \
  -d code_verifier="$VERIFIER"
```

```json
{
  "access_token": "rotur_st_…",
  "token_type": "Bearer",
  "expires_in": 3600,
  "scope": "profile"
}
```

For a public client, leave out `-u` and send `-d client_id="$CLIENT_ID"` instead.

**4. Read who signed in.**

```sh
curl -s https://api.rotur.dev/oauth/userinfo -H "Authorization: Bearer rotur_st_…"
```
{% endtab %}
{% endtabs %}

## What you get back

`/oauth/userinfo` returns:

```json
{
  "sub": "7ebdf483-5b9f-4b70-9edc-a1f2827391f9",
  "username": "kit",
  "name": "Kit",
  "picture": "https://avatars.rotur.dev/7ebdf483-5b9f-4b70-9edc-a1f2827391f9",
  "profile": "https://rotur.dev/@kit"
}
```

| Field | What it is |
| --- | --- |
| `sub` | The person's Rotur user ID. It never changes, so use it as the key for your own account record. |
| `username` | Their username. It can change. |
| `name` | Their display name. It can be empty. |
| `picture` | Their avatar. The address stays the same when they change it. |
| `profile` | Their profile page on rotur.dev. |

If you ask for the `email` scope, you may also get `email` and `email_verified`. See [Scopes and the consent screen](scopes.md).

## Things to know

* **Access tokens last one hour** and there are no refresh tokens. Start your own session when someone signs in, and send them through Sign in with Rotur again when you next need a fresh token. See [Handle errors and denials](errors.md).
* **The token only reads who signed in**: the public profile, plus the email address if you asked for it and they shared it. It can't post, read friends or do anything else on the account. To act on someone's account, see [Accounts and tokens](../accounts-and-tokens/README.md).
* **Rotur keeps out people who can't use your app.** People you have banned, people banned or suspended on Rotur, under-18s on an adults-only app and teens whose parent hasn't allowed your app can't approve it, and their existing tokens stop working.
* **The redirect URI must match exactly**, character for character, including the trailing slash.

## Next steps

* [Set up your app](set-up-your-app.md): icons, more redirect URIs, secrets and your team.
* [Sign users in](sign-users-in.md): every parameter and response in detail.
* [Meet the Developer Terms](developer-terms.md): what your app must do before you launch.
