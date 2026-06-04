# Authentication

The SDK provides multiple ways to authenticate with the Rotur API.

## Popup Login (Browser)

The simplest way — opens a `rotur.dev/auth` popup window. The user logs in, and the token is returned to your app via `postMessage`.

```ts
const rotur = new Rotur();
await rotur.login();

// With options:
await rotur.login({
  system: "originOS",   // pre-select a system
  timeout: 60_000,      // 60 second timeout (default: 120s)
  signal: abortCtrl.signal,  // AbortController signal
});
```

### How it works

1. Opens `https://rotur.dev/auth` in a popup
2. If the popup is blocked, falls back to a fullscreen iframe
3. Listens for a `postMessage` with `{ type: "rotur-auth-token", token: "..." }`
4. Resolves the promise with the authenticated client

## Manual Token

If you already have a token (e.g. persisted from a previous session):

```ts
const rotur = new Rotur({ token: storedToken });

// Or set it later:
rotur.setToken(storedToken);
```

## Link Code Flow

For non-browser environments (CLI, desktop apps, servers), use the link code flow:

```ts
// 1. Get a link code
const { code } = await rotur.link.getCode();
console.log("Visit rotur.dev and enter:", code);

// 2. Poll until the user links on the website
const token = await rotur.link.pollUntilLinked(code, 1500, 120_000);
// interval: 1500ms, timeout: 120s
```

Or manually:

```ts
const { code } = await rotur.link.getCode();
// ...user visits rotur.dev and enters code...
const { linked, token } = await rotur.link.linkedUser(code);
if (linked && token) rotur.setToken(token);
```

## Refresh Token

Rotate your main token (invalidates the old one):

```ts
const { token } = await rotur.me.refreshToken();
rotur.setToken(token); // update stored token
```

## Check Auth

Verify the current token is valid and see what type it is:

```ts
const result = await rotur.me.checkAuth();
// { auth: true, username: "alice", token_type: "main" }
// { auth: true, username: "alice", token_type: "sub", permissions: [...] }
// { auth: false, username: "" }
```

## Token Abilities

Check what permissions the current token has:

```ts
const abilities = await rotur.me.abilities();
// { token_type: "main", permissions: [...] }
// { token_type: "sub", name: "my-bot", id: "...", permissions: [...] }
```

## Logout

```ts
rotur.logout(); // clears token and disconnects WebSocket
```
