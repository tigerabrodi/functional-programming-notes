### Currying

Currying transforms a function that takes multiple arguments into a sequence of functions that each take a single argument. This creates a chain of partially-applied functions.

```typescript
// Regular function (non-curried)
function add(a, b) {
  return a + b;
}
add(2, 3); // 5

// Curried version
const curriedAdd = curry2(add);
curriedAdd(2)(3); // 5
```

The benefit is that you can partially apply arguments:

```typescript
const add2 = curriedAdd(2); // Creates a new function that adds 2 to anything
add2(3); // 5
add2(10); // 12
```

### Uncurrying

Uncurrying does the opposite - it transforms a sequence of single-argument functions back into a function that takes multiple arguments at once.

```typescript
const curriedMultiply = (a) => (b) => a * b;
const multiply = uncurry2(curriedMultiply);
multiply(4, 5); // 20
```

### Partial Application

Partial application is similar to currying but fixes a specific number of arguments, rather than transforming to a chain of single-argument functions.

```typescript
function divide(a, b) {
  return a / b;
}
const divideFrom10 = partial(divide, 10);
divideFrom10(2); // 5 (10/2)
```

For functions with more arguments, `partial3_1` and `partial3_2` let you fix the first one or two arguments respectively.

### Purpose in Functional Programming

1. **Composition** - Curried functions are easier to compose with other functions
2. **Reusability** - Create specialized functions from general ones
3. **Readability** - In complex transformations, can make code more readable
4. **Point-free programming** - Enables writing functions without mentioning their arguments

For example, with currying you can easily compose transformations:

```typescript
const numbers = [1, 2, 3, 4];

// With currying
const double = (x) => x * 2;
const isEven = (x) => x % 2 === 0;
const sum = (a, b) => a + b;

const sumOfDoubledEvens = numbers.filter(isEven).map(double).reduce(sum, 0);
```

The `curry2`, `curry3`, and `curry4` functions in your code provide convenient ways to transform regular functions into curried ones with 2, 3, or 4 arguments respectively. The `uncurry` functions do the reverse operation when needed.

Functional libraries often provide these helpers to make it easier to switch between curried and uncurried forms as needed.
