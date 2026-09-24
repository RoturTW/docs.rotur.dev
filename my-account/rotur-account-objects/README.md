# Rotur Account Objects

Your Rotur account is a JSON object of keys and values. The server manages some keys; you can set the rest yourself. Read the whole object with [`GET /me`](../../claw/api-endpoints/me.md).

> **Base URL:** `https://api.rotur.dev`
> **Auth:** `Authorization: Bearer <token>`. Sub-tokens need `account:profile` to update keys. The update endpoint also accepts the token as an `auth` field in the JSON body, but not as a query parameter.

## Update a key

`POST /me/update` (or `PATCH /v2/me`) sets one key at a time. Send the key and its new value as JSON:

```http
POST /me/update
Authorization: Bearer YOUR_TOKEN
Content-Type: application/json

{ "key": "pronouns", "value": "she/her" }
```

**Response `200`:**

```json
{
  "message": "User key updated successfully",
  "username": "mist",
  "key": "pronouns",
  "value": "she/her"
}
```

## Limits

| Limit | Value |
| --- | --- |
| Key name | Up to 20 characters |
| Key value | Up to 1,000 characters |
| Whole account object | Up to 25,000 bytes across all keys that don't start with `sys.` |

You cannot write keys that start with `sys.`; the server manages them. These keys are also locked: `key`, `password`, `max_size`, `created`, `last_login`, `sys.id` and `sys.index`.

## Read-only keys

| Key | Description |
| --- | --- |
| `key` | Your account token. Keep it secret; anyone with it can act as you. |
| `created` | When the account was created, in milliseconds. Example: `1712792411288`. |
| `max_size` | Your file storage limit in bytes, as a string. Set from your [subscription tier](../subscriptions.md). Example: `"5000000"` (5 MB on Free). |
| `discord_id` | The ID of the Discord account linked to this Rotur account. Set when you link Discord. Example: `"603952506330021898"`. |
| `sys.id` | Your permanent user ID. |
| `sys.index` | Your account number, in order of sign-up. |
| `sys.currency` | Your Rotur credit balance, rounded to 2 decimal places. It cannot go negative. Example: `14.35`. |
| `sys.friends` | Usernames of your friends. Example: `["throwaway", "rm"]`. |
| `sys.requests` | Usernames that have sent you a friend request. Example: `["temp"]`. |
| `sys.blocked` | Usernames you have blocked. |
| `sys.purchases` | IDs of items you own. Example: `["c4068074d5ed5bfcae9a91874383dab9"]`. |
| `sys.total_logins` | How many times you have signed in with your password. Example: `106`. |
| `sys.last_login` | When you last signed in with your password, in milliseconds. |
| `sys.transactions` | Your credit history. Each entry has `type`, `user`, `amount`, `note`, `time` and `new_total`. How far back it goes depends on your tier; see [Transactions and Taxes](../transactions-and-taxes.md). |
| `sys.notes` | Your private notes about other users, keyed by username. See [Friend Notes](../friend-notes.md). |
| `sys.subscription` | Your subscription: `tier`, `active`, `next_billing` and billing details. |
| `sys.social_links` | Up to 3 social links shown on your profile. |
| `sys.email_verified` | Whether your email address is verified. |

Badges are not read from the account object. Get them from [`GET /badges`](../../claw/api-endpoints/badges.md) or the `badges` field of a profile. See [Rotur Badges](../rotur-badges.md).

## Writable keys

| Key | Description |
| --- | --- |
| `username` | Your username, case-insensitive. 3–20 characters of lowercase letters, numbers, `_`, `.` and `-`. The new name must not be taken. |
| `display_name` | The name shown on your profile. |
| `email` | Your email address. Changing it marks your email as unverified and sends a new verification email. |
| `private` | `true` to make your profile visible only to you and your friends. |
| `bio` | Your profile bio. The maximum length depends on your tier: 200 characters on Free, up to 1,000 on Pro. Paid tiers can use [bio templates](../bio-templates.md). |
| `pronouns` | Shown on your profile. Example: `"she/her"`. |
| `pfp` | Set it to an image data URI to upload a new avatar. The avatar is then served at `https://avatars.rotur.dev/<username>`. Animated GIF avatars need Plus or higher. |
| `banner` | Set it to an image data URI to upload a profile banner. Banners cost a one-time 30-credit unlock unless you have Pro or higher. Animated banners need Plus or higher. |
| `system` | Set it to the exact name of a registered Rotur system to move your account to that system. |
| `theme` | Colors that apps can use to match your preferences. See the example below. |
| `wallpaper` | A URL to use as your desktop wallpaper. Pexels images work well because Pexels does not enforce CORS. Example: `https://images.pexels.com/photos/1612351/pexels-photo-1612351.jpeg`. |

Example `theme`:

```json
{
  "primary": "#111111",
  "secondary": "#333333",
  "tertiary": "#555555",
  "text": "#ffffff",
  "background": "#000000",
  "accent": "#57cdac"
}
```

For keys used only by originOS, see [originOS specific keys](originos-specific-keys.md).

## Errors

| Status | When |
| --- | --- |
| `400` | The key or value is missing, too long, or locked; the key starts with `sys.`; the account would exceed 25,000 bytes; the bio is longer than your tier allows; the new username or email is invalid or taken; `pfp` or `banner` is not a data URI |
| `403` | The token is missing or invalid, lacks `account:profile`, or you don't have enough credits to unlock banners |
| `404` | The `system` you asked for does not exist |
