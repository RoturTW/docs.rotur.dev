---
description: Give badges, ban people, ask for payments, look up users, handle reports and ask safety questions from your server, with your app's ID and secret. Examples in Go, Python, PHP and JavaScript.
---

# Use your app secret on the server

Some things only your app may do: give its badges, ban people from it, ask people to pay, list its users, file and close reports, and ask whether someone may message or spend. Your server does these with your app's ID and one of its secrets.

* **Base URL:** `https://api.rotur.dev/v2/apps/<app ID>`
* **Auth:** HTTP Basic, with your app's ID as the username and a secret (`rsec_…`) as the password.
* **Bodies:** JSON, in and out.

Make secrets on [rotur.dev/me/developer](https://rotur.dev/me/developer), and keep them on your server. Never put one in a web page or an app people download.

## A helper, and each call

Each example has a small helper that makes one call and fails on an error answer, then makes the calls below. The examples read the secret from `ROTUR_APP_SECRET`. The helpers declare your app's ID: if the same program has the code from [Check who's calling](check-whos-calling.md), declare it once.

| Call | What it does | More |
| --- | --- | --- |
| `GET /users?q=ki` | Your users whose usernames contain "ki" | [See who uses your app](users.md) |
| `PUT /badges/streak/users/kit` with `{"delta": 1}` | Adds 1 to kit's progress on your `streak` badge, giving it to them if they don't have it | [Give badges](badges.md) |
| `PUT /bans/someone` with `{"reason": "Spam", "expires_at": …}` | Keeps someone out of your app until `expires_at` (Unix milliseconds), or for good without it | [Ban people from your app](bans.md) |
| `POST /payment-requests` with `{"payer": "alex", "to": "kit", "amount": 5, "note": "Sticker pack"}` | Asks alex to pay kit 5 credits. Send alex to `approve_url` | [Take payments](payments.md) |
| `GET /payment-requests/<id>` | Whether it's `pending`, `paid`, `declined`, `cancelled` or `expired` | [Take payments](payments.md) |
| `POST /reports`, then `POST /reports/<id>/resolve` | Files a report for your team, then closes it | [Handle reports](handle-reports.md) |
| `POST /signals/message` with `{"from": "kit", "to": "alex"}` | Whether kit may message alex: `{"allowed": true}`, or `allowed: false` with a `reason` to show them | [Declarations and safety signals](safety.md) |

Wherever a call names a person, you can use their username or their Rotur ID. Prefer the ID: it never changes.

{% tabs %}
{% tab title="Go" %}
{% code title="rotur_app.go" %}
```go
package main

import (
	"bytes"
	"encoding/json"
	"fmt"
	"io"
	"net/http"
	"os"
)

const appID = "app_0123456789abcdef" // your app's ID

// roturApp calls the apps API as your app. body may be nil.
func roturApp(method, path string, body any) (map[string]any, error) {
	var data io.Reader
	if body != nil {
		encoded, _ := json.Marshal(body)
		data = bytes.NewReader(encoded)
	}
	req, err := http.NewRequest(method, "https://api.rotur.dev/v2/apps/"+appID+path, data)
	if err != nil {
		return nil, err
	}
	req.SetBasicAuth(appID, os.Getenv("ROTUR_APP_SECRET"))
	req.Header.Set("Content-Type", "application/json")
	res, err := http.DefaultClient.Do(req)
	if err != nil {
		return nil, err
	}
	defer res.Body.Close()
	var answer map[string]any
	json.NewDecoder(res.Body).Decode(&answer) // nothing to decode after a 204
	if res.StatusCode >= 300 {
		return answer, fmt.Errorf("rotur answered %s: %v", res.Status, answer["error"])
	}
	return answer, nil
}
```
{% endcode %}

{% code title="main.go" %}
```go
package main

import (
	"fmt"
	"log"
	"time"
)

// must stops at the first error, to keep the example short.
func must(answer map[string]any, err error) map[string]any {
	if err != nil {
		log.Fatal(err)
	}
	return answer
}

func main() {
	users := must(roturApp("GET", "/users?q=ki", nil))
	fmt.Println(users["users"])

	must(roturApp("PUT", "/badges/streak/users/kit", map[string]any{"delta": 1}))

	tomorrow := time.Now().Add(24 * time.Hour).UnixMilli()
	must(roturApp("PUT", "/bans/someone", map[string]any{"reason": "Spam", "expires_at": tomorrow}))

	request := must(roturApp("POST", "/payment-requests", map[string]any{
		"payer": "alex", "to": "kit", "amount": 5, "note": "Sticker pack", "reference": "order-42",
	}))
	fmt.Println("Send alex to", request["approve_url"])
	status := must(roturApp("GET", "/payment-requests/"+request["id"].(string), nil))["status"]
	fmt.Println("The payment is", status)

	report := must(roturApp("POST", "/reports", map[string]any{
		"target_type": "user", "target_user": "someone", "category": "spam", "reporter": "kit",
	}))
	must(roturApp("POST", "/reports/"+report["report_id"].(string)+"/resolve", map[string]any{"outcome": "resolved", "note": "Banned for a day"}))

	signal := must(roturApp("POST", "/signals/message", map[string]any{"from": "kit", "to": "alex"}))
	fmt.Println("May kit message alex?", signal["allowed"], signal["reason"])
}
```
{% endcode %}
{% endtab %}

{% tab title="Python" %}
With [requests](https://pypi.org/project/requests/):

{% code title="rotur_app.py" %}
```python
import os

import requests

APP_ID = "app_0123456789abcdef"  # your app's ID


def rotur_app(method, path, body=None):
    """Calls the apps API as your app. Raises RuntimeError on an error answer."""
    res = requests.request(
        method,
        f"https://api.rotur.dev/v2/apps/{APP_ID}{path}",
        json=body,
        auth=(APP_ID, os.environ["ROTUR_APP_SECRET"]),
        timeout=10,
    )
    answer = res.json() if res.content else None
    if not res.ok:
        raise RuntimeError(f"Rotur answered {res.status_code}: {(answer or {}).get('error')}")
    return answer
```
{% endcode %}

{% code title="examples.py" %}
```python
import time

from rotur_app import rotur_app

users = rotur_app("GET", "/users?q=ki")["users"]

rotur_app("PUT", "/badges/streak/users/kit", {"delta": 1})

tomorrow = int(time.time() + 86400) * 1000
rotur_app("PUT", "/bans/someone", {"reason": "Spam", "expires_at": tomorrow})

request = rotur_app("POST", "/payment-requests", {"payer": "alex", "to": "kit", "amount": 5, "note": "Sticker pack", "reference": "order-42"})
print("Send alex to", request["approve_url"])
status = rotur_app("GET", f"/payment-requests/{request['id']}")["status"]
print("The payment is", status)

report = rotur_app("POST", "/reports", {"target_type": "user", "target_user": "someone", "category": "spam", "reporter": "kit"})
rotur_app("POST", f"/reports/{report['report_id']}/resolve", {"outcome": "resolved", "note": "Banned for a day"})

signal = rotur_app("POST", "/signals/message", {"from": "kit", "to": "alex"})
print("May kit message alex?", signal["allowed"], signal.get("reason"))
```
{% endcode %}
{% endtab %}

{% tab title="PHP" %}
{% code title="rotur_app.php" %}
```php
<?php
const ROTUR_APP_ID = 'app_0123456789abcdef'; // your app's ID

// Calls the apps API as your app. Throws on an error answer.
function rotur_app(string $method, string $path, ?array $body = null): ?array
{
    $curl = curl_init('https://api.rotur.dev/v2/apps/' . ROTUR_APP_ID . $path);
    curl_setopt_array($curl, [
        CURLOPT_CUSTOMREQUEST => $method,
        CURLOPT_USERPWD => ROTUR_APP_ID . ':' . getenv('ROTUR_APP_SECRET'),
        CURLOPT_HTTPHEADER => ['Content-Type: application/json'],
        CURLOPT_RETURNTRANSFER => true,
        CURLOPT_TIMEOUT => 10,
    ]);
    if ($body !== null) {
        curl_setopt($curl, CURLOPT_POSTFIELDS, json_encode($body));
    }
    $answer = json_decode((string) curl_exec($curl), true);
    $status = curl_getinfo($curl, CURLINFO_RESPONSE_CODE);
    if ($status === 0 || $status >= 300) {
        throw new RuntimeException("Rotur answered $status: " . ($answer['error'] ?? ''));
    }
    return $answer;
}
```
{% endcode %}

{% code title="examples.php" %}
```php
<?php
require __DIR__ . '/rotur_app.php';

$users = rotur_app('GET', '/users?q=ki')['users'];

rotur_app('PUT', '/badges/streak/users/kit', ['delta' => 1]);

$tomorrow = (time() + 86400) * 1000;
rotur_app('PUT', '/bans/someone', ['reason' => 'Spam', 'expires_at' => $tomorrow]);

$request = rotur_app('POST', '/payment-requests', ['payer' => 'alex', 'to' => 'kit', 'amount' => 5, 'note' => 'Sticker pack', 'reference' => 'order-42']);
echo "Send alex to {$request['approve_url']}\n";
$status = rotur_app('GET', "/payment-requests/{$request['id']}")['status'];
echo "The payment is $status\n";

$report = rotur_app('POST', '/reports', ['target_type' => 'user', 'target_user' => 'someone', 'category' => 'spam', 'reporter' => 'kit']);
rotur_app('POST', "/reports/{$report['report_id']}/resolve", ['outcome' => 'resolved', 'note' => 'Banned for a day']);

$signal = rotur_app('POST', '/signals/message', ['from' => 'kit', 'to' => 'alex']);
echo 'May kit message alex? ' . json_encode($signal) . "\n";
```
{% endcode %}
{% endtab %}

{% tab title="Node.js (SDK)" %}
`RoturApp` from `rotur-sdk/server` has a method for each call:

{% code title="examples.mjs" %}
```js
import { RoturApp } from "rotur-sdk/server";

const app = new RoturApp("app_0123456789abcdef", process.env.ROTUR_APP_SECRET);

const { users } = await app.users({ q: "ki" });

await app.badges.progress("streak", "kit", { delta: 1 });

await app.bans.add("someone", { reason: "Spam", expiresAt: Date.now() + 86_400_000 });

const request = await app.payments.create({ payer: "alex", to: "kit", amount: 5, note: "Sticker pack", reference: "order-42" });
console.log("Send alex to", request.approve_url);
const { status } = await app.payments.get(request.id);
console.log("The payment is", status);

const report = await app.reports.file({ targetType: "user", targetUser: "someone", category: "spam", reporter: "kit" });
await app.reports.resolve(report.report_id, "resolved", "Banned for a day");

const signal = await app.signals.message("kit", "alex");
console.log("May kit message alex?", signal.allowed, signal.reason);
```
{% endcode %}

Errors throw `ApiError`, with `status`, `code` and the answer in `data`.
{% endtab %}

{% tab title="Node.js" %}
Without the SDK, with `fetch`:

{% code title="rotur-app.mjs" %}
```js
const APP_ID = "app_0123456789abcdef"; // your app's ID
const auth = "Basic " + btoa(`${APP_ID}:${process.env.ROTUR_APP_SECRET}`);

// Calls the apps API as your app. Throws on an error answer.
export async function roturApp(method, path, body) {
  const res = await fetch(`https://api.rotur.dev/v2/apps/${APP_ID}${path}`, {
    method,
    headers: { Authorization: auth, "Content-Type": "application/json" },
    body: body && JSON.stringify(body),
  });
  const answer = res.status === 204 ? null : await res.json();
  if (!res.ok) throw new Error(`Rotur answered ${res.status}: ${answer?.error}`);
  return answer;
}
```
{% endcode %}

{% code title="examples.mjs" %}
```js
import { roturApp } from "./rotur-app.mjs";

const { users } = await roturApp("GET", "/users?q=ki");

await roturApp("PUT", "/badges/streak/users/kit", { delta: 1 });

await roturApp("PUT", "/bans/someone", { reason: "Spam", expires_at: Date.now() + 86_400_000 });

const request = await roturApp("POST", "/payment-requests", { payer: "alex", to: "kit", amount: 5, note: "Sticker pack", reference: "order-42" });
console.log("Send alex to", request.approve_url);
const { status } = await roturApp("GET", `/payment-requests/${request.id}`);
console.log("The payment is", status);

const report = await roturApp("POST", "/reports", { target_type: "user", target_user: "someone", category: "spam", reporter: "kit" });
await roturApp("POST", `/reports/${report.report_id}/resolve`, { outcome: "resolved", note: "Banned for a day" });

const signal = await roturApp("POST", "/signals/message", { from: "kit", to: "alex" });
console.log("May kit message alex?", signal.allowed, signal.reason);
```
{% endcode %}
{% endtab %}

{% tab title="Any language" %}
With curl, where `APP` is your app's ID. `jq` reads IDs out of the answers.

{% code title="examples.sh" %}
```sh
API="https://api.rotur.dev/v2/apps/$APP"
AUTH="$APP:$ROTUR_APP_SECRET"
JSON="Content-Type: application/json"

curl -s "$API/users?q=ki" -u "$AUTH"

curl -s -X PUT "$API/badges/streak/users/kit" -u "$AUTH" -H "$JSON" -d '{"delta": 1}'

TOMORROW=$(( ($(date +%s) + 86400) * 1000 ))
curl -s -X PUT "$API/bans/someone" -u "$AUTH" -H "$JSON" -d "{\"reason\": \"Spam\", \"expires_at\": $TOMORROW}"

REQUEST=$(curl -s -X POST "$API/payment-requests" -u "$AUTH" -H "$JSON" \
  -d '{"payer": "alex", "to": "kit", "amount": 5, "note": "Sticker pack", "reference": "order-42"}' | jq -r .id)
curl -s "$API/payment-requests/$REQUEST" -u "$AUTH"

REPORT=$(curl -s -X POST "$API/reports" -u "$AUTH" -H "$JSON" \
  -d '{"target_type": "user", "target_user": "someone", "category": "spam", "reporter": "kit"}' | jq -r .report_id)
curl -s -X POST "$API/reports/$REPORT/resolve" -u "$AUTH" -H "$JSON" -d '{"outcome": "resolved", "note": "Banned for a day"}'

curl -s -X POST "$API/signals/message" -u "$AUTH" -H "$JSON" -d '{"from": "kit", "to": "alex"}'
```
{% endcode %}
{% endtab %}
{% endtabs %}

## Errors

Error answers are JSON with `error`, a sentence you can log, and usually `code`. [Call the apps API](apps-api.md#errors) lists them.

## Related

* [Call the apps API](apps-api.md): every endpoint, including the ones only your team can use on rotur.dev.
* [Receive webhooks](webhooks.md): what Rotur tells your server.
