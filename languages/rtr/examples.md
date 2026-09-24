# Examples

Each example is a complete program. `exit()` and the `update` and `mouse onmove` events are provided by the host program, not by the interpreter, so check that your host supports them.

## Count in a loop

Adds 1 to a variable 8,000,000 times, then exits.

```js
event (onload) {
  frames = 0
  repeat (8000000) {
    frames += 1
  }
  exit()
}
```

## Count on each update

Adds 1 on each `update` event and exits after 10,000 updates.

```js
event (update) {
  frames += 1
  if (frames > 10000) {
    exit()
  }
}

event (onload) {
  frames = 0
}
```

## Log the mouse position

Logs the mouse position whenever the mouse moves.

```js
event (mouse onmove) {
  log(mouse.x, mouse.y)
}
```
