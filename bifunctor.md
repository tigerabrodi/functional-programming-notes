# What is a Bifunctor?

A bifunctor is like a functor, but it allows mapping over two type parameters instead of just one. While a functor has a single map function, a bifunctor has two mapping functions:

- **mapLeft** or **first**: Maps over the first type parameter
- **mapRight** or **second**: Maps over the second type parameter (this is typically equivalent to the regular map for functors)

## Common Example: Either

The most common example of a bifunctor is the Either type:

```typescript
type Either<E, A> = Left<E> | Right<A>;
type Left<E> = { tag: "left"; value: E };
type Right<A> = { tag: "right"; value: A };

const left = <E>(value: E): Either<E, never> => ({ tag: "left", value });
const right = <A>(value: A): Either<never, A> => ({ tag: "right", value });
```

With Either, we can map over either the error type (E) or the success type (A):

```typescript
// Map only the success (right) value - this is the standard functor map
const mapRight = <E, A, B>(
  f: (a: A) => B,
  either: Either<E, A>
): Either<E, B> => {
  if (either.tag === "left") return either;
  return right(f(either.value));
};

// Map only the error (left) value
const mapLeft = <E, A, F>(
  f: (e: E) => F,
  either: Either<E, A>
): Either<F, A> => {
  if (either.tag === "left") return left(f(either.value));
  return either;
};

// Bimap maps both sides at once
const bimap = <E, A, F, B>(
  f: (e: E) => F,
  g: (a: A) => B,
  either: Either<E, A>
): Either<F, B> => {
  if (either.tag === "left") return left(f(either.value));
  return right(g(either.value));
};
```

## Other Bifunctor Examples

Besides Either, there are other types that can implement the bifunctor interface:

- **Tuple/Pair**: A simple pair of values [A, B]
- **Result/Validation**: Similar to Either but focused on validation results
- **These**: A type that can have both values present, one value, or the other

## Practical Benefits of Bifunctors

Bifunctors are especially useful for:

- **Error Handling**: Transform both error and success values without unwrapping
- **Data Validation**: Process both valid and invalid data in a type-safe way
- **Pair Processing**: Transform both elements of a pair independently

## Example: Error Handling with Bifunctor

```typescript
type ApiError = { code: number; message: string };
type User = { id: number; name: string };

// Initial result from API
const result: Either<ApiError, User> = left({
  code: 404,
  message: "User not found",
});

// Transform the error to a user-friendly message
const userFriendly = mapLeft(
  (error: ApiError) => `Error ${error.code}: ${error.message}`,
  result
);
// userFriendly: Either<string, User>

// In one step with bimap
const processed = bimap(
  (error: ApiError) => `Error ${error.code}: ${error.message}`,
  (user: User) => user.name,
  result
);
// processed: Either<string, string>
```

Bifunctors give you the flexibility to transform both sides of a two-parameter type, extending the power of functors to more complex data structures.
