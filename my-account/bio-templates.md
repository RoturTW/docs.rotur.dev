# rotur Bio Templates

Rotur Bio Templates let you dynamically display information from your Rotur account directly in your user bio. This is perfect for showing things like your total credits, email, or other safe account info without manually updating your bio every time something changes.

This is a paid feature. Subscribing to [my Ko-fi](https://ko-fi.com/mistium/tiers) at any tier gives you access to this system. You can use the syntax `{{ user key_name }}` in your bio to include the value of that key from your profile.

Some examples include:

* `{{ user email }}` → displays your registered email.
* `{{ user sys.currency }}` → displays the total credits you have.
* `{{ user username }}` → displays your account username.

Only safe and primitive values (strings, numbers, booleans) are allowed. Objects, nested sensitive data, or other private fields will be ignored.

## Setup

Once you have a subscription, you can edit your bio in your profile settings. Anywhere you include `{{ user key_name }}`, the server will replace it with the actual value from your account whenever someone views your profile.

For example, a bio like:

```
Hi, I'm {{ user username }} and I currently have {{ user sys.currency }} credits!
```

might render as:

```
Hi, I'm Sophie and I currently have 1234 credits!
```

### Different tiers

Available from **Lite** and above. Bio templating lets you use dynamic template expressions in your bio text. Templates use the `{{ ... }}` syntax.

| Template | Minimum Tier | Description |
|---|---|---|
| `{{ user key }}` | Lite | Display a field from your profile (e.g. `{{ user username }}`) |
| `{{ time format }}` | Plus | Display your current local time (e.g. `{{ time 15:04 }}`) |
| `{{ url address }}` | Pro | Fetch and render content from an external URL |

### Key points:

* Only safe keys are processed; unknown keys are replaced with an empty string.
* You cannot chain keys like `theme.text` to access nested data. (Note: `sys.*` keys are items of root object and are not nested)
* No JavaScript or client-side processing is needed, all handling happens server-side.

## Who can see these values?

Anyone who can view your profile can see the dynamically inserted values. Sensitive or private fields are automatically filtered out, so only safe information will be displayed.

## Limits

There are no hard limits on how many keys you can use in your bio. However, extremely large bios may be truncated or may not display fully on smaller devices.

## What tiers get Bio Templates?

Any tier → includes Bio Templates<br>
Free accounts → not available

Subscribe [here](https://ko-fi.com/mistium/tiers) to unlock this feature.
