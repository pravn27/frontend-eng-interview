# 03 - Basic Types

## 📖 TypeScript Primitive Types

TypeScript provides type annotations for JavaScript's primitive types plus additional types for better type safety.

```typescript
// The basic primitive types
let name: string = "Alice";
let age: number = 30;
let isActive: boolean = true;
let nothing: null = null;
let notDefined: undefined = undefined;
```

---

## 🎯 String

Represents textual data.

```typescript
// String type
let firstName: string = "John";
let lastName: string = "Doe";

// Template literals work
let fullName: string = `${firstName} ${lastName}`;

// String methods are typed
let upperName: string = firstName.toUpperCase();
let nameLength: number = firstName.length;

// ❌ Error: Type 'number' is not assignable to type 'string'
let badString: string = 42;
```

---

## 🔢 Number

Represents all numbers (integers and floating-point).

```typescript
// Number type
let integer: number = 42;
let float: number = 3.14;
let negative: number = -10;

// Special numeric values
let infinity: number = Infinity;
let notANumber: number = NaN;

// Numeric separators (ES2021)
let million: number = 1_000_000;
let binary: number = 0b1010;
let hex: number = 0xff;
let octal: number = 0o744;

// ❌ Error: Type 'string' is not assignable to type 'number'
let badNumber: number = "42";
```

### BigInt

For integers larger than `Number.MAX_SAFE_INTEGER`:

```typescript
// BigInt type (ES2020+)
let bigNumber: bigint = 9007199254740991n;
let anotherBig: bigint = BigInt(9007199254740991);

// ❌ Cannot mix bigint and number
let sum = bigNumber + 1; // Error: can't add bigint and number
let sumCorrect = bigNumber + 1n; // ✅ OK
```

---

## ✅ Boolean

Represents true/false values.

```typescript
// Boolean type
let isComplete: boolean = false;
let hasPermission: boolean = true;

// From expressions
let isAdult: boolean = age >= 18;
let isValid: boolean = name.length > 0;

// ❌ Error: Type 'number' is not assignable to type 'boolean'
let badBoolean: boolean = 1;

// ❌ Error: Type 'string' is not assignable to type 'boolean'
let anotherBad: boolean = "true";
```

---

## 🚫 Null and Undefined

TypeScript distinguishes between `null` and `undefined`.

```typescript
// Null - intentional absence of value
let empty: null = null;

// Undefined - variable declared but not assigned
let notAssigned: undefined = undefined;

// With strictNullChecks enabled (recommended):
let name: string = null; // ❌ Error
let age: number = undefined; // ❌ Error

// Use union types to allow null/undefined
let maybeName: string | null = null;
let maybeAge: number | undefined = undefined;
```

### Optional Values

```typescript
// Optional parameter
function greet(name?: string) {
  // name is string | undefined
  console.log(`Hello, ${name ?? "World"}!`);
}

// Optional property
interface User {
  name: string;
  age?: number; // number | undefined
}
```

---

## 🔣 Symbol

Unique, immutable identifiers.

```typescript
// Symbol type
const id: symbol = Symbol("id");
const anotherId: symbol = Symbol("id");

console.log(id === anotherId); // false - each Symbol is unique

// Unique symbol (const only)
const uniqueKey: unique symbol = Symbol("key");

// Use as object keys
const obj = {
  [id]: "value",
};
```

---

## 📊 Type Comparison

| Type        | Example               | Notes              |
| ----------- | --------------------- | ------------------ |
| `string`    | `"hello"`, `'world'`  | Textual data       |
| `number`    | `42`, `3.14`, `NaN`   | All numeric values |
| `bigint`    | `100n`, `BigInt(100)` | Large integers     |
| `boolean`   | `true`, `false`       | Logical values     |
| `null`      | `null`                | Intentional empty  |
| `undefined` | `undefined`           | Uninitialized      |
| `symbol`    | `Symbol("key")`       | Unique identifiers |

---

## 💻 Practical Examples

### 1. User Profile

```typescript
// Define types for a user profile
let username: string = "john_doe";
let displayName: string = "John Doe";
let age: number = 28;
let isVerified: boolean = true;
let bio: string | null = null;
let followerCount: number = 1500;
let joinDate: string = "2024-01-15";

// Calculate account age
function getAccountAgeInDays(joinDate: string): number {
  const joined = new Date(joinDate);
  const now = new Date();
  const diffTime = Math.abs(now.getTime() - joined.getTime());
  const diffDays = Math.ceil(diffTime / (1000 * 60 * 60 * 24));
  return diffDays;
}
```

### 2. Product Information

```typescript
let productId: number = 12345;
let productName: string = "Wireless Headphones";
let price: number = 99.99;
let inStock: boolean = true;
let discount: number | null = null;
let rating: number = 4.5;

function calculateFinalPrice(price: number, discount: number | null): number {
  if (discount === null) {
    return price;
  }
  return price * (1 - discount / 100);
}

console.log(calculateFinalPrice(price, 20)); // 79.992
console.log(calculateFinalPrice(price, null)); // 99.99
```

### 3. Configuration Settings

```typescript
let apiUrl: string = "https://api.example.com";
let timeout: number = 5000;
let retryCount: number = 3;
let debugMode: boolean = false;
let apiKey: string | undefined = process.env.API_KEY;

function createConfig(): {
  apiUrl: string;
  timeout: number;
  retryCount: number;
  debugMode: boolean;
  apiKey: string;
} {
  if (apiKey === undefined) {
    throw new Error("API_KEY environment variable is required");
  }

  return {
    apiUrl,
    timeout,
    retryCount,
    debugMode,
    apiKey,
  };
}
```

---

## ⚠️ Common Mistakes

### 1. Confusing Type Wrappers

```typescript
// ❌ Don't use wrapper objects
let name: String = "Alice"; // String object
let age: Number = 30; // Number object

// ✅ Use primitive types
let name: string = "Alice"; // string primitive
let age: number = 30; // number primitive
```

### 2. Implicit Any

```typescript
// ❌ With strict mode, this is an error
function process(value) {
  // Parameter 'value' implicitly has an 'any' type
  return value * 2;
}

// ✅ Add type annotation
function process(value: number): number {
  return value * 2;
}
```

### 3. Null Checks

```typescript
// ❌ Without strictNullChecks, this might crash
function getLength(str: string): number {
  return str.length; // str could be null!
}

// ✅ Handle null explicitly
function getLength(str: string | null): number {
  if (str === null) {
    return 0;
  }
  return str.length;
}
```

---

## 🏋️ Practice Exercises

### Exercise 1: Type the Variables

Add appropriate types to these variables:

```typescript
let title = "The Great Gatsby";
let pages = 180;
let isAvailable = true;
let isbn = "978-0-7432-7356-5";
let rating = 4.7;
let borrowedBy = null;
```

### Exercise 2: Fix the Errors

Find and fix the type errors:

```typescript
let count: number = "5";
let isReady: boolean = 1;
let message: string = undefined;
```

---

## ❓ Interview Questions

**Q1: What's the difference between `null` and `undefined` in TypeScript?**

> `undefined` means a variable has been declared but not assigned a value. `null` is an intentional absence of a value. With `strictNullChecks`, they're distinct types that must be explicitly handled.

**Q2: Should you use `Number` or `number`?**

> Always use lowercase `number`. The uppercase `Number` refers to the JavaScript Number object wrapper, which is rarely needed and can cause subtle bugs.

**Q3: How do you handle optional values?**

> Use union types (`string | null` or `string | undefined`), optional parameters (`param?: type`), or optional properties (`prop?: type`).

**Q4: What is `bigint` used for?**

> `bigint` is for integers larger than `Number.MAX_SAFE_INTEGER` (2^53 - 1). It's useful for precise large number calculations.

**Q5: Does TypeScript have a `char` type?**

> No, TypeScript doesn't have a separate character type. Characters are single-character strings: `let char: string = "A"`.

---

## 🔑 Key Takeaways

> ### 💡 Summary
>
> - **Primitive types**: `string`, `number`, `boolean`, `null`, `undefined`, `symbol`, `bigint`
> - **Use lowercase** type names (`string`, not `String`)
> - **Enable strictNullChecks** for null safety
> - **Use union types** for nullable values (`string | null`)
> - **Type inference** often makes annotations optional
> - **Avoid implicit any** - always type function parameters

---

**Next:** [04 - Type Annotations & Inference](./04_Type_Annotations_Inference.md) - Learn when to add types and when TypeScript infers them!
