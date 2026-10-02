---
description: Ask someone for a Rotur account token from your website, through rotur.dev/auth.
---

# Get a token with rotur.dev/auth

[rotur.dev/auth](https://rotur.dev/auth) signs someone in on Rotur and hands your page a Rotur account token with the permissions they choose. It works as a redirect, a popup or an iframe.

{% hint style="warning" %}
This gives your app a token to **act on the account**. If you're building an app for other people, use [Sign in with Rotur](../build-an-app/sign-people-in.md) instead. Only Sign in with Rotur applies your app's bans and Rotur's age and parental checks for apps.
{% endhint %}

If you write JavaScript, the [Rotur SDK](../rotur-sdk/account/authentication.md)'s `rotur.login()` builds the URL, opens a popup (or an iframe when popups are blocked) and waits for the token for you. The rest of this page is for apps that can't use the SDK.

## Query parameters

| Parameter | Required | Description |
| --- | --- | --- |
| `return_to` | Yes | The full URL of your page. The token is only ever sent to this URL's origin. It must be `http` or `https` |
| `requires` | No | Comma-separated [permissions](tokens/permissions.md) your app needs, such as `posts:view,posts:create`. The person sees them ticked and can change them. `full` asks for every permission an app can have |
| `system` | No | The [system](../build-an-app/migrate-from-systems.md) to record for new accounts, such as `originOS`. Deprecated |
| `signup` | No | `1` opens the create-account screen first |
| `select_account` | No | `1` lets the person pick or sign in to another account instead of being asked "continue as" |

Build the URL with `URL.searchParams`, which encodes `return_to` for you.

## What the person sees

1. They sign in, or confirm the account they're already signed in with.
2. They see your site's name and the permissions you asked for, and choose which to grant. If they've given your site a token before, they can reuse it.
3. Rotur creates a sub-token with those permissions and sends it to your page.

Your app never gets the account's own (main) token. Asking for `full` offers every permission an app can have, as a sub-token the person can see and remove. Only Rotur's own sites (`rotur.dev`, its subdomains and `originchats.com`, over HTTPS) are given the account itself, without a permissions screen.

`tokens:manage` and `account:delete` can't be given to any app. `account:email` and `account:signins` are only given for adults.

## Receive the token

How the token reaches you depends on how you opened the page.

### Redirect

Send the person to the auth page:

```js
const url = new URL("https://rotur.dev/auth");
url.searchParams.set("return_to", location.href);
url.searchParams.set("requires", "account:view,posts:view,posts:create");
location.href = url.toString();
```

When they finish, Rotur sends them back to `return_to` with the token in a `token` query parameter. Read it and remove it from the address bar:

```js
const params = new URLSearchParams(location.search);
const token = params.get("token");
if (token) {
  params.delete("token");
  history.replaceState(null, "", location.pathname + (params.size ? `?${params}` : ""));
  localStorage.setItem("rotur_token", token);
}
```

### Popup or iframe

If the auth page has a `window.opener` (a popup) or is inside a frame, it posts a message to that window instead of redirecting. The message only goes to the origin of `return_to`, and always comes from `https://rotur.dev`:

```js
{
  type: "rotur-auth-token",
  token: "rotur_st_…",
  return_to: "https://your.app/page",
  scope: "scoped",          // "scoped" for a new token, "existing" for a reused one
  permissions: ["…"],       // what the token holds
  id: "st_…"                // the token's ID
}
```

A popup closes itself after sending the message.

```js
function signIn() {
  const url = new URL("https://rotur.dev/auth");
  url.searchParams.set("return_to", location.href);
  url.searchParams.set("requires", "account:view");
  window.open(url, "rotur-auth", "popup,width=480,height=720");
}

window.addEventListener("message", (event) => {
  if (event.origin !== "https://rotur.dev") return;
  if (event.data?.type !== "rotur-auth-token" || !event.data.token) return;
  localStorage.setItem("rotur_token", event.data.token);
});
```

Call `signIn()` from a click, so the browser doesn't block the popup.

### Iframe

```html
<iframe
  id="rotur-auth"
  allow="publickey-credentials-get; publickey-credentials-create"
  style="position: fixed; inset: 0; width: 100%; height: 100%; border: 0"
></iframe>

<script>
  const frame = document.getElementById("rotur-auth");
  const url = new URL("https://rotur.dev/auth");
  url.searchParams.set("return_to", location.href);
  url.searchParams.set("requires", "account:view");
  frame.src = url.toString();

  window.addEventListener("message", (event) => {
    if (event.origin !== "https://rotur.dev") return;
    if (event.data?.type !== "rotur-auth-token" || !event.data.token) return;
    frame.remove();
    localStorage.setItem("rotur_token", event.data.token);
  });
</script>
```

Inside an iframe:

- **Google, GitHub and Discord sign-in** don't work, because those providers refuse to be framed. The page hides their buttons, so people sign in with their username and password. Prefer a popup opened from a click.
- **Passkeys** only work if the iframe delegates them with `allow="publickey-credentials-get; publickey-credentials-create"`, as above.

## Use the token

Send it as `Authorization: Bearer <token>`. To find out who it belongs to, call [`GET /me`](../claw/api-endpoints/me.md): it returns the fields the token's permissions allow. Each endpoint in the [API reference](../api-reference/README.md) lists the permission it needs.

The token works until the person revokes it, it expires, or nobody uses it for 30 days. See [Manage tokens](tokens/README.md).

## Security

- Always check `event.origin === "https://rotur.dev"` before trusting a message.
- Use HTTPS. A token is access to the account, so treat it like a password.
- If your site sends a Content Security Policy, allow `https://rotur.dev` in `frame-src` for the iframe flow.
