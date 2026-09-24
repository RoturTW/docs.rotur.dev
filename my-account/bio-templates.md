# Rotur Bio Templates

Bio templates show live values in your bio, such as your credit balance or your local time. Write a template in your `bio` key and the server fills it in each time someone loads your profile.

Bio templates need a paid [subscription](subscriptions.md) (Lite or higher). On the Free tier, templates are shown as plain text.

## Syntax

A template is `{{ type argument }}`. For example, this bio:

```
Hi, I'm {{ user username }} and I have {{ user sys.currency }} credits.
```

might render as:

```
Hi, I'm sophie and I have 1234 credits.
```

| Template | Shows |
| --- | --- |
| `{{ user key }}` | The value of a key on your account, such as `{{ user username }}` or `{{ user sys.currency }}` |
| `{{ time format }}` | Your current local time, such as `{{ time 15:04 }}` |
| `{{ url address }}` | Text fetched from an external URL |
| `{{ flex economy% }}` | Your share of all credits in the economy, as a percentage, such as `0.42%` |

Templates with an unknown type, or a `user` key that doesn't exist, render as an empty string.

## `user`

Shows a top-level key from your [account object](rotur-account-objects/README.md). Besides account keys, `followers` and `following` show your follower counts.

* Only strings, numbers and booleans are shown. Arrays and objects, such as `sys.friends` or `theme`, render as an empty string.
* You cannot read nested values. `theme.text` does not work. Keys like `sys.currency` work because they are top-level keys whose names contain a dot.
* Sensitive keys never render: `key`, `password`, `email`, and any key whose name contains `token`, `password` or `secret`.

## `time`

Shows the current time in the timezone set in your account's `timezone` key (a whole-hour offset such as `UTC+1`). If `timezone` is missing or invalid, UTC is used. With no format, it uses `15:04`.

The format can be a [Go time layout](https://pkg.go.dev/time#pkg-constants) such as `15:04` or `02/01/2006`, or a shorthand:

| Shorthand | Meaning | Example |
| --- | --- | --- |
| `h` | Hour (24-hour) | `{{ time h:m }}` → `14:05` |
| `m` | Minute in a time, month in a date | `{{ time d/m/y }}` → `24/09/2026` |
| `s` | Second | `{{ time h:m:s }}` → `14:05:09` |
| `d`, `y` | Day, year | `{{ time d/m/y h:m }}` → `24/09/2026 14:05` |
| `a` at the end | 12-hour clock with AM/PM | `{{ time h:m a }}` → `02:05 PM` |

Use each letter once. `HH:MM` does not work.

## `url`

Fetches the address through a proxy and shows the start of the response. The request times out after 3 seconds, and at most the first 1,000 bytes are shown. If the request fails, the template renders as an empty string. You can point it at a visit counter to show how many times your profile has been viewed.

## Limits

There is no limit on the number of templates. Your bio, including the templates, must fit your tier's bio length, and the rendered result is cut to the same length.

| Tier | Bio length |
| --- | --- |
| Free | 200 characters |
| Lite | 300 characters |
| Plus | 500 characters |
| Pro | 1,000 characters |

Templates are rendered by the server when your profile is fetched with [`GET /profile`](../claw/api-endpoints/profile.md). Anyone who can see your profile sees the rendered values. Your own `bio` key, as returned by `GET /me`, keeps the raw template text.
