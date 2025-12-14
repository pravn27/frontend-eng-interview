# 04 - Type Annotations & Inference

## 📖 Type Annotations vs Inference

TypeScript can determine types in two ways:

1. **Type Annotation** - You explicitly write the type
2. **Type Inference** - TypeScript figures out the type automatically

```typescript
// Type Annotation - explicit
let name: string = "Alice";

// Type Inference - TypeScript infers string
let name = "Alice"; // TypeScript knows this is string
```

---

## 🎯 Type Inference

TypeScript automatically infers types based on values.

### Variable Inference

```typescript
// TypeScript infers types from initialization
let message = "Hello"; // inferred as string
let count = 42; // inferred as number
let isActive = true; // inferred as boolean
let items = [1, 2, 3]; // inferred as number[]

// Hover over variables in VS Code to see inferred types!
```

### Best Common Type

```typescript
// TypeScript finds the best common type
let mixed = [1, 2, "three"]; // inferred as (string | number)[]
let coords = [0, 0, 0]; // inferred as number[]
```

### Contextual Typing

```typescript
// TypeScript infers from context
document.addEventListener("click", (event) => {
  // event is inferred as MouseEvent
  console.log(event.clientX, event.clientY);
});

const numbers = [1, 2, 3, 4, 5];
numbers.forEach((num) => {
  // num is inferred as number
  console.log(num.toFixed(2));
});
```

---

## 📚 When to Use Annotations

### ✅ Always Annotate

**1. Function Parameters**

```typescript
// ❌ Error with strict mode
function greet(name) {
  return `Hello, ${name}`;
}

// ✅ Always annotate parameters
function greet(name: string): string {
  return `Hello, ${name}`;
}
```

**2. Empty Arrays**

```typescript
// ❌ Inferred as never[] - can't add anything
let items = [];
items.push("test"); // Error!

// ✅ Annotate empty arrays
let items: string[] = [];
items.push("test"); // OK
```

**3. When Type Can't Be Inferred**

```typescript
// ❌ Inferred as any
let response = JSON.parse(data);

// ✅ Annotate the expected type
interface ApiResponse {
  users: User[];
  total: number;
}
let response: ApiResponse = JSON.parse(data);
```

**4. Object Literals for API Responses**

```typescript
// ✅ Annotate when building objects incrementally
interface User {
  id: number;
  name: string;
}

const user: User = {
  id: 1,
  name: "Alice",
};
```

### ❌ Don't Over-Annotate

**When inference is clear:**

```typescript
// ❌ Redundant - TypeScript already knows
let name: string = "Alice";
let count: number = 42;
let flag: boolean = true;

// ✅ Let TypeScript infer
let name = "Alice";
let count = 42;
let flag = true;
```

**Return types (usually inferred):**

```typescript
// ❌ Redundant annotation
function add(a: number, b: number): number {
  return a + b;
}

// ✅ Return type inferred correctly
function add(a: number, b: number) {
  return a + b; // inferred as number
}

// ✅ But annotation is good for public APIs
export function add(a: number, b: number): number {
  return a + b;
}
```

---

## 💻 Practical Examples

### 1. Function Declarations

```typescript
// Parameter types are required
// Return type can be inferred but explicit is clearer

// Inferred return type (number)
function multiply(a: number, b: number) {
  return a * b;
}

// Explicit return type
function divide(a: number, b: number): number {
  return a / b;
}

// Void return type
function logMessage(message: string): void {
  console.log(message);
}

// Arrow functions
const square = (n: number): number => n * n;
const greet = (name: string) => `Hello, ${name}`; // returns string
```

### 2. Object Type Inference

```typescript
// TypeScript infers object shape
const user = {
  id: 1,
  name: "Alice",
  email: "alice@example.com",
};
// Inferred as: { id: number; name: string; email: string }

// ✅ Annotate for stricter typing
interface User {
  id: number;
  name: string;
  email: string;
  role?: string;
}

const user: User = {
  id: 1,
  name: "Alice",
  email: "alice@example.com",
};
```

### 3. Array Inference

```typescript
// Homogeneous arrays
const numbers = [1, 2, 3, 4]; // number[]
const names = ["Alice", "Bob"]; // string[]

// Mixed arrays
const mixed = [1, "two", 3]; // (string | number)[]

// Empty arrays need annotation
const items: string[] = [];

// Array of objects
const users = [
  { name: "Alice", age: 30 },
  { name: "Bob", age: 25 },
]; // { name: string; age: number }[]
```

### 4. Async Functions

```typescript
// Return type is Promise<T>
async function fetchUser(id: number): Promise<User> {
  const response = await fetch(`/api/users/${id}`);
  return response.json();
}

// Inferred return type
async function fetchData(url: string) {
  const response = await fetch(url);
  return response.json(); // Promise<any> - needs annotation!
}

// ✅ Better with annotation
async function fetchData(url: string): Promise<ApiResponse> {
  const response = await fetch(url);
  return response.json();
}
```

---

## 🧩 Type Widening and Narrowing

### Type Widening

```typescript
// let allows reassignment, so type is widened
let message = "hello"; // string (widened)

// const is immutable, so type is narrowed to literal
const greeting = "hello"; // "hello" (literal type)

// Object properties are widened
const config = {
  url: "https://api.example.com", // string, not literal
  timeout: 5000, // number, not literal
};
```

### Preventing Widening

```typescript
// Use const assertion
const config = {
  url: "https://api.example.com",
  timeout: 5000,
} as const;
// Now: { readonly url: "https://api.example.com"; readonly timeout: 5000 }

// Or use explicit literal types
let direction: "left" | "right" = "left";
```

---

## ⚠️ Common Mistakes

### 1. Missing Return Type Causing Issues

```typescript
// ❌ Accidental any return
function getData(id: number) {
  return fetch(`/api/${id}`).then((r) => r.json());
}
// Returns Promise<any> - loses type safety!

// ✅ Explicit return type
function getData(id: number): Promise<User> {
  return fetch(`/api/${id}`).then((r) => r.json());
}
```

### 2. Empty Array Without Type

```typescript
// ❌ Inferred as never[]
const results = [];
results.push({ name: "test" }); // Error!

// ✅ Annotate the type
const results: Result[] = [];
results.push({ name: "test" }); // OK
```

### 3. Delayed Initialization

```typescript
// ❌ Variable used before assignment
let user: User;
console.log(user.name); // Error: used before being assigned

// ✅ Initialize or mark as possibly undefined
let user: User | undefined;
if (user) {
  console.log(user.name);
}
```

---

## 🔧 Best Practices

### 1. Annotate Public APIs

```typescript
// For exported functions, explicit types help users
export function createUser(name: string, age: number): User {
  return { id: Date.now(), name, age };
}
```

### 2. Let TypeScript Work for You

```typescript
// ✅ Good - minimal annotations
const users = data.map((item) => ({
  id: item.id,
  name: item.name,
}));

// ❌ Over-annotated
const users: { id: number; name: string }[] = data.map(
  (item): { id: number; name: string } => ({
    id: item.id,
    name: item.name,
  })
);
```

### 3. Use Inference for Implementation

```typescript
// Implementation details can rely on inference
function processData(data: InputData) {
  const cleaned = data.filter((item) => item.valid);
  const mapped = cleaned.map((item) => item.value);
  const result = mapped.reduce((sum, val) => sum + val, 0);
  return result;
}
```

---

## 🏋️ Practice Exercises

### Exercise 1: Identify Types

What types does TypeScript infer?

```typescript
const name = "TypeScript";
const version = 5.0;
const features = ["types", "interfaces", "generics"];
const config = { strict: true, target: "ES2022" };
```

### Exercise 2: Fix the Inference

Fix this code so it compiles:

```typescript
const items = [];
items.push("apple");
items.push("banana");

function process(data) {
  return data.length;
}
```

---

## ❓ Interview Questions

**Q1: When should you use type annotations vs. let TypeScript infer?**

> Use annotations for function parameters, empty arrays, ambiguous types, and public APIs. Let TypeScript infer for variable initialization, return types of simple functions, and implementation details.

**Q2: What is type widening?**

> Type widening is when TypeScript infers a more general type than the literal value. `let x = "hello"` is widened to `string`, while `const x = "hello"` stays as the literal type `"hello"`.

**Q3: What is contextual typing?**

> TypeScript infers types based on context, like event handlers or array methods. In `arr.map(x => x.toFixed())`, TypeScript knows `x` is a number from the array type.

**Q4: Why annotate function parameters but not return types?**

> Parameters can't be inferred without call sites. Return types can usually be inferred from the function body. However, explicit return types help catch bugs and document intent.

**Q5: How do you prevent type widening?**

> Use `as const` assertion, explicit literal type annotations, or const declarations for primitives.

---

## 🔑 Key Takeaways

> ### 💡 Summary
>
> - **Type inference** handles most cases automatically
> - **Always annotate** function parameters
> - **Annotate empty arrays** and ambiguous types
> - **Let inference work** for implementation details
> - **Use explicit types** for public APIs
> - **`as const`** prevents type widening

---

**Next:** [05 - Arrays and Tuples](./05_Arrays_and_Tuples.md) - Learn about typed collections!
