# Files

Accessed via `rotur.files`. Each user has a personal file system for storing data.

## List Files

```ts
// Index (metadata only)
const index = await rotur.files.index();

// All entries
const entries = await rotur.files.all();
```

## Get a File

```ts
// By UUID
const file = await rotur.files.getByUUID("uuid-here");

// By path
const file = await rotur.files.getByPath("documents/readme.txt");
```

## Storage Usage

```ts
const { used, max } = await rotur.files.usage();
```

## Batch Operations

```ts
// Get file sizes
const sizes = await rotur.files.stats(["uuid1", "uuid2"]);

// Get multiple files by UUID
const files = await rotur.files.byUUIDs(["uuid1", "uuid2"]);

// Path index
const pathIndex = await rotur.files.pathIndex();
```

## Upload

```ts
await rotur.files.upload({ /* file data */ });
```

## Delete All Files

```ts
await rotur.files.deleteAll();
```
