# Cosmetics

`rotur.cosmetics` browses, buys, equips, and gifts cosmetics: visual items such as profile overlays and backgrounds. Browsing and reading other users' cosmetics needs no token; everything else does.

Cosmetics are returned as `CosmeticCatalogEntryPublic`: `{ id, cosmetic_type, name, description, image_url, pricing_type, price, creator, creator_pct, featured, purchases, created_at, raw_url? }`. `pricing_type` is `"free"` or `"paid"`.

## rotur.cosmetics.shop(options?)

Lists cosmetics in the shop.

**Auth:** None.

| Option | Type | Description |
| --- | --- | --- |
| `type` | string | Only this cosmetic type, for example `"overlay"` |
| `featured` | boolean | Only featured cosmetics |
| `search` | string | Search text |
| `sort` | string | `"newest"`, `"price_low"`, `"price_high"`, or `"popular"` |
| `limit` | number | Maximum results |
| `offset` | number | Results to skip |

```ts
const { items, total } = await rotur.cosmetics.shop({
  type: "overlay",
  featured: true,
  sort: "newest",
  limit: 20,
});
```

**Returns:** `{ items, total, offset, limit }`

## rotur.cosmetics.get(id)

Gets one cosmetic.

**Auth:** None.

```ts
const cosmetic = await rotur.cosmetics.get("cosmetic-id");
```

**Returns:** `CosmeticCatalogEntryPublic`

## rotur.cosmetics.mine()

Lists the cosmetics you own and the ones you have equipped.

**Auth:** Required. Sub-tokens need `cosmetics:view`.

```ts
const { active_cosmetics, owned_cosmetics } = await rotur.cosmetics.mine();
```

**Returns:** `{ active_cosmetics, owned_cosmetics }`. `active_cosmetics` maps each cosmetic type to the equipped cosmetic; `owned_cosmetics` is an array.

## rotur.cosmetics.purchase(id)

Buys a cosmetic.

**Auth:** Required. Sub-tokens need `cosmetics:buy`.

```ts
const result = await rotur.cosmetics.purchase("cosmetic-id");
```

**Returns:** `{ message, cosmetic, price, creator_share?, platform_share?, new_total? }`

## rotur.cosmetics.equip(id)

Equips a cosmetic you own.

**Auth:** Required. Sub-tokens need `cosmetics:equip`.

```ts
await rotur.cosmetics.equip("cosmetic-id");
```

**Returns:** `{ message }`

## rotur.cosmetics.unequip(type)

Unequips the cosmetic of the given type.

**Auth:** Required. Sub-tokens need `cosmetics:equip`.

```ts
await rotur.cosmetics.unequip("overlay");
```

**Returns:** `{ message }`

## rotur.cosmetics.gift(id, to, note?)

Buys a cosmetic for another user.

**Auth:** Required. Sub-tokens need `cosmetics:gift`.

```ts
const result = await rotur.cosmetics.gift("cosmetic-id", "alice", "Enjoy");
```

**Returns:** `{ message, gift_id, cosmetic, to, price, tax, total_paid, creator_share, platform_share, new_balance }`

## rotur.cosmetics.gifts()

Lists cosmetic gifts you have received and sent.

**Auth:** Required. Sub-tokens need `cosmetics:view`.

```ts
const { received, sent } = await rotur.cosmetics.gifts();
```

**Returns:** `{ received, sent }`. Each gift is `{ id, cosmetic_id, from_user_id, to_user_id, note, amount, created_at, claimed_at? }`.

## rotur.cosmetics.forUser(username)

Gets the cosmetics a user has equipped.

**Auth:** None.

```ts
const { active_cosmetics } = await rotur.cosmetics.forUser("alice");
```

**Returns:** `{ active_cosmetics, id, username }`

## rotur.cosmetics.forUsers(usernames)

Gets equipped cosmetics for several users in one request.

**Auth:** None.

```ts
const byUser = await rotur.cosmetics.forUsers(["alice", "bob"]);
// { alice: { active_cosmetics, id, username }, bob: { ... } }
```

**Returns:** an object keyed by username, each value shaped like `forUser()`.

## Asset URL helpers

These build asset URLs without making a request.

| Method | URL |
| --- | --- |
| `rotur.cosmetics.getOverlayAssetUrl(id)` | `https://api.rotur.dev/v2/cosmetics/overlays/<id>.gif` |
| `rotur.cosmetics.getBackgroundAssetUrl(id)` | `https://api.rotur.dev/v2/cosmetics/backgrounds/<id>.mp4` |

**Auth:** None.

**Returns:** `string`
