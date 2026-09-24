# Notifications

`rotur.notifications` reads and manages your Claw notifications, and sends notifications to other users. Every method needs a token.

Notifications are returned as `NotificationEntry`: `type`, `id`, `timestamp`, `created`, `read`, optional `actor`, `platform`, and `platform_data`, plus fields specific to the notification type.

## rotur.notifications.list(afterDays?)

Lists your notifications from the last `afterDays` days. `afterDays` defaults to `30`.

**Auth:** Required. Sub-tokens need `notifications:view`.

```ts
const notifications = await rotur.notifications.list(7);
```

**Returns:** `NotificationEntry[]`

## rotur.notifications.markRead(ids?)

Marks the given notifications as read, or every notification when you omit `ids`.

**Auth:** Required. Sub-tokens need `notifications:view`.

```ts
await rotur.notifications.markRead(["notification-id"]);
await rotur.notifications.markRead();
```

**Returns:** `{ success, updated? }`

## rotur.notifications.read(id)

Marks one notification as read.

**Auth:** Required. Sub-tokens need `notifications:view`.

```ts
await rotur.notifications.read("notification-id");
```

**Returns:** `{ success }`

## rotur.notifications.delete(id)

Deletes one notification.

**Auth:** Required. Sub-tokens need `notifications:view`.

```ts
await rotur.notifications.delete("notification-id");
```

**Returns:** `{ success }`

## rotur.notifications.clear()

Deletes all your notifications.

**Auth:** Required. Sub-tokens need `notifications:view`.

```ts
const { removed } = await rotur.notifications.clear();
```

**Returns:** `{ success, removed }`

## rotur.notifications.create(target, type, options?)

Sends a notification to another user.

**Auth:** Required. Sub-tokens need `notifications:send`.

| Name | Type | Required | Description |
| --- | --- | --- | --- |
| `target` | string | Yes | User to notify |
| `type` | string | Yes | Notification type |
| `options.actor` | string | No | Actor shown on the notification |
| `options.data` | object | No | Type-specific data |
| `options.platform` | string | No | Platform name |
| `options.platformData` | object | No | Platform-specific data, sent as `platform_data` |

```ts
await rotur.notifications.create("alice", "my-app-event", {
  data: { text: "Your build finished" },
});
```

**Returns:** the created `NotificationEntry`.
