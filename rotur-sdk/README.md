# Rotur SDK

`rotur-sdk` is the official TypeScript client for the Rotur API. Use it to log users in and call Rotur from a browser app, a desktop app, or a server.

> **Base URL:** `https://api.rotur.dev/v2` (the SDK sends every request there)
> **Auth:** The SDK sends your token as an `Authorization: Bearer` header. Log in with `rotur.login()` or pass a token to the constructor. See [Authentication](account/authentication.md).

## Install

```bash
npm install rotur-sdk
```

## Quick start

```ts
import { Rotur } from "rotur-sdk";

const rotur = new Rotur();

// Opens the rotur.dev/auth popup
await rotur.login();

const { username } = await rotur.me.checkAuth();
console.log("Logged in as", username);

// Wait for the real-time status WebSocket
const { user_id, user } = await rotur.connectSocket();
```

## Create a client

```ts
// With an existing token, for example from localStorage
const rotur = new Rotur({ token: "your-token-here" });

// With a custom WebSocket URL (default: wss://api.rotur.dev/status/ws)
const rotur = new Rotur({ wsUrl: "wss://api.rotur.dev/status/ws" });
```

When the client has a token, it opens the WebSocket in the background. See [Status and WebSocket](realtime/status.md).

## Token and session

```ts
rotur.token;        // current token (string | null)
rotur.loggedIn;     // true when a token is set
rotur.setToken(t);  // set a token and open the WebSocket if it is not connected
rotur.logout();     // clear the token and disconnect the WebSocket and Beam
```

## Permissions

Sub-tokens only work for the methods their permissions allow. Each method page lists the permission it needs on its **Auth** line:

- **Required. Sub-tokens need `x`.** The method sends your token, and a sub-token must include permission `x`.
- **Required. Main token only.** The method needs your main account token (permission `full`).
- **Required. No specific permission.** The method sends your token, but no permission is mapped to it.
- **None.** The method sends no token.

The same mapping is exported as `METHOD_PERMISSIONS` (`"namespace.method"` to permission, or `null`). `resolvePermissions(methods)` turns a list of methods into the permission list to request at login.

## Namespaces

| Namespace | Description |
| --- | --- |
| [`rotur.me`](account/me.md) | Your account: data, credits, badges, blocking, notes, billing |
| [`rotur.profiles`](account/profiles.md) | Public profiles, existence checks, image URLs |
| [`rotur.posts`](social/posts.md) | Posts, replies, likes, reposts, polls, bookmarks, feeds |
| [`rotur.friends`](social/friends.md) | Friend list and friend requests |
| [`rotur.following`](social/following.md) | Follow, unfollow, follower and following lists |
| [`rotur.notifications`](social/notifications.md) | Claw notifications |
| [`rotur.keys`](marketplace/keys.md) | Access keys: create, buy, manage |
| [`rotur.items`](marketplace/items.md) | Marketplace items: create, buy, sell, transfer |
| [`rotur.gifts`](marketplace/gifts.md) | Credit gift codes: create, claim, cancel |
| [`rotur.tokens`](marketplace/tokens.md) | Sub-tokens: create, manage, revoke |
| [`rotur.cosmetics`](marketplace/cosmetics.md) | Cosmetics shop, purchase, equip, gift |
| [`rotur.groups`](platform/groups.md) | Groups: members, roles, events, tips, products |
| [`rotur.systems`](platform/systems.md) | Registered systems and system badges |
| [`rotur.stats`](platform/stats.md) | Economy, user, post, and follower statistics |
| [`rotur.standing`](platform/standing.md) | Account standing lookups |
| [`rotur.devfund`](platform/devfund.md) | Dev fund escrow transfers |
| [`rotur.check`](platform/check.md) | Bulk ban checks |
| [`rotur.status`](realtime/status.md) | HTTP status lookup and update |
| [`rotur.socket`](realtime/status.md) | Real-time WebSocket: presence, activities, rooms |
| [`rotur.push`](realtime/push.md) | Web push notifications |
| [`rotur.validators`](utilities/validators.md) | Generate and check validators |
| [`rotur.link`](utilities/linking.md) | Link-code login for non-browser apps |
| [`rotur.files`](utilities/files.md) | The user's file system |
| [`rotur.storage`](utilities/storage.md) | App-scoped key/value storage on the user's account |
| `rotur.signing` | Sign, verify, encrypt, and decrypt with the account's signing key |
| `rotur.avatars` | Avatar, banner, background, and overlay URLs and uploads |
| `rotur.emojis` | Custom emojis: upload, save, delete |
| `rotur.trust` | Trust scores and user verification |
| `rotur.accounts` | Registration, password reset, email verification |
| `rotur.sessions` | Cookie-based sessions |
| `rotur.ai` | AI completions and chat |
| `rotur.reports` | Report posts, replies, and users |
| `rotur.pets` | Pet catalog, purchase, equip |
| `rotur.moderation` | Moderator tools (moderator role required) |
| `rotur.admin` | Network admin tools |
| `rotur.beam` | Peer-to-peer file and text transfer. Connects when you call `rotur.beam.connect()` |

## Errors

Every failed request throws an `ApiError` with the HTTP status and the response body:

```ts
import { ApiError } from "rotur-sdk";

try {
  await rotur.me.transfer("someone", 100);
} catch (err) {
  if (err instanceof ApiError) {
    console.log(err.status); // HTTP status code
    console.log(err.data);   // for example { error: "Insufficient funds" }
  }
}
```

Methods that need a token throw `ApiError` with status `401` before sending anything when no token is set.

## Other exports

```ts
import {
  RoturSocket,        // the WebSocket client class
  BeamClient,         // the Beam client class
  performAuth,        // popup login without a Rotur instance
  AuthError,          // thrown by login() and performAuth()
  ApiError,           // thrown by failed requests
  iconToSvg,          // render a Rotur icon string to SVG markup
  METHOD_PERMISSIONS, // "namespace.method" -> required permission
  NAMESPACE_BY_CLASS, // namespace class name -> namespace key
  resolvePermissions, // permission list for a set of methods
} from "rotur-sdk";
```

The package has two more entry points:

- `rotur-sdk/vite`: a Vite plugin that scans your code for SDK calls and adds the matching permissions to the login flow.
- `rotur-sdk/preact`: Preact components (`BadgeDetail`, `PetAnimation`). Import their styles from `rotur-sdk/preact.css`.
