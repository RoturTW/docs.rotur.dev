# Rotur Bio Templates

Bio Templates let you show live information from your Rotur account directly in your bio. Show your credits, your local time, or even content fetched from a URL, without ever editing your bio by hand.

This is a paid feature. Subscribing to [Ko-fi](https://ko-fi.com/mistium/tiers) at any tier unlocks it. Write `{{ user key_name }}` anywhere in your bio and the server replaces it with the value of that key whenever someone views your profile.

Some examples:

* `{{ user username }}` shows your account username.
* `{{ user sys.currency }}` shows how many credits you have.
* `{{ user followers }}` shows your follower count.

Only safe primitive values (strings, numbers, booleans) are shown. Objects and nested data are skipped. Sensitive keys are always filtered out: your token, password, email, and anything containing "token", "password" or "secret" will never render.

## Setup

Once you have a subscription, just edit your bio. For example, a bio like:

```
Hi, I'm {{ user username }} and I currently have {{ user sys.currency }} credits!
```

might render as:

```
Hi, I'm Sophie and I currently have 1234 credits!
```

## Template Types and Tiers

Templates use the `{{ ... }}` syntax. Higher tiers unlock more template types.

| Template | Minimum Tier | Description |
|---|---|---|
| `{{ user key }}` | Lite | Display a field from your profile (e.g. `{{ user username }}`) |
| `{{ flex economy% }}` | Lite | Display your share of all credits in the economy, as a percentage |
| `{{ time format }}` | Plus | Display your current local time (e.g. `{{ time 15:04 }}`) |
| `{{ url address }}` | Pro | Fetch and render content from an external URL |

### Notes on each type

* `{{ user key }}` also works with `followers`, `following` and `bio`.
* `{{ time }}` defaults to `15:04` if you give no format. It uses the `timezone` key on your account (a UTC offset like `UTC+1`) to work out your local time.
* `{{ url }}` fetches through a proxy with a 3 second timeout and renders at most the first 1,000 bytes of the response. You can point it at a counter service to track profile visits.

### Key points

* Only safe keys are processed; unknown keys are replaced with an empty string.
* You cannot chain keys like `theme.text` to access nested data. (`sys.*` keys are top-level keys, not nested.)
* Everything is rendered server-side. No client work is needed.

## Who can see these values?

Anyone who can view your profile sees the rendered values. Sensitive fields are filtered automatically, so only safe information is ever displayed.

## Limits

There is no hard limit on how many templates you can use. Your bio itself is limited by your tier's bio length (200 characters on Free, 500 on Plus, 1,000 on Pro), and the rendered result is cut to that length too.

## What tiers get Bio Templates?

Any paid tier (Lite and above) includes Bio Templates. Free accounts do not have them.

Subscribe [here](https://ko-fi.com/mistium/tiers) to unlock this feature.
