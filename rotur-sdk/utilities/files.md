# Files

`rotur.files` reads and writes the signed-in user's file system. Every method needs a token.

Files are returned as `FileEntry`: `{ uuid, name, path, size, created, modified }`.

## rotur.files.index()

Gets the file index (metadata only).

**Auth:** Required. Sub-tokens need `files:view`.

```ts
const index = await rotur.files.index();
```

**Returns:** `FileEntry[]`

## rotur.files.all()

Gets every file entry.

**Auth:** Required. Sub-tokens need `files:view`.

```ts
const entries = await rotur.files.all();
```

**Returns:** `FileEntry[]`

## rotur.files.getByUUID(uuid)

Gets one file by UUID.

**Auth:** Required. Sub-tokens need `files:view`.

```ts
const file = await rotur.files.getByUUID("uuid");
```

**Returns:** `FileEntry`

## rotur.files.getByPath(path)

Gets one file by path.

**Auth:** Required. Sub-tokens need `files:view`.

```ts
const file = await rotur.files.getByPath("documents/readme.txt");
```

**Returns:** `FileEntry`

## rotur.files.byUUIDs(uuids)

Gets several files by UUID in one request.

**Auth:** Required. Sub-tokens need `files:view`.

```ts
const files = await rotur.files.byUUIDs(["uuid-1", "uuid-2"]);
```

**Returns:** `FileEntry[]`

## rotur.files.stats(uuids)

Gets the size and modification time of several files.

**Auth:** Required. Sub-tokens need `files:view`.

```ts
const { stats } = await rotur.files.stats(["uuid-1", "uuid-2"]);
```

**Returns:** `{ stats: Array<{ uuid, ok, size?, mtime? }> }`

## rotur.files.pathIndex()

Gets a map of paths to UUIDs.

**Auth:** Required. Sub-tokens need `files:view`.

```ts
const { index, username } = await rotur.files.pathIndex();
```

**Returns:** `{ index: Record<string, string>, username }`

## rotur.files.usage()

Gets how much storage you use and your limit.

**Auth:** Required. Sub-tokens need `files:view`.

```ts
const { used, max } = await rotur.files.usage();
```

**Returns:** `{ used, max }`

## rotur.files.upload(files)

Uploads files. The object you pass is sent as the request body.

**Auth:** Required. Sub-tokens need `files:manage`.

```ts
await rotur.files.upload(payload);
```

**Returns:** the response body as an object.

## rotur.files.deleteAll()

Deletes every file in your file system.

{% hint style="danger" %}
This removes all files, not only the ones your app created.
{% endhint %}

**Auth:** Required. Sub-tokens need `files:delete`.

```ts
await rotur.files.deleteAll();
```

**Returns:** `{ message }`
