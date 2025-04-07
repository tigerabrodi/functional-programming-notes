# Algebraic Data Types (ADTs) and Pattern Matching

Algebraic Data Types (ADTs) are a way to compose types in functional programming, creating complex data structures from simpler ones. Let's explore how they work and how to use pattern matching to work with them in TypeScript.

## Types of Composite Types

### 1. Product Types

Product types combine multiple types where a value contains all components simultaneously.

```typescript
// Product type using an interface
interface User {
  name: string;
  age: number;
  address: Address;
}

// Product type using a tuple
type UserTuple = [string, number, Address];
```

The cardinality (number of possible values) of a product type is the product of the cardinalities of its component types. For example, if we have:

- `name` with 2^16 possible string values
- `age` with 100 possible values
- `address` with 1000 possible values

Then our `User` type has 2^16 × 100 × 1000 possible values.

### 2. Sum Types

Sum types represent variants or alternatives - a value is exactly one of the possible options.

```typescript
// Sum type of number OR boolean
type NumOrBool = number | boolean;

// More complex sum type with tagged variants
type Shape =
  | { type: "circle"; radius: number }
  | { type: "rectangle"; width: number; height: number }
  | { type: "triangle"; base: number; height: number };
```

The cardinality of a sum type is the sum of the cardinalities of its component types. If there are 2^53 possible number values and 2 possible boolean values, then `NumOrBool` has 2^53 + 2 possible values.

## Algebraic Data Types (ADTs)

ADTs are composite types created using products and sums. The types we've seen previously (Option, Either, List) are all examples of ADTs:

```typescript
// Option is a sum type
type Option<A> = Some<A> | None;

// Some is a product type
interface Some<A> {
  readonly _tag: "Some";
  readonly value: A;
}

// None is a simple tagged type
interface None {
  readonly _tag: "None";
}
```

## Pattern Matching

Pattern matching is how we extract and work with values from ADTs. It involves:

1. Identifying which variant of a sum type we have
2. Extracting the contained data
3. Processing that data to produce a result

In functional languages, pattern matching is built into the language. In TypeScript, we can simulate it using discriminated unions and conditional logic:

```typescript
// Pattern matching on Option type
function toString<A>(option: Option<A>): string {
  // Check which variant we have
  if (option._tag === "None") {
    // None case
    return "None";
  } else {
    // Some case - extract and transform the value
    return `Some(${option.value})`;
  }
}
```

For more complex cases, we can build helper functions:

```typescript
// Pattern matching helper for Option
function match<A, B>(
  option: Option<A>,
  patterns: {
    None: () => B;
    Some: (value: A) => B;
  }
): B {
  if (option._tag === "None") {
    return patterns.None();
  } else {
    return patterns.Some(option.value);
  }
}

// Using the match helper
const result = match(some(42), {
  None: () => "Nothing here",
  Some: (value) => `Got value: ${value}`,
});
```

## Exhaustiveness Checking

One crucial aspect of pattern matching is that it should be exhaustive - handling all possible variants. TypeScript can help ensure this:

```typescript
// Using never to enforce exhaustiveness
function processShape(shape: Shape): number {
  switch (shape.type) {
    case "circle":
      return Math.PI * shape.radius * shape.radius;
    case "rectangle":
      return shape.width * shape.height;
    case "triangle":
      return 0.5 * shape.base * shape.height;
    default:
      // This ensures we've handled all cases
      const exhaustiveCheck: never = shape;
      return exhaustiveCheck;
  }
}
```

If you add a new variant to `Shape` but forget to update `processShape`, TypeScript will give you a compile-time error.

## Benefits of ADTs and Pattern Matching

1. **Type Safety**: The compiler ensures you handle all cases
2. **Expressiveness**: Complex data structures can be modeled clearly
3. **Composability**: Build complex types from simple ones
4. **Maintainability**: When you change a type, the compiler helps you update all the code that uses it

ADTs and pattern matching form a powerful combination for modeling domains in a type-safe, maintainable way. They're particularly valuable for representing states, events, messages, and other concepts where you need to enumerate all possibilities explicitly.
