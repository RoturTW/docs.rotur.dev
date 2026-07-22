# Connecting

{% hint style="warning" %}
This page documents the legacy Rotur websocket, which is deprecated but kept around as a reference. For new projects, we recommend the REST API at [https://api.rotur.dev](https://api.rotur.dev) together with the [Rotur SDK](../../rotur-sdk/README.md).
{% endhint %}


Rotur's websocket is hosted at&#x20;

```
wss://rotur.mistium.com

or

wss://ws.rotur.dev
```

Both websocket urls lead to the same server so if you connect to ws.rotur.dev and your friend connects to rotur.mistium.com you two can still chat just fine!



The first step is simply to open a websocket connection with rotur!

{% code title="rotur.js" lineNumbers="true" fullWidth="false" %}
```javascript
my_rotur = new WebSocket("wss://ws.rotur.dev")

my_rotur.onopen = () => {
  console.log("Connected!")
  // your logic for handling packets goes here
}
```
{% endcode %}
