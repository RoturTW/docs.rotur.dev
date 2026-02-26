# Structure

RTR is a lightweight scripting language with a simple but powerful structure. This document outlines the core components and structure of RTR code.

## Program Structure

An RTR program consists of one or more event blocks. Each event block contains a series of statements that are executed when the event is triggered. Note that newlines have no syntactic meaning in RTR - they are purely for readability.

```js
event (eventName) {
    /* Statements */
}

event (anotherEvent) {
    /* More statements */
}
```

## Statement Types

### 1. Variable Declarations

```go
variable := value;
```

### 2. Function Definitions

```js
functionName = (param1, param2)~{
    /* Function body */
}
```

### 3. Control Structures

```javascript
if (condition) {
    /* Code */
} elif (otherCondition) {
    /* Code */
} else {
    /* Code */
}

while (condition) {
    /* Code */
}

repeat (times) {
    /* Code */
}

for (variable, range) {
    /* Code */
}
```

### 4. Function Calls

```go
result := functionName(arg1, arg2);
```

### 5. Object Operations

```js
obj.property = obj.method(arg1, arg2);
```

## Expression Structure

### 1. Literals

* Numbers: `42`, `3.14`
* Strings: `"Hello"`, `'World'`
* Booleans: `true`, `false`
* Null: `null`
* Arrays: `[1, 2, 3]`
* Objects: `{key: value}`

### 2. Operators

#### Arithmetic: `+`, `-`, `*`, `/`, `%`, `^`

#### Comparison: `==`, `!=`, `>=`, `<=`, `>`, `<`

#### Logical: `!`, `?`

#### Assignment: `=`, `+=`, `-=`, `*=`, `/=`, `^=`, `%=` , `??=`&#x20;

* #### =

Assigns the left hand side to the right hand side, assigning the variable's type to the value's type

```java
myVariable := "hi";
obj.prop = "im a property";
```

* #### :=

Declares a variable with an immutable type (cannot be changed)

```go
variable := "hello";
variable = 5; // errors
```

```go
variable := "bleh";
scope {
    variable := ":3";
    log(variable); // :3
}
log(variable); // bleh
```

* #### +=, -=, \*=, /=, %=, ^=



### 3. Function Calls

```js
functionName(arg1, arg2);
```

### 4. Property Access

```js
object.property;
array[index];
```

## Scope Structure

RTR uses lexical scoping with the following rules:

1. Global Scope: Variables defined outside any event or function
2. Event Scope: Variables defined within an event block
3. Function Scope: Variables defined within a function
4. Block Scope: Variables defined within a `scope` block

```js
/* Global scope */
globalVar = 42;

event (onload) {
    /* Event scope */
    eventVar := "Hello";
    
    function = ()~{
        /* Function scope */
        funcVar := true;
    };
    
    scope {
        /* Block scope */
        blockVar := 123;
    }
}
```

## Event Structure

Events are the primary organizational unit in RTR. They follow this structure:

```js
event (eventName) {
    /* Event initialization */
    /* Variable declarations */
    /* Function definitions */
    /* Main event logic */
}
```

Common events include:

* `onload`: Triggered when the program starts
* `onclick`: Triggered on mouse click
* `onkey`: Triggered on keyboard input
* `ontick`: Triggered on each frame/tick

## Function Structure

Functions in RTR can be defined in two ways:

1. Named Functions:

```js
functionName = (param1, param2)~{
    /* Function body */
    return(value);
}
```

2. Anonymous Functions:

```js
obj.method = (param)~{
    /* Function body */
};
```

## Error Handling

RTR provides basic error handling through the event system:

```js
event (onerror) {
    /* Handle errors */
    log("Error occurred:", error);
}
```

## Best Practices

1. Use meaningful event names
2. Keep functions small and focused
3. Use proper scoping to avoid variable conflicts
4. Use multiline comments for documentation
5. Use built-in functions when available
6. Handle errors appropriately
7. Use proper indentation for readability (though not required)
