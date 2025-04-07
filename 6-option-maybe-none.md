# Option Types in Functional Programming

Option types (also called Maybe types) provide a safe, explicit way to handle potentially missing values. Let's explore this powerful concept with TypeScript examples.

## The Problem with Null

Null represents the absence of a value, but traditional implementations cause significant problems:

1. **Type Pollution**: Null can appear in any type without explicit declaration
2. **Runtime Errors**: Null leads to common errors like "Cannot read property of null"
3. **Implicit Handling**: Functions must handle null cases even when it's not their primary concern

## Option Type Pattern

Option types solve these problems by making the possibility of missing values explicit in the type system:

```typescript
// Option type definition
type Option<A> = Some<A> | None;

// Some contains a value
type Some<A> = {
  readonly value: A;
};

// None represents no value
type None = typeof none;

// Helper functions
const some = <A>(x: A): Option<A> => ({
  value: x,
});

const none = Symbol("None");
```

Now you're forced to handle the None case.

Here's an alternative implementation using discriminated unions with tags:

```typescript
// Tagged union implementation
interface Some<A> {
  readonly _tag: "Some";
  readonly value: A;
}

interface None {
  readonly _tag: "None";
}

type Option<A> = Some<A> | None;

// Helper functions
const some = <A>(a: A): Option<A> => ({
  _tag: "Some",
  value: a,
});

const none: Option<never> = {
  _tag: "None",
};
```

## Using Option Types in Practice

Let's see how Option types make code safer by looking at a division example:

```typescript
// Traditional approach - partial function
function unsafeDivide(x: number, y: number): number {
  return x / y; // Dangerous: returns Infinity when y is 0
}

// Using Option types - total function
function safeDivide(x: number, y: number): Option<number> {
  return y === 0 ? none : some(x / y);
}
```

## Type Guards for Option Types

To work effectively with Option types, we need ways to check which variant we have:

```typescript
// Check if an Option is None
function isNone<A>(option: Option<A>): option is None {
  return option === none; // For Symbol implementation
  // Or: return option._tag === 'None';  // For tagged implementation
}

// Check if an Option is Some
function isSome<A>(option: Option<A>): option is Some<A> {
  return !isNone(option);
}
```

## Working with Option Types

Here's how we can compose functions that return Option types:

```typescript
// Function that might fail
function safeDivide(x: number, y: number): Option<number> {
  return y === 0 ? none : some(x / y);
}

// Function that expects a regular number
function increment(x: number): number {
  return x + 1;
}

// Combining them manually (we'll see cleaner ways later with Functors)
function divideAndIncrement(x: number, y: number): Option<number> {
  const divisionResult = safeDivide(x, y);

  if (isNone(divisionResult)) {
    return none;
  }

  return some(increment(divisionResult.value));
}
```

## Utility Functions for Option Types

To make working with Option types easier, we can create helper functions:

```typescript
// Apply a function to a Some value, or return None
function map<A, B>(option: Option<A>, fn: (a: A) => B): Option<B> {
  return isNone(option) ? none : some(fn(option.value));
}

// Get the value or a default
function getOrElse<A>(option: Option<A>, defaultValue: A): A {
  return isNone(option) ? defaultValue : option.value;
}

// Example usage
const result = safeDivide(10, 2);
const incrementedResult = map(result, (x) => x + 1);
console.log(getOrElse(incrementedResult, 0)); // 6

const failedResult = safeDivide(10, 0);
console.log(getOrElse(failedResult, 0)); // 0
```

## Benefits of Option Types

1. **Explicit API**: Functions clearly indicate when they might not return a value
2. **Compiler Assistance**: Type checking forces you to handle both Some and None cases
3. **No Surprises**: No unexpected null errors at runtime
4. **Composable**: Option types can be composed with other functional patterns

Option types transform the problem of null values from an implicit, error-prone situation into an explicit, type-safe pattern. Later, when we learn about Functors, we'll see even more elegant ways to work with Option types in a functional style.
