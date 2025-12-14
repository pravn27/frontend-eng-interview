# 14 - Type Assertions

## 📖 What are Type Assertions?

Type assertions tell TypeScript you know more about a value's type than it can infer. They're a way to override TypeScript's type inference.

```typescript
// TypeScript thinks this is Element | null
const element = document.getElementById("myDiv");

// You know it's an HTMLDivElement
const div = document.getElementById("myDiv") as HTMLDivElement;
```

---

## 🎯 Assertion Syntax

### as Syntax (Recommended)

```typescript
const value = someValue as string;
const element = document.querySelector(".btn") as HTMLButtonElement;
const data = JSON.parse(json) as User;
```

### Angle Bracket Syntax

```typescript
const value = <string>someValue;
const element = <HTMLButtonElement>document.querySelector(".btn");

// ⚠️ Doesn't work in JSX/TSX files
// Use 'as' syntax instead
```

---

## 📚 Common Use Cases

### 1. DOM Elements

```typescript
// querySelector returns Element | null
const button = document.querySelector("button") as HTMLButtonElement;
const input = document.getElementById("email") as HTMLInputElement;
const canvas = document.querySelector("canvas") as HTMLCanvasElement;

// Access specific properties
input.value = "test@example.com";
const ctx = canvas.getContext("2d");
button.disabled = true;
```

### 2. JSON Parsing

```typescript
interface User {
  id: number;
  name: string;
  email: string;
}

// JSON.parse returns 'any'
const json = '{"id": 1, "name": "Alice", "email": "alice@example.com"}';
const user = JSON.parse(json) as User;

console.log(user.name); // TypeScript knows this is string
```

### 3. Event Targets

```typescript
function handleSubmit(event: Event) {
  const form = event.target as HTMLFormElement;
  const formData = new FormData(form);
  // ...
}

function handleInput(event: Event) {
  const input = event.target as HTMLInputElement;
  console.log(input.value);
}
```

### 4. API Responses

```typescript
interface ApiResponse<T> {
  data: T;
  status: number;
}

async function fetchUser(id: number): Promise<User> {
  const response = await fetch(`/api/users/${id}`);
  const result = (await response.json()) as ApiResponse<User>;
  return result.data;
}
```

---

## 🔷 Non-Null Assertion

The `!` operator asserts that a value is not `null` or `undefined`.

```typescript
// Without assertion
const element = document.getElementById("app");
element.innerHTML = "Hello"; // Error: possibly null

// With non-null assertion
const element = document.getElementById("app")!;
element.innerHTML = "Hello"; // OK

// In function parameters
function process(value: string | null) {
  // You're sure value is not null here
  console.log(value!.toUpperCase());
}

// ⚠️ Use sparingly - crashes if actually null!
```

---

## 🔒 const Assertion

`as const` creates the narrowest possible type.

```typescript
// Without as const
const config = {
  endpoint: "/api",
  timeout: 5000,
};
// Type: { endpoint: string; timeout: number }

// With as const
const config = {
  endpoint: "/api",
  timeout: 5000,
} as const;
// Type: { readonly endpoint: "/api"; readonly timeout: 5000 }

// Arrays
const colors = ["red", "green", "blue"] as const;
// Type: readonly ["red", "green", "blue"]

// Extract literal union
type Color = (typeof colors)[number];
// Type: "red" | "green" | "blue"
```

---

## 💻 Practical Examples

### 1. Form Handling

```typescript
interface FormData {
  username: string;
  password: string;
  rememberMe: boolean;
}

function handleSubmit(event: SubmitEvent): void {
  event.preventDefault();

  const form = event.target as HTMLFormElement;
  const username = (form.elements.namedItem("username") as HTMLInputElement)
    .value;
  const password = (form.elements.namedItem("password") as HTMLInputElement)
    .value;
  const rememberMe = (form.elements.namedItem("remember") as HTMLInputElement)
    .checked;

  const data: FormData = { username, password, rememberMe };
  console.log(data);
}
```

### 2. Type-Safe Object Access

```typescript
const cache = new Map<string, unknown>();

function getFromCache<T>(key: string): T | undefined {
  const value = cache.get(key);
  return value as T | undefined;
}

// Usage
interface User {
  name: string;
}

cache.set("user", { name: "Alice" });
const user = getFromCache<User>("user");
console.log(user?.name);
```

### 3. Working with Third-Party Libraries

```typescript
// Library returns 'any'
declare function getConfig(): any;

interface AppConfig {
  apiUrl: string;
  debug: boolean;
}

// Assert the type you expect
const config = getConfig() as AppConfig;

// Safer: validate at runtime too
function getTypedConfig(): AppConfig {
  const config = getConfig();
  if (typeof config.apiUrl !== "string" || typeof config.debug !== "boolean") {
    throw new Error("Invalid config");
  }
  return config as AppConfig;
}
```

---

## ⚠️ Double Assertion

When direct assertion isn't allowed, use double assertion through `unknown`.

```typescript
// ❌ Error: Can't assert directly
const value = "hello" as number;

// ✅ Double assertion (use with caution!)
const value = "hello" as unknown as number;

// Common use: incompatible but known-safe conversion
interface UserV1 {
  name: string;
}

interface UserV2 {
  fullName: string;
  age: number;
}

// Not directly compatible, but you know the data
const oldUser: UserV1 = { name: "Alice" };
const newUser = oldUser as unknown as UserV2; // Dangerous!
```

---

## ❌ When NOT to Use Assertions

### 1. Lying to the Compiler

```typescript
// ❌ Dangerous - will crash at runtime
const str = "hello";
const num = str as unknown as number;
console.log(num.toFixed(2)); // Runtime error!

// ❌ Hiding real type issues
interface User {
  name: string;
  email: string;
}

const badUser = {} as User; // Compiles but missing properties!
console.log(badUser.name.toUpperCase()); // Crash!
```

### 2. When Type Guards Are Better

```typescript
// ❌ Assertion without checking
function process(value: unknown) {
  const str = value as string;
  console.log(str.toUpperCase()); // Might crash!
}

// ✅ Type guard is safer
function process(value: unknown) {
  if (typeof value === "string") {
    console.log(value.toUpperCase()); // Safe
  }
}
```

---

## 🔧 Best Practices

### 1. Prefer Type Guards

```typescript
// ✅ Type guard
if (element instanceof HTMLInputElement) {
  console.log(element.value);
}

// Over assertion
const input = element as HTMLInputElement;
console.log(input.value);
```

### 2. Validate at Runtime

```typescript
// ✅ Validate before asserting
function parseUser(json: string): User {
  const parsed = JSON.parse(json);

  if (
    typeof parsed !== "object" ||
    typeof parsed.name !== "string" ||
    typeof parsed.email !== "string"
  ) {
    throw new Error("Invalid user data");
  }

  return parsed as User;
}
```

### 3. Use Non-Null Sparingly

```typescript
// ❌ Risky
element!.innerHTML = "Hello";

// ✅ Safer
if (element) {
  element.innerHTML = "Hello";
}

// Or
element?.innerHTML = "Hello";
```

---

## 🏋️ Practice Exercises

### Exercise 1: DOM Assertions

Write typed functions to get form inputs safely with proper assertions.

### Exercise 2: API Response

Create a type-safe function that parses and validates API responses.

---

## ❓ Interview Questions

**Q1: What is a type assertion?**

> Type assertion tells TypeScript to treat a value as a specific type, overriding inference. It doesn't change runtime behavior - it's only for the compiler.

**Q2: as vs angle brackets?**

> Both do the same thing. `as` works in JSX/TSX files while angle brackets conflict with JSX syntax. Prefer `as` for consistency.

**Q3: When should you avoid assertions?**

> When you're not certain about the type, when type guards would work, or when you're hiding type errors rather than fixing them.

**Q4: What is non-null assertion?**

> The `!` postfix operator asserts a value is not null/undefined. Use cautiously - if wrong, it causes runtime errors.

**Q5: What is double assertion?**

> Asserting through `unknown` to bypass TypeScript's safety checks: `value as unknown as Type`. Should be rare and justified.

---

## 🔑 Key Takeaways

> ### 💡 Summary
>
> - **Type assertions** override TypeScript's inference
> - **Use `as` syntax** (works in JSX)
> - **`as const`** for literal types
> - **`!`** for non-null assertion
> - **Prefer type guards** when possible
> - **Validate at runtime** when asserting external data
> - **Double assertion** is a last resort

---

**Next:** [15 - Generics Basics](./15_Generics_Basics.md) - Learn about generic types!
