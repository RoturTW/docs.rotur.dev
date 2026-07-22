# Authentication

{% hint style="warning" %}
This page documents the legacy Rotur websocket, which is deprecated but kept around as a reference. For new projects, we recommend the REST API at [https://api.rotur.dev](https://api.rotur.dev) together with the [Rotur SDK](../../rotur-sdk/README.md).
{% endhint %}


Authentication is handled by a special peer named `sys-rotur` . sys-rotur is a restricted username, you are not able to set yourself as sys-rotur under any circumstance.  Authenticating requires you to be connected to the websocket server and in the roturTW room.

## Requirements for auth

* Be connected to ws.rotur.dev or rotur.mistium.com
* Have successfully handshaked
* Not already be authed
* Have a valid[ preauthorised rotur username](../connecting/setid.md#pre-authorised-rotur-usernames)
* Be in the "roturTW" room
