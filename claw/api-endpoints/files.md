# Files

Read and write your file storage, which uses the OFSF (Origin File System Format). Every file is an entry identified by a UUID, and the server also keeps an index from file paths to UUIDs.

> **Auth:** Every endpoint requires a token and works on your own files only. Sub-tokens need `files:view` to read, `files:manage` to write, and `files:delete` to delete everything.

{% hint style="info" %}
`GET /files`, `/files/by-uuid`, `/files/index`, `/files/entries` and `/files/by-path/...` send their JSON with an `application/octet-stream` content type. Parse the body as JSON yourself.
{% endhint %}

### File entries

A file entry is an array of 14 values. The ones the server reads are:

| Position (1-based) | Value |
| --- | --- |
| 1 | Type, such as `.txt` or `.folder` |
| 2 | Name, without the type |
| 3 | Location, such as `origin/(c) users/mist` |
| 4 | Data. For a folder, the UUIDs of its children |
| 8 | Created time, in Unix milliseconds (set by the server) |
| 9 | Edited time, in Unix milliseconds (set by the server) |

A file's path is its location, a `/`, then its name and type, all lowercase: `origin/(c) users/mist/document.txt`. File UUIDs are 32 hexadecimal characters.

### Storage limits

The total size of your files is capped by your subscription tier: 5 MB (Free), 25 MB (Lite), 100 MB (Plus), 1 GB (Pro) or 10 GB (Max).

## POST `/files`

Applies a batch of changes to your files. If any change is invalid or the result would go over your storage limit, nothing is saved.

**Auth:** Required. Sub-tokens need `files:manage`.

### Parameters

| Name | In | Type | Required | Description |
| --- | --- | --- | --- | --- |
| `updates` | body | array | Yes | The changes to apply, in order |

Each change has a `command` and a `uuid`:

| Command | Other fields | Effect |
| --- | --- | --- |
| `UUIDa` | `dta`: a full 14-value entry | Adds a file. If a file already has that UUID, the change is skipped. If a file already has the same path, it is replaced |
| `UUIDr` | `idx`: position 1–14, `dta`: the new value | Replaces one value in an existing entry |
| `UUIDd` | none | Deletes a file |

### Example

```http
POST /files
Authorization: Bearer <token>
Content-Type: application/json

{
  "updates": [
    {
      "command": "UUIDa",
      "uuid": "a1b2c3d4e5f6a7b8c9d0e1f2a3b4c5d6",
      "dta": [".txt", "document", "origin/(c) users/mist", "hello", null, 0, 0, 0, 0, "", "", 1024, [], "a1b2c3d4e5f6a7b8c9d0e1f2a3b4c5d6"]
    },
    {
      "command": "UUIDr",
      "uuid": "b7c8d9e0f1a2b3c4d5e6f7a8b9c0d1e2",
      "idx": 4,
      "dta": "new file data"
    },
    {
      "command": "UUIDd",
      "uuid": "c3d4e5f6a7b8c9d0e1f2a3b4c5d6e7f8"
    }
  ]
}
```

**Response `200`:**

```json
{
  "payload": "Successfully Updated Origin Files",
  "used_size": 15970000,
  "available_size": 84030000
}
```

`used_size` and `available_size` are in bytes.

### Errors

| Status | When |
| --- | --- |
| `400` | The body is not valid JSON or has no `updates` |
| `400` | `payload` is `Invalid file update` (unknown command, bad UUID, bad entry, or `idx` out of range), or the server could not load or save your files |
| `413` | `payload` is `Max Upload Size Exceeded`. `used_size` is what the total would have been and `available_size` is negative |

## GET `/files`

Returns one file entry by UUID. `GET /files/by-uuid?uuid=...` does the same.

**Auth:** Required. Sub-tokens need `files:view`.

### Parameters

| Name | In | Type | Required | Description |
| --- | --- | --- | --- | --- |
| `uuid` | query | string | Yes | The file's UUID |

### Example

```http
GET /files?uuid=a1b2c3d4e5f6a7b8c9d0e1f2a3b4c5d6
Authorization: Bearer <token>
```

**Response `200`:** the file entry.

### Errors

| Status | When |
| --- | --- |
| `400` | `UUID is required` |
| `500` | The file could not be read, for example because the UUID does not exist |

## GET `/files/index`

Returns all your file entries, with the data left out of files larger than 50 KB. Use it to list files without downloading everything.

**Auth:** Required. Sub-tokens need `files:view`.

### Example

```http
GET /files/index
Authorization: Bearer <token>
```

**Response `200`:** an array of file entries.

## GET `/files/entries`

Returns all your file entries with their full data.

**Auth:** Required. Sub-tokens need `files:view`.

### Example

```http
GET /files/entries
Authorization: Bearer <token>
```

**Response `200`:** an array of file entries.

## POST `/files/by-uuid`

Returns several file entries at once.

**Auth:** Required. Sub-tokens need `files:view`.

### Parameters

| Name | In | Type | Required | Description |
| --- | --- | --- | --- | --- |
| `username` | body | string | Yes | Must be present, but files always come from your own account |
| `uuids` | body | string[] | Yes | UUIDs of the files to return |

### Example

```http
POST /files/by-uuid
Authorization: Bearer <token>
Content-Type: application/json

{ "username": "mist", "uuids": ["a1b2c3d4e5f6a7b8c9d0e1f2a3b4c5d6"] }
```

**Response `200`:**

```json
{
  "files": [...]
}
```

### Errors

| Status | When |
| --- | --- |
| `400` | `username` or `uuids` is missing |

## GET `/files/by-path/*path`

Returns a file entry by its path. Matching ignores case.

**Auth:** Required. Sub-tokens need `files:view`.

### Parameters

| Name | In | Type | Required | Description |
| --- | --- | --- | --- | --- |
| `path` | path | string | Yes | The file's path, URL-encoded |

### Example

```http
GET /files/by-path/origin/(c)%20users/mist/document.txt
Authorization: Bearer <token>
```

**Response `200`:** the file entry.

### Errors

| Status | When |
| --- | --- |
| `404` | `File not found` (the path is not in your index) |

## GET `/files/path-index`

Returns the map of your file paths to UUIDs.

**Auth:** Required. Sub-tokens need `files:view`.

### Example

```http
GET /files/path-index
Authorization: Bearer <token>
```

**Response `200`:**

```json
{
  "index": {
    "origin/(c) users/mist/document.txt": "a1b2c3d4e5f6a7b8c9d0e1f2a3b4c5d6"
  },
  "username": "mist"
}
```

## POST `/files/stats`

Returns the size and last-modified time of the files you list.

**Auth:** Required. Sub-tokens need `files:view`.

### Parameters

| Name | In | Type | Required | Description |
| --- | --- | --- | --- | --- |
| `uuids` | body | string[] | Yes | UUIDs of the files |

### Example

```http
POST /files/stats
Authorization: Bearer <token>
Content-Type: application/json

{ "uuids": ["a1b2c3d4e5f6a7b8c9d0e1f2a3b4c5d6", "not-a-file"] }
```

**Response `200`:**

```json
{
  "stats": [
    { "uuid": "a1b2c3d4e5f6a7b8c9d0e1f2a3b4c5d6", "size": 412, "mtime": "2024-05-07T04:12:01Z", "ok": true },
    { "uuid": "not-a-file", "mtime": "0001-01-01T00:00:00Z", "ok": false }
  ]
}
```

`size` is the stored size in bytes. `ok` is `false` for UUIDs that are invalid or do not exist.

### Errors

| Status | When |
| --- | --- |
| `400` | `uuids` is missing |

## GET `/files/usage`

Returns how much storage your files use.

**Auth:** Required. Sub-tokens need `files:view`.

### Example

```http
GET /files/usage
Authorization: Bearer <token>
```

**Response `200`:**

```json
{
  "username": "mist",
  "size": "15.23 MB"
}
```

`size` is a readable string in bytes, KB, MB or GB.

## DELETE `/files`

Deletes all of your files.

**Auth:** Required. Sub-tokens need `files:delete`.

{% hint style="danger" %}
This removes your entire file system and cannot be undone.
{% endhint %}

### Example

```http
DELETE /files
Authorization: Bearer <token>
```

**Response `200`:**

```json
{
  "message": "deleted",
  "username": "mist"
}
```

## Legacy endpoints

These older paths still work and behave like their replacements.

| Endpoint | Same as |
| --- | --- |
| GET `/read-files` | GET `/files/entries` |
| GET `/read-file?uuid=...` | GET `/files?uuid=...` |
| GET `/read-index` | GET `/files/index` |
