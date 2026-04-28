# Handshake

The handshake is unmodified from cl4, using the same format and same inputs. This is the first command you should send to the server.

```javascript
{
  "cmd": "handshake",
  "val": {
    "language": "Javascript", // the language that you are connecting using
    "version": {
      "editorType": "Scratch", // the type of your environment
      "versionNumber": 3
    }
  },
  "listener": "handshake_cfg"
}


```

Then the server should return a set of packets listed below. In the same order

```javascript
{
  "cmd": "client_ip",
  "val": "" // your ip
}
```

```javascript
{
  "cmd": "server_version", 
  "val": "0.2.0" // the version of the websocket server
}
```

```javascript
{
  "cmd": "statuscode",
  "code": "I:100 | OK",
  "code_id": 100,
  "listener": "handshake_cfg" // handshake completed statuscode
}
```
