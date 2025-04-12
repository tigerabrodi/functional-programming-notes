## What Are Higher-Order Functions?

A higher-order function is simply a function that either:

1. Takes one or more functions as arguments
2. Returns a function as its result
3. Or both of the above

This might sound abstract, but you've almost certainly used them before, especially if you've worked with arrays in JavaScript.

## Practical Examples You Already Know

### Array Methods

The most common higher-order functions you've probably used are array methods:

```javascript
// filter is a higher-order function because it takes a function as an argument
const numbers = [1, 2, 3, 4, 5];
const evenNumbers = numbers.filter((num) => num % 2 === 0);
// Result: [2, 4]
```

Other common array methods that are higher-order functions:

- `map` - transforms each element
- `reduce` - combines elements
- `sort` - orders elements
- `forEach` - performs an action for each element

## Why Higher-Order Functions Matter

Higher-order functions are powerful because they:

1. **Enable Code Reuse**: Create general-purpose functions that can be specialized
2. **Improve Readability**: Express intent more clearly
3. **Support Composition**: Build complex behavior from simple pieces
4. **Facilitate Abstraction**: Hide implementation details

## Function Currying and Partial Application

The author discusses redesigning the `filter` function to take the predicate function first:

```typescript
// Traditional approach
function filter<A>(array: A[], predicate: (a: A) => boolean): A[] {
  return array.filter(predicate);
}

// Curried approach
function filter<A>(predicate: (a: A) => boolean): (array: A[]) => A[] {
  return (array) => array.filter(predicate);
}
```

This curried version enables partial application, which is extremely useful:

```typescript
// Create a specialized filter once
const getPositiveNumbers = filter((n: number) => n > 0);

// Use it many times
const positiveFromList1 = getPositiveNumbers([1, -2, 3, -4]); // [1, 3]
const positiveFromList2 = getPositiveNumbers([-5, -6, 7, 8]); // [7, 8]
```

## Parameter Order Matters

The author makes an important point about parameter order in higher-order functions:

> "When you're defining higher-order functions, it's typically a good idea to put the data input parameters at the end of your parameter list."

This is because:

1. It makes partial application more intuitive
2. The function parameters typically define _how_ something is done
3. The data parameters define _what_ it's done to

This ordering makes creating reusable specialized functions easier.

## Lambda Functions (Arrow Functions)

The author mentions lambda functions (arrow functions in JavaScript/TypeScript) as being particularly well-suited for higher-order functions because:

1. They have concise syntax
2. They're easy to pass around
3. They make the intent clear

```typescript
// The traditional way
function isPositive(n) {
  return n > 0;
}
const positiveNumbers = numbers.filter(isPositive);

// With lambda
const positiveNumbers = numbers.filter((n) => n > 0);
```

## Practical Example: Building Utility Functions

Let's see how higher-order functions enable you to build a library of useful utilities:

```typescript
// Our curried filter
const filter =
  <A>(predicate: (a: A) => boolean) =>
  (array: A[]): A[] =>
    array.filter(predicate);

// Create specialized filters
const positive = filter((n: number) => n > 0);
const even = filter((n: number) => n % 2 === 0);
const nonEmpty = filter((s: string) => s.length > 0);

// Use them
positive([1, -2, 3, -4]); // [1, 3]
even([1, 2, 3, 4]); // [2, 4]
nonEmpty(["", "hello", "world", ""]); // ["hello", "world"]
```

This approach lets you create a vocabulary of operations that makes your code more expressive and maintainable.

## Key Takeaway

Higher-order functions allow you to abstract patterns of behavior, not just data. They're a cornerstone of functional programming because they let you think about _transformations_ and _operations_ as things you can combine and reuse.

When you learn to use higher-order functions effectively, you find yourself writing less code that's more expressive and flexible.
