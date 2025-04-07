## Product Types - The "AND" Types

A product type is where you combine different values and need ALL of them together. Think of them as "this AND that AND that other thing."

```typescript
// This is a product type
interface Person {
  name: string; // Need this AND
  age: number; // Need this
}
```

It's called a "product" type because mathematically, the number of possible values multiplies. If you have 100 possible names and 120 possible ages, you'd have 100 × 120 = 12,000 possible Person values.

Real-world examples:

- Address (street AND city AND zipcode)
- User profile (username AND email AND password)
- Car (make AND model AND year)

## Sum Types - The "OR" Types

A sum type represents alternatives - a value can be ONE of several possibilities. Think of them as "this OR that OR that other thing."

```typescript
// This is a sum type
type PaymentMethod =
  | { type: "creditCard"; cardNumber: string; expiry: string }
  | { type: "paypal"; email: string }
  | { type: "cash" };
```

It's called a "sum" type because the number of possible values adds up. If you have 1 million possible credit card configurations, 10,000 possible PayPal emails, and 1 cash option, you have 1,000,000 + 10,000 + 1 = 1,010,001 possible payment methods.

Real-world examples:

- Result (success OR failure)
- UserStatus (active OR suspended OR deleted)
- Shape (circle OR rectangle OR triangle)

## Why This Matters

Understanding product vs. sum types helps you model your data appropriately:

1. When you need to represent something with multiple properties that all exist together, use a product type (object/interface/class)

2. When you need to represent different possible states or variants where only one applies at a time, use a sum type (union type with discriminators)

The math part (cardinality) may seem academic, but it helps reason about your types' completeness and the possible states your program can be in.
