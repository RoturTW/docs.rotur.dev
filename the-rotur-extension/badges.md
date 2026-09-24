# Badges

Badges are awarded to accounts for reaching goals or meeting requirements, and show on the user's profile. These blocks read the badges on the logged-in account. For the list of badges and how to get them, see [Rotur badges](../my-account/rotur-badges.md).

The extension loads the badges with the account when the user logs in.

## Blocks

| Block | Type | Returns |
| --- | --- | --- |
| `<badges loaded successfully>` | Boolean | `true` while a user is logged in and connected |
| `(all user badges)` | Reporter | JSON array of the badges on the account |
| `(total badge count)` | Reporter | How many badges the account has |
| `<does user have badge: [badge]>` | Boolean | `true` if the account has a badge whose name or ID matches `BADGE` |
| `(badge info [badge])` | Reporter | Always `{}` in the current version |

### Example

```
when authenticated
if <does user have badge: [badge]> then
  say [Thanks for the support]
end
```

## Hidden blocks

These blocks are hidden from the palette and only appear in older projects:

| Block | Type | Current behavior |
| --- | --- | --- |
| `(all badges)` | Reporter | Always returns `{}` |
| `redownload badges` | Command | Reloads the account's badges from Rotur |
