# Friend Notes

Friend Notes let you privately store small bits of information about other users on Rotur, similar to Discord-style notes. Your notes are **visible only to you**, never to the other user. Use them for reminders, context, or anything you want to remember about someone.

This is a paid feature. You need a **Plus** subscription or higher on [Ko-fi](https://ko-fi.com/mistium/tiers).

Notes are stored on your account under the `sys.notes` key.

> **Authentication:** Required. Send your token in an `Authorization` header as `Authorization: Bearer YOUR_TOKEN` (preferred). `auth` query parameter is still accepted as a legacy fallback.

## How to Use Friend Notes

### Writing Notes

To set or update your note for a user:

```
POST https://api.rotur.dev/me/note/{username}?note={encoded_note}
```

You can also send the note as JSON: `{"note": "your note"}`.

### Deleting Notes

To delete your note for a user:

```
DELETE https://api.rotur.dev/me/note/{username}
```

### Reading Notes

To read all your notes:

```
GET https://api.rotur.dev/me/notes
```

The response is a dictionary keyed by username:

```json
{
  "notes": {
    "goober123": "Met in Origin chat\nLikes purple themes",
    "colon_three": "Artist, commissions open"
  }
}
```

If you have no note for a user, they simply won't appear in the response.

## Privacy

Friend Notes are 100% private:

* Only you can view your notes.
* Other users are never notified that you added, edited, or deleted a note about them.
* Notes never appear in public profile data.

## Limits

* Maximum note length: 300 characters.
* Notes are plain text. No formatting or markup is interpreted.
* Each user gets one note. Writing again replaces the previous text.

## Who Can Use Friend Notes

Plus tier and above include Friend Notes. Free and Lite accounts do not have them.

Subscribe here to unlock this feature: **[https://ko-fi.com/mistium/tiers](https://ko-fi.com/mistium/tiers)**
