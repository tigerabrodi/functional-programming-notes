# Recursion in Functional Programming

Recursion is a fundamental technique in functional programming that replaces traditional loops. Let's explore how it works with TypeScript examples.

## The Problem with Loops in Functional Programming

In functional programming, we avoid mutations and side effects. Traditional loops typically rely on mutable variables:

```typescript
// Imperative approach with mutation
function sumAll(numbers: number[]): number {
  let result = 0; // Mutable variable
  for (let i = 0; i < numbers.length; i++) {
    result += numbers[i]; // Mutation!
  }
  return result;
}
```

## Solving Problems Recursively

Recursion allows us to solve problems by:

1. Breaking them into smaller, similar problems
2. Solving the smallest case (base case)
3. Combining results to solve the original problem

```typescript
// Recursive approach - no mutation
function sumAll(numbers: number[]): number {
  // Base case: empty array
  if (numbers.length === 0) {
    return 0;
  }

  // Split into head and rest
  const [head, ...rest] = numbers;

  // Recursive case: head + sum of rest
  return head + sumAll(rest);
}
```

## Understanding the Recursive Pattern

Let's trace through an example of `sumAll([1, 2, 3, 4])`:

1. Is the array empty? No, so we continue
2. Split into head (1) and rest ([2, 3, 4])
3. Return head + sumAll(rest) which is 1 + sumAll([2, 3, 4])
4. For sumAll([2, 3, 4]):
   - Is the array empty? No
   - Split into head (2) and rest ([3, 4])
   - Return 2 + sumAll([3, 4])
5. For sumAll([3, 4]):
   - Is the array empty? No
   - Split into head (3) and rest ([4])
   - Return 3 + sumAll([4])
6. For sumAll([4]):
   - Is the array empty? No
   - Split into head (4) and rest ([])
   - Return 4 + sumAll([])
7. For sumAll([]):
   - Is the array empty? Yes
   - Return 0 (base case)
8. Work backwards: 4 + 0 = 4, then 3 + 4 = 7, then 2 + 7 = 9, then 1 + 9 = 10

The final result is 10.

## Base Cases Are Crucial

Every recursive function needs at least one base case to avoid infinite recursion. The base case is the simplest form of the problem that can be solved directly.

For the sum function:

- Base case: Empty array returns 0
- Recursive case: For non-empty arrays, add the first element to the sum of remaining elements

## Recursion and Function Composition

Since recursive functions are still pure functions that map inputs to outputs without side effects, they can be composed like any other function:

```typescript
const isEven = (x: number): boolean => x % 2 === 0;

// Composing functions
const isSumEven = (numbers: number[]): boolean => isEven(sumAll(numbers));

console.log(isSumEven([1, 2, 3, 4])); // true (10 is even)
console.log(isSumEven([1, 2, 3])); // false (6 is even)
```

## Practical Recursion Examples

Here are more examples of recursive functions in TypeScript:

```typescript
// Factorial calculation
function factorial(n: number): number {
  if (n <= 1) return 1; // Base case
  return n * factorial(n - 1); // Recursive case
}

// Array mapping with recursion
function mapRecursive<T, U>(arr: T[], fn: (item: T) => U): U[] {
  if (arr.length === 0) return []; // Base case

  const [head, ...rest] = arr;
  return [fn(head), ...mapRecursive(rest, fn)]; // Recursive case
}

// Filter with recursion
function filterRecursive<T>(arr: T[], predicate: (item: T) => boolean): T[] {
  if (arr.length === 0) return []; // Base case

  const [head, ...rest] = arr;
  const filteredRest = filterRecursive(rest, predicate);

  return predicate(head)
    ? [head, ...filteredRest] // Include head if it passes
    : filteredRest; // Skip head if it fails
}
```

## Performance Considerations

While recursion is elegant, it can be less efficient than loops in some languages due to call stack limitations. Modern functional languages and some JavaScript engines optimize for "tail recursion" - a special form of recursion where the recursive call is the last operation in the function.

```typescript
// Tail-recursive version of sumAll
function sumAllTailRec(numbers: number[], acc: number = 0): number {
  if (numbers.length === 0) {
    return acc;
  }

  const [head, ...rest] = numbers;
  return sumAllTailRec(rest, acc + head);
}
```

In tail recursion, the state is passed through parameters rather than building up the call stack, which can be optimized by the compiler or runtime.

Recursion in functional programming isn't just a replacement for loops - it's a different way of thinking about problem-solving that aligns with the composable, immutable nature of the functional paradigm.
