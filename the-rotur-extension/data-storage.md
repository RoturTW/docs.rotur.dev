# Data storage

Data storage keeps your project's data in the logged-in user's Rotur account. Each project picks a storage ID, and its keys are kept separate from other projects' data.

## Set a storage ID

```
set storage id to [id]
```

Command. Every storage block works inside this ID, so set it once after logging in. Until it's set, storage blocks return `Storage ID not set`. You can change it at any time.

| Block | Type | Returns |
| --- | --- | --- |
| `<storage id has been set>` | Boolean | `true` once a storage ID is set |
| `(storage id)` | Reporter | The current storage ID, or an empty string |

## Read and write keys

Every storage block sends a request to Rotur, so the user has to be online and logged in.

| Block | Type | Description |
| --- | --- | --- |
| `(get key from storage [key])` | Reporter | The key's value. Objects come back as JSON; missing keys return an empty string. |
| `set key [key] to [value] in storage` | Command | Saves a value |
| `<key [key] exists in storage>` | Boolean | `true` if the key has a value |
| `delete key [key] from storage` | Command | Removes a key |
| `(get all keys from storage)` | Reporter | JSON array of key names in this storage ID |
| `(get all values from storage)` | Reporter | JSON array of values in this storage ID |
| `clear storage` | Command | Deletes every key in this storage ID |

### Example

```
when authenticated
set storage id to [my-game]
set key [highscore] to (score) in storage
say (get key from storage [highscore])
```

## Storage usage

All sizes are in characters.

| Block | Type | Returns |
| --- | --- | --- |
| `(storage usage (characters))` | Reporter | Size of the data in this storage ID |
| `(storage limit (characters))` | Reporter | The account's total storage limit |
| `(storage remaining (characters))` | Reporter | The account's limit minus this storage ID's usage |
| `(account storage usage (characters))` | Reporter | Storage used across the whole account |
| `(account storage limit (characters))` | Reporter | The account's total storage limit |
| `(account storage remaining (characters))` | Reporter | Storage left on the whole account |
