# Basics

This page covers RTR's syntax: variables, operators, control flow, functions, and objects.

## First program

All code goes inside an event block. The `onload` event runs when the program starts.

```js
event (onload) {
    /* Print a message */
    log("Hello, World!")

    /* Create a variable */
    name = "John"

    /* Use the variable */
    log(join("Hello, ", name))
}
```

End each statement with a new line or a `;`. Comments go between `/*` and `*/`.

## Variables

Assign a variable with `=`. Variables are dynamically typed and can hold any value.

```js
/* Numbers */
age = 25
pi = 3.14159

/* Strings, in double quotes */
name = "Alice"

/* Booleans */
isActive = true
isDone = false

/* Null */
nothing = null

/* Arrays */
numbers = [1, 2, 3, 4, 5]
names = ["John", "Jane", "Bob"]

/* Objects */
person = {"name": "John", "city": "Oslo"}
```

Array and object literals are read as JSON, so object keys must be in double quotes. You can also create an empty object with `obj()` and set its properties one by one.

Reading a variable that has not been set returns `null`.

## Operators

### Arithmetic

```js
a = 10
b = 3

sum = a + b         /* 13 */
difference = a - b  /* 7 */
product = a * b     /* 30 */
quotient = a / b    /* 3.333... */
remainder = a % b   /* 1 */
power = a ^ b       /* 1000 */
```

`%` returns a result with the same sign as the right-hand side, so `-7 % 3` is `2`.

{% hint style="warning" %}
RTR micro has no operator precedence: operators are evaluated from left to right, so `2 + 3 * 4` is `20`. Parentheses do not group parts of an expression. Split a calculation across several variables when the order matters.
{% endhint %}

### Assignment

| Operator | Effect |
| --- | --- |
| `=` | Sets the variable |
| `+=`, `-=`, `*=`, `/=`, `%=`, `^=` | Applies the operator to the current value and the right-hand side |

```js
count = 0
count += 1
```

### Comparison

```js
a = 5
b = 10

log(a == b)  /* false */
log(a != b)  /* true */
log(a > b)   /* false */
log(a < b)   /* true */
log(a >= b)  /* false */
log(a <= b)  /* true */
```

`==` and `!=` compare strictly: `5 == "5"` is `false`.

{% hint style="warning" %}
A line that contains a comparison is never treated as an assignment, so `isLess = a < b` does not set `isLess`. Use comparisons directly in conditions and function arguments.
{% endhint %}

### Logic

Use the [logical functions](functions/logical.md):

```js
andResult = all(true, false)  /* false */
orResult = any(true, false)   /* true */
notResult = not(true)         /* false */
```

## Strings

```js
firstName = "John"
lastName = "Doe"

fullName = join(firstName, " ", lastName)  /* "John Doe" */
nameLength = length(fullName)              /* 8 */
parts = split(fullName, " ")               /* ["John", "Doe"] */
```

## Control flow

### If

```js
age = 18

if (age >= 18) {
    log("Adult")
} elif (age >= 13) {
    log("Teenager")
} else {
    log("Child")
}
```

### While

```js
count = 0
while (count < 5) {
    log(count)
    count += 1
}
```

{% hint style="warning" %}
A `while` loop that runs more than 1,000 times stops with the error `Infinite while loop detected`. Use `repeat` or `for` for longer loops.
{% endhint %}

### Repeat

Runs the block a fixed number of times.

```js
repeat (5) {
    log("Hello")
}
```

### For

Runs the block once for each element of an array, setting the variable to that element.

```js
for (i, range(1, 5)) {
    log(i)
}
```

## Functions

Define a function with `(parameters)~{ body }` and assign it to a variable. Use `return(value)` to return a value.

```js
greet = (name)~{
    return(join("Hello, ", name))
}

message = greet("John")
log(message)  /* Hello, John */
```

```js
multiply = (a, b)~{
    return(a * b)
}

log(multiply(3, 4))  /* 12 */
```

A function can read variables from outside it. Assigning to a variable inside a function creates a local variable and does not change the outer one.

{% hint style="warning" %}
In RTR micro, an `if` block inside a function body fails with an error. `repeat`, `for`, and `while` blocks work.
{% endhint %}

## Objects

Create an empty object with `obj()` and set properties with `.`:

```js
person = obj()
person.name = "John"
person.age = 30
```

A property can hold a function, which you then call as a method:

```js
person.greet = (name)~{
    return(join("Hello, ", name))
}

message = person.greet("Alice")
log(message)  /* Hello, Alice */
```

Methods do not receive `this` in RTR micro. To use the object's own data, read it from the variable, such as `person.name`.

## Built-in functions

See [Functions](functions/README.md) for the full list.

| Category | Functions |
| --- | --- |
| [Math](functions/math.md) | `min`, `max`, `abs`, `round`, `floor`, `ceil`, `sqrt`, `sin`, `cos`, `tan`, `asin`, `acos`, `atan` |
| [String](functions/string.md) | `join`, `split`, `chr`, `ord`, `length`, `toStr` |
| [Array](functions/array.md) | `length`, `item`, `range` |
| [Object](functions/object.md) | `obj`, `keys`, `values`, `has`, `set`, `del` |
| [Logical](functions/logical.md) | `all`, `any`, `not` |
| [Utility](functions/utility.md) | `log`, `return`, `typeof`, `type`, `toNum`, `input` |
