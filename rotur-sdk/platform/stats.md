# Stats

Accessed via `rotur.stats`. All methods are public (no auth required).

## Economy Stats

```ts
const stats = await rotur.stats.economy();
// { average, total, variance, currency_comparison: { pence, cents } }
```

## User Stats

```ts
const stats = await rotur.stats.users();
// { total_users, banned_users, active_users }
```

## Most Gained Credits

```ts
const leaderboard = await rotur.stats.mostGained(10);
// [{ user: "alice", earned: 500 }, ...]
```

## System Distribution

```ts
const systems = await rotur.stats.systems();
// { "originOS": 150, "rotur": 300, ... }
```

## Follower Leaderboard

```ts
const top = await rotur.stats.followers(10);
// [{ username: "mist", follower_count: 500 }, ...]
```
