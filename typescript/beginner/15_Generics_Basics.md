# 15 - Generics Basics

## 📖 What are Generics?

Generics allow you to write **reusable, type-safe code** that works with multiple types while preserving type information.

```typescript
// Without generics - loses type info
function identity(value: any): any {
  return value;
}

// With generics - preserves type
function identity<T>(value: T): T {
  return value;
}

const num = identity(42); // Type: number
const str = identity("hello"); // Type: string
```

---

## 🎯 Generic Functions

```typescript
// Basic generic function
function firstElement<T>(arr: T[]): T | undefined {
  return arr[0];
}

const first1 = firstElement([1, 2, 3]); // number | undefined
const first2 = firstElement(["a", "b"]); // string | undefined

// Multiple type parameters
function pair<T, U>(first: T, second: U): [T, U] {
  return [first, second];
}

const p1 = pair("hello", 42); // [string, number]
const p2 = pair(true, "yes"); // [boolean, string]

// Explicit type arguments
const p3 = pair<string, number>("hello", 42);
```

---

## 📚 Generic Interfaces

```typescript
// Generic interface
interface Box<T> {
  value: T;
}

const stringBox: Box<string> = { value: "hello" };
const numberBox: Box<number> = { value: 42 };

// Generic interface with methods
interface Container<T> {
  value: T;
  getValue(): T;
  setValue(value: T): void;
}

// Implementing the interface
const container: Container<string> = {
  value: "hello",
  getValue() {
    return this.value;
  },
  setValue(value) {
    this.value = value;
  },
};
```

---

## 🔷 Generic Type Aliases

```typescript
// Generic type alias
type Result<T> =
  | { success: true; value: T }
  | { success: false; error: string };

function divide(a: number, b: number): Result<number> {
  if (b === 0) {
    return { success: false, error: "Division by zero" };
  }
  return { success: true, value: a / b };
}

// Generic response type
type ApiResponse<T> = {
  data: T;
  status: number;
  timestamp: string;
};

type UserResponse = ApiResponse<User>;
type ProductsResponse = ApiResponse<Product[]>;
```

---

## 💻 Practical Examples

### 1. Array Utilities

```typescript
// Get last element
function last<T>(arr: T[]): T | undefined {
  return arr[arr.length - 1];
}

// Filter by type
function filterByType<T>(arr: unknown[], guard: (v: unknown) => v is T): T[] {
  return arr.filter(guard);
}

// Map with index
function mapWithIndex<T, U>(arr: T[], fn: (item: T, index: number) => U): U[] {
  return arr.map(fn);
}

// Group by key
function groupBy<T, K extends string | number>(
  arr: T[],
  keyFn: (item: T) => K
): Record<K, T[]> {
  return arr.reduce((acc, item) => {
    const key = keyFn(item);
    acc[key] = acc[key] || [];
    acc[key].push(item);
    return acc;
  }, {} as Record<K, T[]>);
}
```

### 2. API Helpers

```typescript
// Generic fetch wrapper
async function fetchData<T>(url: string): Promise<T> {
  const response = await fetch(url);
  if (!response.ok) {
    throw new Error(`HTTP error: ${response.status}`);
  }
  return response.json() as Promise<T>;
}

// Usage
interface User {
  id: number;
  name: string;
}

const user = await fetchData<User>("/api/user/1");
const users = await fetchData<User[]>("/api/users");
```

### 3. State Management

```typescript
// Generic state container
function createState<T>(initial: T) {
  let state = initial;

  return {
    get: () => state,
    set: (value: T) => {
      state = value;
    },
    update: (fn: (current: T) => T) => {
      state = fn(state);
    },
  };
}

// Usage
const counter = createState(0);
counter.set(10);
counter.update((n) => n + 1);
console.log(counter.get()); // 11

const user = createState({ name: "Alice", age: 30 });
user.update((u) => ({ ...u, age: u.age + 1 }));
```

---

## 🔒 Generic Constraints

Limit what types can be used with `extends`.

```typescript
// Constraint to objects with length
interface HasLength {
  length: number;
}

function logLength<T extends HasLength>(value: T): T {
  console.log(value.length);
  return value;
}

logLength("hello"); // ✅ string has length
logLength([1, 2, 3]); // ✅ array has length
logLength({ length: 10 }); // ✅ object with length
logLength(42); // ❌ Error: number has no length

// Constraint to object keys
function getProperty<T, K extends keyof T>(obj: T, key: K): T[K] {
  return obj[key];
}

const user = { name: "Alice", age: 30 };
getProperty(user, "name"); // ✅ string
getProperty(user, "age"); // ✅ number
getProperty(user, "email"); // ❌ Error: "email" not in keyof user
```

---

## 🔄 Default Type Parameters

```typescript
// Default generic type
interface Container<T = string> {
  value: T;
}

const stringContainer: Container = { value: "hello" }; // T defaults to string
const numberContainer: Container<number> = { value: 42 };

// Function with default
function createArray<T = number>(length: number, value: T): T[] {
  return Array(length).fill(value);
}

const nums = createArray(3, 0); // number[]
const strs = createArray(3, "x"); // string[]
```

---

## ⚠️ Common Mistakes

### 1. Unnecessary Type Parameters

```typescript
// ❌ T isn't used meaningfully
function logValue<T>(value: T): void {
  console.log(value);
}

// ✅ Just use the concrete type
function logValue(value: unknown): void {
  console.log(value);
}
```

### 2. Missing Constraints

```typescript
// ❌ Can't access .length
function getLength<T>(value: T): number {
  return value.length; // Error!
}

// ✅ Add constraint
function getLength<T extends { length: number }>(value: T): number {
  return value.length;
}
```

### 3. Using any Instead of Generics

```typescript
// ❌ Loses type information
function first(arr: any[]): any {
  return arr[0];
}

// ✅ Preserves type
function first<T>(arr: T[]): T | undefined {
  return arr[0];
}
```

---

## 🏋️ Practice Exercises

### Exercise 1: Generic Stack

Create a generic Stack class with push, pop, and peek methods.

### Exercise 2: Generic Cache

Create a generic cache function with get, set, and clear methods.

---

## ❓ Interview Questions

**Q1: What are generics?**

> Generics allow writing reusable code that works with multiple types while preserving type information. They're like type parameters for functions, classes, and interfaces.

**Q2: When should you use generics?**

> When you have logic that works the same way for different types and you want to preserve the type information, like array utilities, data structures, or API wrappers.

**Q3: What are generic constraints?**

> Constraints limit which types can be used with a generic using `extends`. `T extends HasLength` means T must have a length property.

**Q4: What is keyof in generics?**

> `keyof T` produces a union of T's property names. `K extends keyof T` constrains K to valid keys of T, enabling type-safe property access.

**Q5: Can generics have default values?**

> Yes, `<T = string>` sets a default type. If the type isn't provided or inferred, the default is used.

---

## 🔑 Key Takeaways

> ### 💡 Summary
>
> - **Generics** create reusable, type-safe code
> - **`<T>`** is the convention for type parameters
> - **Constraints** use `extends` to limit types
> - **`keyof`** enables type-safe property access
> - **Default types** provide fallback values
> - **Prefer generics** over `any` for type safety

---

**Next:** [16 - Classes](./16_Classes.md) - Learn about OOP with TypeScript!
