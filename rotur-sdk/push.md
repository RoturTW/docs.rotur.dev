# Push Notifications

Accessed via `rotur.push`. Manage web push notification endpoints and send notifications to users.

## Get VAPID Keys

```ts
const { public_key, subject } = await rotur.push.vapidKeys();
```

Needed to set up a web push subscription on the client side.

## Register a Device

```ts
await rotur.push.register(
  "https://push.example.com/endpoint",
  "p256dh-key-base64",
  "auth-key-base64",
  "my-app",          // source identifier
  "device-fingerprint",
);
```

## Check Registration

```ts
const { registered, device_id } = await rotur.push.check("my-app", "device-fingerprint");
```

## List Registered Endpoints

```ts
const { endpoints, count } = await rotur.push.endpoints();
```

## Delete a Device

```ts
await rotur.push.deleteDevice("device-id");
```

## Allowed Senders

Control which apps can send you push notifications:

```ts
// List allowed senders
const senders = await rotur.push.allowedSenders();

// Allow / remove a sender
await rotur.push.allowSender("bob", "bobs-app");
await rotur.push.removeSender("bob", "bobs-app");
```

## Notification Log

```ts
const { log, count } = await rotur.push.log();
```

## Send Notifications

```ts
// Send to one user
await rotur.push.send("alice", "my-app", {
  title: "New message",
  body: "You have a new message!",
  data: { url: "/messages/123" },
});

// Send to multiple users
await rotur.push.sendMany(["alice", "bob"], "my-app", {
  title: "Announcement",
  body: "Check out the new feature!",
});
```

## Notifiable Users

Check which users of a source are registered for push:

```ts
const { source, users } = await rotur.push.notifiableUsers("my-app");
```
