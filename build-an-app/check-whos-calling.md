---
description: Your server reads the validator that rotur.fetch sends and asks Rotur who made it. Examples in Go, Python, PHP and JavaScript, and the HTTP call for any other language.
---

# Check who's calling

[`rotur.fetch`](call-your-server.md) sends `Authorization: Rotur <validator>` with each request to your server. Your server asks Rotur who made the validator with one HTTP call. It needs no secret and no library.

## The call

```
GET https://api.rotur.dev/v2/validators/verify?v=<validator>&key=<your app ID>
```

URL-encode the validator: it contains a comma.

Rotur always answers `200`, and you only need to read `valid`. Anything else (`400` for a missing parameter, `429`, `5xx`, or no answer) means the check didn't happen: tell the browser to try again.

**Yes:**

```json
{ "valid": true, "id": "7ebdf483-5b9f-4b70-9edc-a1f2827391f9", "username": "kit", "minor": false, "expires_at": 1790000300000 }
```

| Field | What it is |
| --- | --- |
| `id` | Their Rotur ID. It never changes, so key your data by it |
| `username` | Their username. It can change |
| `minor` | `true` for anyone not known to be 18 or over |
| `expires_at` | When the validator stops working, in Unix milliseconds. Keep this answer until then |
| `account_type` | Only for bot and organisation accounts: `bot` or `org` |
| `owner` | Only for those accounts, when the owner shows it: the owner's username |

Their avatar is at `https://avatars.rotur.dev/<username>`.

**No:**

```json
{ "valid": false, "code": "app_banned", "error": "You've been banned from Sketchpad.", "reason": "Spam", "until": 1790086400000 }
```

`error` is a sentence written for the person, so you can show it to them. `code` says why:

| `code` | Why |
| --- | --- |
| `invalid_validator` | The validator is wrong, expired or made for another app |
| `app_banned` | You [banned them](bans.md). `reason` is the reason you gave, and `until` is when the ban ends (Unix milliseconds, `0` if it doesn't) |
| `app_suspended` | Rotur has suspended your app |
| `app_adults_only` | Your app declares mature content, and they aren't known to be 18 or over |
| `parent_blocked_apps`, `parent_approval_needed` | A parent has turned off apps for them, or hasn't approved yours yet |
| `account_unavailable`, `account_blocked` | Their Rotur account can't use apps right now |

Keep a yes until its `expires_at`, so each validator is checked once. Don't keep a no.

## Keep a session

The examples also keep a session, in the way each language usually does, so requests that don't come from `rotur.fetch` know who is calling: page loads, form posts and WebSockets. A request with a validator checks it again, and a refusal ends the session.

A session doesn't hear about bans by itself. When you [ban someone](bans.md), end their sessions too. The `user.banned` [webhook](webhooks.md) tells you when Rotur bans an account.

## In your language

Each example answers `GET /api/me` with who is calling, or `401` with Rotur's `error` and `code`. Put your app's ID in place of `app_0123456789abcdef`.

{% tabs %}
{% tab title="Go" %}
Standard library only. `checkValidator` asks Rotur, and keeps a yes until it expires:

{% code title="rotur.go" %}
```go
package main

import (
	"encoding/json"
	"fmt"
	"net/http"
	"net/url"
	"sync"
	"time"
)

const appID = "app_0123456789abcdef" // your app's ID

// Caller is Rotur's answer about a validator.
type Caller struct {
	Valid     bool   `json:"valid"`
	ID        string `json:"id"`
	Username  string `json:"username"`
	Minor     bool   `json:"minor"`
	ExpiresAt int64  `json:"expires_at"` // Unix ms
	Code      string `json:"code"`       // why not, when Valid is false
	Error     string `json:"error"`      // a sentence to show them
}

var (
	client  = &http.Client{Timeout: 10 * time.Second}
	cacheMu sync.Mutex
	cache   = map[string]Caller{} // validator → a yes, until it expires
)

// checkValidator asks Rotur who made a validator. An error means Rotur
// couldn't answer.
func checkValidator(validator string) (Caller, error) {
	now := time.Now().UnixMilli()
	cacheMu.Lock()
	known, ok := cache[validator]
	cacheMu.Unlock()
	if ok && known.ExpiresAt > now {
		return known, nil
	}
	var caller Caller
	res, err := client.Get("https://api.rotur.dev/v2/validators/verify?" + url.Values{"v": {validator}, "key": {appID}}.Encode())
	if err != nil {
		return caller, err
	}
	defer res.Body.Close()
	if res.StatusCode != http.StatusOK {
		return caller, fmt.Errorf("rotur answered %s", res.Status)
	}
	if err := json.NewDecoder(res.Body).Decode(&caller); err != nil || !caller.Valid {
		return caller, err
	}
	cacheMu.Lock()
	defer cacheMu.Unlock()
	for v, c := range cache {
		if c.ExpiresAt <= now {
			delete(cache, v)
		}
	}
	cache[validator] = caller
	return caller, nil
}
```
{% endcode %}

`withRotur` is middleware that works out who is calling, and keeps sessions in memory. Keep them in your database instead if they should outlast a restart.

{% code title="main.go" %}
```go
package main

import (
	"context"
	"crypto/rand"
	"encoding/json"
	"net/http"
	"strings"
	"sync"
)

var sessions sync.Map // session cookie value → Caller

type callerKey struct{}

// withRotur finds out who is calling, from the validator rotur.fetch sends or
// from the session cookie, and answers 401 when nobody is signed in.
func withRotur(next http.HandlerFunc) http.HandlerFunc {
	return func(w http.ResponseWriter, r *http.Request) {
		caller := Caller{Error: "Sign in with Rotur first"}
		cookie, _ := r.Cookie("session")
		if cookie != nil {
			if saved, ok := sessions.Load(cookie.Value); ok {
				caller = saved.(Caller)
			}
		}
		if validator, ok := strings.CutPrefix(r.Header.Get("Authorization"), "Rotur "); ok {
			checked, err := checkValidator(validator)
			if err != nil {
				http.Error(w, "Rotur didn't answer. Try again.", http.StatusServiceUnavailable)
				return
			}
			if cookie != nil && !checked.Valid {
				sessions.Delete(cookie.Value)
			}
			if checked.Valid && checked.ID != caller.ID {
				id := rand.Text()
				sessions.Store(id, checked)
				http.SetCookie(w, &http.Cookie{Name: "session", Value: id, Path: "/", HttpOnly: true, Secure: true, SameSite: http.SameSiteLaxMode})
			}
			caller = checked
		}
		if !caller.Valid {
			w.Header().Set("Content-Type", "application/json")
			w.WriteHeader(http.StatusUnauthorized)
			json.NewEncoder(w).Encode(map[string]string{"error": caller.Error, "code": caller.Code})
			return
		}
		next(w, r.WithContext(context.WithValue(r.Context(), callerKey{}, caller)))
	}
}

func main() {
	http.HandleFunc("GET /api/me", withRotur(func(w http.ResponseWriter, r *http.Request) {
		caller := r.Context().Value(callerKey{}).(Caller)
		json.NewEncoder(w).Encode(map[string]string{"id": caller.ID, "username": caller.Username})
	}))
	http.ListenAndServe(":8080", nil)
}
```
{% endcode %}

`go run .` starts it on port 8080. It needs Go 1.24 or later.
{% endtab %}

{% tab title="Python" %}
`check_validator` uses only the standard library, so it works with any framework:

{% code title="rotur.py" %}
```python
import json
import threading
import time
import urllib.parse
import urllib.request

APP_ID = "app_0123456789abcdef"  # your app's ID
_cache, _lock = {}, threading.Lock()  # validator → a yes, until its expires_at


def check_validator(validator):
    """Asks Rotur who made a validator, and returns its answer: check answer["valid"].
    Raises urllib.error.URLError when Rotur can't answer."""
    now = time.time() * 1000
    if _cache.get(validator, {}).get("expires_at", 0) > now:
        return _cache[validator]
    query = urllib.parse.urlencode({"v": validator, "key": APP_ID})
    with urllib.request.urlopen(f"https://api.rotur.dev/v2/validators/verify?{query}", timeout=10) as res:
        answer = json.load(res)
    if answer["valid"]:
        with _lock:
            for old in [v for v, a in _cache.items() if a["expires_at"] <= now]:
                del _cache[old]
            _cache[validator] = answer
    return answer
```
{% endcode %}

With Flask, check each request before it reaches your routes, and keep who it is in Flask's signed session cookie:

{% code title="app.py" %}
```python
import os

from flask import Flask, request, session

from rotur import check_validator

app = Flask(__name__)
app.secret_key = os.environ["SESSION_SECRET"]  # any long random string
app.config.update(SESSION_COOKIE_SECURE=True, SESSION_COOKIE_SAMESITE="Lax")


@app.before_request
def who_is_calling():
    header = request.headers.get("Authorization", "")
    if header.startswith("Rotur "):
        answer = check_validator(header.removeprefix("Rotur "))
        if not answer["valid"]:
            session.clear()
            return {"error": answer["error"], "code": answer["code"]}, 401
        session["user"] = {"id": answer["id"], "username": answer["username"], "minor": answer["minor"]}


@app.get("/api/me")
def me():
    if "user" not in session:
        return {"error": "Sign in with Rotur first"}, 401
    return session["user"]
```
{% endcode %}

`flask run --port 8080` starts it.

With FastAPI, make it a dependency. Starlette's `SessionMiddleware` keeps the session (it needs `pip install itsdangerous`):

{% code title="main.py" %}
```python
import os

from fastapi import Depends, FastAPI, HTTPException, Request
from starlette.middleware.sessions import SessionMiddleware

from rotur import check_validator

app = FastAPI()
app.add_middleware(SessionMiddleware, secret_key=os.environ["SESSION_SECRET"], https_only=True)


def rotur_user(request: Request) -> dict:
    header = request.headers.get("authorization", "")
    if header.startswith("Rotur "):
        answer = check_validator(header.removeprefix("Rotur "))
        if not answer["valid"]:
            request.session.clear()
            raise HTTPException(401, {"error": answer["error"], "code": answer["code"]})
        request.session["user"] = {"id": answer["id"], "username": answer["username"], "minor": answer["minor"]}
    if "user" not in request.session:
        raise HTTPException(401, {"error": "Sign in with Rotur first"})
    return request.session["user"]


@app.get("/api/me")
def me(user: dict = Depends(rotur_user)):
    return user
```
{% endcode %}

`uvicorn main:app --port 8080` starts it.
{% endtab %}

{% tab title="PHP" %}
`rotur_require_user()` finds out who is calling, keeps them in the PHP session, and stops with `401` if nobody is signed in. The session also keeps the last yes until its `expires_at`, so each validator is checked once. It needs the curl extension.

{% code title="rotur.php" %}
```php
<?php
const ROTUR_APP_ID = 'app_0123456789abcdef'; // your app's ID

// Asks Rotur who made a validator, and returns its answer: check ['valid'].
function rotur_check(string $validator): array
{
    $query = http_build_query(['v' => $validator, 'key' => ROTUR_APP_ID]);
    $curl = curl_init("https://api.rotur.dev/v2/validators/verify?$query");
    curl_setopt_array($curl, [CURLOPT_RETURNTRANSFER => true, CURLOPT_TIMEOUT => 10]);
    $body = curl_exec($curl);
    $status = curl_getinfo($curl, CURLINFO_RESPONSE_CODE);
    if ($body === false || $status !== 200) {
        throw new RuntimeException('Rotur did not answer. Try again.');
    }
    return json_decode($body, true);
}

// Who is calling: from the validator rotur.fetch sends, or from the session.
function rotur_require_user(): array
{
    session_start(['cookie_httponly' => true, 'cookie_secure' => true, 'cookie_samesite' => 'Lax']);
    $header = $_SERVER['HTTP_AUTHORIZATION'] ?? '';
    $validator = str_starts_with($header, 'Rotur ') ? substr($header, 6) : null;
    $known = $validator === ($_SESSION['rotur_validator'] ?? null) && ($_SESSION['rotur_expires_at'] ?? 0) > time() * 1000;
    if ($validator !== null && !$known) {
        $answer = rotur_check($validator);
        if (!$answer['valid']) {
            session_destroy();
            http_response_code(401);
            header('Content-Type: application/json');
            exit(json_encode(['error' => $answer['error'], 'code' => $answer['code']]));
        }
        session_regenerate_id(true);
        $_SESSION['rotur_validator'] = $validator;
        $_SESSION['rotur_expires_at'] = $answer['expires_at'];
        $_SESSION['rotur_user'] = ['id' => $answer['id'], 'username' => $answer['username'], 'minor' => $answer['minor']];
    }
    if (!isset($_SESSION['rotur_user'])) {
        http_response_code(401);
        header('Content-Type: application/json');
        exit(json_encode(['error' => 'Sign in with Rotur first']));
    }
    return $_SESSION['rotur_user'];
}
```
{% endcode %}

Call it at the top of each script that needs to know who is calling:

{% code title="me.php" %}
```php
<?php
require __DIR__ . '/rotur.php';

$user = rotur_require_user();
header('Content-Type: application/json');
echo json_encode(['id' => $user['id'], 'username' => $user['username']]);
```
{% endcode %}

Apache with PHP-FPM drops the `Authorization` header unless you add `CGIPassAuth On` to the site's configuration or `.htaccess`.
{% endtab %}

{% tab title="Node.js (SDK)" %}
`roturSession` from `rotur-sdk/server` does all of this for Express, Connect and other Node servers. It answers `401` with Rotur's refusal as JSON: `ok: false`, `code`, `error`, and for a ban `reason` and `until`. With no validator, `code` is `signed_out`. With a `secret`, it also keeps a signed session cookie for 7 days, and checks with Rotur again every 5 minutes.

{% code title="server.mjs" %}
```js
import express from "express";
import { roturSession } from "rotur-sdk/server";

const app = express();

// Sets req.rotur to { id, username, minor, avatar }, or answers 401 with why not.
app.use("/api", roturSession({ app: "app_0123456789abcdef", secret: process.env.SESSION_SECRET }));

app.get("/api/me", (req, res) => {
  res.json({ id: req.rotur.id, username: req.rotur.username });
});

app.listen(8080);
```
{% endcode %}

Pass `optional: true` to let requests through with `req.rotur` set to `null` instead.

For servers built on `Request` and `Response` (Hono, Cloudflare Workers, Next.js route handlers), use `roturSession.fromRequest`:

{% code title="route.mjs" %}
```js
import { roturSession } from "rotur-sdk/server";

export async function GET(request) {
  const who = await roturSession.fromRequest(request, { app: "app_0123456789abcdef", secret: process.env.SESSION_SECRET });
  const headers = who.setCookie ? { "Set-Cookie": who.setCookie } : {}; // starts or ends the session
  if (!who.ok) return Response.json(who, { status: 401, headers });
  return Response.json({ id: who.id, username: who.username }, { headers });
}
```
{% endcode %}

`verifyValidator(validator, "app_0123456789abcdef")` does only the check. It resolves to the person with `ok: true`, or to the refusal with `ok: false`.
{% endtab %}

{% tab title="Node.js" %}
Without the SDK, `checkValidator` uses `fetch`, which Node 18 and later have built in:

{% code title="rotur.mjs" %}
```js
const APP_ID = "app_0123456789abcdef"; // your app's ID
const cache = new Map(); // validator → a yes, until its expires_at

// Asks Rotur who made a validator, and resolves to its answer: check `valid`.
export async function checkValidator(validator) {
  if (cache.get(validator)?.expires_at > Date.now()) return cache.get(validator);
  const query = new URLSearchParams({ v: validator, key: APP_ID });
  const res = await fetch(`https://api.rotur.dev/v2/validators/verify?${query}`);
  if (!res.ok) throw new Error(`Rotur answered ${res.status}`);
  const answer = await res.json();
  if (answer.valid) {
    for (const [old, kept] of cache) if (kept.expires_at <= Date.now()) cache.delete(old);
    cache.set(validator, answer);
  }
  return answer;
}
```
{% endcode %}

With Express 5 and `express-session`:

{% code title="server.mjs" %}
```js
import express from "express";
import session from "express-session";
import { checkValidator } from "./rotur.mjs";

const app = express();
app.use(session({ secret: process.env.SESSION_SECRET, resave: false, saveUninitialized: false, cookie: { httpOnly: true, secure: "auto", sameSite: "lax" } }));

// Who is calling: from the validator rotur.fetch sends, or from the session.
app.use("/api", async (req, res, next) => {
  const validator = req.get("Authorization")?.match(/^Rotur (\S+)$/)?.[1];
  if (validator) {
    const answer = await checkValidator(validator);
    if (!answer.valid) return req.session.destroy(() => res.status(401).json({ error: answer.error, code: answer.code }));
    req.session.user = { id: answer.id, username: answer.username, minor: answer.minor };
  }
  if (!req.session.user) return res.status(401).json({ error: "Sign in with Rotur first" });
  next();
});

app.get("/api/me", (req, res) => res.json(req.session.user));

app.listen(8080);
```
{% endcode %}

`express-session` keeps sessions in memory by default, so give it a store for production. Behind a proxy that handles HTTPS, also call `app.set("trust proxy", 1)`, or the session cookie won't be marked secure.
{% endtab %}

{% tab title="Any language" %}
Make the call with your language's HTTP client. With curl:

{% code title="verify.sh" %}
```sh
curl -sG https://api.rotur.dev/v2/validators/verify \
  --data-urlencode "v=$VALIDATOR" \
  --data-urlencode "key=app_0123456789abcdef"
```
{% endcode %}

Then, in your server:

1. Read the `Authorization` header. If it starts with `Rotur `, the rest is the validator.
2. Look the validator up in your cache. If it isn't there, make the call above, and keep a yes until its `expires_at`.
3. If the answer has `"valid": true`, the caller is `id`. Start or update their session.
4. Otherwise answer `401` with `error` and `code`, and end any session the request came with.
5. With no validator, use the session. With no session, answer `401`.
{% endtab %}
{% endtabs %}

## Try it

Sign in to your page, open the browser's developer tools, and copy the `Authorization` header from a request `rotur.fetch` made. Send it to your server:

```sh
curl -i http://localhost:8080/api/me -H "Authorization: Rotur 7ebdf483-…,9c1f…"
```

A validator works for 5 minutes after it was made.

## Related

* [Validators](../accounts-and-tokens/validators.md): how validators work, and the rest of the endpoint.
* [Use your app secret on the server](app-secret.md): badges, bans, payments and reports.
