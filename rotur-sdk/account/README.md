# Account

Log in, manage the signed-in account, and look up other users' public profiles.

| Page | Covers |
| --- | --- |
| [Authentication](authentication.md) | Popup login, link codes, token refresh and checks |
| [Me](me.md) | `rotur.me`: account data, credits, badges, blocking, notes, billing |
| [Profiles](profiles.md) | `rotur.profiles`: public profiles, existence checks, image URLs |

## Other account namespaces

These namespaces have no page of their own yet. Each method calls `https://api.rotur.dev/v2` like the rest of the SDK.

### rotur.accounts

Account sign-up and recovery. None of these send a token.

| Method | Endpoint | Notes |
| --- | --- | --- |
| `register(options)` | `POST /accounts` | `{ username, password, email, captcha, system?, invite_code? }`. The API also needs `date_of_birth`, and takes `app` (the Rotur App ID or slug the account is made through, which becomes its origin app). The SDK type does not list those two yet, so add them yourself. Returns the new account |
| `requestPasswordReset(email)` | `POST /accounts/password/reset-request` | Returns `{ message }` |
| `resetPassword(token, newPassword)` | `POST /accounts/password/reset` | Returns `{ message }` |
| `verifyEmail(token)` | `GET /accounts/verify-email` | Returns `{ message, username }` |
| `deletedCheck(ids)` | `POST /accounts/deleted-check` | Which of these user IDs belong to deleted accounts |
| `bannedCheck(usernames)` | `POST /accounts/banned-check` | Returns `{ banned: string[] }` |

`checkDeleted()` and `passwordResetRequest()` are deprecated aliases of `deletedCheck()` and `requestPasswordReset()`.

### rotur.sessions

Cookie sessions for Rotur's own sites. `start()` (`POST /sessions`) turns your token into a session cookie and only works from rotur.dev. `current()` (`GET /sessions`) returns `{ user, accounts }`, and `end()` (`DELETE /sessions`) signs the session out. Other apps should keep using the token.

### rotur.signing

Signs, verifies, encrypts and decrypts with the account's Ed25519 identity key.

| Method | Auth | Notes |
| --- | --- | --- |
| `sign(content)` | `signing:private` | Returns `{ author_id, key_id, signature }`. Pass a function to put the author ID inside the signed content |
| `verify(proof, content)` | None | Fetches the public key from `GET /users/:id/signing-keys/:keyid`. Returns `boolean` |
| `privateKey()` | `signing:private` | `GET /me/signing-key`, including the private JWK |
| `publicKey(reference, refresh?)` | None | A public key by `{ user_id, key_id }` |
| `encrypt(recipient, content)` | None | Encrypts JSON for a recipient's key. Returns an `EncryptedEnvelope` |
| `decrypt(envelope)` | `signing:private` | Opens an envelope sent to you |
| `preload()` | `signing:private` | Fetches your key in the background |
| `rotate()` | Main token only | `POST /me/signing-key/rotate`. Old signatures still verify |

### rotur.trust

{% hint style="warning" %}
The trust score API has been removed. `rotur.trust.score()` and `rotur.trust.verify()` are still in the SDK, but `GET /trust/:username/score` and `POST /verify/user` no longer exist, so both fail with `404`.
{% endhint %}

### rotur.admin and rotur.moderation

Tools for Rotur staff. They need a staff account or the admin token, and fail for everyone else.
