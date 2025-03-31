# Function Composition in Functional Programming

Function composition is a fundamental technique in functional programming that allows us to combine multiple functions into a new function. Let's explore this concept with clear TypeScript examples.

## What is Function Composition?

Function composition means creating a new function by combining existing functions, where the output of one function becomes the input to the next.

```typescript
// Our basic functions
const increment = (x: number): number => x + 1;
const toString = (x: number): string => x.toString();

// Manual composition - applying functions in sequence
const incrementThenToString = (x: number): string => {
  const incremented = increment(x);
  return toString(incremented);
};

// Usage
console.log(incrementThenToString(5)); // "6"
```

## Creating a Generic Compose Function

Instead of manually creating composite functions each time, we can build a reusable `compose` function:

```typescript
// Simple compose function for two functions
function compose<A, B, C>(g: (b: B) => C, f: (a: A) => B): (a: A) => C {
  return (x: A): C => g(f(x));
}
```

Let's break down what this function does:

1. It takes two functions as parameters:

   - `f`: A function that takes type A and returns type B
   - `g`: A function that takes type B and returns type C

2. It returns a new function that:
   - Takes an input of type A
   - Applies function `f` to this input (producing a value of type B)
   - Then applies function `g` to that result (producing a value of type C)
   - Returns the final value of type C

```typescript
// Using our compose function
const incrementThenToString = compose(toString, increment);

console.log(incrementThenToString(5)); // "6"
```

Notice the parameter order in `compose`: the function that runs second (`toString`) comes first in the arguments list. This follows mathematical notation for function composition (g ∘ f).

## Composing Multiple Functions

To combine more than two functions, we can create a more flexible compose function:

```typescript
// A compose function that handles any number of functions
function composeMany<T>(...fns: Array<(arg: T) => T>): (arg: T) => T {
  return (x: T): T => fns.reduceRight((acc, fn) => fn(acc), x);
}
```

This version might look intimidating, so let's break it down step by step:

1. `...fns` collects all functions passed as arguments into an array
2. The returned function takes an input `x`
3. We use `reduceRight` to process the array of functions from right to left
   - We start with the initial value `x`
   - For each function, we apply it to our accumulated result

### Why `reduceRight` Instead of `reduce`?

We use `reduceRight` (instead of regular `reduce`) because:

- Function composition traditionally applies functions from right to left
- In math notation, (g ∘ f)(x) means "apply f first, then g"
- `reduceRight` processes the array from the last element to the first

Let's trace through an example:

```typescript
const addOne = (x: number) => x + 1;
const multiplyByTwo = (x: number) => x * 2;
const subtractThree = (x: number) => x - 3;

const composed = composeMany(subtractThree, multiplyByTwo, addOne);
const result = composed(5);
```

When we call `composed(5)`, here's what happens:

1. Start with value 5
2. Apply `addOne` (the rightmost function): 5 + 1 = 6
3. Apply `multiplyByTwo` to that result: 6 \* 2 = 12
4. Apply `subtractThree` to that result: 12 - 3 = 9
5. Final result: 9

This execution flow matches the mathematical expression: (subtractThree ∘ multiplyByTwo ∘ addOne)(5)

## Referential Transparency in Action

Function composition works reliably because of referential transparency - the ability to replace a function call with its result without changing program behavior:

```typescript
// These expressions are all equivalent:
const result1 = incrementThenToString(5);
const result2 = toString(increment(5));
const result3 = toString(6);
const result4 = "6";
```

## Practical Example: Data Transformation Pipeline

Here's a real-world example showing how function composition creates clean data transformation pipelines:

```typescript
interface User {
  id: number;
  name: string;
  age: number;
  email: string;
}

// Individual transformation functions
const hideEmail = (user: User): User => ({
  ...user,
  email: user.email.split("@")[0] + "@...",
});

const calculateCategory = (user: User): User & { category: string } => ({
  ...user,
  category: user.age < 18 ? "junior" : user.age < 65 ? "adult" : "senior",
});

const formatForDisplay = (user: User & { category: string }): string =>
  `${user.name} (${user.category}): ${user.email}`;

// Using compose to create a pipeline
const prepareUserForDisplay = compose(
  formatForDisplay,
  calculateCategory,
  hideEmail
);

// Using the pipeline
const user = { id: 1, name: "Alice", age: 28, email: "alice@example.com" };
console.log(prepareUserForDisplay(user)); // "Alice (adult): alice@..."
```

Function composition allows you to build complex operations from simple ones, making your code more declarative, modular, and easier to understand. Each function handles one specific transformation, and composition chains them together in a clear, predictable way.
