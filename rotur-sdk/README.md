# Rotur SDK

The official TypeScript SDK for the Rotur platform. Authenticate, read profiles, post, manage friends, keys, groups, items, gifts, tokens, cosmetics, push notifications, files, and more, all from a single client.

## Installation

```bash
npm install rotur-sdk
```

## Quick Start

```ts
import { Rotur } from "rotur-sdk";

const rotur = new Rotur();

// 1. Log in (opens rotur.dev auth popup)
await rotur.login();

// 2. Use the API
const { username } = await rotur.me.checkAuth();
console.log("Logged in as", username);

// 3. Connect to the real-time status WebSocket
const { user_id, user } = await rotur.connectSocket();
```

## Creating a Client

```ts
// With an existing token (e.g. from localStorage)
const rotur = new Rotur({ token: "your-token-here" });

// With a custom WebSocket URL
const rotur = new Rotur({ wsUrl: "wss://api.rotur.dev/status/ws" });
```

## Authentication

See [Authentication](account/authentication.md) for the full auth flow including popup-based login, link codes, and token management.

## API Namespaces

The `Rotur` client exposes the following namespaces:

| Namespace | Description |
|-----------|-------------|
| `rotur.me` | Your account: profile, transfers, badges, blocking, notes |
| `rotur.profiles` | Public user profiles, existence checks, avatars |
| `rotur.posts` | Create, delete, like, reply, repost, search posts |
| `rotur.friends` | Friend list, send/accept/reject/cancel/remove |
| `rotur.following` | Follow/unfollow, follower/following lists |
| `rotur.notifications` | Claw notifications |
| `rotur.keys` | Access keys: create, buy, manage |
| `rotur.items` | Marketplace items: create, buy, sell, transfer |
| `rotur.gifts` | Gift codes: create, claim, cancel |
| `rotur.tokens` | Sub-tokens: create, manage, revoke |
| `rotur.groups` | Groups: create, join, roles, events, tips, products |
| `rotur.systems` | Registered systems |
| `rotur.stats` | Economy, user, and follower statistics |
| `rotur.status` | HTTP status lookups (use `rotur.socket` for real-time) |
| `rotur.validators` | Generate and validate validator tokens |
| `rotur.link` | Link-code auth flow for non-browser contexts |
| `rotur.cosmetics` | Cosmetics shop, purchase, equip |
| `rotur.push` | Web push notification management |
| `rotur.files` | User file system: upload, read, delete |
| `rotur.storage` | App-scoped key/value storage on the user's account |
| `rotur.standing` | User standing/reputation lookups |
| `rotur.devfund` | Dev fund escrow transfers |
| `rotur.check` | Ban status checks |
| `rotur.socket` | Real-time WebSocket: presence, activities, rooms |

Each namespace is documented in its own page. See the sidebar for details.

## Token & Session

```ts
rotur.token;        // current auth token (string | null)
rotur.loggedIn;     // boolean: is a token set?
rotur.setToken(t);  // manually set a token
rotur.logout();     // clear token & disconnect socket
```

## Error Handling

All API errors throw an `ApiError`:

```ts
import { ApiError } from "rotur-sdk";

try {
  await rotur.me.transfer("someone", 100);
} catch (err) {
  if (err instanceof ApiError) {
    console.log(err.status);  // HTTP status code
    console.log(err.data);    // { error: "Insufficient funds" }
  }
}
```

## Other Exports

Beyond the client, the package exports a few standalone helpers:

```ts
import {
  RoturSocket,          // the WebSocket client class
  performAuth,          // popup auth without a Rotur instance
  AuthError,            // thrown by login/performAuth
  iconToSvg,            // render a Rotur icon string to SVG markup
  METHOD_PERMISSIONS,   // map of "namespace.method" -> required permission
  resolvePermissions,   // compute the permission list for a set of methods
} from "rotur-sdk";
```

There is also a Vite plugin at `rotur-sdk/vite` that scans your code for SDK calls and injects the matching permission scopes into the login flow automatically.

## Real-Time (WebSocket)

See [Status & WebSocket](realtime/status.md) for connecting, joining rooms, setting presence, and handling events.
