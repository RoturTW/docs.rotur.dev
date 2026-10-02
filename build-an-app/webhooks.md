---
description: Hear from Rotur when you must delete someone's data, when a payment is made, and when someone makes a privacy request. Check the signature in Go, Python, PHP or JavaScript.
---

# Receive webhooks

Rotur sends your server a signed `POST` when you must delete what you hold about someone, when someone pays a [payment request](payments.md) your app made, and, if you want them, when someone makes a privacy request. If your app stores anything about the people who use it, set up a webhook: the [Developer Terms](developer-terms.md) require it.

## Events

| `type` | When | What you must do |
| --- | --- | --- |
| `user.deleted` | The person deleted their Rotur account | Delete what you hold about them within 30 days |
| `user.left` | The person left your app from [rotur.dev/me/apps](https://rotur.dev/me/apps) | Delete what you hold about them within 30 days |
| `user.banned` | Rotur banned the account | Delete what you hold about them within 30 days, unless you need to keep evidence for a report you sent to Rotur |
| `payment.completed` | Someone paid a payment request your app made | Deliver what they paid for |
| `privacy.request` | The person asked for a copy of their data (`access`) or for you to delete it (`erasure`). Only sent if you turn privacy requests on | Answer inside your app within one month |
| `app.test` | The owner chose **Send test** | Nothing |

Once you have a webhook, Rotur always sends `user.deleted`, `user.left`, `user.banned` and `payment.completed`. You only get the `user.` events for people who have used your app.

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
| `data.user_id` | The person's Rotur ID. For `payment.completed`, the person who paid |
| `data.kind` | `privacy.request` only: `access` or `erasure` |
| `data.payment_request`, `data.reference`, `data.amount`, `data.to`, `data.payment_id` | `payment.completed` only: the request's ID, your own reference for it, the amount in credits, the Rotur ID of who was paid, and the payment's ID |

The body carries the Rotur ID and nothing else about the person.

## Answer and retries

Answer with any `2xx` status within 10 seconds. Rotur doesn't follow redirects.

If delivery fails, Rotur tries again after 1 minute, 5 minutes and 30 minutes, then gives up. Test events aren't retried. Do the work after you've answered, or make it quick, and use `id` to skip events you've already handled.

## Check the signature

`Rotur-Signature` is `v1=` followed by the hex HMAC-SHA256 of `<id>.<timestamp>.<raw body>`, keyed with your whole signing secret (including `whsec_`). `<id>` and `<timestamp>` are the `Rotur-Webhook-Id` and `Rotur-Webhook-Timestamp` headers.

1. Read the raw request body as bytes, before any JSON parsing.
2. Refuse the delivery if `Rotur-Webhook-Timestamp` is more than 5 minutes from now. This stops old deliveries being replayed.
3. Work out the expected signature and compare it in constant time.

Each example below answers `POST /rotur-webhook` and reads the signing secret from `ROTUR_WEBHOOK_SECRET`.

{% tabs %}
{% tab title="Go" %}
{% code title="main.go" %}
```go
package main

import (
	"crypto/hmac"
	"crypto/sha256"
	"encoding/hex"
	"encoding/json"
	"io"
	"log"
	"net/http"
	"os"
	"strconv"
	"time"
)

var webhookSecret = os.Getenv("ROTUR_WEBHOOK_SECRET") // whsec_…

// verifyWebhook reports whether a delivery came from Rotur in the last 5 minutes.
func verifyWebhook(r *http.Request, body []byte) bool {
	id, timestamp := r.Header.Get("Rotur-Webhook-Id"), r.Header.Get("Rotur-Webhook-Timestamp")
	sent, err := strconv.ParseInt(timestamp, 10, 64)
	if err != nil || time.Since(time.Unix(sent, 0)).Abs() > 5*time.Minute {
		return false
	}
	mac := hmac.New(sha256.New, []byte(webhookSecret))
	mac.Write([]byte(id + "." + timestamp + "."))
	mac.Write(body)
	expected := "v1=" + hex.EncodeToString(mac.Sum(nil))
	return hmac.Equal([]byte(r.Header.Get("Rotur-Signature")), []byte(expected))
}

func main() {
	http.HandleFunc("POST /rotur-webhook", func(w http.ResponseWriter, r *http.Request) {
		body, err := io.ReadAll(io.LimitReader(r.Body, 1<<20))
		if err != nil || !verifyWebhook(r, body) {
			http.Error(w, "Bad signature", http.StatusUnauthorized)
			return
		}
		var event struct {
			ID   string         `json:"id"`
			Type string         `json:"type"`
			Data map[string]any `json:"data"`
		}
		json.Unmarshal(body, &event)
		log.Printf("Rotur sent %s (%s)", event.Type, event.ID)
		switch event.Type {
		case "user.deleted", "user.left", "user.banned":
			// Delete what you hold about event.Data["user_id"].
		case "payment.completed":
			// Deliver what event.Data["reference"] was for.
		}
		w.WriteHeader(http.StatusNoContent)
	})
	http.ListenAndServe(":8080", nil)
}
```
{% endcode %}
{% endtab %}

{% tab title="Python" %}
`verify_webhook` uses only the standard library. Pass it the request's headers (any mapping that ignores case, such as Flask's or Django's `request.headers`) and the raw body:

{% code title="webhook.py" %}
```python
import hashlib
import hmac
import os
import time

from flask import Flask, request


def verify_webhook(headers, body: bytes, secret: str) -> bool:
    """True if the delivery came from Rotur in the last 5 minutes."""
    event_id = headers.get("Rotur-Webhook-Id", "")
    timestamp = headers.get("Rotur-Webhook-Timestamp", "")
    if not timestamp.isdigit() or abs(time.time() - int(timestamp)) > 300:
        return False
    signed = f"{event_id}.{timestamp}.".encode() + body
    expected = "v1=" + hmac.new(secret.encode(), signed, hashlib.sha256).hexdigest()
    return hmac.compare_digest(headers.get("Rotur-Signature", ""), expected)


app = Flask(__name__)


@app.post("/rotur-webhook")
def rotur_webhook():
    if not verify_webhook(request.headers, request.get_data(), os.environ["ROTUR_WEBHOOK_SECRET"]):
        return "Bad signature", 401
    event = request.get_json()
    print("Rotur sent", event["type"], event["id"])
    if event["type"] in ("user.deleted", "user.left", "user.banned"):
        pass  # Delete what you hold about event["data"]["user_id"].
    elif event["type"] == "payment.completed":
        pass  # Deliver what event["data"]["reference"] was for.
    return "", 204
```
{% endcode %}
{% endtab %}

{% tab title="PHP" %}
{% code title="webhook.php" %}
```php
<?php
$secret = getenv('ROTUR_WEBHOOK_SECRET'); // whsec_…
$body = file_get_contents('php://input');
$id = $_SERVER['HTTP_ROTUR_WEBHOOK_ID'] ?? '';
$timestamp = $_SERVER['HTTP_ROTUR_WEBHOOK_TIMESTAMP'] ?? '';
$expected = 'v1=' . hash_hmac('sha256', "$id.$timestamp.$body", $secret);

if (!ctype_digit($timestamp) || abs(time() - (int) $timestamp) > 300
    || !hash_equals($expected, $_SERVER['HTTP_ROTUR_SIGNATURE'] ?? '')) {
    http_response_code(401);
    exit('Bad signature');
}

$event = json_decode($body, true);
error_log("Rotur sent {$event['type']} ({$event['id']})");
switch ($event['type']) {
    case 'user.deleted':
    case 'user.left':
    case 'user.banned':
        // Delete what you hold about $event['data']['user_id'].
        break;
    case 'payment.completed':
        // Deliver what $event['data']['reference'] was for.
        break;
}
http_response_code(204);
```
{% endcode %}
{% endtab %}

{% tab title="Node.js (SDK)" %}
`verifyWebhook` from `rotur-sdk/server` checks the signature and age, and resolves to the event or `null`. Give it the raw body:

{% code title="webhook.mjs" %}
```js
import express from "express";
import { verifyWebhook } from "rotur-sdk/server";

const app = express();

app.post("/rotur-webhook", express.raw({ type: "application/json" }), async (req, res) => {
  const event = await verifyWebhook(process.env.ROTUR_WEBHOOK_SECRET, req.headers, req.body);
  if (!event) return res.status(401).send("Bad signature");
  res.sendStatus(204);
  console.log("Rotur sent", event.type, event.id);
  if (["user.deleted", "user.left", "user.banned"].includes(event.type)) {
    // Delete what you hold about event.data.user_id.
  } else if (event.type === "payment.completed") {
    // Deliver what event.data.reference was for.
  }
});

app.listen(8080);
```
{% endcode %}

It also works in Workers, Deno and Bun: pass `request.headers` and `await request.arrayBuffer()`.
{% endtab %}

{% tab title="Node.js" %}
Without the SDK, with `node:crypto`:

{% code title="webhook.mjs" %}
```js
import express from "express";
import { createHmac, timingSafeEqual } from "node:crypto";

// True if the delivery came from Rotur in the last 5 minutes.
function verifyWebhook(headers, rawBody) {
  const id = headers["rotur-webhook-id"] ?? "";
  const timestamp = headers["rotur-webhook-timestamp"] ?? "";
  if (!/^\d+$/.test(timestamp) || Math.abs(Date.now() / 1000 - timestamp) > 300) return false;
  const expected = Buffer.from(
    "v1=" + createHmac("sha256", process.env.ROTUR_WEBHOOK_SECRET).update(`${id}.${timestamp}.`).update(rawBody).digest("hex"),
  );
  const signature = Buffer.from(headers["rotur-signature"] ?? "");
  return signature.length === expected.length && timingSafeEqual(signature, expected);
}

const app = express();

app.post("/rotur-webhook", express.raw({ type: "application/json" }), (req, res) => {
  if (!verifyWebhook(req.headers, req.body)) return res.status(401).send("Bad signature");
  res.sendStatus(204);
  const event = JSON.parse(req.body);
  console.log("Rotur sent", event.type, event.id);
});

app.listen(8080);
```
{% endcode %}
{% endtab %}

{% tab title="Any language" %}
Work out the signature with your language's HMAC-SHA256. With `openssl`, where `BODY` holds the raw body exactly as received:

{% code title="signature.sh" %}
```sh
printf '%s.%s.%s' "$ID" "$TIMESTAMP" "$BODY" \
  | openssl dgst -sha256 -hmac "$ROTUR_WEBHOOK_SECRET" -r \
  | sed 's/^\([0-9a-f]*\).*/v1=\1/'
```
{% endcode %}

The result must equal `Rotur-Signature`. Compare them in constant time, and check the timestamp is within 5 minutes of now.
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
