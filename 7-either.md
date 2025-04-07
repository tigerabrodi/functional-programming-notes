# Understanding Either Type in Functional Programming

The Either type is another important pattern in functional programming that builds on the concept of Option types. Let's explore how it works and why it's useful.

## The Problem: Option Lacks Error Details

While Option types solve the problem of representing missing values, they have a limitation - they don't tell you _why_ a value is missing. When a function returns `none`, you only know something failed, but not what went wrong.

## Either: Adding Context to Failures

The Either type addresses this limitation by providing two possibilities:

- `Right`: Contains a successful value (similar to `Some`)
- `Left`: Contains an error or failure information

## Implementing Either in TypeScript

```typescript
// Either type definition
type Either<E, A> = Left<E> | Right<A>;

// Left represents an error or failure
interface Left<E> {
  readonly _tag: "Left";
  readonly value: E;
}

// Right represents a success
interface Right<A> {
  readonly _tag: "Right";
  readonly value: A;
}

// Helper functions
const right = <A, E = never>(a: A): Either<E, A> => ({
  _tag: "Right",
  value: a,
});

const left = <E, A = never>(e: E): Either<E, A> => ({
  _tag: "Left",
  value: e,
});
```

The type parameter `E` represents the error type, while `A` represents the success type.

## Practical Example: Division with Error Messages

Let's see how Either improves error handling compared to Option:

```typescript
// Using Option (limited error context)
function divideByIfEvenOption(num: number): Option<number> {
  if (num === 0) return none;
  if (num % 2 !== 0) return none;
  return some(2 / num);
}

// Using Either (with detailed error information)
function divideByIfEven(num: number): Either<string, number> {
  if (num === 0) {
    return left("Cannot divide by zero");
  }

  if (num % 2 !== 0) {
    return left(`${num} is not even`);
  }

  return right(2 / num);
}
```

Now when the function fails, we know exactly why:

```typescript
const result = divideByIfEven(3);

if (result._tag === "Right") {
  console.log(`Result: ${result.value}`);
} else {
  console.log(`Error: ${result.value}`); // "Error: 3 is not even"
}
```

## Type Guards for Either

As with Option, we can create type guards to work with Either:

```typescript
function isLeft<E, A>(either: Either<E, A>): either is Left<E> {
  return either._tag === "Left";
}

function isRight<E, A>(either: Either<E, A>): either is Right<A> {
  return either._tag === "Right";
}
```

## Understanding the `never` Type

You might have noticed `never` in our helper functions:

```typescript
const right = <A, E = never>(a: A): Either<E, A> => ...
```

The `never` type in TypeScript represents a type that cannot exist - it's the empty set of values. We use it as a placeholder when:

1. We need to specify a type parameter that won't be used
2. We want to indicate that a particular case can't happen

In our `right` function, `E` is set to `never` by default because when creating a `Right`, we don't need an error type.

## Either vs Option

- **Option**: Use when you only need to indicate presence/absence of a value
- **Either**: Use when you need to provide information about why something failed

## Working with Either Values

Here's how you can work with Either values:

```typescript
// Mapping over a Right value (similar to Option)
function map<E, A, B>(either: Either<E, A>, fn: (a: A) => B): Either<E, B> {
  return isRight(either) ? right(fn(either.value)) : either;
}

// Getting a value or a default
function getOrElse<E, A>(either: Either<E, A>, defaultValue: A): A {
  return isRight(either) ? either.value : defaultValue;
}

// Transform the error
function mapLeft<E, F, A>(either: Either<E, A>, fn: (e: E) => F): Either<F, A> {
  return isLeft(either) ? left(fn(either.value)) : either;
}
```

Either types provide a robust way to handle operations that might fail while maintaining detailed error information. They're particularly useful for validation, parsing, and other operations where knowing why something failed is as important as knowing that it failed.
