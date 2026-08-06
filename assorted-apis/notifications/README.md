# Notifications

The Rotur notification service lets any application register web push endpoints for a user and send them real-time notifications. It is source-scoped and permission-based, and endpoint data lives in the user's originFS.

**Base URL:** `https://api.rotur.dev`

## Core Concepts

### Sources

A **source** is the name of the application or site registering the endpoint (for example `originChats`). Sources stop notifications being duplicated across different instances of the same service, and let users control who can notify them per source.

### Device Identification

Device IDs are **server-generated** from an HMAC of `(username, source, fingerprint)`. You provide a `fingerprint`: a stable string derived from device-specific signals (user agent, screen resolution, timezone, and so on). The same fingerprint on the same source always produces the same device ID, so re-registering updates the existing endpoint instead of creating a duplicate.

### Permissions

By default, **nobody** can send you notifications. You must explicitly allow senders per source. Each allowed sender also tracks a count of how many notifications they have sent you.

### Storage

Registered endpoints are stored in the user's originFS at:

```
origin/(c) users/<username>/application data/notify@rotur/endpoints.json
```

Allowed senders and the notification log are stored on the user account under `sys.notify_allowed` and `sys.notify_log`.

## Endpoints

| Endpoint | Auth | Description |
| --- | --- | --- |
| [GET `/notify/vapid`](vapid-keys.md) | No | Get the server's VAPID public key for web push |
| [POST `/notify/register`](register-endpoint.md) | Yes | Register a push endpoint |
| [GET `/notify/check`](check-registration.md) | Yes | Check if a device is registered |
| [GET `/notify/endpoints`](list-endpoints.md) | Yes | List all registered endpoints |
| [DELETE `/notify/device/:device_id`](delete-device.md) | Yes | Remove a registered device |
| [GET `/notify/allowed`](allowed-senders.md) | Yes | List allowed senders |
| [POST `/notify/allowed/:username`](allowed-senders.md) | Yes | Allow a sender |
| [DELETE `/notify/allowed/:username`](allowed-senders.md) | Yes | Remove an allowed sender |
| [GET `/notify/log`](notification-log.md) | Yes | View notification history |
| [POST `/notify/:username`](send-notification.md) | Yes | Send a notification to one user |
| [POST `/notify/`](send-notification.md) | Yes | Send a notification to many users |
| [GET `/notify/:source/users`](notifiable-users.md) | Yes | List users you can notify for a source |

The same endpoints exist under `/v2` with a few path differences:

| v1 | v2 |
| --- | --- |
| `DELETE /notify/device/:device_id` | `DELETE /v2/notify/devices/:device_id` |
| `POST /notify/allowed/:username` | `PUT /v2/notify/allowed/:username` |
| `POST /notify/:username` | `POST /v2/notify/users/:username` |
| `POST /notify/` | `POST /v2/notify/users` |
| `GET /notify/:source/users` | `GET /v2/notify/sources/:source/users` |

## Authentication

All endpoints except `GET /notify/vapid` require your Rotur auth key. Prefer passing it in the `Authorization` header (`Authorization: Bearer <token>`). A session cookie is also accepted. The `auth` query parameter is still accepted as a legacy fallback.

If you authenticate with a scoped sub-token instead of your main account key, the token needs these permissions:

| Permission | Needed for |
| --- | --- |
| `notifications:view` | Check, list endpoints, allowed list, log, notifiable users |
| `notifications:send` | Sending notifications |
| `account:settings` | Registering endpoints, deleting devices, changing allowed senders |

Requests with a token that lacks the permission get a `403` with `{"error": "Token lacks permission: <permission>"}`.
