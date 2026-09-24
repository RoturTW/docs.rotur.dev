# Rotur Badges

Badges are small icons shown on your profile. The server works them out from your account each time they are requested, so they are always current.

To read badges, call [`GET /badges`](../claw/api-endpoints/badges.md) for your own account or read the `badges` field of a profile. You can hide and reorder your badges with `/badges/preferences` (see below).

## Badge object

| Field | Type | Description |
| --- | --- | --- |
| `id` | string | Stable badge ID, such as `rich` |
| `name` | string | Display name |
| `icon` | string | An ICN vector drawing string, not an image URL |
| `description` | string | What the badge is for |
| `issuer` | string | Who issued it: `rotur`, or a system name for system badges |
| `evolving` | boolean | `true` if the badge levels up as you progress |
| `level` | number | Current level, for badges with levels |
| `progress` | number | Your current value, such as your credit balance |
| `next_threshold` | number | The value needed for the next level, if there is one |

## Automatic badges

| ID | Name | How to earn it | Levels |
| --- | --- | --- | --- |
| `system` | Your system's name | Your account belongs to a Rotur system, such as originOS | — |
| `official` | Official | The account is an official Rotur service account | — |
| `rich` | rich | Hold at least 200 credits | 200, 500, 1,000, 2,500, 5,000 credits |
| `friendly` | friendly | Have at least 10 friends | 10, 25, 50, 100, 250 friends |
| `pro` | pro | Have a Pro subscription or higher | — |
| `account-age` | *N*-year member | Have an account for at least a year | One level per full year |
| `subscriber-tenure` | *N*-month subscriber | Subscribe for at least a month | One level per month subscribed |
| `pioneer` | Pioneer | Be one of the first 1,000 accounts | First 1,000, 500, 100 accounts |
| `hoarder` | Hoarder | Own at least 5 cosmetics | 5, 15, 30, 60, 100 cosmetics |
| `community-pillar` | Community Pillar | Own a group with at least 5 established members (email verified and account at least 7 days old) | 5, 25, 100, 500 members |
| `keyholder` | Keyholder | Buy paid keys from at least 1 other creator | 1, 3, 10, 25 distinct creators |

## Manually granted badges

Some badges are granted by hand, for example to members of the Rotur dev team, app developers, and creators of Rotur operating systems. They are listed in the badges file below, along with who holds them.

{% @github-files/github-code-block url="https://github.com/RoturTW/Badges/blob/main/badges.json" %}

## System badges

Owners of a Rotur system can define their own badges with levels and award them to users through `/v2/systems/{system}/badges`. These badges show the system's name as their `issuer`.

## Hide and reorder badges

`GET /badges/preferences` returns your preferences and every badge you hold. `PUT /badges/preferences` replaces them. Both are also available under `/v2/me/badges/preferences`.

**Auth:** Required. Sub-tokens need `account:view` to read and `account:profile` to update.

### Parameters

| Name | In | Type | Required | Description |
| --- | --- | --- | --- | --- |
| `hidden_badges` | body | string[] | No | Badge IDs to hide from your profile |
| `badge_order` | body | string[] | No | Badge IDs in the order to show them. Badges you leave out keep their default order after these. |

Each list can hold up to 500 IDs. The `official` and `system` badges always show first and cannot be hidden or moved.

### Example

```http
PUT /badges/preferences
Authorization: Bearer YOUR_TOKEN
Content-Type: application/json

{ "hidden_badges": ["hoarder"], "badge_order": ["pro", "rich"] }
```

**Response `200`:** `preferences`, `badges` (every badge with `hidden` and `pinned` flags) and `visible_badges`.
