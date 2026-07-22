# Login to rotur (auth)

{% hint style="warning" %}
This page documents the legacy Rotur websocket, which is deprecated but kept around as a reference. For new projects, we recommend the REST API at [https://api.rotur.dev](https://api.rotur.dev) together with the [Rotur SDK](../../rotur-sdk/README.md).
{% endhint %}


## Logging into rotur (`auth`)

The `auth` command is used to authenticate yourself with the rotur websocket and become a verified connection.

### Client Request

To authenticate, the message should look like this

```json
{
  "cmd": "auth",
  "val": "token"
}
```

**Field Descriptions:**

* **`cmd`**: Must be `"auth"`. This indicates an auth packet
* **`val`**: A string containing the token of the rotur account you are authenticating as

### Server Response

Once the server processes the private message, it sends a status confirmation back to the sender:

```json
{
  "cmd": "statuscode",
  "code": "I:100 | OK",
  "code_id": 100
}
```
