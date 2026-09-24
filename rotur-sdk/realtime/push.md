# Push notifications

`rotur.push` registers devices for web push and sends push notifications to users. Every push is tagged with a `source`, a string that identifies your app. `vapidKeys()` needs no token; every other method does.

## rotur.push.vapidKeys()

Gets the VAPID public key you need to create a web push subscription in the browser.

**Auth:** None.

```ts
const { public_key, subject } = await rotur.push.vapidKeys();
```

**Returns:** `{ public_key, subject }`

## rotur.push.register(endpoint, p256dh, auth, source, fingerprint)

Registers a browser push subscription for the signed-in user.

**Auth:** Required. Sub-tokens need `account:settings`.

| Name | Type | Description |
| --- | --- | --- |
| `endpoint` | string | Push subscription endpoint URL |
| `p256dh` | string | Subscription `p256dh` key |
| `auth` | string | Subscription `auth` key |
| `source` | string | Your app's source identifier |
| `fingerprint` | string | Identifier for this device |

```ts
await rotur.push.register(
  "https://push.example.com/endpoint",
  "p256dh-key-base64",
  "auth-key-base64",
  "my-app",
  "device-fingerprint",
);
```

**Returns:** `{ message, device_id, source, updated }`

## rotur.push.check(source, fingerprint)

Checks whether a device is registered for a source.

**Auth:** Required. Sub-tokens need `notifications:view`.

```ts
const { registered, device_id } = await rotur.push.check("my-app", "device-fingerprint");
```

**Returns:** `{ registered, device_id?, endpoint?, source?, created_at? }`

## rotur.push.endpoints()

Lists your registered devices.

**Auth:** Required. Sub-tokens need `notifications:view`.

```ts
const { endpoints, count } = await rotur.push.endpoints();
```

**Returns:** `{ endpoints, count }`. Each endpoint is `{ device_id, endpoint, p256dh, auth, source, created_at }`.

## rotur.push.deleteDevice(deviceId)

Removes a registered device.

**Auth:** Required. Sub-tokens need `account:settings`.

```ts
await rotur.push.deleteDevice("device-id");
```

**Returns:** `{ message, device_id }`

## rotur.push.allowedSenders()

Lists the users allowed to send you push notifications, per source.

**Auth:** Required. Sub-tokens need `notifications:view`.

```ts
const senders = await rotur.push.allowedSenders();
// { "my-app": { senders: [{ username: "bob", count: 3 }] } }
```

**Returns:** an object keyed by source. Each value is `{ senders: Array<{ username, count }> }`.

## rotur.push.allowSender(username, source)

Allows a user to send you push notifications through a source.

**Auth:** Required. Sub-tokens need `account:settings`.

```ts
await rotur.push.allowSender("bob", "bobs-app");
```

**Returns:** `{ message, username, source }`

## rotur.push.removeSender(username, source)

Stops a user from sending you push notifications through a source.

**Auth:** Required. Sub-tokens need `account:settings`.

```ts
await rotur.push.removeSender("bob", "bobs-app");
```

**Returns:** `{ message, username, source }`

## rotur.push.log()

Lists push notifications you have received.

**Auth:** Required. Sub-tokens need `notifications:view`.

```ts
const { log, count } = await rotur.push.log();
```

**Returns:** `{ log, count }`. Each entry is `{ from, source, title?, body?, at }`.

## rotur.push.send(username, source, options?)

Sends a push notification to one user.

**Auth:** Required. Sub-tokens need `notifications:send`.

| Option | Type | Description |
| --- | --- | --- |
| `title` | string | Notification title |
| `body` | string | Notification text |
| `data` | object | Extra data delivered with the notification |

```ts
await rotur.push.send("alice", "my-app", {
  title: "New message",
  body: "You have a new message",
  data: { url: "/messages/123" },
});
```

**Returns:** `{ success, message, title?, body?, data? }`

## rotur.push.sendMany(users, source, options?)

Sends the same push notification to several users. Takes the same options as `send()`.

**Auth:** Required. Sub-tokens need `notifications:send`.

```ts
const { results } = await rotur.push.sendMany(["alice", "bob"], "my-app", {
  title: "Announcement",
  body: "Check out the new feature",
});
```

**Returns:** `{ success, results }`. Each result is `{ username, code, result }`, where `result` is the `send()` response or `{ error }`.

## rotur.push.notifiableUsers(source)

Lists the users you can send push notifications to through a source.

**Auth:** Required. Sub-tokens need `notifications:view`.

```ts
const { users } = await rotur.push.notifiableUsers("my-app");
```

**Returns:** `{ success: true, source, users: Array<{ username, id }> }`
