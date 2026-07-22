# Cosmetics

Accessed via `rotur.cosmetics`. Cosmetics are visual items (overlays, etc.) that users can purchase and equip.

## Browse the Shop

```ts
const { items, total } = await rotur.cosmetics.shop({
  type: "overlay",          // filter by cosmetic type
  featured: true,           // only featured items
  search: "cool",           // search name/description
  sort: "newest",           // "newest" | "price_low" | "price_high" | "popular"
  limit: 20,
  offset: 0,
});
```

No auth required.

## Get Cosmetic Details

```ts
const item = await rotur.cosmetics.get("cosmetic-id");
```

## My Cosmetics

```ts
const { active_cosmetics, owned_cosmetics } = await rotur.cosmetics.mine();
// active_cosmetics: { [type]: cosmetic }
// owned_cosmetics: cosmetic[]
```

## Purchase

```ts
const result = await rotur.cosmetics.purchase("cosmetic-id");
// { message, cosmetic, price, creator_share, platform_share, new_total }
```

## Equip / Unequip

```ts
await rotur.cosmetics.equip("cosmetic-id");
await rotur.cosmetics.unequip("overlay");  // type of cosmetic to unequip
```

## Another User's Cosmetics

Look up what a user has equipped (no auth required):

```ts
const { active_cosmetics, username, id } = await rotur.cosmetics.forUser("alice");
```

Or several users at once:

```ts
const byUser = await rotur.cosmetics.forUsers(["alice", "bob"]);
// { alice: { active_cosmetics, ... }, bob: { active_cosmetics, ... } }
```
