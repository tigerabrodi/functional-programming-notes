# Currying in Functional Programming

Currying is a technique that transforms a function that takes multiple arguments into a sequence of functions that each take a single argument. This aligns with the functional programming principle of working with unary functions (functions that take exactly one input).

## What is Currying?

In functional programming, we prefer functions that take only one input (unary functions). Currying helps us convert multi-parameter functions into a series of unary functions.

```typescript
// Regular function with multiple parameters
function sum(a: number, b: number): number {
  return a + b;
}

// Curried version
function curriedSum(a: number): (b: number) => number {
  return function (b: number): number {
    return a + b;
  };
}

// Or more concisely with arrow functions
const curriedSumArrow =
  (a: number) =>
  (b: number): number =>
    a + b;
```

## How It Works

The curried function:

1. Takes the first parameter
2. Returns a new function that captures that parameter in closure
3. The returned function takes the next parameter
4. This continues until all parameters are collected
5. The final function performs the calculation with all parameters

```typescript
// Using the curried function
const result1 = sum(5, 3); // Regular: 8
const result2 = curriedSum(5)(3); // Curried: 8

// We can also partially apply the function
const add5 = curriedSum(5); // Creates a function that adds 5
console.log(add5(3)); // 8
console.log(add5(10)); // 15
```

## Creating Reusable Function Factories

One of the major benefits of currying is creating specialized functions from more general ones:

```typescript
// Create specialized incrementing functions
const increment = curriedSum(1);
const addTen = curriedSum(10);

console.log(increment(7)); // 8
console.log(addTen(5)); // 15
```

## Currying Functions with More Parameters

Currying works with any number of parameters by nesting functions:

```typescript
// Regular 3-parameter function
const calculateVolume = (
  length: number,
  width: number,
  height: number
): number => length * width * height;

// Curried version
const curriedVolume =
  (length: number) =>
  (width: number) =>
  (height: number): number =>
    length * width * height;

// Using the curried function
console.log(calculateVolume(2, 3, 4)); // 24
console.log(curriedVolume(2)(3)(4)); // 24

// Creating specialized functions
const boxWithLength2 = curriedVolume(2);
const boxWithLength2Width3 = boxWithLength2(3);

console.log(boxWithLength2Width3(4)); // 24
console.log(boxWithLength2(5)(1)); // 10
```

## Generic Curry Function

We can also create a helper function to curry any function automatically:

```typescript
// A simple curry function for 2 parameters
function curry<A, B, C>(fn: (a: A, b: B) => C): (a: A) => (b: B) => C {
  return (a: A) => (b: B) => fn(a, b);
}

// Using our curry helper
const sum = (a: number, b: number): number => a + b;
const curriedSum = curry(sum);

console.log(curriedSum(5)(3)); // 8
```

## Why Curry in Functional Programming?

1. **Composition**: Curried functions are easier to compose with other functions
2. **Partial Application**: Create specialized functions by fixing some parameters
3. **Point-Free Style**: Write functions without explicitly mentioning the data they operate on
4. **Unary functions**: Work with the functional programming principle of unary functions

Currying transforms our approach from "a function that takes multiple inputs" to "a function that takes one input and returns another function," aligning with the mathematical approach of functional programming where each function is a clean, single transformation.
