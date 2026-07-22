# Cosmetics

Cosmetics are visual items you can buy for your Rotur account. The first cosmetic type is **overlays**: animated GIFs rendered on top of your avatar. More types may be added later.

> **Base URL:** `https://api.rotur.dev`
>
> **Authentication:** Endpoints marked "Auth" accept your token as an `auth` query parameter or an `Authorization: Bearer <token>` header.

{% hint style="info" %}
Authenticated endpoints require a verified email address and an accepted Terms of Service. If either is missing you get a `403` explaining which one.
{% endhint %}

***

## Core Concepts

### Cosmetic Types

Each cosmetic has a `cosmetic_type` that determines what it is and where it applies.

| Type | Description | Active Slot |
|---|---|---|
| `overlay` | A GIF overlay rendered on top of your avatar | One overlay at a time |

Each type has its own active slot, so you can have one cosmetic of each type equipped at the same time.

### Pricing Types

| Type | Description |
|---|---|
| `free` | No cost. Acquired instantly through the purchase endpoint. |
| `paid` | Costs credits. Revenue is split between the creator and the platform. |

### Revenue Split

When a paid cosmetic is purchased:

* **`creator_pct`%** of the price goes to the cosmetic's creator
* The **remaining %** goes to the platform (the `mist` account)

For example, if an overlay costs 100 credits and `creator_pct` is 80, the creator receives 80 credits and the platform receives 20.

### Subscriber Discount

If you have any active subscription tier, you pay **20% less** for paid cosmetics. The whole discounted price goes to the creator and the platform takes nothing.

### Limits

* You can own at most **200** cosmetics.
* Cosmetic IDs are 1-50 characters of letters, numbers, hyphens and underscores.

## Endpoints

| Endpoint | Method | Auth | Permission | Description |
|---|---|---|---|---|
| [`/cosmetics/shop`](shop.md) | GET | No | - | Browse the cosmetics shop |
| [`/cosmetics/items/:id`](item-detail.md) | GET | No | - | Get details for a single cosmetic |
| [`/cosmetics/mine`](my-cosmetics.md) | GET | Yes | `cosmetics:view` | View your owned and active cosmetics |
| [`/cosmetics/purchase/:id`](purchase.md) | POST | Yes | `cosmetics:buy` | Purchase or acquire a cosmetic |
| [`/cosmetics/equip/:id`](equip.md) | POST | Yes | `cosmetics:equip` | Equip a cosmetic you own |
| [`/cosmetics/unequip`](unequip.md) | POST | Yes | `cosmetics:equip` | Remove your active cosmetic of a given type |
| [`/cosmetics/gift`](gift.md) | POST | Yes | `cosmetics:gift` | Gift a paid cosmetic to another user |
| [`/cosmetics/gifts/mine`](my-gifts.md) | GET | Yes | `cosmetics:view` | List cosmetic gifts you have sent and received |
| [`/cosmetics/overlays/:file`](overlay-assets.md) | GET | No | - | Fetch a raw overlay GIF asset |
| [`/profile/:username/cosmetics`](profile-cosmetics.md) | GET | No | - | View another user's active cosmetics |

### Admin Endpoints

These manage the catalog itself. They require the server's admin token in the `Authorization` header, not a user token.

| Endpoint | Method | Description |
|---|---|---|
| `/cosmetics/admin/list` | GET | List all catalog entries. Optional `?type=` filter. Returns `{"cosmetics": [...], "total": n}` |
| `/cosmetics/admin/create` | POST | Add a cosmetic to the catalog |
| `/cosmetics/admin/update/:id` | PATCH | Update a catalog entry |
| `/cosmetics/admin/delete/:id` | DELETE | Remove a cosmetic from the catalog |

### v2 API

The same functionality is available under `https://api.rotur.dev/v2` with slightly different paths:

| v1 | v2 |
|---|---|
| `GET /cosmetics/shop` | `GET /v2/cosmetics` |
| `GET /cosmetics/items/:id` | `GET /v2/cosmetics/:id` |
| `GET /cosmetics/mine` | `GET /v2/cosmetics/mine` |
| `POST /cosmetics/purchase/:id` | `POST /v2/cosmetics/:id/purchase` |
| `POST /cosmetics/equip/:id` | `POST /v2/cosmetics/:id/equip` |
| `POST /cosmetics/unequip` | `POST /v2/cosmetics/unequip` |
| `POST /cosmetics/gift` | `POST /v2/cosmetics/gifts` |
| `GET /cosmetics/gifts/mine` | `GET /v2/cosmetics/gifts/mine` |
| `GET /cosmetics/overlays/:file` | `GET /v2/cosmetics/overlays/:file` |
| `GET /profile/:username/cosmetics` | `GET /v2/users/:username/cosmetics` |
