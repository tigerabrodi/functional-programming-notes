## Helper Functions Explained

### `fromNullable`

This function creates an `Option` type from a value that might be null or undefined:

```typescript
function fromNullable<A>(value: A | null | undefined): Option<A> {
  return value === null || value === undefined ? none : some(value);
}
```

This is useful for converting potentially null values from external sources (like APIs) into the Option type, which is more predictable to work with.

### `getOrElse`

This function extracts the value from an Option, falling back to a default value if it's None:

```typescript
interface Option<A> {
  // Other methods...
  getOrElse: (defaultValue: A) => A;
}

// Implementation for Some
const some = <A>(value: A): Option<A> => ({
  // Other methods...
  getOrElse: (defaultValue) => value,
});

// Implementation for None
const none: Option<never> = {
  // Other methods...
  getOrElse: (defaultValue) => defaultValue,
};
```

It's basically a safe way to extract the value, providing a fallback when needed.

### `mapLeft`

This method is specific to the `Either` type. While regular `map` transforms the value in the `Right` variant, `mapLeft` transforms the error in the `Left` variant:

```typescript
interface Either<E, A> {
  map: <B>(f: (a: A) => B) => Either<E, B>;
  mapLeft: <F>(f: (e: E) => F) => Either<F, A>;
}

// Implementation for Left
const left = <E>(error: E): Either<E, never> => ({
  map: (f) => left(error),
  mapLeft: (f) => left(f(error)),
});

// Implementation for Right
const right = <A>(value: A): Either<never, A> => ({
  map: (f) => right(f(value)),
  mapLeft: (f) => right(value),
});
```

This is useful for transforming or standardizing error values.

## Breaking Down the Final Example

Let's go through the example step by step:

```typescript
const loadUserProfile = (userId: string): Task<Either<Error, string>> => {
  return fetchJson(`/api/users/${userId}`)
    .map((userData) =>
      fromNullable(userData)
        .map((user) => fromNullable(user.name).getOrElse("UNKNOWN"))
        .map((name) => name.toUpperCase())
        .map((name) => `Welcome back, ${name}!`)
        .getOrElse("No user found")
    )
    .mapLeft((error) => new Error("Error loading profile"));
};
```

Here's what's happening:

1. `fetchJson(`/api/users/${userId}`)` - This makes an API request to fetch user data

   - It returns a `Task<Either<HttpError, UserData>>` (a wrapper for an asynchronous operation that might fail)

2. `.map(userData => ...)` - We transform the successful response (if there is one)

   - This only runs if the HTTP request was successful

3. `fromNullable(userData)` - Convert the response to an Option

   - Handles the case where the API returns null or undefined

4. `.map(user => fromNullable(user.name).getOrElse('UNKNOWN'))`

   - Check if the user has a name property
   - If there's no name, use 'UNKNOWN' instead

5. `.map(name => name.toUpperCase())`

   - Convert the name to uppercase

6. `.map(name => `Welcome back, ${name}!`)`

   - Format the greeting message

7. `.getOrElse('No user found')`

   - If at any point we had None (no user data), return 'No user found'
   - Otherwise, return the greeting we built

8. `.mapLeft(error => new Error('Error loading profile'))`
   - If the HTTP request failed, standardize the error message

The beauty of this approach is that:

- The core logic (formatting the name and greeting) is completely separated from error handling
- Each processing step is clearly defined and can be tested independently
- We're handling all possible edge cases systematically (API errors, missing user, missing name)

## Comparing with Traditional Approach

Let's compare with the traditional approach:

```typescript
// Traditional approach with try/catch
const loadUserProfile = async (userId: string) => {
  try {
    // Handle HTTP errors
    const response = await fetch(`/api/users/${userId}`);
    if (!response.ok) throw new Error("Failed to fetch");

    // Handle missing data
    const userData = await response.json();
    if (!userData) return "No user found";

    // Handle missing name
    const formattedName = userData.name
      ? userData.name.toUpperCase()
      : "UNKNOWN";
    const greeting = `Welcome back, ${formattedName}!`;

    return greeting;
  } catch (error) {
    console.error(error);
    return "Error loading profile";
  }
};
```

The traditional approach:

- Mixes error handling with business logic
- Has manual null checking throughout
- Makes it harder to see the main "happy path" logic
- Is harder to test because the logic is more tightly coupled

The functor-based approach keeps these concerns separate, leading to more maintainable code.
