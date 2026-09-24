# Events

Events organize an RTR program. Each event block runs when the host program triggers that event.

```js
event (eventName) {
    /* statements */
}
```

| Event | When it runs |
| --- | --- |
| `onload` | Once, when the program is created |
| Any other name | When the host program triggers it by name |

The interpreter itself only runs `onload`. Every other event name, such as `update`, is defined by the program that hosts RTR. Hosts can trigger events with the interpreter's `runEvent(name)` method. Event names can have two words, such as `event (mouse onmove)`.

All events share the same variables, so a variable set in `onload` keeps its value in later events:

```js
event (onload) {
    frames = 0
}

event (update) {
    frames += 1
}
```
