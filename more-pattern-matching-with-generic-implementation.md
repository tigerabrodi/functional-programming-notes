## Pattern Matching with Handlers Object

### Option Type Pattern Matching

First, let's define the Option type structure:

```typescript
// Option type definition
type Option<A> = Some<A> | None;

// Some variant holds a value
type Some<A> = { tag: "some"; value: A };
function some<A>(value: A): Some<A> {
  return { tag: "some", value };
}

// None variant represents absence of value
type None = { tag: "none" };
const none: None = { tag: "none" };
```

Now, let's create a better match function that takes the option first and then a handlers object:

```typescript
// Improved match function with handlers object
function match<A, B>(
  option: Option<A>,
  handlers: {
    some: (value: A) => B;
    none: () => B;
  }
): B {
  if (option.tag === "none") {
    return handlers.none();
  } else {
    return handlers.some(option.value);
  }
}
```

This API is more intuitive to use:

```typescript
// Create an option with a value
const maybeNum: Option<number> = some(12);

// Pattern match using handlers object
const result = match(maybeNum, {
  none: () => "num does not exist",
  some: (num) => `num is ${num}`,
});

console.log(result); // Output: "num is 12"

// With none value
const emptyOption: Option<number> = none;
const result2 = match(emptyOption, {
  none: () => "num does not exist",
  some: (num) => `num is ${num}`,
});

console.log(result2); // Output: "num does not exist"
```

For supporting different return types (widening):

```typescript
// Match with different return types
function matchW<A, B, C>(
  option: Option<A>,
  handlers: {
    some: (value: A) => B;
    none: () => C;
  }
): B | C {
  if (option.tag === "none") {
    return handlers.none();
  } else {
    return handlers.some(option.value);
  }
}

// Example usage with different return types
const result = matchW(maybeNum, {
  none: () => "num does not exist", // returns string
  some: (num) => -2, // returns number
});
```

### Either Type Pattern Matching

The Either type definition:

```typescript
// Either type definition
type Either<E, A> = Left<E> | Right<A>;

// Left variant typically for errors
type Left<E> = { tag: "left"; value: E };
function left<E>(value: E): Left<E> {
  return { tag: "left", value };
}

// Right variant typically for success
type Right<A> = { tag: "right"; value: A };
function right<A>(value: A): Right<A> {
  return { tag: "right", value };
}
```

Enhanced pattern matching with a handlers object:

```typescript
function match<E, A, B>(
  either: Either<E, A>,
  handlers: {
    left: (error: E) => B;
    right: (value: A) => B;
  }
): B {
  if (either.tag === "left") {
    return handlers.left(either.value);
  } else {
    return handlers.right(either.value);
  }
}
```

Example usage:

```typescript
// Create an Either with a success value
const errorOrNum: Either<string, number> = right(12);

// Pattern match with handlers object
const result = match(errorOrNum, {
  left: (error) => `error happened: ${error}`,
  right: (num) => `num is ${num}`,
});

console.log(result); // Output: "num is 12"

// With error value
const errorCase: Either<string, number> = left("bad input");
const result2 = match(errorCase, {
  left: (error) => `error happened: ${error}`,
  right: (num) => `num is ${num}`,
});

console.log(result2); // Output: "error happened: bad input"
```

### List Type Pattern Matching

The List type definition:

```typescript
// List type definition
type List<A> = Nil | Cons<A>;

// Nil variant represents empty list
type Nil = { tag: "nil" };
const nil: Nil = { tag: "nil" };

// Cons variant represents a list node with head and tail
type Cons<A> = { tag: "cons"; head: A; tail: List<A> };
function cons<A>(head: A, tail: List<A>): Cons<A> {
  return { tag: "cons", head, tail };
}
```

Improved match function with handlers object:

```typescript
function match<A, B>(
  list: List<A>,
  handlers: {
    nil: () => B;
    cons: (head: A, tail: List<A>) => B;
  }
): B {
  if (list.tag === "nil") {
    return handlers.nil();
  } else {
    return handlers.cons(list.head, list.tail);
  }
}
```

Example usage:

```typescript
// Create a list: [1, 2, 3]
const myList: List<number> = cons(1, cons(2, cons(3, nil)));

// Pattern match with handlers object
const result = match(myList, {
  nil: () => "list is empty",
  cons: (head, tail) => `head is ${head}`,
});

console.log(result); // Output: "head is 1"

// With empty list
const emptyList: List<number> = nil;
const result2 = match(emptyList, {
  nil: () => "list is empty",
  cons: (head, tail) => `head is ${head}`,
});

console.log(result2); // Output: "list is empty"
```

## A Generic Pattern Matching Function

We can take this approach even further by creating a generic pattern matcher:

```typescript
// Generic pattern matcher for any tagged union
function matchPattern<T extends { tag: string }, R>(
  value: T,
  handlers: { [K in T["tag"]]: (value: Extract<T, { tag: K }>) => R }
): R {
  const handler = handlers[value.tag as T["tag"]];
  return handler(value as any);
}
```

This allows us to work with any tagged union type in a consistent way:

```typescript
// Using the generic pattern matcher with Option
const maybeNum: Option<number> = some(12);
const result = matchPattern(maybeNum, {
  some: ({ value }) => `num is ${value}`,
  none: () => "num does not exist",
});

// Using with Either
const errorOrNum: Either<string, number> = right(12);
const result2 = matchPattern(errorOrNum, {
  left: ({ value }) => `error happened: ${value}`,
  right: ({ value }) => `num is ${value}`,
});

// Using with List
const myList: List<number> = cons(1, cons(2, cons(3, nil)));
const result3 = matchPattern(myList, {
  nil: () => "list is empty",
  cons: ({ head, tail }) => `head is ${head}`,
});
```

## Custom Implementation of TS-Pattern Style

If you like the TS-Pattern library's chaining API but want to implement it yourself:

```typescript
function match<T>(value: T) {
  return {
    with<R>(pattern: any, handler: (matched: any) => R) {
      // Simple pattern matching for tagged unions
      if (typeof value === "object" && value !== null && "tag" in value) {
        if (pattern.tag === value.tag) {
          return {
            with: this.with,
            exhaustive: () => handler(value),
          };
        }
      }

      return {
        with: this.with,
        exhaustive: this.exhaustive,
      };
    },
    exhaustive<R>(): R {
      throw new Error("No pattern matched");
    },
  };
}
```

Using the custom implementation:

```typescript
// Using custom pattern matcher with chaining
const result = match(myList)
  .with({ tag: "nil" }, () => "list is empty")
  .with({ tag: "cons" }, ({ head, tail }) => `head is ${head}`)
  .exhaustive();

console.log(result);
```

This handlers object approach has several advantages:

1. Cleaner API that puts the value to match first, then the handlers
2. Named handlers make the code more readable and self-documenting
3. Better TypeScript support - object property names help ensure you handle all cases
4. More consistent with idiomatic JavaScript object handling

The pattern matching technique remains the same core concept: examining a value's structure and applying different logic based on which variant it is, while extracting the data contained within it.
