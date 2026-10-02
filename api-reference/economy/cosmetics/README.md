# Cosmetics

Cosmetics are visual items for a Rotur profile: avatar overlays and profile backgrounds. Use these endpoints to browse the shop, buy or gift cosmetics, equip them, and read the cosmetics other users have equipped.

> **Base URL:** `https://api.rotur.dev`
>
> **Auth:** Endpoints marked as requiring auth take your token in the `Authorization: Bearer <token>` header. The legacy `auth` query parameter is still accepted. Sub-tokens need the permission listed for each endpoint; your main token has every permission.

{% hint style="info" %}
Authenticated endpoints also require a verified email address and accepted Terms of Service. If either is missing you get a `403` with `"error": "Email address not verified"` or `"error": "Terms-Of-Service are not accepted or outdated"`.
{% endhint %}

## Concepts

### Cosmetic types

Each cosmetic has a `cosmetic_type`. You can have one cosmetic of each type equipped at a time.

| Type | What it is | Asset |
| --- | --- | --- |
| `overlay` | An animated image rendered on top of your avatar | GIF, served from [`/cosmetics/overlays/:file`](overlay-assets.md) |
| `background` | A video background for your profile | MP4, served from [`/cosmetics/backgrounds/:file`](overlay-assets.md) |

### Custom cosmetics

Besides catalog items, a user can own two cosmetics that are not in the shop:

| ID | Type | When you have it |
| --- | --- | --- |
| `custom_overlay` | `overlay` | You uploaded a custom overlay (a Plus perk, or granted by an equipped pet) |
| `custom_background` | `background` | You uploaded a background video (a Pro perk, or granted by an equipped pet) |

They are added to your owned cosmetics and equipped when you upload the file, and unequipped when the upload or the perk goes away. Their `image_url` and `raw_url` point at `https://avatars.rotur.dev/.overlay/<username>` or `https://avatars.rotur.dev/.backgrounds/<username>`.

### Pricing

| `pricing_type` | Behavior |
| --- | --- |
| `free` | No cost. You claim it through the [purchase endpoint](purchase.md). |
| `paid` | Costs credits. The price is split between the creator and the platform. |

A cosmetic with a `price` of `0` is treated as free, whatever its `pricing_type`.

### Revenue split

For a paid cosmetic, `creator_pct`% of the price goes to the creator and the rest goes to the platform (the `mist` account). For example, if an overlay costs 100 credits and `creator_pct` is 80, the creator receives 80 credits and the platform receives 20.

### Subscriber discount

If you have any active subscription, you pay 20% less for paid cosmetics. The whole discounted price goes to the creator and the platform takes nothing. The discount does not apply to [gifts](gift.md).

### Limits

* You can own at most 200 cosmetics.
* Cosmetic IDs are 1–50 characters: letters, numbers, hyphens and underscores.

## Endpoints

| Endpoint | Auth | Permission | Description |
| --- | --- | --- | --- |
| [GET `/cosmetics/shop`](shop.md) | No | – | Browse the shop |
| [GET `/cosmetics/items/:id`](item-detail.md) | No | – | Get one cosmetic |
| [GET `/cosmetics/mine`](my-cosmetics.md) | Yes | `cosmetics:view` | List your owned and equipped cosmetics |
| [POST `/cosmetics/purchase/:id`](purchase.md) | Yes | `cosmetics:buy` | Buy or claim a cosmetic |
| [POST `/cosmetics/equip/:id`](equip.md) | Yes | `cosmetics:equip` | Equip a cosmetic you own |
| [POST `/cosmetics/unequip`](unequip.md) | Yes | `cosmetics:equip` | Unequip your cosmetic of one type |
| [POST `/cosmetics/gift`](gift.md) | Yes | `cosmetics:gift` | Gift a paid cosmetic to another user |
| [GET `/cosmetics/gifts/mine`](my-gifts.md) | Yes | `cosmetics:view` | List gifts you sent and received |
| [GET `/cosmetics/overlays/:file`](overlay-assets.md) | No | – | Fetch an overlay GIF |
| [GET `/cosmetics/backgrounds/:file`](overlay-assets.md) | No | – | Fetch a background MP4 |
| [GET `/profile/:username/cosmetics`](profile-cosmetics.md) | No | – | Get a user's equipped cosmetics |
| [POST `/profile/cosmetics`](profile-cosmetics.md) | No | – | Get equipped cosmetics for up to 100 users |

### Admin endpoints

These manage the catalog. They take the server's admin token in the `Authorization` header, not a user token, and return `403` with `"error": "Invalid admin authentication"` otherwise.

| Endpoint | Description |
| --- | --- |
| GET `/cosmetics/admin/list` | List catalog entries. Optional `type` query filter. Returns `{"cosmetics": [...], "total": n}` |
| POST `/cosmetics/admin/create` | Add a cosmetic to the catalog. Returns `201` |
| PATCH `/cosmetics/admin/update/:id` | Update a catalog entry |
| DELETE `/cosmetics/admin/delete/:id` | Remove a catalog entry |

### v2 paths

The same endpoints are available under `https://api.rotur.dev/v2`:

| v1 | v2 |
| --- | --- |
| `GET /cosmetics/shop` | `GET /v2/cosmetics` |
| `GET /cosmetics/items/:id` | `GET /v2/cosmetics/:id` |
| `GET /cosmetics/mine` | `GET /v2/cosmetics/mine` |
| `POST /cosmetics/purchase/:id` | `POST /v2/cosmetics/:id/purchase` |
| `POST /cosmetics/equip/:id` | `POST /v2/cosmetics/:id/equip` |
| `POST /cosmetics/unequip` | `POST /v2/cosmetics/unequip` |
| `POST /cosmetics/gift` | `POST /v2/cosmetics/gifts` |
| `GET /cosmetics/gifts/mine` | `GET /v2/cosmetics/gifts/mine` |
| `GET /cosmetics/overlays/:file` | `GET /v2/cosmetics/overlays/:file` |
| `GET /cosmetics/backgrounds/:file` | `GET /v2/cosmetics/backgrounds/:file` |
| `GET /profile/:username/cosmetics` | `GET /v2/users/:username/cosmetics` |
| `POST /profile/cosmetics` | `POST /v2/profiles/cosmetics` |
| `GET /cosmetics/admin/list` | `GET /v2/cosmetics/admin` |
| `POST /cosmetics/admin/create` | `POST /v2/cosmetics/admin` |
| `PATCH /cosmetics/admin/update/:id` | `PATCH /v2/cosmetics/admin/:id` |
| `DELETE /cosmetics/admin/delete/:id` | `DELETE /v2/cosmetics/admin/:id` |
