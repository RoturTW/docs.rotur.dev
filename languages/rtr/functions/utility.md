# Utility functions

| Function | Effect |
| --- | --- |
| `log(a, b, ...)` | Writes the arguments to the console, separated by spaces. Arrays and objects are written as JSON. |
| `return(value)` | Ends the current function and returns `value` |
| `typeof(value)`, `type(value)` | Returns the value's JavaScript type, such as `"number"`, `"string"`, `"boolean"`, or `"object"`. Arrays, objects, and functions you define are all `"object"`. |
| `toNum(value)` | Converts `value` to a whole number, dropping any fraction. Returns `0` if it is not a number. |
| `input(message)` | Asks the user for text with `message` as the prompt, and returns it |

## log

```js
log("Hello, World!")  /* Hello, World! */
log("Value:", 42)     /* Value: 42 */
```

## return

```js
greet = (name)~{
    return(join("Hello, ", name))
}
```

## typeof

```js
typeof("a")  /* "string" */
typeof([1])  /* "object" */
```

## toNum

```js
toNum("42")  /* 42 */
toNum(3.9)   /* 3 */
```
