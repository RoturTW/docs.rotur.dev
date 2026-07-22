# /files

The files API manages user file storage via the OFSF (Origin File System Format). All endpoints require authentication. Sub-tokens need `files:view` to read, `files:manage` to write, and `files:delete` to wipe.

**Base URL:** `https://api.rotur.dev`

{% hint style="info" %}
The read endpoints (`GET /files`, `/files/index`, `/files/entries`, `/files/by-path/...`, `GET /files/by-uuid`) return their JSON payload as an `application/octet-stream` body rather than with a JSON content type.
{% endhint %}

## Update Files

### POST `/files`

Applies a batch of file operations (add, replace, delete) to the authenticated user's file system.

**Body (JSON):**

```json
{
  "updates": [
    {
      "command": "UUIDa",
      "uuid": "a1b2c3d4e5f6a7b8c9d0e1f2a3b4c5d6",
      "dta": ["type", "name", "location", "data", null, 0, 0, 1715054321000, 1715054321000, "", "", 1024, [], "uuid"]
    },
    {
      "command": "UUIDr",
      "uuid": "existing-file-uuid",
      "idx": 3,
      "dta": "new file data"
    },
    {
      "command": "UUIDd",
      "uuid": "file-to-delete-uuid"
    }
  ]
}
```

The maximum file system size depends on your subscription tier. If you exceed it you get a `413` with the payload `Max Upload Size Exceeded`.

## Get File by UUID

### GET `/files?uuid=FILE_UUID`

Returns a single file's data by its UUID. Returns `400` if `uuid` is missing.

## Get Files Index

### GET `/files/index`

Returns a lightweight index of all files. File data is stripped for files over 50KB.

## Get All Files

### GET `/files/entries`

Returns all files with full data included.

## Get Files by UUIDs

### POST `/files/by-uuid`

Returns multiple files at once.

**Body (JSON):**

```json
{
  "username": "mist",
  "uuids": ["a1b2c3d4e5f6", "b7c8d9e0f1a2"]
}
```

Both fields are required. **Response:** `{ "files": [...] }`

## File Usage

### GET `/files/usage`

Returns the total storage used by the authenticated user.

**Response:**

```json
{
  "username": "mist",
  "size": "15.23 MB"
}
```

## Delete All Files

### DELETE `/files`

Deletes the authenticated user's entire file system.

**Response:**

```json
{
  "message": "deleted",
  "username": "mist"
}
```

## Get File by Path

### GET `/files/by-path/*path`

Retrieves a file by its originFS path (case-insensitive). Returns `404` if the path is not in the index.

**Example:**

```
GET /files/by-path/origin/(c) users/mist/document.txt
```

## Path Index

### GET `/files/path-index`

Returns a map of all file paths to their UUIDs.

**Response:**

```json
{
  "index": {
    "origin/(c) users/mist/document.txt": "a1b2c3d4e5f6"
  },
  "username": "mist"
}
```

## File Stats

### POST `/files/stats`

Returns size and modification stats for a list of file UUIDs.

**Body (JSON):**

```json
{
  "uuids": ["a1b2c3d4e5f6", "b7c8d9e0f1a2"]
}
```

**Response:** `{ "stats": [...] }`

## Legacy Endpoints

These endpoints are maintained for backwards compatibility:

| Endpoint | Equivalent |
| --- | --- |
| `GET /read-files` | `GET /files/entries` |
| `GET /read-file?uuid=...` | `GET /files?uuid=...` |
| `GET /read-index` | `GET /files/index` |
