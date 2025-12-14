# 12 - Type Narrowing

## 📖 What is Type Narrowing?

Type narrowing is the process of refining a type to a more specific type within a code block. TypeScript uses control flow analysis to understand when types become more specific.

```typescript
function printValue(value: string | number) {
  // Here: value is string | number

  if (typeof value === "string") {
    // Here: value is string (narrowed)
    console.log(value.toUpperCase());
  } else {
    // Here: value is number (narrowed)
    console.log(value.toFixed(2));
  }
}
```

---

## 🎯 typeof Guards

Use `typeof` for primitive type checking.

```typescript
function process(value: string | number | boolean) {
  if (typeof value === "string") {
    return value.toUpperCase();
  }
  if (typeof value === "number") {
    return value.toFixed(2);
  }
  // value is boolean here
  return value ? "Yes" : "No";
}

// typeof returns: "string" | "number" | "boolean" | "undefined" |
//                 "object" | "function" | "symbol" | "bigint"
```

---

## 📚 Truthiness Narrowing

```typescript
function printName(name: string | null | undefined) {
  if (name) {
    // name is string (truthy check eliminates null/undefined)
    console.log(name.toUpperCase());
  } else {
    console.log("No name provided");
  }
}

// Logical operators
function getValue(value: string | null): string {
  return value ?? "default"; // Nullish coalescing
}

function process(value: string | undefined) {
  const result = value && value.toUpperCase(); // Short-circuit
}
```

---

## 🔷 Equality Narrowing

```typescript
function compare(a: string | number, b: string | boolean) {
  if (a === b) {
    // a and b are both string (only common type)
    console.log(a.toUpperCase());
    console.log(b.toUpperCase());
  }
}

// Checking for null
function process(value: string | null) {
  if (value !== null) {
    // value is string
    console.log(value.length);
  }
}

// Switch statements
function getArea(
  shape: { kind: "circle"; radius: number } | { kind: "square"; side: number }
) {
  switch (shape.kind) {
    case "circle":
      return Math.PI * shape.radius ** 2;
    case "square":
      return shape.side ** 2;
  }
}
```

---

## 🔍 in Operator Narrowing

Check if a property exists in an object.

```typescript
type Fish = { swim: () => void };
type Bird = { fly: () => void };

function move(animal: Fish | Bird) {
  if ("swim" in animal) {
    animal.swim(); // animal is Fish
  } else {
    animal.fly(); // animal is Bird
  }
}

// With optional properties
interface Admin {
  name: string;
  privileges: string[];
}

interface User {
  name: string;
  email: string;
}

function printInfo(person: Admin | User) {
  console.log(person.name);

  if ("privileges" in person) {
    console.log("Privileges:", person.privileges);
  }

  if ("email" in person) {
    console.log("Email:", person.email);
  }
}
```

---

## 🏛️ instanceof Narrowing

Check class instances.

```typescript
class Dog {
  bark() {
    console.log("Woof!");
  }
}

class Cat {
  meow() {
    console.log("Meow!");
  }
}

function makeSound(animal: Dog | Cat) {
  if (animal instanceof Dog) {
    animal.bark();
  } else {
    animal.meow();
  }
}

// With built-in types
function processDate(value: Date | string) {
  if (value instanceof Date) {
    return value.toISOString();
  }
  return new Date(value).toISOString();
}
```

---

## 💻 Custom Type Guards

Create functions that narrow types.

```typescript
// Type predicate function
function isString(value: unknown): value is string {
  return typeof value === "string";
}

function isNumber(value: unknown): value is number {
  return typeof value === "number";
}

function process(value: unknown) {
  if (isString(value)) {
    console.log(value.toUpperCase()); // value is string
  } else if (isNumber(value)) {
    console.log(value.toFixed(2)); // value is number
  }
}

// Complex type guard
interface User {
  type: "user";
  name: string;
  email: string;
}

interface Admin {
  type: "admin";
  name: string;
  permissions: string[];
}

function isAdmin(person: User | Admin): person is Admin {
  return person.type === "admin";
}

function handlePerson(person: User | Admin) {
  if (isAdmin(person)) {
    console.log("Admin permissions:", person.permissions);
  } else {
    console.log("User email:", person.email);
  }
}
```

---

## 🎯 Discriminated Unions

Use a common property to discriminate between types.

```typescript
interface Circle {
  kind: "circle";
  radius: number;
}

interface Rectangle {
  kind: "rectangle";
  width: number;
  height: number;
}

interface Triangle {
  kind: "triangle";
  base: number;
  height: number;
}

type Shape = Circle | Rectangle | Triangle;

function getArea(shape: Shape): number {
  switch (shape.kind) {
    case "circle":
      return Math.PI * shape.radius ** 2;
    case "rectangle":
      return shape.width * shape.height;
    case "triangle":
      return (shape.base * shape.height) / 2;
  }
}

// Exhaustiveness checking
function getAreaExhaustive(shape: Shape): number {
  switch (shape.kind) {
    case "circle":
      return Math.PI * shape.radius ** 2;
    case "rectangle":
      return shape.width * shape.height;
    case "triangle":
      return (shape.base * shape.height) / 2;
    default:
      // This ensures all cases are handled
      const _exhaustive: never = shape;
      return _exhaustive;
  }
}
```

---

## 🔐 Assertion Functions

Functions that throw if condition is false.

```typescript
function assert(condition: unknown, message: string): asserts condition {
  if (!condition) {
    throw new Error(message);
  }
}

function assertIsString(value: unknown): asserts value is string {
  if (typeof value !== "string") {
    throw new Error("Value must be a string");
  }
}

function processValue(value: unknown) {
  assertIsString(value);
  // value is now string
  console.log(value.toUpperCase());
}

// Non-null assertion
function assertNonNull<T>(value: T): asserts value is NonNullable<T> {
  if (value === null || value === undefined) {
    throw new Error("Value cannot be null or undefined");
  }
}
```

---

## ⚠️ Common Mistakes

### 1. Not Handling All Cases

```typescript
type Status = "pending" | "active" | "completed";

function getMessage(status: Status): string {
  if (status === "pending") return "Waiting...";
  if (status === "active") return "In progress";
  // ❌ Missing "completed" case!
}

// ✅ Use exhaustive check
function getMessage(status: Status): string {
  switch (status) {
    case "pending":
      return "Waiting...";
    case "active":
      return "In progress";
    case "completed":
      return "Done!";
  }
}
```

### 2. Incorrect Type Guard

```typescript
// ❌ Type predicate doesn't match logic
function isString(value: unknown): value is string {
  return typeof value === "number"; // Bug!
}

// ✅ Logic matches predicate
function isString(value: unknown): value is string {
  return typeof value === "string";
}
```

---

## 🏋️ Practice Exercises

### Exercise 1: Type Guards

Create type guards for:

- `isArray` - checks if value is an array
- `isObject` - checks if value is a non-null object
- `isFunction` - checks if value is a function

### Exercise 2: Discriminated Union

Create a discriminated union for API responses (success, error, loading).

---

## ❓ Interview Questions

**Q1: What is type narrowing?**

> Type narrowing refines a broader type to a more specific type within a code block using control flow analysis. It allows safe access to type-specific properties.

**Q2: What's a type predicate?**

> A type predicate is a return type of `value is Type` that tells TypeScript a function is a type guard. When true, the parameter is narrowed to that type.

**Q3: How do you ensure exhaustive handling?**

> Assign to `never` in the default case. If a case is missing, assignment will error since the type won't be `never`.

**Q4: typeof vs instanceof?**

> `typeof` checks primitive types (string, number, boolean). `instanceof` checks class instances and prototype chain.

**Q5: What are assertion functions?**

> Functions with return type `asserts condition` or `asserts value is Type`. They throw if false, otherwise TypeScript narrows the type.

---

## 🔑 Key Takeaways

> ### 💡 Summary
>
> - **Type narrowing** refines types in code blocks
> - **typeof** for primitives
> - **instanceof** for classes
> - **in** for property checking
> - **Type predicates** for custom guards
> - **Discriminated unions** for tagged types
> - **Exhaustive checks** ensure all cases handled

---

**Next:** [13 - Special Types](./13_Special_Types.md) - Learn about any, unknown, never, and void!
