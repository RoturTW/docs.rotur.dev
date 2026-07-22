## OUTDATED PLEASE USE&#x20;

[get-user-data.md](./get-user-data.md "mention")

# Login


Logging into rotur is pretty simple. Ensure you meet the requirements for authentication in [.](./ "mention")

This uses a standard Rotur pmsg (private message) to send an authentication request to the Rotur auth server.

```javascript
{
  "cmd": "pmsg",
  "val": {
    "id": "", // this should just be an empty string
    "command": "login", // command to send to the rotur server
    "source": "0", // your program's rotur port. 0 is the root
    "client": {
      "system": "originOS", // the operating system you are using
      "version": "v5.5.4"
    },
    "payload": [
      "Mist", // your rotur account username
      "" // an md5 hash of your rotur account password
    ],
    "timestamp": 1739576518281 // when your message was sent (unix ms)
  },
  "id": "sys-rotur"
}
```

## Successful auth

Upon successful authentication (your username and password are correct) you will recieve this packet back from the sys-rotur peer.&#x20;

```json
{
  "cmd": "pmsg",
  "val": {
    "username": "sys-rotur", // username is included here for backwards compat
    "source_command": "login", // the command that triggered this response
    "target": 0, // this means that this message was sent to the port 0
    "first_login": false, // tells you if this is the first time you have logged in today
    "token": "rotur account token", // the authorisation token for your rotur account
    "payload": { rotur account object } // the full json object for your rotur account
  },
  "origin": {
    "username": "sys-rotur" // the sys.rotur connection object (from the websocket, this is fully trustworthy)
  },
  "rooms": "roturTW" // tells you what room the message was sent in
}
```

Find out about rotur account objects here: [rotur-account-objects](../../my-account/rotur-account-objects/ "mention")

## Incorrect details

If your password or username is wrong you will recieve this message

```javascript
{
  "cmd": "pmsg",
  "val": {
    "username": "sys-rotur",
    "source_command": "login",
    "target": "0", // this means that this message was sent to the port 0
    "payload": "Invalid Details" // the fail message
  },
  "origin": {
    "username": "sys-rotur" // the sys.rotur connection object (from the websocket, this is fully trustworthy)
  },
  "rooms": "roturTW" // tells you what room the message was sent in
}
```
