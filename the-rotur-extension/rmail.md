# Rmail

The rMail blocks send short messages with a subject between logged-in users, and keep the ones you receive in a mailbox in your project.

{% hint style="warning" %}
In the current extension, mail is delivered over the socket on your designation and kept only in memory. The recipient has to be connected on the same designation when you send it, and their mailbox is empty again after the project reloads. It doesn't read or write the server-side mailbox from the [Rmail API](../assorted-apis/rmail.md).
{% endhint %}

## Send mail

```
(send mail with subject: [Subject] and message: [Message] to: [user])
```

Reporter. Sends mail to the user named in `TO`.

| Returns | When |
| --- | --- |
| `Mail sent` | The mail was handed to the socket |
| `Not Connected` / `Not Logged In` | There's no connection or no logged-in user |
| Error message | Sending failed |

`Mail sent` doesn't confirm delivery. If the recipient isn't on your designation, the mail is dropped.

## Receive mail

```
when mail received
```

Hat. Fires when mail arrives.

## Read the mailbox

Each mail gets a number when it arrives, starting at `1` and counting up. The `[ID]` input in the blocks below is that number. Deleting mail doesn't renumber the rest.

| Block | Type | Returns |
| --- | --- | --- |
| `(get mail list)` | Reporter | JSON array of `{ "id", "subject", "from" }` for every mail |
| `(get body of mail at index [1])` | Reporter | The full mail as JSON, or `Mail not found` |
| `delete mail at index [1]` | Command | Removes one mail |
| `delete all mail` | Command | Empties the mailbox |

Example `get mail list` result:

```json
[
  { "id": "1", "subject": "Subject", "from": "mist" }
]
```

Example `get body of mail at index [1]` result:

```json
{
  "id": "1",
  "subject": "Subject",
  "message": "Message",
  "from": "mist",
  "timestamp": 1723684583612
}
```
