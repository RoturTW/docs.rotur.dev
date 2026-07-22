# Link

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

You should then recieve these messages from rotur

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
