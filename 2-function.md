# Functions in Functional Programming

In functional programming, functions are pure transformations that take inputs and produce outputs without side effects. Let's explore the key concepts with TypeScript examples.

## Functions as Transformations

Functions work like pipes that transform inputs to outputs:

```typescript
// Increment function: Transforms a number by adding 1
const increment = (x: number): number => x + 1;

// ToString function: Transforms a number to its string representation
const toString = (x: number): string => x.toString();

// IsEven function: Transforms a number to a boolean
const isEven = (x: number): boolean => x % 2 === 0;
```

## Key Properties of Functions in Functional Programming

### 1. Deterministic

Functions must return the same output for the same input every time:

```typescript
// Deterministic: Always returns the same result for the same input
const add = (a: number, b: number): number => a + b;

// Non-deterministic (avoid in FP): Returns different results for same input
const randomMultiplier = (x: number): number => x * Math.random();
```

### 2. Total Functions

Functions must be defined for all values in their input domain:

```typescript
// Not total (bad): Division is undefined when y is 0
const unsafeDivide = (x: number, y: number): number => x / y;

// Total (good): Handles all possible inputs
const safeDivide = (x: number, y: number): number | null =>
  y === 0 ? null : x / y;
```

### 3. No Side Effects

Functions shouldn't change anything outside their scope:

```typescript
// Has side effects (bad)
const printAndReturn = (x: number): number => {
  console.log(x); // Side effect: Writes to console
  return x;
};

// Pure function (good)
const double = (x: number): number => x * 2;
```

### 4. Immutability

Never mutate data, create new transformed values instead:

```typescript
// Mutates data (bad)
const addToArray = (arr: number[], value: number): number[] => {
  arr.push(value);
  return arr;
};

// Immutable approach (good)
const addToArrayImmutably = (arr: number[], value: number): number[] => [
  ...arr,
  value,
];
```

## Referential Transparency

A function call can be replaced with its result without changing program behavior:

```typescript
// Example of referential transparency
const multiply = (a: number, b: number): number => a * b;

// These two expressions are equivalent
const result1 = multiply(5, 3) + multiply(5, 3); // Function calls
const result2 = 15 + 15; // Direct results
```

This is powerful for:

- Testing (predictable outcomes)
- Optimization (caching results)
- Parallel execution (no dependencies between functions)
- Code reasoning (simpler mental model)

With these principles, functional programming creates a clean mapping between input and output domains, making code more predictable and easier to understand.
