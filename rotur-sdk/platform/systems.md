# Systems

{% hint style="warning" %}
Systems are deprecated. On 2 October 2026 they were replaced by Rotur Apps, and every system became an app. The `/v2/systems` endpoints these methods call still work, but each response sends a `Deprecation: true` header. Move to apps: see [Migrate from systems](../../build-an-app/migrate-from-systems.md).
{% endhint %}

`rotur.systems` reads and manages systems, the platforms registered on Rotur such as originOS. It also manages system badges: badges a system defines, with tiers unlocked at progress thresholds. `list()` and `badges()` need no token; every other method does.

Each system is now backed by the app it became. Badges live on that app, and only the app's owner and managers (or Rotur staff) can change the system, list its users, or manage its badges. They are checked by account ID, not by the owner name stored on the system.

Systems are returned as `System`: `{ name, owner: { name, discord_id }, wallpaper, designation, icon, requires_invite, badges? }`.

## rotur.systems.list()

Lists every system.

**Auth:** None.

```ts
const systems = await rotur.systems.list();
// { originOS: { name: "originOS", owner: { name: "...", discord_id: "..." }, ... } }
```

**Returns:** `Record<string, System>`, keyed by system name.

## rotur.systems.users(system)

Lists the accounts on a system. Only the app's owner and managers can call it.

**Auth:** Required. Sub-tokens need `groups:view`.

```ts
const users = await rotur.systems.users("originOS");
```

**Returns:** `string[]` of usernames.

## rotur.systems.update(system, key, value)

Sets one field on a system. Only the app's owner and managers can call it.

**Auth:** Required. Sub-tokens need `account:settings`.

| Key | Value |
| --- | --- |
| `name` | string. Also renames the system on every account that uses it |
| `wallpaper` | string URL |
| `designation` | string |
| `icon` | string |
| `requires_invite` | boolean |

`owner_name` and `owner_discord_id` can only be changed by Rotur staff. Changing `owner_name` moves the app to the new owner as well.

```ts
await rotur.systems.update("originOS", "wallpaper", "https://example.com/bg.png");
```

**Returns:** `{ message }`

## rotur.systems.reload()

Reloads the system list on the server. Only Rotur staff can call it.

**Auth:** Required. Sub-tokens need `account:settings`.

```ts
await rotur.systems.reload();
```

**Returns:** `{ message }`

## rotur.systems.badges(system)

Lists a system's badge definitions, sorted by ID. They are the badges of the app the system became.

**Auth:** None.

```ts
const badges = await rotur.systems.badges("originOS");
```

**Returns:** `SystemBadgeDefinition[]`: `{ id, label, unit?, tiers, created_at, updated_at }`. Each tier is `{ threshold, name, color, glyph, description }`.

## rotur.systems.createBadge(system, definition)

Adds a badge definition to a system. A system can have up to 100 badges.

**Auth:** Required. Sub-tokens need `account:settings`.

| Field | Rules |
| --- | --- |
| `id` | 1 to 40 lowercase letters, numbers, `_` or `-` |
| `label` | 1 to 80 characters |
| `unit` | Optional, up to 30 characters |
| `tiers` | 1 to 50 tiers with unique, non-negative thresholds |
| `tiers[].name` | 1 to 200 characters |
| `tiers[].color` | Optional six-digit hex colour. A default is picked when left out |
| `tiers[].glyph` | 1 to 2000 characters of ICN |
| `tiers[].description` | Up to 200 characters |

```ts
await rotur.systems.createBadge("originOS", {
  id: "apps-opened",
  label: "Apps opened",
  unit: "apps",
  tiers: [
    { threshold: 10, name: "Explorer", color: "#4caf50", glyph: "...", description: "Opened 10 apps" },
  ],
});
```

**Returns:** the created `SystemBadgeDefinition`. Fails with `409` if the ID is taken or the system already has 100 badges.

## rotur.systems.updateBadge(system, badge, definition)

Replaces a badge definition. The `badge` argument sets the ID, and the same rules as `createBadge()` apply.

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

**Returns:** nothing. The API answers `204 No Content`.

## rotur.systems.setBadgeProgress(system, badge, username, progress)

Sets a user's progress on a badge. `progress` must be a finite, non-negative number. You can only give badges to the app's own users, which includes accounts made on the system.

**Auth:** Required. Sub-tokens need `account:settings`.

```ts
await rotur.systems.setBadgeProgress("originOS", "apps-opened", "alice", 12);
```

**Returns:** `{ grant, badge }`. `grant` is `{ system, badge_id, progress, granted_at, updated_at }` and `badge` is the badge as it now shows on the profile.

## rotur.systems.revokeBadge(system, badge, username)

Removes a badge from a user.

**Auth:** Required. Sub-tokens need `account:settings`.

```ts
await rotur.systems.revokeBadge("originOS", "apps-opened", "alice");
```

**Returns:** nothing. The API answers `204 No Content`, or `404` if the user does not have the badge.
