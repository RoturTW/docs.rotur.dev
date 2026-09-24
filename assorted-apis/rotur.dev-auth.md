# rotur.dev/auth

If you are making a website that connects to rotur, your users will often trust it more if it uses the official way to login to rotur.

## About

<https://rotur.dev/auth> signs the user in and hands your app a token. It works as a redirect, a popup or an iframe.

# YOU SHOULD USE THE ROTUR SDK
https://docs.rotur.dev/rotur-sdk/rotur-sdk

`rotur.login()` in the SDK builds the URL, opens the popup (or an iframe when the popup is blocked) and listens for the token for you. The rest of this page is for apps that cannot use the SDK.

## Query parameters

| Parameter | Required | Description |
| --- | --- | --- |
| `return_to` | Yes | The full URL of your page. The token is only ever delivered to this URL's origin, so without it your app never receives one. |
| `system` | No | The system name to sign in to, e.g. `originOS`. |
| `requires` | No | Comma-separated permission scopes your app needs, e.g. `posts:view,posts:create`. Use `full` to ask for full account access. |
| `signup` | No | `1` opens the create-account screen first. |
| `select_account` | No | `1` skips the "continue as" prompt so the user can pick or sign in to another account. |

If you build the URL by hand, encode `return_to` with `encodeURIComponent`. `URL.searchParams.set` does it for you.

## What the user sees

1. They sign in, or confirm the account they are already signed in with.
2. On rotur.dev subdomains and originchats.com the token is sent straight away.
3. On any other site they choose which permissions to grant. You get a scoped token limited to those permissions, or their main token if they allow full access.

## Receiving the token

How the token reaches you depends on how the page was opened.

### Redirect

Send the user to the auth page:

```js
const url = new URL("https://rotur.dev/auth");
url.searchParams.set("return_to", location.href);
location.href = url.toString();
```

When they finish, rotur.dev navigates back to `return_to` with the token added as a `token` query parameter. Read it and remove it from the address bar:

```js
const params = new URLSearchParams(location.search);
const token = params.get("token");
if (token) {
  params.delete("token");
  history.replaceState(null, "", location.pathname + (params.size ? `?${params}` : ""));
}
```

### Popup or iframe

If the auth page has a `window.opener` (a popup) or is inside a frame, it posts a message to that window instead of redirecting. The message is only sent to the origin of `return_to` and always comes from `https://rotur.dev`:

```js
{
  type: "rotur-auth-token",
  token: "…",
  return_to: "https://your.app/page",
  scope: "full",         // "full", "scoped" or "existing"
  permissions: ["…"],    // scoped and existing tokens only
  id: "…"                // scoped and existing tokens only
}
```

A popup closes itself after sending the message.

#### Iframe example

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
  url.searchParams.set("requires", "full");
  frame.src = url.toString();

  window.addEventListener("message", (event) => {
    if (event.origin !== "https://rotur.dev") return;
    if (event.data?.type !== "rotur-auth-token") return;

    frame.remove();
    localStorage.setItem("rotur_token", event.data.token);
  });
</script>
```

#### Limits inside an iframe

- **Google, GitHub and Discord sign-in** do not work in a frame, because those providers refuse to be framed. The auth page hides those buttons when it is framed, so users sign in with their username and password. Prefer a popup opened from a click when you can.
- **Passkeys** only work if the iframe delegates them with `allow="publickey-credentials-get; publickey-credentials-create"`, as in the example above. Without it the passkey button is hidden.

## Security

- Always check `event.origin === "https://rotur.dev"` before trusting a message.
- Use HTTPS. The token grants access to the user's account, so treat it like a password.
- If your site sends a Content Security Policy, allow `https://rotur.dev` in `frame-src` for the iframe flow.
