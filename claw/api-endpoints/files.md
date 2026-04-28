# /files

The files API manages user file storage via the OFSF (Origin File System Format). All endpoints require authentication.

**Base URL:** `https://social.rotur.dev`

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

The maximum file system size depends on your subscription tier.

## Get File by UUID

### GET `/files?uuid=FILE_UUID`

Returns a single file's data by its UUID.

## Get Files Index

### GET `/files/index`

Returns a lightweight index of all files (data stripped for files over 50KB).

## Get All Files

### GET `/files/entries`

Returns all files with full data included.

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

## Get File by Path

### GET `/files/by-path/*path`

Retrieves a file by its originFS path.

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

Returns size and modification time for a list of file UUIDs.

**Body (JSON):**

```json
{
  "uuids": ["a1b2c3d4e5f6", "b7c8d9e0f1a2"]
}
```

## Legacy Endpoints

These endpoints are maintained for backwards compatibility:

| Endpoint | Equivalent |
| --- | --- |
| `GET /read-files` | `GET /files/entries` |
| `GET /read-file?uuid=...` | `GET /files?uuid=...` |
| `GET /read-index` | `GET /files/index` |
