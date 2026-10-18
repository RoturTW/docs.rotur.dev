# Authentication

{% hint style="info" %}
Apps sign people in with `new Rotur({ app })` and `rotur.signIn()`, from version 3.1. See [Sign people in](../../build-an-app/sign-people-in.md). The ways below are for Rotur's own clients and for tools on your own account.
{% endhint %}

The SDK gives you a few ways to get a token for the Rotur API.

## Sign in with rotur.dev/auth

`rotur.login()` signs the person in on [rotur.dev/auth](../../accounts-and-tokens/rotur-dev-auth.md) and gives your app a token with the permissions they choose. Since 3.2 it can use a popup, a full-page redirect or a sign-in box on your page.

```ts
const rotur = new Rotur();

signInButton.onclick = async () => {
  await rotur.login({ requires: ["validators:generate"] });
  rotur.token;   // the app's token
  rotur.granted; // the permissions the person gave it
};
```

Options:

| Option | Description |
| --- | --- |
| `requires` | The [permissions](#permission-scopes) your app needs: an array, or a comma-separated string. `"full"` asks for every permission an app can have |
| `mode` | `"auto"` (default), `"popup"`, `"redirect"` or `"embed"`. See below |
| `container` | For `mode: "embed"`: the element (or a selector for it) to put the sign-in box in. Giving one makes `"auto"` use the box |
| `theme` | The box's colours: `"dark"` (default) or `"light"` |
| `prompt` | `"select_account"`: the box lets the person choose the account instead of reusing the one it knows |
| `returnTo` | The page rotur.dev hands the token to. Default: this page's origin and path, without its query or fragment |
| `timeout` | Milliseconds to wait. Default: 15 minutes for a popup, no limit for the box |
| `signal` | An `AbortSignal` to stop waiting |
| `system` | The system an account made during sign-in joins. Deprecated: new apps leave it out |

### Modes

- **`"auto"`**: a popup when called from a click. If the browser blocks it, the tab goes to rotur.dev and comes back. Installed web apps and in-app browsers (Instagram, TikTok…) go to rotur.dev in the tab straight away, as a popup can't hand its answer back there. With a `container`, the sign-in box.
- **`"popup"`**: only a popup. Rejects with `popup_blocked` if the browser blocks it. Call it from a click or key press.
- **`"redirect"`**: the tab goes to rotur.dev and comes back with the token in the address's fragment (`#rotur_token=…`), never the query. The SDK sends a random `state` and only takes an answer that carries it, so a link with someone else's token in it can't sign anyone in.
- **`"embed"`**: rotur.dev's sign-in box in `container`. Someone who already let your site in through the box, with the same or more permissions, gets in without a click. Anything new opens rotur.dev in a popup from the box's button. The box removes itself when sign-in finishes; if the person closes the popup it stays, so they can try again.

```ts
await rotur.login({ mode: "embed", container: "#signin", requires: ["files:view", "files:manage"], theme: "light" });
```

A page inside another site's frame can't go to rotur.dev in the tab (rotur.dev won't load in a frame), so there a blocked popup rejects with `popup_blocked` instead. Sign in from a click there, or use the box.

### Coming back from a redirect

`new Rotur()` finishes a redirect as the page loads. It checks the `state` it saved before leaving, takes the answer out of the address, and puts back the address the page had, query and fragment included. Then:

- the first `rotur.login()` resolves with that sign-in, or rejects with `"cancelled"`, instead of leaving again. A page that signs in as it loads doesn't loop;
- `rotur.finishLogin()` resolves to `{ token, permissions, id }` (or null), for apps that keep the token themselves:

```ts
const back = await rotur.finishLogin().catch(() => null);
if (back) localStorage.setItem("rotur_token", back.token);
```

### Errors

`login()` rejects with an `AuthError`:

| `code` | Why |
| --- | --- |
| `cancelled` | They pressed Cancel on rotur.dev |
| `popup_closed` | They closed the rotur.dev window |
| `popup_blocked` | The browser blocked the popup, with `mode: "popup"` or in a page inside another site's frame |
| `state_mismatch` | The page came back with a sign-in this browser didn't start. Nothing was signed in |
| `timeout`, `aborted` | `timeout` passed, or your `signal` aborted |

```ts
import { AuthError } from "rotur-sdk";

try {
  await rotur.login({ requires: ["validators:generate"] });
} catch (err) {
  if (err instanceof AuthError && (err.code === "cancelled" || err.code === "popup_closed")) {
    showSignedOut();
  } else throw err;
}
```

Before 3.2 the SDK showed rotur.dev in a full-window frame when the popup was blocked. rotur.dev no longer allows that, so those versions show a blank overlay for two minutes. They also wait for the timeout after Cancel. Upgrade to 3.2: see [the migration guide](https://git.rotur.dev/rotur/sdk/src/branch/main/MIGRATING.md).

### performAuth

`rotur.login()` wraps the standalone `performAuth()`, which takes the same options and resolves to `{ token, permissions, id }` without a client. `handleAuthRedirect()` finishes a redirect without a client. With the `<script>` build they're `Rotur.performAuth` and `Rotur.handleAuthRedirect`.

```ts
import { handleAuthRedirect, performAuth } from "rotur-sdk";

const back = await handleAuthRedirect(); // on load
const { token, permissions } = await performAuth({ mode: "popup", requires: ["posts:view"] });
```

### Permission scopes

`requires` lists the permissions your app needs. Pass an array (`["posts:view", "posts:create"]`) or a comma-separated string (`"posts:view,posts:create"`).

| Your app… | `requires` |
| --- | --- |
| Shows who's signed in | `account:view` |
| Proves who's calling to your own server ([validators](../utilities/validators.md)) | `validators:generate` |
| Reads the person's files | `files:view` |
| Saves files | `files:view`, `files:manage` (and `files:delete`) |
| Shows their balance | `credits:view` |
| Charges or sends credits | `credits:view`, `credits:transfer` |
| Posts as them | `posts:view`, `posts:create` |

Rotur asks for the password before an app gets `credits:transfer`, `credits:manage`, `account:settings`, `gifts:create`, `items:buy`, `cosmetics:buy`, `cosmetics:gift` or `signing:private`. Name them in `requires` when your app needs them: the consent card then shows them on and waits for the password. The person can still turn them off, so check `rotur.granted`.

Without `requires`, apps on *.rotur.dev are offered every permission an app can have (the password ones off), and any other site gets only `account:view`.

`"full"` offers every permission an app can have, as a token the person can see and remove. No app gets the account's own token, so methods marked **Main token only** don't work with any app's token.

Older versions of rotur-sdk only accept an array, so pass `["full"]` there.

If you use the Vite plugin from `rotur-sdk/vite`, it scans your code for SDK calls and adds the matching permissions to `requires`. The `METHOD_PERMISSIONS` and `resolvePermissions` exports expose the same mapping if you want to compute them yourself.

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

## Sign out

```ts
await rotur.signOut(); // clears the token, disconnects the WebSocket and Beam, and makes the sign-in box ask who's signing in next time
```

`rotur.logout()` does the same.
