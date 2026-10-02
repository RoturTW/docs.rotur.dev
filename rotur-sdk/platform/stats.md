# Stats

`rotur.stats` reads platform-wide statistics. No method needs a token.

## rotur.stats.economy()

Gets credit economy statistics.

**Auth:** None.

```ts
const stats = await rotur.stats.economy();
```

**Returns:** `{ average, total, variance, currency_comparison: { pence, cents } }`. `pence` and `cents` are strings, such as `"0.50p / credit"`.

## rotur.stats.users()

Gets account counts.

**Auth:** None.

```ts
const { total_users, active_users } = await rotur.stats.users();
```

**Returns:** `{ total_users, banned_users, active_users }`

## rotur.stats.mostGained(max?)

Lists the users who gained the most credits over the last month. `max` defaults to `10`, which is also the most it returns.

**Auth:** None.

```ts
const leaderboard = await rotur.stats.mostGained(10);
// [{ user: "alice", earned: 500 }, ...]
```

**Returns:** `Array<{ user, earned }>`

## rotur.stats.followers(max?)

Lists the users with the most followers. `max` defaults to `10` and is capped at `100`.

**Auth:** None.

```ts
const top = await rotur.stats.followers(10);
// [{ username: "mist", follower_count: 500 }, ...]
```

**Returns:** `Array<{ username, follower_count }>`

## rotur.stats.systems()

Counts users per system. Banned and private accounts are left out.

**Auth:** None.

```ts
const systems = await rotur.stats.systems();
// { originOS: 150, rotur: 300 }
```

**Returns:** `Record<string, number>`, keyed by system name.

## rotur.stats.posts(days?)

Counts posts per day over the last `days` days, including today. `days` defaults to `7` and can be from `1` to `90`; other values fall back to `7`.

**Auth:** None.

```ts
const { total, buckets } = await rotur.stats.posts(30);
```

**Returns:** `{ days, total, buckets: Array<{ date, start, count }> }`
