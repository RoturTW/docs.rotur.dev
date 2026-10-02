# Files

`rotur.files` reads and writes the signed-in user's file system. Every method needs a token.

Files use the OFSF format. Each file or folder is an entry: an array of 14 values (type, name, location, data, created time, and so on), identified by a UUID. See [Files](../../claw/api-endpoints/files.md) in the API reference for the entry layout, the change commands, and storage limits.

{% hint style="warning" %}
The SDK types file results as `FileEntry` (`{ uuid, name, path, size, created, modified }`), but the API does not send that shape. The return types below are what the API actually sends. Cast the results until the SDK types are fixed.
{% endhint %}

## rotur.files.index()

Gets every entry, with the data of files over 50 KB replaced by `false`. Use it to list files without downloading large ones.

**Auth:** Required. Sub-tokens need `files:view`.

```ts
const index = await rotur.files.index();
```

**Returns:** a flat array of entries, 14 values per file or folder, one after another. `null` or `[]` when the user has no files.

## rotur.files.all()

Gets every entry, including all file data. The response can be large.

**Auth:** Required. Sub-tokens need `files:view`.

```ts
const entries = await rotur.files.all();
```

**Returns:** a flat array, as for `index()`.

## rotur.files.getByUUID(uuid)

Gets one entry by UUID.

**Auth:** Required. Sub-tokens need `files:view`.

```ts
const entry = await rotur.files.getByUUID("a1b2c3d4e5f6a7b8c9d0e1f2a3b4c5d6");
```

**Returns:** one entry, an array of 14 values.

## rotur.files.getByPath(path)

Gets one entry by path, such as `origin/(c) users/alice/readme.txt`. Paths are matched in lowercase.

**Auth:** Required. Sub-tokens need `files:view`.

```ts
const entry = await rotur.files.getByPath("origin/(c) users/alice/readme.txt");
```

**Returns:** one entry, an array of 14 values. Throws `ApiError` `404` when no file has that path.

## rotur.files.byUUIDs(uuids)

Gets several entries by UUID in one request.

**Auth:** Required. Sub-tokens need `files:view`.

{% hint style="warning" %}
The API also needs a `username` field in the body, which `byUUIDs()` does not send, so it currently fails with `400`. Call `getByUUID()` for each file instead.
{% endhint %}

```ts
const { files } = await rotur.files.byUUIDs(["uuid-1", "uuid-2"]);
```

**Returns:** `{ files }`, an object of entries keyed by UUID.

## rotur.files.stats(uuids)

Gets the size and modification time of several files.

**Auth:** Required. Sub-tokens need `files:view`.

```ts
const { stats } = await rotur.files.stats(["uuid-1", "uuid-2"]);
```

**Returns:** `{ stats: Array<{ uuid, ok, size?, mtime? }> }`. `size` is in bytes, `mtime` is an ISO date string, and `ok` is `false` for UUIDs that are invalid or do not exist.

## rotur.files.pathIndex()

Gets a map of paths to UUIDs.

**Auth:** Required. Sub-tokens need `files:view`.

```ts
const { index, username } = await rotur.files.pathIndex();
```

**Returns:** `{ index: Record<string, string>, username }`

## rotur.files.usage()

Gets how much storage you use.

**Auth:** Required. Sub-tokens need `files:view`.

```ts
const { size } = await rotur.files.usage();
// "15.23 MB"
```

**Returns:** `{ username, size }`. `size` is a readable string in bytes, KB, MB, or GB. The SDK types this as `{ used, max }`, which the API does not send.

## rotur.files.upload(files)

Applies a batch of changes to your files. The object you pass is sent as the request body, so it must be `{ updates: [...] }`, where each change has a `command` (`UUIDa` to add, `UUIDr` to replace one value, `UUIDd` to delete) and a `uuid`. If any change is invalid, or the result would go over your storage limit, nothing is saved.

**Auth:** Required. Sub-tokens need `files:manage`.

```ts
await rotur.files.upload({
  updates: [{ command: "UUIDd", uuid: "a1b2c3d4e5f6a7b8c9d0e1f2a3b4c5d6" }],
});
```

**Returns:** `{ payload, used_size?, available_size? }`. Throws `ApiError` `413` when you are over your storage limit, and `400` for other failures.

## rotur.files.deleteAll()

Deletes every file in your file system.

{% hint style="danger" %}
This removes all files, not only the ones your app created.
{% endhint %}

**Auth:** Required. Sub-tokens need `files:delete`.

```ts
await rotur.files.deleteAll();
```

**Returns:** `{ message, username }`
