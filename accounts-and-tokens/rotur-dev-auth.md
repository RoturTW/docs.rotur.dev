---
description: Ask someone for a Rotur token from your website, through rotur.dev/auth, in a popup, a full-page redirect or a sign-in box on your page.
---

# Get a token with rotur.dev/auth

[rotur.dev/auth](https://rotur.dev/auth) signs someone in on Rotur and hands your page a token with the permissions they choose. It works in a popup, as a full-page redirect, or through a small sign-in box you put on your page.

{% hint style="warning" %}
This gives your app a token to **act on the account**. If you're building an app for other people, use [Sign in with Rotur](../build-an-app/sign-people-in.md) instead. Only Sign in with Rotur applies your app's bans and Rotur's age and parental checks for apps.
{% endhint %}

If you write JavaScript, the [Rotur SDK](../rotur-sdk/account/authentication.md)'s `rotur.login()` does all of this for you, including the checks below. The rest of this page is for apps that can't use the SDK.

## Query parameters

| Parameter | Required | Description |
| --- | --- | --- |
| `return_to` | Yes | Your page's address. The token is only ever sent to this address's origin. It must be `http` or `https` |
| `requires` | No | Comma-separated [permissions](tokens/permissions.md) your app needs, such as `posts:view,posts:create`. See [Which permissions to ask for](#which-permissions-to-ask-for) |
| `state` | No, but use it | 16 to 128 random URL-safe characters (`A-Z a-z 0-9 - _`). It comes back with the answer, so you can tell your own sign-in from one someone else started |
| `response_mode` | No | `fragment`: a full-page answer comes back in the address's fragment (`#rotur_token=…`) instead of the query. Needs `state` |
| `system` | No | The [system](../build-an-app/migrate-from-systems.md) to record for new accounts, such as `originOS`. Deprecated |
| `signup` | No | `1` opens the create-account screen first |
| `select_account` | No | `1` lets the person pick or sign in to another account instead of being asked "continue as" |

Build the URL with `URL.searchParams`, which encodes `return_to` for you.

## What the person sees

1. They sign in, or pick an account they're already signed in with.
2. They see your site's name and the permissions you asked for, and choose which to grant. If they've given your site a token that covers what you ask for, it's reused without asking again.
3. Rotur creates a token with those permissions and sends it to your page.

Your app never gets the account's own (main) token, on any site. `requires=full` offers every permission an app can have, as a token the person can see and remove under Your apps.

`tokens:manage` and `account:delete` can't be given to any app. `account:email` and `account:signins` are only given for adults.

### Permissions that need the password

Rotur asks for the person's password before an app gets `credits:transfer`, `credits:manage`, `account:settings`, `gifts:create`, `items:buy`, `cosmetics:buy`, `cosmetics:gift` or `signing:private`.

- If you **name** one in `requires`, the card shows it switched on, says your app needs it, and waits for the password before Allow works. The person can still turn it off.
- If you ask for `full` (or send no `requires` from a *.rotur.dev site), these are listed but start switched off.

Check `permissions` in the answer to see what you got.

### Which permissions to ask for

| Your app… | `requires` |
| --- | --- |
| Shows who's signed in | `account:view` |
| Proves who's calling to your own server, with [validators](validators.md) | `validators:generate` |
| Reads the person's files | `files:view` |
| Saves files | `files:view,files:manage` (and `files:delete` to delete) |
| Shows their balance | `credits:view` |
| Charges or sends credits | `credits:view,credits:transfer` |
| Posts as them | `posts:view,posts:create` |
| Their blocked list | `blocked:view,blocked:manage` |

If you send no `requires`, sites on *.rotur.dev are offered every permission an app can have (the password ones switched off), and any other site gets only `account:view`. Name what you need: an app elsewhere that calls validators or files without naming them gets `403` from the API.

## Popup

Open the page from a click, so the browser doesn't block it:

```js
function signIn() {
  const state = crypto.randomUUID();
  sessionStorage.setItem("rotur_state", state);
  const url = new URL("https://rotur.dev/auth");
  url.searchParams.set("return_to", location.origin + location.pathname);
  url.searchParams.set("requires", "validators:generate");
  url.searchParams.set("state", state);
  url.searchParams.set("response_mode", "fragment");
  return window.open(url, "rotur-auth", "popup,width=480,height=720");
}

const popup = signIn();
window.addEventListener("message", (event) => {
  if (event.origin !== "https://rotur.dev" || event.source !== popup) return;
  if (event.data?.state !== sessionStorage.getItem("rotur_state")) return;
  if (event.data.type === "rotur-auth-token") save(event.data.token);
  if (event.data.type === "rotur-auth-cancelled") showSignedOut(); // they pressed Cancel
});
```

The popup posts one of these to the page that opened it, only to the origin of `return_to`, and closes itself:

```js
{
  type: "rotur-auth-token",
  token: "rotur_st_…",
  return_to: "https://your.app/page",
  scope: "scoped",          // "scoped" for a new token, "existing" for a reused one
  permissions: ["…"],       // what the token holds
  id: "st_…",               // the token's ID
  state: "…"                // yours, if you sent one
}

{ type: "rotur-auth-cancelled", return_to: "…", state: "…" }
```

If the person closes the window instead, nothing is sent. Check `popup.closed` every half second and stop waiting when it's true.

## Full-page redirect

Use this when the popup is blocked, and in installed web apps and in-app browsers (Instagram, TikTok…), where a popup can't hand its answer back:

```js
const state = crypto.randomUUID();
sessionStorage.setItem("rotur_state", state);
const url = new URL("https://rotur.dev/auth");
url.searchParams.set("return_to", location.origin + location.pathname);
url.searchParams.set("requires", "validators:generate");
url.searchParams.set("state", state);
url.searchParams.set("response_mode", "fragment");
location.assign(url);
```

The person comes back to `return_to` with the answer in the fragment, which browsers don't send to servers or put in Referer headers:

```
https://your.app/page#rotur_token=rotur_st_…&rotur_permissions=validators%3Agenerate&rotur_token_id=st_…&state=…
https://your.app/page#rotur_error=access_denied&state=…      (they pressed Cancel)
```

When your page loads, check the state, then take the answer out of the address:

```js
const answer = new URLSearchParams(location.hash.slice(1));
if (answer.has("rotur_token") || answer.has("rotur_error")) {
  history.replaceState(null, "", location.pathname + location.search);
  const expected = sessionStorage.getItem("rotur_state");
  sessionStorage.removeItem("rotur_state");
  if (!expected || answer.get("state") !== expected) {
    // Not a sign-in this browser started: someone sent a link with their own token in it.
  } else if (answer.has("rotur_token")) {
    save(answer.get("rotur_token"));
  } else {
    showSignedOut(); // don't send them straight back to rotur.dev
  }
}
```

{% hint style="warning" %}
Without `state` and `response_mode=fragment`, the token comes back as `?token=…` in the query, as it always has, and a Cancel as `?error=access_denied`. That still works, but a link with someone else's token in it can then sign a visitor in as them, and the token ends up in your server's logs. If your server takes the answer at a callback route, keep the state in the session and compare it there.
{% endhint %}

## Sign-in box

`https://rotur.dev/auth/embed` is the only rotur.dev page another site can put in a frame. It's a small "Sign in with Rotur" box:

- Someone who already let your site in through the box, with the same or more permissions, gets the token at once, without a click.
- Anything new opens rotur.dev in a popup from the box's button, where passkeys and Google, GitHub and Discord sign-in work, and where your page can't cover or fake the consent card.
- The box answers only the page around it, and only if that page is on the origin of `return_to`. On any other site it says it can't sign in there.

```html
<iframe id="rotur-box" title="Sign in with Rotur" referrerpolicy="origin"
  style="width: 100%; height: 150px; border: 0; color-scheme: dark"></iframe>

<script>
  const frame = document.getElementById("rotur-box");
  const state = crypto.randomUUID();
  const url = new URL("https://rotur.dev/auth/embed");
  url.searchParams.set("return_to", location.origin + location.pathname);
  url.searchParams.set("requires", "files:view,files:manage");
  url.searchParams.set("state", state);
  url.searchParams.set("theme", "dark"); // or "light"
  frame.src = url.toString();

  window.addEventListener("message", (event) => {
    if (event.origin !== "https://rotur.dev" || event.source !== frame.contentWindow) return;
    if (event.data?.state !== state) return;
    if (event.data.type === "rotur-auth:resize") frame.style.height = `${event.data.height}px`;
    if (event.data.type === "rotur-auth-token") { frame.remove(); save(event.data.token); }
    if (event.data.type === "rotur-auth-cancelled") frame.remove();
  });
</script>
```

| Parameter | Description |
| --- | --- |
| `return_to`, `requires`, `state`, `system` | As for `/auth`. `state` is required |
| `theme` | `dark` (default) or `light` |
| `prompt` | `select_account`: don't hand back the account the box knows; let the person choose. Use it after they sign out of your app |

- Keep `referrerpolicy="origin"` on the frame. Firefox only tells the box which site it's on through the referrer, and the box refuses when it can't tell.
- Set the frame's `color-scheme` to the box's `theme`, or browsers paint a solid background behind its rounded corners.
- The box also sends `{ type: "rotur-auth:ready" }` once it loads, and `{ type: "rotur-auth:status", code }` with `popup_closed`, `popup_blocked` or `covered` when one of those happens. It stays on the page so the person can try again.
- In Chromium the box's button only works once the box has been fully visible and uncovered for half a second (it checks with IntersectionObserver v2), so a page can't hide the box under something and trick a click. Elsewhere, a click only ever opens rotur.dev's own window, where nothing is given without a second click there.
- If your site sends a Content Security Policy, allow `https://rotur.dev` in `frame-src`.

`https://rotur.dev/auth` itself can't be framed: open it in a popup or the whole tab.

## Use the token

Send it as `Authorization: Bearer <token>`. To find out who it belongs to, call [`GET /me`](../claw/api-endpoints/me.md): it returns the fields the token's permissions allow. Each endpoint in the [API reference](../api-reference/README.md) lists the permission it needs.

The token works until the person revokes it, it expires, or nobody uses it for 30 days. See [Manage tokens](tokens/README.md).

## Security

- Only take messages whose `event.origin` is `https://rotur.dev`, from the window you opened (`event.source`), carrying your `state`.
- Use `state` and `response_mode=fragment` for full-page sign-ins, and check the state before using a token.
- Use HTTPS. A token is access to the account, so treat it like a password.
