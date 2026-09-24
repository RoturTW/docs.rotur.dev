# Connect to Rotur

A project connects to Rotur by logging a user in. Logging in opens the Rotur socket, joins your designation, and loads the account, friends and balance. This page covers the connection and login blocks.

## Quick start

```
when green flag clicked
connect to server with designation: [rtr], system: [rotur] and version: [v9]
open login prompt with style [https://origin.mistium.com/Resources/auth.css]

when authenticated
say (join [Logged in as ] (client username))
```

Set the designation, system and version first, because logging in reads them. See [Designations](rotur-designations.md) for how the designation is used.

## Connection blocks

### Connect to server

```
connect to server with designation: [rtr], system: [rotur] and version: [v9]
```

Command. Stores your designation, system and version. If a user is already logged in, it also opens the socket. If nobody is logged in yet it doesn't connect; use one of the login blocks below.

| Argument | Default | Description |
| --- | --- | --- |
| `DESIGNATION` | `rtr` | The room your client joins. Other users see you on this designation. |
| `SYSTEM` | `rotur` | The system name. The dropdown lists the systems from `https://api.rotur.dev/systems`, and you can drop a reporter in. The login prompt passes it to rotur.dev. |
| `VERSION` | `v9` | Your app's version. It's attached to the messages you send as part of the `client` object. |

### Other connection blocks

| Block | Type | Description |
| --- | --- | --- |
| `<account server online>` | Boolean | `true` if `https://api.rotur.dev/systems` responds successfully |
| `<connected to server>` | Boolean | `true` while the socket is open |
| `disconnect from server` | Command | Logs out and closes the socket |
| `when connected to server` | Hat | Fires when the socket is ready |
| `when disconnected from server` | Hat | Fires when the socket closes |

The socket reconnects on its own 3 seconds after it closes, as long as a user is still logged in.

## Log in

### Login prompt

```
open login prompt with style [https://origin.mistium.com/Resources/auth.css]
```

Command. Opens `https://rotur.dev/auth` in a popup so the user can sign in there, then connects and fires `when authenticated`. If the browser blocks the popup, the page opens in a full-screen frame instead. The prompt times out after 120 seconds.

{% hint style="info" %}
The current extension doesn't use the `STYLE_URL` input. The login page is always rotur.dev's own.
{% endhint %}

### Log in with a token

```
(login with token: [token])
```

Reporter. Logs in with an existing Rotur token and connects.

| Returns | When |
| --- | --- |
| `Logged In` | The token was accepted and the socket connected |
| `No token provided` | The input is empty |
| Error message | The token was rejected or the connection failed |

### Other login blocks

| Block | Type | Description |
| --- | --- | --- |
| `<authenticated>` | Boolean | `true` when connected and the account has loaded |
| `when authenticated` | Hat | Fires after a successful login with the prompt or a token |
| `(user token)` | Reporter | The token for the current session, or empty if nobody is logged in |
| `logout` | Command | Logs out and closes the socket, the same as `disconnect from server` |

{% hint style="warning" %}
Username and password login has been removed. The hidden `login with username: ... and password: ...` blocks in older projects now return "Username/password login was retired. Use the login prompt or login with token." The hidden `register with username: ...` block tells users to register on rotur.dev instead.
{% endhint %}

## Delete account

```
(delete account)
```

Reporter in the **DANGER ZONE** category, hidden until you click **Show Danger Zone**. It asks the user to confirm, then deletes the logged-in account and disconnects. It returns `Account Deleted Successfully`, `Cancelled`, or `Failed to delete account: ` followed by the error.

## Connecting without the extension

Older versions of the extension connected through CloudLink to `wss://rotur.mistium.com` in the `roturTW` room. That protocol is documented in the [Deprecated](../deprecated/what-is-a-websocket.md) section. The current extension doesn't use it. It talks to `https://api.rotur.dev` and the status socket at `wss://api.rotur.dev/status/ws` through the [Rotur SDK](../rotur-sdk/README.md), which you can use directly outside TurboWarp.
