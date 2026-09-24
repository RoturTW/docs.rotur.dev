# Check

`rotur.check` runs bulk checks on accounts.

## rotur.check.banned(usernames)

Checks a list of usernames and returns the ones that are banned.

**Auth:** Required. Not listed in `METHOD_PERMISSIONS`.

```ts
const { banned } = await rotur.check.banned(["alice", "bob", "charlie"]);
// ["bob"]
```

**Returns:** `{ banned: string[] }`
