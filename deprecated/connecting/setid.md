# SetID

This command is also largely unchanged in comparison to the cl4 standard, except for one change. you are able to change your username to anything without disconnecting first. The server will make a fake disconnect and reconnect for you and rename you on all other active clients.

```javascript
{
  "cmd": "setid",
  "val": "", // the username you want other users to know you as
  "listener": "username_cfg" // the listener for your packet
}
```

You should then recieve these packets from the server

```javascript
{
  "cmd": "ulist",
  "mode": "set", // overwrite your local userlist
  "val": [ // an array of user objects. These may be updated later to contain more info
    {"username": "ori-EwZWksbrYDBAxziSZwWejSwK2QMMzGZf"}
  ],
  "room": "default"
}
```

```javascript
{
  "cmd": "statuscode",
  "code": "I:100 | OK", 
  "code_id": 100, 
  "listener": "username_cfg", // this is the response for your set username packet
  "val": {
    "room": "default", // the room your username was changed in
    "username": "ori-EwZWksbrYDBAxziSZwWejSwK2QMMzGZf" // the value your username is set to
  }
}
```

### Pre-authorised rotur usernames

A pre-authorised rotur username is any username that has a 3 letter designation, then a hyphen, then a string of 32 random letters.

#### Valid

* ori-EwZWksbrYDBAxziSZwWejSwK2QMMzGZf
* rtr-EwZWksbrYDBAxziSZwWejSwK2QMMzGZf
* wow-EwZWksbrYDBAxziSZwWejSwK2QMMzGZf

#### Invalid

* oriEwZWksbrYDBAxziSZwWejSwK2QMMzGZf
* r-EwZWksbrYDBAxziSZwWejSwK2QMMzGZf
* hello-EwZWksbrYDBAxziSZwWejSwK2QMMzGZf

You can connect without the 32 characters, but the designation is required to connect otherwise you will be kicked.
