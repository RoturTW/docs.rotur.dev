# Structure

This page describes how an RTR program is laid out: event blocks, statements, expressions, and scope.

## Program

A program is one or more event blocks. The host runs a block's statements when its event happens. Code outside an event block does not run.

```js
event (eventName) {
    /* statements */
}

event (anotherEvent) {
    /* more statements */
}
```

Statements end at a new line or a `;`. Spaces outside strings are ignored, so indentation is only for readability. Comments go between `/*` and `*/`.

## Statements

| Statement | Syntax |
| --- | --- |
| Assignment | `variable = value` |
| Compound assignment | `variable += value` (also `-=`, `*=`, `/=`, `%=`, `^=`) |
| Property assignment | `object.property = value` |
| Function definition | `name = (param1, param2)~{ body }` |
| Function call | `name(arg1, arg2)` |
| Return | `return(value)` |
| If | `if (condition) { } elif (condition) { } else { }` |
| While | `while (condition) { }` |
| Repeat | `repeat (times) { }` |
| For | `for (variable, array) { }` |
| Scope | `scope { }` |

## Expressions

### Literals

| Type | Examples |
| --- | --- |
| Number | `42`, `3.14`, `-7` |
| String | `"Hello"` (double quotes only) |
| Boolean | `true`, `false` |
| Null | `null` |
| Array | `[1, 2, 3]` |
| Object | `{"key": "value"}` (JSON, keys in double quotes) |

### Operators

| Kind | Operators |
| --- | --- |
| Arithmetic | `+`, `-`, `*`, `/`, `%`, `^` |
| Comparison | `==`, `!=`, `>=`, `<=`, `>`, `<` |
| Assignment | `=`, `+=`, `-=`, `*=`, `/=`, `%=`, `^=` |

Operators are evaluated from left to right with no precedence. For negation, call `not(value)` or `!(value)`.

### Property access

```js
object.property
object.method(arg1, arg2)
```

To read an array element by index, use [`item`](functions/array.md#item): `item(array, 0)`.

## Scope

RTR keeps variables in layers:

1. **Built-ins**: the [built-in functions](functions/README.md) and variables.
2. **Program**: variables assigned in event blocks. All events share this layer, so a variable set in `onload` can be read in another event.
3. **Function**: each function call adds a layer for its parameters and local variables.
4. **Block**: a `scope { }` block adds a layer.

Reading a variable searches from the innermost layer outwards. Assigning always writes to the innermost layer, so assigning inside a function or `scope` block creates a local variable:

```js
event (onload) {
    variable = "outer"
    scope {
        variable = "inner"
        log(variable)  /* inner */
    }
    log(variable)  /* outer */
}
```

Assigning to a property (`object.property = value`) changes the object itself, wherever it was created.

## Built-in variables

| Variable | Contents |
| --- | --- |
| `platform` | The host program's `name` and `version` |
| `rtr` | `version` of the interpreter and its `environment` |
| `mouse` | `x`, `y`, `down`, `clicked`, `moved` |
| `keysdown` | Array of keys currently held |

The host program updates `mouse` and `keysdown`.

## Events

See [Events](events.md).
