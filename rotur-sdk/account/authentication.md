# Authentication

{% hint style="info" %}
Apps sign people in with `new Rotur({ app })` and `rotur.signIn()`, from version 3.1. See [Sign people in](../../build-an-app/sign-people-in.md). The ways below are for Rotur's own clients and for tools on your own account.
{% endhint %}

The SDK gives you a few ways to get a token for the Rotur API.

## Popup Login (Browser)

`rotur.login()` opens `rotur.dev/auth` in a popup. The person signs in and picks what your app may do, and the token comes back to your app through `postMessage`.

```ts
const rotur = new Rotur();
await rotur.login();

// With options:
await rotur.login({
  system: "originOS",        // system for new sign-ups (deprecated)
  timeout: 60_000,           // 60 second timeout (default: 120s)
  signal: abortCtrl.signal,  // AbortController signal
  requires: ["posts:create"], // permission scopes to request
});

// Ask for full account access:
await rotur.login({ requires: "full" });
```

### How it works

1. Opens `https://rotur.dev/auth?return_to=<current page>&requires=<scopes>` in a popup
2. If the popup is blocked, falls back to a fullscreen iframe
3. Listens for a `postMessage` from `https://rotur.dev` or your own origin with `{ type: "rotur-auth-token", token: "..." }`
4. Sets the token, opens the WebSocket, and resolves with the client

Browsers only allow the popup when `login()` runs from a click or key press. Call it from a "Sign in" button rather than on page load or from a socket event, or your users always get the iframe.

Inside the iframe, Google, GitHub and Discord sign-in are unavailable because those providers refuse to be framed, so users sign in with their username and password. See [rotur.dev/auth](../../accounts-and-tokens/rotur-dev-auth.md) for details.

On failure the promise rejects with an `AuthError` whose `code` is one of `"timeout"`, `"aborted"`, `"popup_blocked"`, or `"no_token"`:

```ts
import { AuthError } from "rotur-sdk";

try {
  await rotur.login();
} catch (err) {
  if (err instanceof AuthError && err.code === "timeout") {
    console.log("User never finished logging in");
  }
}
```

### performAuth

`rotur.login()` wraps the standalone `performAuth` function and accepts `system`, `timeout`, `signal` and `requires`. Call `performAuth` directly when you need `returnTo` or `popupOnly`:

```ts
import { performAuth } from "rotur-sdk";

const { token } = await performAuth({
  system: "originOS",
  returnTo: "https://myapp.com/after-login", // default: current page URL
  popupOnly: true, // throw AuthError("popup_blocked") instead of iframe fallback
});
rotur.setToken(token);
```

`system` is the system an account made during sign-in joins. Systems are deprecated and have been replaced by Rotur Apps, so new apps can leave it out.

### Permission scopes

The `requires` option lists the permission scopes your app needs. Pass either a comma-separated string (`"posts:view,posts:create"`) or an array with one scope per entry (`["posts:view", "posts:create"]`). Use `"full"` to ask for full account access.

`"full"` only gives you the account's own token on Rotur's own sites (`rotur.dev` and its subdomains, and `originchats.com`, over HTTPS). Anywhere else the person is offered a scoped token with every permission an app can have. They can see that token and remove it at any time, and methods marked **Main token only** do not work with it.

Older versions of rotur-sdk only accept an array, so pass `["full"]` there.

If you use the Vite plugin from `rotur-sdk/vite`, it scans your code for SDK calls and injects the matching scopes automatically, so you rarely need to pass `requires` by hand. The `METHOD_PERMISSIONS` and `resolvePermissions` exports expose the same mapping if you want to compute scopes yourself.

## Manual Token

If you already have a token (e.g. persisted from a previous session):

```ts
const rotur = new Rotur({ token: storedToken });

// Or set it later:
rotur.setToken(storedToken);
```

## Link Code Flow

For non-browser environments (CLI, desktop apps, servers), use the link code flow. The person enters the code on rotur.dev and picks the permissions your app gets. The app always receives a scoped sub-token, never the account's own token. Codes expire after 10 minutes.

```ts
// 1. Get a link code
const { code } = await rotur.link.getCode();
console.log("Visit rotur.dev and enter:", code);

// 2. Poll until the user links on the website
const token = await rotur.link.pollUntilLinked(code, 1500, 120_000);
// interval: 1500ms, timeout: 120s
// The token is set on the client automatically.
```

{% hint style="warning" %}
`GET /link/user` answers `404` until the code is linked, and the SDK throws `ApiError` for any error status. So `linkedUser()` throws until the person has linked the code, and `pollUntilLinked()` stops with that error on its first check instead of waiting. Until this is fixed, poll `linkedUser()` yourself and treat a `404` as "not linked yet".
{% endhint %}

Or manually:

```ts
import { ApiError } from "rotur-sdk";

const { code } = await rotur.link.getCode();
// ...user visits rotur.dev and enters code...
try {
  const { linked, token } = await rotur.link.linkedUser(code);
  if (linked && token) rotur.setToken(token);
} catch (err) {
  if (!(err instanceof ApiError && err.status === 404)) throw err;
  // not linked yet, try again shortly
}
```

See [Linking](../utilities/linking.md) for the rest of `rotur.link`.

## Refresh Token

Rotate your main token (invalidates the old one). This needs the main token.

```ts
const { token } = await rotur.me.refreshToken();
rotur.setToken(token); // update stored token
```

## Check Auth

Verify the current token is valid and see what type it is. Sub-tokens need `account:view`, or the request fails with `403`.

```ts
const result = await rotur.me.checkAuth();
// { auth: true, username: "alice", token_type: "main" }
// { auth: true, username: "alice", token_type: "sub", permissions: [...] }
```

An invalid or missing token makes the request fail with `ApiError` (`401` or `403`) rather than returning `auth: false`.

## Token Abilities

Check what permissions the current token has:

```ts
const abilities = await rotur.me.abilities();
// { token_type: "main", permissions: [...] }   every permission
// { token_type: "sub", name: "my-bot", id: "...", permissions: [...] }
```

Any valid token can call this.

## Logout

```ts
rotur.logout(); // clears the token and disconnects the WebSocket and Beam
```
