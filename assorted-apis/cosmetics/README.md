# Cosmetics

Cosmetics are purchasable visual items for your Rotur account. The first cosmetic type is **overlays**, with more types planned in the future.

> **Base URL:** `https://api.rotur.dev/`
>
> **Authentication:** Endpoints that modify your account require a valid `auth` query parameter or `Authorization: Bearer <token>` header.

***

## Core Concepts

### Cosmetic Types

Each cosmetic has a `cosmetic_type` that determines what it is and where it applies. Currently supported types:

| Type | Description | Active Slot |
|---|---|---|
| `overlay` | A GIF overlay rendered on top of your avatar | One overlay at a time |

Additional types (badges, frames, etc.) can be added in the future. Each type has its own active slot — you can have one of each type equipped simultaneously.

### Pricing Types

| Type | Description |
|---|---|
| `free` | No cost, instantly acquired on purchase |
| `paid` | Costs credits; revenue is split between the creator and the platform |

### Revenue Split

When a paid cosmetic is purchased:
- **`creator_pct`%** of the price goes to the cosmetic's creator
- The **remaining %** goes to the platform (`Mist` account)

For example, if an overlay costs 100 credits and the creator has `creator_pct` set to 80, the creator receives 80 credits and Mist receives 20.

### User Cosmetics Storage

Structure:

```json
{
  "active_cosmetics": {
    "overlay": "cat_ears"
  },
  "owned_cosmetics": ["cat_ears", "huopa", "maga"]
}
```

## Endpoints

| Endpoint | Method | Auth | Admin | Description |
|---|---|---|---|---|
| [`/cosmetics/shop`](shop.md) | GET | No | No | Browse the cosmetics shop |
| [`/cosmetics/items/:id`](item-detail.md) | GET | No | No | Get details for a single cosmetic |
| [`/cosmetics/mine`](my-cosmetics.md) | GET | Yes | No | View your owned and active cosmetics |
| [`/cosmetics/purchase/:id`](purchase.md) | POST | Yes | No | Purchase or acquire a cosmetic |
| [`/cosmetics/equip/:id`](equip.md) | POST | Yes | No | Equip a cosmetic you own |
| [`/cosmetics/unequip`](unequip.md) | POST | Yes | No | Remove your active cosmetic of a given type |
| [`/cosmetics/admin/list`](admin-list.md) | GET | No | Yes | List all catalog entries (admin) |
| [`/cosmetics/admin/create`](admin-create.md) | POST | No | Yes | Add a cosmetic to the catalog (admin) |
| [`/cosmetics/admin/update/:id`](admin-update.md) | PATCH | No | Yes | Update a catalog entry (admin) |
| [`/cosmetics/admin/delete/:id`](admin-delete.md) | DELETE | No | Yes | Remove a cosmetic from the catalog (admin) |

***

## Adding a New Cosmetic Type

To add a new cosmetic type (e.g. `badge`, `frame`):

1. Add a new `CosmeticType` constant in `cosmetics.go`
2. Add the type to the `switch` validation in `adminCreateCosmetic` and `adminUpdateCosmetic`
3. Add equip/unequip logic in `equipCosmetic`/`unequipCosmetic` if the type needs to set a user property (like overlays set `sys.overlay`)
4. Create the asset directory under `COSMETICS_ASSETS_PATH/<type>/`
5. Add any new avatar handlers if the cosmetic affects profile rendering
