---
description: The origin badge Rotur gives people who joined through your app, and levelled badges you give your users.
---

# Give badges

Badges show on people's Rotur profiles. Your app has two kinds.

## The origin badge

People who created their Rotur account through your app, for example by signing up in the middle of [signing in to it](../advanced-oauth/authorize.md#when-someone-signs-up-during-sign-in), get your app's origin badge automatically.

* Its ID is `app:<your slug>`.
* It shows your app's name and icon, says "Joined Rotur through *your app*", and links to your website if you've set one.
* People can hide it like any other badge.
* It's hidden while your app is suspended.

Signing in to your app doesn't give the origin badge. Which apps someone uses isn't shown on their public profile.

## Your own badges

You can define up to 100 badges and give them to your users. Each badge has levels: someone reaches a level when their progress reaches its threshold, and their profile shows the highest level they've reached.

You can manage badges on your app's **Badges** page on rotur.dev, or with the API below. Giving badges to people is done from your server.

On profiles, your badges have the ID `<prefix>:<badge id>`. The prefix is your slug, or for an app that used to be a [system](migrate-from-systems.md), the system's old name. `GET /v2/apps/<app>/badges` returns it as `prefix`.

### The badge definition

```json
{
  "id": "drawings",
  "label": "Drawings",
  "unit": "drawings",
  "tiers": [
    { "threshold": 1, "name": "Sketcher", "color": "#4ade80", "glyph": "…", "description": "Made {value} {unit}" },
    { "threshold": 25, "name": "Artist", "color": "#60a5fa", "glyph": "…", "description": "Made {value} {unit}" },
    { "threshold": 100, "name": "Master", "color": "#f472b6", "glyph": "…", "description": "Level {level}: made {value} {unit}" }
  ]
}
```

| Field | Rules |
| --- | --- |
| `id` | 1 to 40 lowercase letters, numbers, `_` and `-`, starting with a letter or number |
| `label` | 1 to 80 characters |
| `unit` | Optional, up to 30 characters |
| `tiers` | 1 to 50 levels. Sorted by threshold for you |
| `tiers[].threshold` | A number, 0 or more. Each must be different |
| `tiers[].name` | 1 to 200 characters. Can use `{value}`, `{level}` and `{unit}` |
| `tiers[].color` | Optional. A six-digit hex colour such as `#4ade80`. A default is picked if you leave it out |
| `tiers[].glyph` | The icon: an ICN glyph, 1 to 2000 characters |
| `tiers[].description` | Optional, up to 200 characters. Can use `{value}`, `{level}` and `{unit}` |

In names and descriptions, `{value}` becomes the person's progress, `{level}` their level (starting at 1) and `{unit}` your unit.

Someone whose progress is below the lowest threshold doesn't show the badge.

## List your badges

`GET /v2/apps/<app>/badges`

**Auth:** None.

**Response `200`:**

```json
{
  "badges": [{ "id": "drawings", "label": "Drawings", "unit": "drawings", "tiers": [], "created_at": 1790000000000, "updated_at": 1790000000000 }],
  "prefix": "sketchpad"
}
```

## Create a badge

`POST /v2/apps/<app>/badges`

**Auth:** [App credentials](apps-api.md), or the dashboard.

Send a [badge definition](#the-badge-definition) as JSON.

```sh
curl -X POST https://api.rotur.dev/v2/apps/$APP/badges \
  -u "$APP:$ROTUR_CLIENT_SECRET" -H "Content-Type: application/json" \
  -d '{"id": "drawings", "label": "Drawings", "unit": "drawings",
       "tiers": [{"threshold": 1, "name": "Sketcher", "glyph": "…", "description": "Made {value} {unit}"}]}'
```

**Response `201`:** the saved definition, with `created_at` and `updated_at`.

| Status | When |
| --- | --- |
| `400` | A field breaks the rules above. `error` says which |
| `409` | A badge with that ID already exists (`Badge already exists`), or you already have 100 badges |

## Update or delete a badge

`PUT /v2/apps/<app>/badges/<badge>` replaces the definition. Send the whole definition; the `id` in the path wins. Returns `200` with the saved definition, or `404` `Badge not found`.

`DELETE /v2/apps/<app>/badges/<badge>` deletes it. Returns `204`, or `404` `Badge not found`.

**Auth:** [App credentials](apps-api.md), or the dashboard.

## Give a badge

`PUT /v2/apps/<app>/badges/<badge>/users/<user>` (or `PATCH`)

Sets someone's progress on a badge. If they don't have it yet, this gives it to them. `<user>` is a username or Rotur user ID, and must be one of [your users](users.md).

**Auth:** [App credentials](apps-api.md), or the dashboard.

Send exactly one of:

| Field | Type | What it does |
| --- | --- | --- |
| `progress` | number | Sets progress to this value |
| `delta` | number | Adds this to their current progress (it can be negative) |

Progress can't go below 0.

```sh
# One more drawing
curl -X PUT https://api.rotur.dev/v2/apps/$APP/badges/drawings/users/kit \
  -u "$APP:$ROTUR_CLIENT_SECRET" -H "Content-Type: application/json" \
  -d '{"delta": 1}'
```

**Response `200`:**

```json
{
  "grant": { "system": "sketchpad", "badge_id": "drawings", "progress": 26, "granted_at": 1790000000000, "updated_at": 1790086400000 },
  "badge": {
    "id": "sketchpad:drawings",
    "name": "Artist",
    "icon": "…",
    "description": "Made 26 drawings",
    "issuer": "Sketchpad",
    "evolving": true,
    "level": 2,
    "progress": 26,
    "next_threshold": 100
  }
}
```

`badge` is how it now looks on their profile, or `null` if their progress is below the lowest threshold.

| Status | When |
| --- | --- |
| `400` | You sent both or neither of `progress` and `delta` (`Provide exactly one of progress or delta`), or progress would go below 0 |
| `403` | They aren't one of your users (`Apps may only give badges to their own users`) |
| `404` | The badge or the user doesn't exist |

## Take a badge away

`DELETE /v2/apps/<app>/badges/<badge>/users/<user>`

Removes the badge and the person's progress.

**Auth:** [App credentials](apps-api.md), or the dashboard.

Returns `204`. Returns `404` `Badge grant not found` if they don't have it, and `403` if they aren't one of your users.
