# Link

{% hint style="warning" %}
This page documents the legacy Rotur websocket, which is deprecated but kept around as a reference. For new projects, we recommend the REST API at [https://api.rotur.dev](https://api.rotur.dev) together with the [Rotur SDK](../../rotur-sdk/README.md).
{% endhint %}


Linking is done identically to how it is done in cloudlink4.

Rotur's main room is "roturTW" where the auth server resides, but after you have authed you can join other private rooms where you can communicate

```javascript
{
  "cmd": "link",
  "val":[
    "roturTW" // the room to link to
  ],
  "listener":"link" // tells the server to mark the response with this same listener
}
```

You should then receive these messages from rotur

```javascript
{
  "cmd": "ulist",
  "mode": "set",
  "val": [ // the current room members full of user objects
    {user object},
    {user object},
    {user object},
    {user object},
    {"username": "sys-rotur"} // the server connection
 }
 "room": "roturTW"
}
```

```javascript
{
  "cmd": "statuscode",
  "code": "I:100 | OK",
  "code_id": 100,
  "listener": "link" // the response for your link command
}
```
