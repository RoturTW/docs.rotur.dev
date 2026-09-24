# Items

The **My Keys** blocks work with Rotur keys: things a user can buy or be given, such as access to a paid feature. Use them to check whether the user owns a key, read a key's data, and buy one.

You create and manage keys on [rotur.dev/keys](https://rotur.dev/keys); the **Mange My Keys** button in the palette opens that page. The extension has no blocks for creating keys. To create them from code, use the [Keys API](../assorted-apis/keys.md) or [`rotur.keys`](../rotur-sdk/marketplace/keys.md) in the SDK.

## Blocks

| Block | Type | Returns |
| --- | --- | --- |
| `(my keys)` | Reporter | JSON of the keys the logged-in user has |
| `<do I own key of id: [item]>` | Boolean | `true` if the logged-in user owns the key |
| `(get key info for id: [item])` | Reporter | The key as JSON |
| `(get key data for id: [item])` | Reporter | The key's `data` field as JSON, or the whole key if it has no `data` |
| `(purchase key with id: [item])` | Reporter | Buys the key and returns Rotur's response as JSON. The balance refreshes afterwards. |

If a request fails, these reporters return the error message instead. `do I own key of id:` returns `false` on any error.

### Example

```
when authenticated
if <not <do I own key of id: [premium-key-id]>> then
  say (purchase key with id: [premium-key-id])
end
```

If a key has a price, buying it spends the user's credits. The seller's fees are listed in [Transactions and taxes](../my-account/transactions-and-taxes.md).

## Hidden blocks

Older versions of the extension had blocks for creating, updating, deleting and listing marketplace items. The item-creation and "items created by me" blocks have been removed. These blocks are hidden from the palette and only appear in older projects:

| Block | Current behavior |
| --- | --- |
| `(get public items, page: [1])` | Returns up to 50 items that are for sale, as JSON |
| `(get public item pages)` | Always returns `1` |
| `(keys - update [KEY] to [DATA] for id: [KEY])` | Tries to update a key's data |
| `(keys - delete (ID) [KEY])` | Tries to delete a key |
| `(items - disable purchases on (ID) [ITEM])` | Deletes the key `ITEM`. It runs the same code as `keys - delete`. |
| `(items - enable purchases on (ID) [ITEM])` | Always returns `Manage key listings on rotur.dev` |

Manage your keys on [rotur.dev/keys](https://rotur.dev/keys) instead of using these blocks.
