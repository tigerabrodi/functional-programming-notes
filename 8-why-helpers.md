The core issue is that once a value is wrapped in an Option or Either type, you can't work with it directly. You need to:

1. Unwrap it
2. Apply your logic
3. Rewrap it

These helper functions automate this process, making your code cleaner and less error-prone.

Let's see why they're useful with a practical example:

```typescript
// Without helper functions
function calculateDiscount(price: Option<number>): Option<number> {
  if (isNone(price)) {
    return none;
  } else {
    return some(price.value * 0.9); // 10% discount
  }
}

// With map helper
function calculateDiscount(price: Option<number>): Option<number> {
  return map(price, (p) => p * 0.9);
}
```

The benefits are:

1. **Less boilerplate**: The mapping pattern (check-unwrap-transform-rewrap) is so common that having a utility function makes code much cleaner.

2. **Safer transformation**: The helpers handle all the null checks, ensuring you never accidentally try to access a value that doesn't exist.

3. **Composability**: You can chain multiple operations together:

   ```typescript
   // Without helpers - gets very nested and hard to read
   if (isNone(price)) {
     return none;
   } else {
     const discounted = price.value * 0.9;
     if (discounted < 5) {
       return some(5); // Minimum price
     } else {
       return some(Math.round(discounted)); // Round to whole number
     }
   }

   // With helpers - much cleaner
   return map(
     map(price, (p) => p * 0.9),
     (d) => (d < 5 ? 5 : Math.round(d))
   );
   ```

4. **Functional pattern**: This approach follows functional programming principles where you transform data through a series of pure functions rather than directly manipulating values.

As for `getOrElse`, it's useful when you finally need to extract the actual value:

```typescript
// Instead of:
const finalPrice = isNone(discountedPrice)
  ? defaultPrice
  : discountedPrice.value;

// You can write:
const finalPrice = getOrElse(discountedPrice, defaultPrice);
```

These helpers form the foundation for more advanced functional programming patterns, and as you build more complex transformations, their value becomes even more apparent.
