# Notifications

The Rotur notification service lets any application register push endpoints on behalf of a user and send real-time notifications to them. It is source-scoped, permission-based, and stores endpoint data in the user's originFS.

## Core Concepts

### Sources

A **source** is the name of the application or site registering the endpoint (e.g. `originChats`). This prevents notification duplication across different instances of the same service and lets users control who can notify them on a per-source basis.

### Device Identification

Device IDs are **server-generated** from an HMAC of `(username, source, fingerprint)`. The client provides a `fingerprint` — a stable string derived from device-specific signals (e.g. user agent, screen resolution, timezone). The same fingerprint on the same source always produces the same device ID, so re-registering simply updates the endpoint URL rather than creating a duplicate.

### Permissions

By default, **nobody** can send you notifications. You must explicitly allow users per source. Each allowed sender also tracks a count of how many notifications they've sent you.

### Storage

Registered endpoints are stored in the user's originFS at:

```
origin/(c) users/<username>/application data/notify@rotur/endpoints.json
```

Allowed senders and the notification log are stored on the user account object under `sys.notify_allowed` and `sys.notify_log`.

## Endpoints

| Endpoint | Auth | Description |
| --- | --- | --- |
| [GET `/notify/vapid`](vapid-keys.md) | No | Retrieve the server's VAPID public key for web push |
| [POST `/notify/register`](register-endpoint.md) | Yes | Register a push endpoint |
| [GET `/notify/check`](check-registration.md) | Yes | Check if a device is registered |
| [GET `/notify/endpoints`](list-endpoints.md) | Yes | List all registered endpoints |
| [DELETE `/notify/device/:device_id`](delete-device.md) | Yes | Remove a registered device |
| [GET `/notify/allowed`](allowed-senders.md) | Yes | List allowed senders |
| [POST `/notify/allowed/:username`](allowed-senders.md) | Yes | Allow a sender |
| [DELETE `/notify/allowed/:username`](allowed-senders.md) | Yes | Remove an allowed sender |
| [GET `/notify/log`](notification-log.md) | Yes | View notification history |
| [POST `/notify/:username`](send-notification.md) | Yes | Send a notification |

## Authentication

All endpoints (except `/notify/vapid`) require a valid `auth` query parameter (your Rotur user token).

**Base URL:** `https://api.rotur.dev`
