# Check

`rotur.check` runs bulk checks on accounts.

## rotur.check.banned(usernames)

Checks a list of usernames and returns the ones that are banned. You can also pass user IDs. The list is sent as comma-separated text.

**Auth:** Required. No specific permission. The endpoint itself accepts calls without a token, but the SDK throws `ApiError` `401` until a token is set.

```ts
const { banned } = await rotur.check.banned(["alice", "bob", "charlie"]);
// ["bob"]
```

**Returns:** `{ banned: string[] }`
