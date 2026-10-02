---
description: Keep someone out of your app, for a while or for good. Rotur enforces it.
---

# Ban people from your app

A ban keeps one Rotur account out of your app. Rotur enforces it for you:

* their tokens for your app stop working straight away;
* they can't approve your app on the consent screen, so they can't sign in again;
* they see "You've been banned from *your app*." with your reason and when the ban ends.

They are never told who on your team banned them.

You can ban anyone with a Rotur account, even someone who has never used your app, so you can keep a known troublemaker out in advance. You can't ban the app's owner or managers: remove them from the team first.

Bans only cover your app. For behaviour that breaks Rotur's rules or the law, [send a report to Rotur](handle-reports.md#send-a-report-to-rotur) as well.

## Ban someone

`PUT /v2/apps/<app>/bans/<user>`

Bans the account, or updates an existing ban. `<user>` is a username or Rotur user ID.

**Auth:** [App credentials](apps-api.md), or the dashboard.

### Parameters

| Name | In | Type | Required | Description |
| --- | --- | --- | --- | --- |
| `reason` | body | string | Yes | Why. **The banned person sees this.** Up to 300 characters |
| `note` | body | string | No | A private note for your team. Up to 1000 characters |
| `expires_at` | body | integer | No | When the ban ends, in Unix milliseconds. Leave it out for a permanent ban |

### Example

```sh
curl -X PUT https://api.rotur.dev/v2/apps/$APP/bans/grimtag \
  -u "$APP:$ROTUR_CLIENT_SECRET" -H "Content-Type: application/json" \
  -d '{"reason": "Harassing other artists", "note": "Third warning", "expires_at": 1791000000000}'
```

**Response `200`:**

```json
{
  "id": "1f2e3d4c-…",
  "username": "grimtag",
  "avatar": "https://avatars.rotur.dev/1f2e3d4c-…",
  "reason": "Harassing other artists",
  "note": "Third warning",
  "created_at": 1790000000000,
  "updated_at": 1790000000000,
  "expires_at": 1791000000000,
  "by": "app"
}
```

`by` is `"app"` for bans made with your app's credentials, or the team member who made it from the dashboard. Only your team sees `by` and `note`.

### Errors

| Status | When |
| --- | --- |
| `400` | `reason` is missing or too long, `note` is too long, or `expires_at` is in the past |
| `400` | They're on your app's team (`Remove them from the app's team before banning them`) |
| `404` | No such account (`User not found`) |

## Lift a ban

`DELETE /v2/apps/<app>/bans/<user>`

**Auth:** [App credentials](apps-api.md), or the dashboard.

Returns `204`. They can sign in again straight away. Their old tokens stay revoked.

Returns `404` with `They aren't banned` if there's no ban.

## List bans

`GET /v2/apps/<app>/bans`

Lists bans that are still in force, most recently changed first. Bans that have ended are left out.

**Auth:** [App credentials](apps-api.md), or the dashboard.

**Response `200`:**

```json
{
  "bans": [
    {
      "id": "1f2e3d4c-…",
      "username": "grimtag",
      "avatar": "https://avatars.rotur.dev/1f2e3d4c-…",
      "reason": "Harassing other artists",
      "note": "Third warning",
      "created_at": 1790000000000,
      "updated_at": 1790000000000,
      "expires_at": 1791000000000,
      "by": "app"
    }
  ]
}
```

## Check whether someone is banned

There's no endpoint to look up one person's ban, and you don't need one. Once you ban someone, their sign-in to your app stops working, so they can't make new validators. A validator they made before the ban gets `app_banned` when your server [checks it](check-whos-calling.md), at the latest when your server's cached answer runs out, within 5 minutes.

If you keep your own sessions, end theirs when you ban them. The `user.banned` [webhook](webhooks.md) is for bans *from Rotur*, not from your app.
