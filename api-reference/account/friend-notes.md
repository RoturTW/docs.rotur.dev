# Friend Notes

Friend notes are private notes you keep about other Rotur users, like Discord's user notes. Only you can see them. The other user is never notified and notes never appear in public profile data.

Writing notes needs a **Plus** [subscription](subscriptions.md) or higher. You can still read and delete your notes after your subscription ends.

> **Base URL:** `https://api.rotur.dev`
> **Auth:** `Authorization: Bearer <token>`. The legacy `auth` query parameter is also accepted. Sub-tokens need `account:profile` for all note endpoints.

Each user can have one note from you, up to 300 characters of plain text. Writing a new note replaces the old one. Notes are stored in `sys.notes` on your account.

## POST `/me/note/{username}`

Creates or replaces your note about a user. `PUT /v2/me/notes/{username}` does the same.

**Auth:** Required. Plus tier or higher.

### Parameters

| Name | In | Type | Required | Description |
| --- | --- | --- | --- | --- |
| `username` | path | string | Yes | The user the note is about |
| `note` | body | string | Yes | Note text, up to 300 characters. You can send it as the `note` query parameter instead of a JSON body. |

### Example

```http
POST /me/note/goober123
Authorization: Bearer YOUR_TOKEN
Content-Type: application/json

{ "note": "Met in Origin chat\nLikes purple themes" }
```

**Response `200`:**

```json
{ "success": true }
```

### Errors

| Status | When |
| --- | --- |
| `400` | `note` is missing or longer than 300 characters |
| `403` | Your tier is below Plus, or the token is missing, invalid, or lacks `account:profile` |

## DELETE `/me/note/{username}`

Deletes your note about a user. `DELETE /v2/me/notes/{username}` does the same.

**Auth:** Required.

**Response `200`:**

```json
{ "success": true }
```

## GET `/me/notes`

Returns all your notes, keyed by username. Users you have no note for are left out. `GET /v2/me/notes` does the same.

**Auth:** Required.

**Response `200`:**

```json
{
  "notes": {
    "goober123": "Met in Origin chat\nLikes purple themes",
    "colon_three": "Artist, commissions open"
  }
}
```
