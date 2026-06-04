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

## Get Cosmetic Details

```ts
const item = await rotur.cosmetics.get("cosmetic-id");
```

## My Cosmetics

```ts
const { active_cosmetics, owned_cosmetics } = await rotur.cosmetics.mine();
```

## Purchase

```ts
const result = await rotur.cosmetics.purchase("cosmetic-id");
// { message, cosmetic, price, new_total, ... }
```

## Equip / Unequip

```ts
await rotur.cosmetics.equip("cosmetic-id");
await rotur.cosmetics.unequip("overlay");  // type of cosmetic to unequip
```

## Admin Endpoints

These require admin privileges:

```ts
// List all cosmetics in the catalog
const { cosmetics, total } = await rotur.cosmetics.adminList({ type: "overlay" });

// Create a cosmetic
await rotur.cosmetics.adminCreate({
  id: "my-overlay",
  cosmetic_type: "overlay",
  name: "My Overlay",
  description: "A cool overlay",
  image_url: "https://example.com/overlay.gif",
  pricing_type: "paid",
  price: 50,
  creator: "mist",
  creator_pct: 70,
  featured: true,
});

// Update a cosmetic
await rotur.cosmetics.adminUpdate("my-overlay", {
  name: "Updated Name",
  price: 75,
  featured: false,
});

// Delete a cosmetic
await rotur.cosmetics.adminDelete("my-overlay");
```
