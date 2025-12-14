# 07 - Functions

## 📖 Functions in TypeScript

TypeScript adds type annotations to function parameters and return values.

```typescript
// JavaScript function
function greet(name) {
  return `Hello, ${name}!`;
}

// TypeScript function
function greet(name: string): string {
  return `Hello, ${name}!`;
}
```

---

## 🎯 Function Type Annotations

### Parameter Types

```typescript
// Parameters must be typed
function add(a: number, b: number): number {
  return a + b;
}

// ❌ Error: Argument of type 'string' is not assignable
add("1", 2);

// ✅ Correct usage
add(1, 2); // 3
```

### Return Types

```typescript
// Explicit return type
function multiply(a: number, b: number): number {
  return a * b;
}

// Inferred return type (TypeScript figures it out)
function divide(a: number, b: number) {
  return a / b; // Return type inferred as number
}

// Void - no return value
function log(message: string): void {
  console.log(message);
}

// Never - function never returns (throws or infinite loop)
function throwError(message: string): never {
  throw new Error(message);
}
```

---

## 📚 Function Expressions

### Arrow Functions

```typescript
// Arrow function with types
const add = (a: number, b: number): number => a + b;

// Arrow function with inferred return
const multiply = (a: number, b: number) => a * b;

// Arrow function with no return
const log = (message: string): void => console.log(message);

// Multi-line arrow function
const greet = (name: string): string => {
  const greeting = `Hello, ${name}!`;
  return greeting;
};
```

### Function Type Expressions

```typescript
// Type for a function
type MathOperation = (a: number, b: number) => number;

const add: MathOperation = (a, b) => a + b;
const subtract: MathOperation = (a, b) => a - b;
const multiply: MathOperation = (a, b) => a * b;

// Function type in interface
interface Calculator {
  add: (a: number, b: number) => number;
  subtract: (a: number, b: number) => number;
}
```

---

## 🔷 Optional and Default Parameters

### Optional Parameters

```typescript
// Optional parameter with ?
function greet(name: string, greeting?: string): string {
  return `${greeting || "Hello"}, ${name}!`;
}

greet("Alice"); // "Hello, Alice!"
greet("Bob", "Hi"); // "Hi, Bob!"

// Optional parameters must come last
function badFunc(a?: string, b: number) {} // ❌ Error
function goodFunc(a: number, b?: string) {} // ✅ OK
```

### Default Parameters

```typescript
// Default parameter values
function greet(name: string, greeting: string = "Hello"): string {
  return `${greeting}, ${name}!`;
}

greet("Alice"); // "Hello, Alice!"
greet("Bob", "Hi"); // "Hi, Bob!"

// Default parameters can be anywhere
function createUrl(
  path: string,
  protocol: string = "https",
  domain: string = "example.com"
): string {
  return `${protocol}://${domain}/${path}`;
}
```

---

## 🔄 Rest Parameters

```typescript
// Rest parameters with type
function sum(...numbers: number[]): number {
  return numbers.reduce((acc, n) => acc + n, 0);
}

sum(1, 2, 3); // 6
sum(1, 2, 3, 4, 5); // 15

// Rest with other parameters
function greetAll(greeting: string, ...names: string[]): string {
  return `${greeting}, ${names.join(" and ")}!`;
}

greetAll("Hello", "Alice", "Bob", "Charlie");
// "Hello, Alice and Bob and Charlie!"
```

---

## 💻 Practical Examples

### 1. Event Handlers

```typescript
// Click handler type
type ClickHandler = (event: MouseEvent) => void;

const handleClick: ClickHandler = (event) => {
  console.log(`Clicked at (${event.clientX}, ${event.clientY})`);
};

// Input handler
type InputHandler = (event: Event) => void;

const handleInput: InputHandler = (event) => {
  const target = event.target as HTMLInputElement;
  console.log(`Input value: ${target.value}`);
};
```

### 2. Callback Functions

```typescript
// Function that takes a callback
function fetchData(url: string, callback: (data: unknown) => void): void {
  fetch(url)
    .then((res) => res.json())
    .then(callback);
}

// Async callback type
type AsyncCallback<T> = (error: Error | null, data: T | null) => void;

function fetchUser(id: number, callback: AsyncCallback<User>): void {
  fetch(`/api/users/${id}`)
    .then((res) => res.json())
    .then((data) => callback(null, data))
    .catch((error) => callback(error, null));
}
```

### 3. Higher-Order Functions

```typescript
// Function that returns a function
function createMultiplier(factor: number): (n: number) => number {
  return (n) => n * factor;
}

const double = createMultiplier(2);
const triple = createMultiplier(3);

console.log(double(5)); // 10
console.log(triple(5)); // 15

// Function that takes and returns functions
type Transform<T> = (value: T) => T;

function compose<T>(f: Transform<T>, g: Transform<T>): Transform<T> {
  return (value) => f(g(value));
}
```

### 4. Method Overloading

```typescript
// Overload signatures
function format(value: string): string;
function format(value: number): string;
function format(value: Date): string;

// Implementation signature
function format(value: string | number | Date): string {
  if (typeof value === "string") {
    return value.toUpperCase();
  } else if (typeof value === "number") {
    return value.toFixed(2);
  } else {
    return value.toISOString();
  }
}

format("hello"); // "HELLO"
format(3.14159); // "3.14"
format(new Date()); // "2025-01-01T00:00:00.000Z"
```

### 5. Generic Functions

```typescript
// Generic function for arrays
function first<T>(arr: T[]): T | undefined {
  return arr[0];
}

const firstNumber = first([1, 2, 3]); // number | undefined
const firstString = first(["a", "b"]); // string | undefined

// Generic with constraints
function getProperty<T, K extends keyof T>(obj: T, key: K): T[K] {
  return obj[key];
}

const user = { name: "Alice", age: 30 };
getProperty(user, "name"); // string
getProperty(user, "age"); // number
```

---

## 🔒 Function Type Guards

```typescript
// Type guard function
function isString(value: unknown): value is string {
  return typeof value === "string";
}

function processValue(value: string | number) {
  if (isString(value)) {
    console.log(value.toUpperCase()); // TypeScript knows it's string
  } else {
    console.log(value.toFixed(2)); // TypeScript knows it's number
  }
}
```

---

## ⚠️ Common Mistakes

### 1. Forgetting Parameter Types

```typescript
// ❌ Error with strict mode
function process(data) {
  return data.length;
}

// ✅ Add types
function process(data: string): number {
  return data.length;
}
```

### 2. Optional Before Required

```typescript
// ❌ Error: Required parameter after optional
function greet(greeting?: string, name: string) {}

// ✅ Optional parameters last
function greet(name: string, greeting?: string) {}
```

### 3. Wrong Return Type

```typescript
// ❌ Return type doesn't match
function add(a: number, b: number): string {
  return a + b; // Error: number not assignable to string
}

// ✅ Correct return type
function add(a: number, b: number): number {
  return a + b;
}
```

---

## 🏋️ Practice Exercises

### Exercise 1: Type the Functions

Add proper types to these functions:

```typescript
function calculateArea(width, height) {
  return width * height;
}

function formatCurrency(amount, currency) {
  return `${currency}${amount.toFixed(2)}`;
}

const filterNumbers = (arr, predicate) => arr.filter(predicate);
```

### Exercise 2: Create Callback Types

Create types for these callback patterns:

- Error-first callback: `(error, result) => void`
- Promise resolver: `(value) => void`
- Comparator function: `(a, b) => number`

---

## ❓ Interview Questions

**Q1: What's the difference between optional and default parameters?**

> Optional parameters (`?`) may be undefined. Default parameters have a fallback value. Both can be omitted when calling, but default parameters are never undefined.

**Q2: What is function overloading in TypeScript?**

> Multiple function signatures for the same function name, allowing different parameter types/counts. TypeScript resolves which overload to use based on arguments.

**Q3: What is `void` vs `never` return type?**

> `void` means the function doesn't return a meaningful value. `never` means the function never returns (throws error or infinite loop).

**Q4: How do you type a callback function?**

> Use a function type: `(param: Type) => ReturnType` or define a type alias: `type Callback = (data: string) => void`.

**Q5: What are rest parameters?**

> `...args: Type[]` collects remaining arguments into an array. Rest parameters must be last and have an array type.

---

## 🔑 Key Takeaways

> ### 💡 Summary
>
> - **Always type parameters** - Required for type safety
> - **Return types often inferred** - But explicit is clearer
> - **Optional parameters** use `?` and must be last
> - **Default parameters** provide fallback values
> - **Rest parameters** use `...args: Type[]`
> - **Function types** use `(params) => ReturnType` syntax
> - **Overloads** enable multiple signatures

---

**Next:** [08 - Union & Intersection Types](./08_Union_Intersection_Types.md) - Learn to combine types!
