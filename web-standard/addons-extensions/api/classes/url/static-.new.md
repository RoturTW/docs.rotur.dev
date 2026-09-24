# (static) .new()

Creates a URL instance from its parts. The arguments are positional, in the order below.

The Example column shows each part of `web://wow.bouncy.flf/subdir/subdir2/shocker.txt?test=wow&crazy=noway`.

## Parameters

| # | Name | Type | Default | Example |
| --- | --- | --- | --- | --- |
| 1 | Scheme | string | Browser's choice, or `null` | `web` |
| 2 | Domain name | string | Browser's choice, or `null` | `bouncy` |
| 3 | Domain top | string | Browser's choice, or `null` | `flf` |
| 4 | Domain sub | string | `null` | `wow` |
| 5 | Path | string | Empty string | `subdir/subdir2` |
| 6 | Params | object | `null` | `{"test": "wow", "crazy": "noway"}` |
| 7 | File name | string | `index.osl`, or the browser's default for other file types | `shocker.txt` |
