# 13 - Special Types

## 📖 TypeScript Special Types

TypeScript has several special types that serve specific purposes: `any`, `unknown`, `never`, and `void`.

---

## 🚫 any

The `any` type opts out of type checking. **Avoid when possible.**

```typescript
let anything: any = "hello";
anything = 42; // ✅ OK
anything = true; // ✅ OK
anything.foo.bar.baz; // ✅ No error (but crashes at runtime!)

// any disables all type checking
function dangerous(value: any) {
  value.nonExistent(); // No error, but crashes
  return value.whatever; // No error, returns undefined
}
```

### When to Use any

```typescript
// ❌ Avoid: Lazy typing
let data: any = fetchData();

// ✅ OK: Third-party library without types
declare const thirdPartyLib: any;

// ✅ OK: Gradual migration from JavaScript
function legacyFunction(options: any): any {
  // Migrating from JS, will add types later
}

// ✅ OK: Truly dynamic content
function parseJSON(json: string): any {
  return JSON.parse(json);
}
```

---

## ❓ unknown

The `unknown` type is a type-safe alternative to `any`. You must narrow it before use.

```typescript
let value: unknown = "hello";

// ❌ Can't use directly
value.toUpperCase(); // Error!

// ✅ Must narrow first
if (typeof value === "string") {
  value.toUpperCase(); // OK
}

// ✅ Type guard
function processValue(value: unknown): string {
  if (typeof value === "string") {
    return value.toUpperCase();
  }
  if (typeof value === "number") {
    return value.toString();
  }
  return String(value);
}
```

### unknown vs any

```typescript
// any - no safety
function dangerousAny(value: any) {
  return value.foo.bar; // No error, crashes at runtime
}

// unknown - must check first
function safeUnknown(value: unknown) {
  if (typeof value === "object" && value !== null && "foo" in value) {
    return (value as { foo: unknown }).foo;
  }
  return undefined;
}
```

---

## ⛔ never

The `never` type represents values that never occur. Used for:

1. Functions that never return
2. Exhaustive checking
3. Impossible types

```typescript
// Function that throws
function throwError(message: string): never {
  throw new Error(message);
}

// Infinite loop
function infiniteLoop(): never {
  while (true) {
    // Never exits
  }
}

// Exhaustive check
type Status = "pending" | "active" | "completed";

function getStatusText(status: Status): string {
  switch (status) {
    case "pending":
      return "Waiting";
    case "active":
      return "In progress";
    case "completed":
      return "Done";
    default:
      const _exhaustive: never = status;
      return _exhaustive;
  }
}

// Impossible intersection
type Impossible = string & number; // never
```

---

## 🕳️ void

The `void` type means a function doesn't return a meaningful value.

```typescript
// No return statement
function logMessage(message: string): void {
  console.log(message);
}

// Return undefined explicitly
function doSomething(): void {
  // Do work
  return undefined; // OK
  return; // OK
}

// void vs undefined
let voidValue: void = undefined; // OK
let undefinedValue: undefined = undefined; // OK

// void in callbacks
type Callback = () => void;

const callbacks: Callback[] = [];

// void return allows any return (ignored)
callbacks.push(() => "string"); // OK - return ignored
callbacks.push(() => 42); // OK - return ignored
```

---

## 📊 Comparison Table

| Type      | Can Assign Any | Must Narrow | Use Case                      |
| --------- | -------------- | ----------- | ----------------------------- |
| `any`     | ✅             | ❌          | Escape hatch, migration       |
| `unknown` | ✅             | ✅          | Safe handling of unknown data |
| `never`   | ❌             | N/A         | Unreachable code, exhaustive  |
| `void`    | Only undefined | ❌          | Function returns nothing      |

---

## 💻 Practical Examples

### 1. Safe JSON Parsing

```typescript
function parseJSON(json: string): unknown {
  try {
    return JSON.parse(json);
  } catch {
    return null;
  }
}

interface User {
  name: string;
  age: number;
}

function isUser(value: unknown): value is User {
  return (
    typeof value === "object" &&
    value !== null &&
    "name" in value &&
    "age" in value &&
    typeof (value as User).name === "string" &&
    typeof (value as User).age === "number"
  );
}

const data = parseJSON('{"name": "Alice", "age": 30}');
if (isUser(data)) {
  console.log(data.name); // Safe to use
}
```

### 2. Error Handling

```typescript
function fail(message: string): never {
  throw new Error(message);
}

function assertNever(value: never): never {
  throw new Error(`Unexpected value: ${value}`);
}

type Shape = { kind: "circle" } | { kind: "square" };

function handleShape(shape: Shape) {
  switch (shape.kind) {
    case "circle":
      return "Circle";
    case "square":
      return "Square";
    default:
      return assertNever(shape); // Compile error if case missing
  }
}
```

### 3. Event Handlers

```typescript
type ClickHandler = (event: MouseEvent) => void;
type SubmitHandler = (event: SubmitEvent) => void;

function setupHandlers(onClick: ClickHandler, onSubmit: SubmitHandler): void {
  document.addEventListener("click", onClick);
  document.querySelector("form")?.addEventListener("submit", onSubmit);
}

// void allows returning values (they're ignored)
setupHandlers(
  (e) => console.log(e.clientX), // Returns undefined
  (e) => e.preventDefault() // Returns undefined
);
```

---

## ⚠️ Common Mistakes

### 1. Using any Instead of unknown

```typescript
// ❌ Dangerous
function process(data: any) {
  return data.someProperty; // No type checking
}

// ✅ Safe
function process(data: unknown) {
  if (typeof data === "object" && data !== null && "someProperty" in data) {
    return (data as { someProperty: unknown }).someProperty;
  }
  return undefined;
}
```

### 2. Confusing void and undefined

```typescript
// ❌ void is not exactly undefined
function bad(): void {
  return null; // Error!
}

// ✅ void can be undefined
function good(): void {
  return undefined; // OK
  return; // OK
  // Or no return at all
}
```

### 3. Misusing never

```typescript
// ❌ never for possible return
function bad(x: number): never {
  if (x > 0) return x; // Error! never means it can't return
  throw new Error("negative");
}

// ✅ Correct use of never
function good(x: number): number {
  if (x > 0) return x;
  throw new Error("must be positive"); // This branch is never
}
```

---

## 🏋️ Practice Exercises

### Exercise 1: Safe Data Processing

Write a function that safely processes unknown API data with proper type checking.

### Exercise 2: Exhaustive Handler

Create a discriminated union and handler with exhaustive `never` checking.

---

## ❓ Interview Questions

**Q1: Difference between any and unknown?**

> `any` disables type checking. `unknown` requires type narrowing before use. Prefer `unknown` for type safety.

**Q2: When is never used?**

> For functions that never return (throw/infinite loop), exhaustive checking, and impossible types (conflicting intersections).

**Q3: What does void mean?**

> The function doesn't return a meaningful value. Different from `undefined` - void callbacks can return anything (ignored).

**Q4: How to handle unknown safely?**

> Use type guards (typeof, instanceof, in), type predicates, or assertion functions to narrow the type before use.

**Q5: Can you assign to never?**

> Nothing can be assigned to `never` except `never` itself. It's the bottom type of TypeScript's type system.

---

## 🔑 Key Takeaways

> ### 💡 Summary
>
> - **any**: Escape hatch, disables type checking (avoid)
> - **unknown**: Safe unknown type, requires narrowing
> - **never**: Unreachable code, exhaustive checks
> - **void**: No meaningful return value
> - **Prefer unknown** over any for safety
> - **Use never** for exhaustive pattern matching

---

**Next:** [14 - Type Assertions](./14_Type_Assertions.md) - Learn about type casting!
