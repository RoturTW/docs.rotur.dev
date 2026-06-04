# Notifications

Accessed via `rotur.notifications`. Requires authentication.

## List Notifications

```ts
const notifs = await rotur.notifications.list(1);  // after N days
```

Returns an array of `NotificationEntry` objects with `type`, `id`, `timestamp`, and type-specific fields.
