# URL

A parsed Rotur web address. Each part of the address is stored as its own key.

For `web://wow.bouncy.flf/subdir/subdir2/shocker.txt?test=wow&crazy=noway`:

```json5
{
    "scheme": "web",
    "domain_name": "bouncy",
    "domain_top": "flf",
    "domain_sub": "wow",
    "path": "subdir/subdir2",
    "params": {
        "test": "wow",
        "crazy": "noway"
    },
    "file_name": "shocker.txt"
}
```

| Key | Part of the address |
| --- | --- |
| `scheme` | `web` |
| `domain_sub` | `wow` |
| `domain_name` | `bouncy` |
| `domain_top` | `flf` |
| `path` | `subdir/subdir2` |
| `file_name` | `shocker.txt` |
| `params` | `test=wow&crazy=noway` |

For `local://` URLs, the file path is stored in `domain_name`.

## Methods

| Method | Description |
| --- | --- |
| [`.new()`](static-.new.md) (static) | Creates a URL from its parts |
| [`.parse()`](static-.parse.md) (static) | Creates a URL from a string |
| [`.format()`](.format.md) | Returns the URL as a string |
| [`.getAsyncData()`](.getasyncdata.md) | Fetches the content at the URL |
| [`.getFetchUrl()`](.getfetchurl.md) | Returns the address the content is fetched from |
| [`.getTitle()`](.gettitle.md) | Returns a tab title derived from the URL |
