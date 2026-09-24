# Systems

`rotur.systems` reads and manages systems, the platforms registered on Rotur such as originOS. It also manages system badges: badges a system defines, with tiers unlocked at progress thresholds. `list()` and `badges()` need no token; every other method does.

Systems are returned as `System`: `{ name, owner: { name, discord_id }, wallpaper, designation, icon, badges? }`.

## rotur.systems.list()

Lists every system.

**Auth:** None.

```ts
const systems = await rotur.systems.list();
// { originOS: { name: "originOS", owner: { name: "mist", discord_id: "..." }, ... } }
```

**Returns:** `Record<string, System>`, keyed by system name.

## rotur.systems.users(system)

Lists the users of a system.

**Auth:** Required. Sub-tokens need `groups:view`.

```ts
const users = await rotur.systems.users("originOS");
```

**Returns:** `string[]` of usernames.

## rotur.systems.update(system, key, value)

Sets one field on a system.

**Auth:** Required. Sub-tokens need `account:settings`.

```ts
await rotur.systems.update("originOS", "wallpaper", "https://example.com/bg.png");
```

**Returns:** `{ message }`

## rotur.systems.reload()

Reloads the system list on the server.

**Auth:** Required. Sub-tokens need `account:settings`.

```ts
await rotur.systems.reload();
```

**Returns:** `{ message }`

## rotur.systems.badges(system)

Lists a system's badge definitions.

**Auth:** None.

```ts
const badges = await rotur.systems.badges("originOS");
```

**Returns:** `SystemBadgeDefinition[]`: `{ id, label, unit?, tiers, created_at?, updated_at? }`. Each tier is `{ threshold, name, color, glyph, description }`.

## rotur.systems.createBadge(system, definition)

Adds a badge definition to a system.

**Auth:** Required. Sub-tokens need `account:settings`.

```ts
await rotur.systems.createBadge("originOS", {
  id: "apps-opened",
  label: "Apps opened",
  unit: "apps",
  tiers: [
    { threshold: 10, name: "Explorer", color: "#4caf50", glyph: "★", description: "Opened 10 apps" },
  ],
});
```

**Returns:** the created `SystemBadgeDefinition`.

## rotur.systems.updateBadge(system, badge, definition)

Replaces a badge definition.

**Auth:** Required. Sub-tokens need `account:settings`.

```ts
await rotur.systems.updateBadge("originOS", "apps-opened", definition);
```

**Returns:** the updated `SystemBadgeDefinition`.

## rotur.systems.deleteBadge(system, badge)

Deletes a badge definition.

**Auth:** Required. Sub-tokens need `account:settings`.

```ts
await rotur.systems.deleteBadge("originOS", "apps-opened");
```

**Returns:** the response body (untyped).

## rotur.systems.setBadgeProgress(system, badge, username, progress)

Sets a user's progress on a badge.

**Auth:** Required. Sub-tokens need `account:settings`.

```ts
await rotur.systems.setBadgeProgress("originOS", "apps-opened", "alice", 12);
```

**Returns:** the response body (untyped).

## rotur.systems.revokeBadge(system, badge, username)

Removes a badge from a user.

**Auth:** Required. Sub-tokens need `account:settings`.

```ts
await rotur.systems.revokeBadge("originOS", "apps-opened", "alice");
```

**Returns:** the response body (untyped).
