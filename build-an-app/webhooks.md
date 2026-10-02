---
description: Hear from Rotur when you must delete someone's data, and take privacy requests.
---

# Receive webhooks

Rotur sends your server a signed `POST` when you must delete what you hold about someone, and, if you want them, when someone makes a privacy request. If your app stores anything about the people who use it, set up a webhook: the [Developer Terms](developer-terms.md) require it.

## Events

| `type` | When | What you must do |
| --- | --- | --- |
| `user.deleted` | The person deleted their Rotur account | Delete what you hold about them within 30 days |
| `user.left` | The person left your app from [rotur.dev/me/apps](https://rotur.dev/me/apps) | Delete what you hold about them within 30 days |
| `user.banned` | Rotur banned the account | Delete what you hold about them within 30 days, unless you need to keep evidence for a report you sent to Rotur |
| `privacy.request` | The person asked for a copy of their data (`access`) or for you to delete it (`erasure`). Only sent if you turn privacy requests on | Answer inside your app within one month |
| `app.test` | The owner chose **Send test** | Nothing |

`user.deleted`, `user.left` and `user.banned` are always sent once you have a webhook. You only get `user.left`, `user.banned` and `user.deleted` for people who have used your app.

`user.banned` is about bans from Rotur. Bans you make in your own app don't send a webhook.

## Set up a webhook

Only the app's owner can do this.

1. Open your app on [rotur.dev/me/developer](https://rotur.dev/me/developer) and go to **Webhooks**.
2. Enter your endpoint's URL. It must be `https://` on the public internet: private, loopback, link-local and carrier-grade NAT addresses (including Tailscale) are refused.
3. Tick **Send privacy requests** if you want them.
4. Choose **Add webhook**. Copy the **signing secret** (`whsec_…`). It's shown once.
5. Choose **Send test** and check your server answered.

To change the signing secret, rotate it from the same page. The new one is shown once, and the old one stops working straight away. The page also shows your last 20 deliveries, but only since Rotur last restarted.

## What Rotur sends

```http
POST /rotur-webhook HTTP/1.1
Host: sketchpad.example
Content-Type: application/json
User-Agent: Rotur-Webhooks/1
Rotur-Webhook-Id: evt_5f2b8c…
Rotur-Webhook-Timestamp: 1790000000
Rotur-Signature: v1=3d5a0f…

{"id":"evt_5f2b8c…","type":"user.deleted","created_at":1790000000,"app":"app_0123456789abcdef","data":{"user_id":"7ebdf483-5b9f-4b70-9edc-a1f2827391f9"}}
```

| Field | What it is |
| --- | --- |
| `id` | The event's ID. The same on every retry, so use it to ignore duplicates |
| `type` | One of the [events](#events) |
| `created_at` | When the event happened, in Unix seconds |
| `app` | Your app's ID |
| `data.user_id` | The person's Rotur user ID, the same as `sub` from sign-in |
| `data.kind` | `privacy.request` only: `access` or `erasure` |

The body carries the Rotur user ID and nothing else about the person.

## Answer and retries

Answer with any `2xx` status within 10 seconds. Rotur doesn't follow redirects.

If delivery fails, Rotur tries again after 1 minute, 5 minutes and 30 minutes, then gives up. Test events aren't retried. Do the work after you've answered, or make it quick, and use `id` to skip events you've already handled.

## Check the signature

Every delivery is signed, so you can be sure it came from Rotur. `Rotur-Signature` is `v1=` followed by the hex HMAC-SHA256 of `<id>.<timestamp>.<raw body>`, keyed with your whole signing secret (including `whsec_`).

1. Read the raw request body as bytes, before any JSON parsing.
2. Reject the delivery if `Rotur-Webhook-Timestamp` is more than 5 minutes from now. This stops old deliveries being replayed.
3. Work out the expected signature and compare it in constant time.

{% tabs %}
{% tab title="Node.js" %}
{% code title="webhook.mjs" %}
```js
import http from "node:http";
import { createHmac, timingSafeEqual } from "node:crypto";

const SECRET = process.env.ROTUR_WEBHOOK_SECRET; // whsec_…

function verify(headers, rawBody) {
  const id = headers["rotur-webhook-id"] ?? "";
  const timestamp = headers["rotur-webhook-timestamp"] ?? "";
  const signature = headers["rotur-signature"] ?? "";
  const age = Math.abs(Date.now() / 1000 - Number(timestamp));
  if (!id || !(age <= 300)) return false; // missing, or too old: maybe replayed

  const expected =
    "v1=" + createHmac("sha256", SECRET).update(`${id}.${timestamp}.`).update(rawBody).digest("hex");
  return signature.length === expected.length && timingSafeEqual(Buffer.from(signature), Buffer.from(expected));
}

http.createServer((req, res) => {
  const chunks = [];
  req.on("data", (chunk) => chunks.push(chunk));
  req.on("end", () => {
    const rawBody = Buffer.concat(chunks);
    if (!verify(req.headers, rawBody)) {
      res.writeHead(401).end();
      return;
    }
    res.writeHead(204).end();

    const event = JSON.parse(rawBody);
    switch (event.type) {
      case "user.deleted":
      case "user.left":
      case "user.banned":
        // Delete what you hold about event.data.user_id.
        break;
      case "privacy.request":
        // event.data.kind is "access" or "erasure". Answer inside your app.
        break;
    }
  });
}).listen(8080);
```
{% endcode %}

If you use Express, read the body with `express.raw({ type: "application/json" })` on this route so you get the exact bytes Rotur signed.
{% endtab %}

{% tab title="Python" %}
```python
import hashlib
import hmac
import time

def verify(headers, raw_body: bytes, secret: str) -> bool:
    event_id = headers.get("Rotur-Webhook-Id", "")
    timestamp = headers.get("Rotur-Webhook-Timestamp", "")
    signature = headers.get("Rotur-Signature", "")
    try:
        if not event_id or abs(time.time() - int(timestamp)) > 300:
            return False
    except ValueError:
        return False

    message = f"{event_id}.{timestamp}.".encode() + raw_body
    expected = "v1=" + hmac.new(secret.encode(), message, hashlib.sha256).hexdigest()
    return hmac.compare_digest(signature, expected)
```

In Flask, pass `request.get_data()` as `raw_body`.
{% endtab %}
{% endtabs %}

## Privacy requests

With **Send privacy requests** on, people can ask your app for their data, or ask you to delete it, from [rotur.dev/me/apps](https://rotur.dev/me/apps). Each person can send each kind once a day.

When you get a `privacy.request`:

* `kind: "access"`: give them a copy of what you hold about them, inside your app.
* `kind: "erasure"`: delete what you hold about them, and tell them inside your app.

Answer within one month. If privacy requests are off, people are told to contact you directly, or to leave your app to have their data deleted.

## Remove a webhook

The owner can remove the webhook from the same page. Rotur then stops sending events, and nothing is queued for later.
