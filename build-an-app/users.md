---
description: Who counts as one of your app's users, what Rotur tells you about them, and how they leave.
---

# See who uses your app

Rotur keeps a short record of who uses each app: when they first used it and when they last did. Nothing else: no activity, no content, no history.

## Who counts as a user

Someone becomes one of your app's users when:

* they sign in with Rotur and your app swaps the code for a token, or
* they created their Rotur account through your app.

Their "last used" time updates when they sign in and when your app calls `/oauth/userinfo` with their token, at most once an hour.

If your app used to be a [system](migrate-from-systems.md), accounts made on that system count as your users for badges and signals. They only appear in the list below once they sign in through your app.

Only your own users can be given your [badges](badges.md), named as reporters in your [reports](handle-reports.md) or asked about in [signals](safety.md). You can [ban](bans.md) anyone, user or not.

## List your users

`GET /v2/apps/<app>/users`

Lists the people who have used your app, most recently active first.

**Auth:** [App credentials](apps-api.md), or the dashboard.

### Parameters

| Name | In | Type | Required | Description |
| --- | --- | --- | --- | --- |
| `q` | query | string | No | Only usernames containing this text |
| `offset` | query | integer | No | How many to skip. Defaults to `0` |
| `limit` | query | integer | No | How many to return, up to 100. Defaults to `50` |

### Example

```sh
curl "https://api.rotur.dev/v2/apps/app_0123456789abcdef/users?limit=2" \
  -u "app_0123456789abcdef:$ROTUR_CLIENT_SECRET"
```

**Response `200`:**

```json
{
  "users": [
    {
      "id": "7ebdf483-5b9f-4b70-9edc-a1f2827391f9",
      "username": "kit",
      "avatar": "https://avatars.rotur.dev/7ebdf483-5b9f-4b70-9edc-a1f2827391f9",
      "joined_at": 1790000000000,
      "last_used_at": 1790086400000
    }
  ],
  "total": 42,
  "offset": 0
}
```

Times are Unix milliseconds. `username` and `avatar` are `null` for accounts that are banned on Rotur or no longer exist.

That is everything Rotur shares about your users. Your team never sees anyone's age, date of birth, email address, sign-in history or standing on Rotur.

## When people leave

People see the apps they use at [rotur.dev/me/apps](https://rotur.dev/me/apps). From there they can:

* **Leave your app.** Rotur revokes every token they gave your app, removes them from your users list and sends your webhook a `user.left` event. Delete what you hold about them.
* **Send you a privacy request**, if you've turned privacy requests on for your webhook: a request for a copy of their data, or for you to delete it. See [Webhooks](webhooks.md).

Rotur also tells your webhook when someone deletes their Rotur account (`user.deleted`) or is banned from Rotur (`user.banned`).
