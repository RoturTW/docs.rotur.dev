# Basics

This guide covers the fundamental concepts and syntax of the RTR language.

## Getting Started

RTR is a simple but powerful scripting language. Here's a basic example:

```c
event (onload) {
    /* Print a message */
    log("Hello, World!");
    
    /* Create a variable */
    name := "John";
    
    /* Use the variable */
    log(join("Hello, ", name));
}
```

## Variables

Variables in RTR are dynamically typed and can hold any type of value.

```go
/* Numbers */
age := 25;
pi := 3.14159;

/* Strings */
name := "Alice";
greeting := 'Hello';

/* Booleans */
isActive := true;
isDone := false;

/* Arrays */
numbers := [1, 2, 3, 4, 5];
names := ["John", "Jane", "Bob"];

/* Objects */
person := {name: "John", age: 30, isActive: true};
```

## Basic Operations

### Arithmetic Operations

```go
a := 10; b := 3;

sum := a + b;        /* 13 */
difference := a - b; /* 7 */
product := a * b;    /* 30 */
quotient := a / b;   /* 3.333... */
remainder := a % b;  /* 1 */
power := a ^ b;      /* 1000 */
```

### String Operations

```go
firstName := "John"; lastName := "Doe";

/* String concatenation */
fullName := join(firstName, " ", lastName);

/* String length */
nameLength := length(fullName);

/* String splitting */
parts := split(fullName, " ");
```

### Comparison Operations

```go
a := 5; b := 10;

isEqual := a == b;      /* false */
isNotEqual := a != b;   /* true */
isGreater := a > b;     /* false */
isLess := a < b;        /* true */
isGreaterOrEqual := a >= b; /* false */
isLessOrEqual := a <= b;    /* true */
```

### Logical Operations

```go
isTrue := true; isFalse := false;

andResult := all(isTrue, isFalse);  /* false */
orResult := any(isTrue, isFalse);   /* true */
notResult := not(isTrue);           /* false */
```

## Control Flow

### If Statements

```c
age := 18;

if (age >= 18) {
    log("Adult");
} elif (age >= 13) {
    log("Teenager");
} else {
    log("Child");
}
```

### Loops

#### While Loop

```c
count := 0;
while (count < 5) {
    log(count);
    count += 1;
}
```

#### Repeat Loop

```lua
repeat (5) {
    log("Hello");
}
```

#### For Loop

```js
for (i, range(1, 5)) {
    log(i);
}
```

## Functions

### Function Definition

```js
greet = (name)~{
    return(join("Hello, ", name));
}
```

### Function Call

```go
message := greet("John");
log(message);  /* Outputs: Hello, John */
```

### Function with Multiple Parameters

```js
calculate = (a, b, operation)~{
    if (operation == "add") {
        return(a + b);
    } elif (operation == "subtract") {
        return(a - b);
    } elif (operation == "multiply") {
        return(a * b);
    } elif (operation == "divide") {
        return(a / b);
    }
}
```

## Objects and Methods

### Object Creation

```go
person := obj(); person.name = "John"; person.age = 30;
```

```go
person := { name: "John", age: 30 };
```

### Method Definition

```js
person.greet = (name)~{
    return(join("Hello, ", name, "! I am ", this.name));
}
```

### Method Call

```go
message := person.greet("Alice");
log(message);  /* Outputs: Hello, Alice! I am John */
```

## Built-in Functions

### Mathematical Functions

```js
min(5, 3, 8);     /* Returns 3 */
max(5, 3, 8);     /* Returns 8 */
abs(-5);          /* Returns 5 */
round(3.7);       /* Returns 4 */
floor(3.7);       /* Returns 3 */
ceil(3.7);        /* Returns 4 */
sqrt(16);         /* Returns 4 */
```

### String Functions

```js
join("Hello", " ", "World");  /* Returns "Hello World" */
split("Hello World", " ");    /* Returns ["Hello", "World"] */
chr(65);                      /* Returns "A" */
ord("A");                     /* Returns 65 */
```

### Array Functions

```js
numbers = [1, 2, 3, 4, 5];
length(numbers);              /* Returns 5 */
item(numbers, 2);             /* Returns 3 */
range(1, 5);                  /* Returns [1, 2, 3, 4, 5] */
```

### Object Functions

```js
person := { name: "John", age: 30 };
keys(person);                 /* Returns ["name", "age"] */
values(person);               /* Returns ["John", 30] */
has(person, "name");          /* Returns true */
```

## Error Handling

Basic error handling can be done using the `onerror` event:

```js
event (onerror) {
    log("An error occurred:", error);
}
```

## Best Practices

1. Use meaningful variable names
2. Use multiline comments for documentation
3. Break complex operations into smaller functions
4. Use proper indentation for readability (though not required)
5. Handle errors appropriately
6. Use built-in functions when available
7. Keep functions small and focused
8. Use proper scoping
