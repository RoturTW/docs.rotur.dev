# Systems

Accessed via `rotur.systems`. Systems are registered platforms on Rotur.

## List All Systems

```ts
const systems = await rotur.systems.list();
// { "originOS": { name: "originOS", owner: { name: "mist", discord_id: "..." }, ... }, ... }
```

## Get System Users

```ts
const users = await rotur.systems.users("originOS");
```

## Update System

```ts
await rotur.systems.update("originOS", "wallpaper", "https://example.com/bg.png");
```

## Reload Systems

```ts
await rotur.systems.reload();
```
