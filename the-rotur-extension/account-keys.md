# Account keys

A Rotur account is an object of keys and values. These blocks read and set keys on the logged-in account. Use them for user preferences; for app data, use [Data storage](data-storage.md).

## Keys on every account

These are the keys you can expect on an account. The `[KEY]` dropdowns list the keys the logged-in account actually has.

| Key | Writable | Description |
| --- | --- | --- |
| `username` | No | The user's name |
| `created` | No | Unix timestamp of when the account was created |
| `last_login` | No | Unix timestamp of the user's last login |
| `max_size` | No | The maximum number of characters of OFSF data the account can store |
| `system` | No | The operating system the account was created with |
| `pfp` | Yes | URL of the user's profile picture |
| `accent` | Yes | Hex code of the user's preferred accent color |
| `onboot` | Yes | Array of apps to open when the user logs in |
| `theme` | Yes | Object of colors for customization. Default: `{"primary":"#222","secondary":"#555","tertiary":"#777","text":"#fff","background":"#050505","accent":"#57cdac"}` |

### System keys

Keys that start with `sys.` are managed by Rotur. You can read them, but setting one returns "System keys cannot be modified directly".

| Key | Description |
| --- | --- |
| `sys.requests` | Array of incoming friend requests |
| `sys.friends` | Array of usernames you're friends with |
| `sys.items` | Array of IDs of items the user has made |
| `sys.purchases` | Array of IDs of items the user has bought or made |
| `sys.currency` | The user's credit balance |

## Read keys

| Block | Type | Returns |
| --- | --- | --- |
| `(get [KEY])` | Reporter | The key's value. Objects and arrays come back as JSON. Missing keys return an empty string. |
| `<key [KEY] exists>` | Boolean | `true` if the account has the key |
| `(get all keys)` | Reporter | JSON array of key names |
| `(get all values)` | Reporter | JSON array of values, in the same order as `get all keys` |
| `(get account object)` | Reporter | The whole account as a JSON object |
| `when account updated` | Hat | Fires when a key on the account changes, including changes made from another client |

Reading keys is instant: the extension keeps a copy of the account in memory and updates it as changes arrive over the socket.

## Set a key

```
(set [KEY] to [value])
```

Reporter. Sends the new value to Rotur, updates the local copy and fires `when account updated`. The request takes a moment, so it doesn't finish instantly.

| Returns | When |
| --- | --- |
| `Key Set` | The key was updated |
| `Key Too Long, Limit is 1000 Characters` | The value is longer than 1000 characters (checked before sending) |
| `Key length exceeds 20 characters` | The key name is longer than 20 characters |
| `Total account size exceeds 25000 bytes` | The update would take the account over its 25,000-character limit |
| `System keys cannot be modified directly` | The key starts with `sys.` |
| `Key '<key>' cannot be updated` | The key is read-only, such as `created` or `last_login` |
| `Not Connected` / `Not Logged In` | There's no connection or no logged-in user |
