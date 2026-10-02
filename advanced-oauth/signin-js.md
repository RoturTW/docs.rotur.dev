---
description: Add a Sign in with Rotur button, and the browser's own "Continue as" prompt, to a website with one script tag and no SDK.
---

# Sign-in button with signin.js

`signin.js` adds a Sign in with Rotur button, and the browser's "Continue as" prompt, to a website with one script tag. It gives your page an access token for the profile, and doesn't need the SDK, a callback page or a server. To also call your own server, or to act on people's accounts, use the [JavaScript SDK](../build-an-app/sign-people-in.md) instead.

## 1. Set up your app

1. Go to [rotur.dev/me/developer](https://rotur.dev/me/developer) and choose **Create app** (or open an app you already have).
2. Under **Redirect URIs**, add your site's address, such as `https://yoursite.com`. For testing, `http://localhost:3000` works too.
3. Tick **This app can't keep a secret**. This lets the sign-in finish in the browser. If your site has a server that should hold a secret instead, see [With a server](#with-a-server).

Copy your app's **client ID**. It starts with `app_`.

## 2. Add the script and a button

```html
<script src="https://rotur.dev/signin.js" data-client-id="app_YOUR_ID" data-on-sign-in="signedIn"></script>

<div data-rotur-signin></div>

<script>
  function signedIn({ user, accessToken }) {
    console.log("Signed in as", user.username, user.id);
  }
</script>
```

That's all. Every element with `data-rotur-signin` becomes a **Sign in with Rotur** button, and `signedIn` runs when someone signs in.

## What people see

* **The button**, in every browser. It opens Rotur's sign-in in a small window. People sign in if they need to, and see what your app will get. When they choose **Authorize**, the window closes and your page has them.
* **A "Continue as …" prompt in the corner of the page**, in Chrome, Edge and other Chromium browsers. Anyone already signed in to Rotur sees their account and can sign in with one click, without leaving your page. When they come back later, the browser signs them straight back in. This is the browser's own sign-in feature (FedCM), the same one Google's sign-in prompt uses. Firefox and Safari don't have it yet, so people there use the button.

## What you get

```js
{
  user: {
    id: "…",                // Rotur ID. Never changes: use this to identify people.
    username: "mist",       // Can change, so don't use it as a key.
    name: "Mist",           // Display name
    picture: "https://avatars.rotur.dev/…",
    profile: "https://rotur.dev/@mist",
    email: "…"              // Only with data-scope="profile email", and only for adults who allow it
  },
  accessToken: "…",         // Works for an hour against https://api.rotur.dev/oauth/userinfo
  expiresIn: 3600,
  scope: "profile"
}
```

Key people by `user.id`. Keep them signed in with your own session or cookie. The access token only lasts an hour, and there's no refresh token. When you need to check who someone is again, sign them in again: in Chromium browsers that's automatic and invisible for returning users.

## Options

Put these on the script tag to apply them everywhere, or on one button to change only that button.

| Attribute | Default | What it does |
| --- | --- | --- |
| `data-client-id` | (required) | Your app's client ID, `app_…`. |
| `data-on-sign-in` | | Name of a global function to call with the result. |
| `data-on-error` | | Name of a global function to call with an error. Cancelling or closing the window are errors too, with `code` set to `access_denied` or `closed`. |
| `data-scope` | `profile` | `profile email` also asks for their email address. People can turn that off, and under-18s never share it. The corner prompt only ever gives `profile`. |
| `data-mode` | `browser` | `server` hands your page a code for your server to swap, instead of a token. See [With a server](#with-a-server). |
| `data-prompt` | `on` | `off` turns off the corner prompt. Useful on pages where someone is already signed in to your site. |
| `data-theme` | `dark` | `light` for a white button. |
| `data-text` | `signin` | `continue` says "Continue with Rotur" instead of "Sign in with Rotur". |

The script also fires a `rotur:signin` event on `document` (and `rotur:signin-error` on failure), and gives you `window.RoturSignIn`:

```js
RoturSignIn.signIn();                  // open the sign-in window yourself, e.g. from your own button
RoturSignIn.prompt();                  // show the corner prompt yourself (with data-prompt="off")
RoturSignIn.renderButton(element, { theme: "light" });
RoturSignIn.onSignIn((result) => { /* … */ });
```

`signIn()` and `prompt()` return promises too. `prompt()` resolves to `null` if the browser can't show the prompt or nobody is signed in to Rotur.

## With a server

If your app keeps its secret on a server, leave **This app can't keep a secret** off and add `data-mode="server"`. Your page then gets a code instead of a token:

```js
function signedIn({ code, codeVerifier, redirectUri }) {
  fetch("/auth/rotur", { method: "POST", headers: { "Content-Type": "application/json" }, body: JSON.stringify({ code, codeVerifier, redirectUri }) });
}
```

Your server swaps it with the secret:

```js
// Node.js
const response = await fetch("https://api.rotur.dev/oauth/token", {
  method: "POST",
  headers: { Authorization: "Basic " + Buffer.from(`${CLIENT_ID}:${CLIENT_SECRET}`).toString("base64") },
  body: new URLSearchParams({ grant_type: "authorization_code", code, redirect_uri: redirectUri, code_verifier: codeVerifier }),
});
const { access_token } = await response.json();
const user = await fetch("https://api.rotur.dev/oauth/userinfo", { headers: { Authorization: `Bearer ${access_token}` } }).then((r) => r.json());
// user.sub is the Rotur ID
```

## Without the script

Everything `signin.js` does is standard, so you can do it yourself in any language or framework.

**The popup.** Open `https://api.rotur.dev/oauth/authorize` in a window, with the usual [PKCE parameters](authorize.md#1-send-the-person-to-rotur), plus:

* `response_mode=web_message`
* `redirect_uri` set to your page's origin, such as `https://yoursite.com` (no path). It must be the origin of one of your app's redirect URIs.

When the person chooses **Authorize** or **Cancel**, Rotur posts a message to the page that opened the window, then closes it:

```js
window.addEventListener("message", (event) => {
  if (event.origin !== "https://rotur.dev" || event.data?.type !== "rotur:signin" || event.data.state !== myState) return;
  if (event.data.error) return; // "access_denied" when they cancelled
  exchange(event.data.code);    // swap at /oauth/token with redirect_uri = your origin and your code_verifier
});
```

**The corner prompt (FedCM):**

```js
const credential = await navigator.credentials.get({
  identity: {
    providers: [{
      configURL: "https://api.rotur.dev/fedcm/config.json",
      clientId: "app_YOUR_ID",
      params: { code_challenge: challenge, code_challenge_method: "S256" },
    }],
  },
  mediation: "optional",
});
// credential.token is an authorization code for the profile scope.
// Swap it at /oauth/token with redirect_uri = your page's origin and your code_verifier.
```

## If it doesn't work

* **"redirect_uri must be the origin of a registered redirect URI"**: add your site's address (`https://yoursite.com`, with the right `http` or `https` and port) under **Redirect URIs**.
* **"this app keeps a secret"**: tick **This app can't keep a secret**, or use `data-mode="server"`.
* **The window doesn't open**: the browser blocked it. `signIn()` must run straight from a click, not after an `await`.
* **The window never reports back**: if your site sends `Cross-Origin-Opener-Policy: same-origin`, change it to `same-origin-allow-popups`.
* **Your site has a Content Security Policy**: allow `script-src https://rotur.dev` and `connect-src https://api.rotur.dev`.
* **The corner prompt doesn't appear**: check that the browser is Chromium-based, that the person is signed in to Rotur in that browser, and that `data-prompt` isn't `off`. If someone closes the prompt, Chrome waits a while before showing it again on your site.

## Next steps

* [Scopes and the consent screen](scopes.md): what people agree to, and why some are refused.
* [Authorise, swap and refresh](authorize.md): every error the token endpoint can give.
* [Meet the Developer Terms](../build-an-app/developer-terms.md): what every app must do, such as deleting data when asked.
