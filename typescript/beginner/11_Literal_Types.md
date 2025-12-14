# 11 - Literal Types

## 📖 What are Literal Types?

Literal types are types that represent **exact values** rather than general types. They allow you to specify that a variable can only have one specific value.

```typescript
// General type - any string
let generalString: string = "hello";

// Literal type - only this exact value
let literalString: "hello" = "hello";
literalString = "world"; // ❌ Error: Type '"world"' is not assignable to type '"hello"'
```

---

## 🎯 String Literal Types

```typescript
// Single literal
type Yes = "yes";
type No = "no";

// Union of literals (most common)
type Answer = "yes" | "no" | "maybe";

let response: Answer = "yes"; // ✅ OK
response = "no"; // ✅ OK
response = "perhaps"; // ❌ Error

// Direction type
type Direction = "north" | "south" | "east" | "west";

function move(direction: Direction): void {
  console.log(`Moving ${direction}`);
}

move("north"); // ✅ OK
move("up"); // ❌ Error: Argument of type '"up"' is not assignable
```

---

## 🔢 Numeric Literal Types

```typescript
// Specific numbers only
type DiceRoll = 1 | 2 | 3 | 4 | 5 | 6;

function rollDice(): DiceRoll {
  return Math.ceil(Math.random() * 6) as DiceRoll;
}

// HTTP status codes
type SuccessCode = 200 | 201 | 204;
type ErrorCode = 400 | 401 | 403 | 404 | 500;

// Month numbers
type Month = 1 | 2 | 3 | 4 | 5 | 6 | 7 | 8 | 9 | 10 | 11 | 12;

function getMonthName(month: Month): string {
  const months = [
    "Jan",
    "Feb",
    "Mar",
    "Apr",
    "May",
    "Jun",
    "Jul",
    "Aug",
    "Sep",
    "Oct",
    "Nov",
    "Dec",
  ];
  return months[month - 1];
}
```

---

## ✅ Boolean Literal Types

```typescript
// Boolean literals
type True = true;
type False = false;

// In practice, usually with unions
type SuccessResponse = {
  success: true;
  data: unknown;
};

type ErrorResponse = {
  success: false;
  error: string;
};

type Response = SuccessResponse | ErrorResponse;

function handleResponse(response: Response) {
  if (response.success) {
    console.log(response.data); // TypeScript knows success is true
  } else {
    console.log(response.error); // TypeScript knows success is false
  }
}
```

---

## 📚 Template Literal Types

Combine literal types with template strings.

```typescript
// Basic template literal type
type Greeting = `Hello, ${string}`;
let greeting: Greeting = "Hello, World"; // ✅
greeting = "Hi, World"; // ❌ Error

// Event names
type EventName = `on${string}`;
type ClickEvent = `on${"Click" | "DoubleClick"}`;
// "onClick" | "onDoubleClick"

// CSS units
type CSSValue = `${number}${"px" | "em" | "rem" | "%"}`;
let width: CSSValue = "100px"; // ✅
let height: CSSValue = "50em"; // ✅
let margin: CSSValue = "10vw"; // ❌ Error

// HTTP methods with endpoints
type Endpoint = "/users" | "/posts" | "/comments";
type Method = "GET" | "POST";
type Route = `${Method} ${Endpoint}`;
// "GET /users" | "GET /posts" | "GET /comments" | "POST /users" | ...
```

---

## 💻 Practical Examples

### 1. Configuration Options

```typescript
type Theme = "light" | "dark" | "system";
type Language = "en" | "es" | "fr" | "de" | "ja";
type FontSize = "small" | "medium" | "large";

interface AppConfig {
  theme: Theme;
  language: Language;
  fontSize: FontSize;
  notifications: boolean;
}

const config: AppConfig = {
  theme: "dark",
  language: "en",
  fontSize: "medium",
  notifications: true,
};
```

### 2. State Machine

```typescript
type LoadingState = "idle" | "loading" | "success" | "error";

interface State<T> {
  status: LoadingState;
  data: T | null;
  error: string | null;
}

function createInitialState<T>(): State<T> {
  return {
    status: "idle",
    data: null,
    error: null,
  };
}

function setLoading<T>(state: State<T>): State<T> {
  return { ...state, status: "loading" };
}

function setSuccess<T>(state: State<T>, data: T): State<T> {
  return { status: "success", data, error: null };
}

function setError<T>(state: State<T>, error: string): State<T> {
  return { status: "error", data: null, error };
}
```

### 3. API Endpoints

```typescript
type HttpMethod = "GET" | "POST" | "PUT" | "DELETE";
type UserEndpoint = "/users" | "/users/:id" | "/users/:id/profile";
type PostEndpoint = "/posts" | "/posts/:id" | "/posts/:id/comments";

type Endpoint = UserEndpoint | PostEndpoint;

interface ApiRequest {
  method: HttpMethod;
  endpoint: Endpoint;
  body?: unknown;
}

function makeRequest(request: ApiRequest): Promise<Response> {
  return fetch(request.endpoint, {
    method: request.method,
    body: request.body ? JSON.stringify(request.body) : undefined,
  });
}
```

### 4. Event Handling

```typescript
type MouseEventType = "click" | "dblclick" | "mouseenter" | "mouseleave";
type KeyboardEventType = "keydown" | "keyup" | "keypress";
type FormEventType = "submit" | "reset" | "change" | "input";

type EventType = MouseEventType | KeyboardEventType | FormEventType;

function addEventListener(
  element: HTMLElement,
  event: EventType,
  handler: (e: Event) => void
): void {
  element.addEventListener(event, handler);
}
```

---

## 🔒 const Assertions

`as const` creates the narrowest possible type (literal types).

```typescript
// Without as const - types are widened
const config = {
  url: "https://api.example.com",
  method: "GET",
};
// Type: { url: string; method: string }

// With as const - literal types
const config = {
  url: "https://api.example.com",
  method: "GET",
} as const;
// Type: { readonly url: "https://api.example.com"; readonly method: "GET" }

// Arrays with as const
const directions = ["north", "south", "east", "west"] as const;
// Type: readonly ["north", "south", "east", "west"]

type Direction = (typeof directions)[number];
// Type: "north" | "south" | "east" | "west"
```

---

## 🔄 Type Narrowing with Literals

```typescript
type Status = "pending" | "approved" | "rejected";

function handleStatus(status: Status): string {
  // TypeScript knows the exact value in each case
  switch (status) {
    case "pending":
      return "Waiting for review";
    case "approved":
      return "Request approved!";
    case "rejected":
      return "Request denied";
    default:
      // Exhaustiveness check
      const _exhaustive: never = status;
      return _exhaustive;
  }
}
```

---

## ⚠️ Common Mistakes

### 1. Widening with let

```typescript
// ❌ let widens the type
let status = "pending"; // Type: string
status = "approved"; // ✅ Works, but...

function process(s: "pending" | "approved") {}
process(status); // ❌ Error: string not assignable

// ✅ Use const or type annotation
const status = "pending"; // Type: "pending"
// OR
let status: "pending" | "approved" = "pending";
```

### 2. Missing Literal in Union

```typescript
type Color = "red" | "green" | "blue";

function setColor(color: Color) {}

setColor("red"); // ✅
setColor("yellow"); // ❌ Error - not in union

// ✅ Add to union if needed
type Color = "red" | "green" | "blue" | "yellow";
```

### 3. Object Property Widening

```typescript
const obj = { method: "GET" };
// obj.method is string, not "GET"

function request(method: "GET" | "POST") {}
request(obj.method); // ❌ Error

// ✅ Fix with as const
const obj = { method: "GET" } as const;
request(obj.method); // ✅ Works
```

---

## 🏋️ Practice Exercises

### Exercise 1: Create Literal Types

Create literal types for:

- T-shirt sizes: XS, S, M, L, XL, XXL
- Playing card suits: hearts, diamonds, clubs, spades
- Traffic light colors: red, yellow, green

### Exercise 2: Template Literals

Create template literal types for:

- CSS colors: `rgb(...)` or `rgba(...)`
- API versioning: `/v1/...`, `/v2/...`
- Log levels with timestamps: `[INFO] ...`, `[ERROR] ...`

---

## ❓ Interview Questions

**Q1: What are literal types?**

> Literal types represent exact values, not just their type category. `"hello"` is a literal type while `string` is a general type. They enable precise type checking.

**Q2: How do you create a union of literals?**

> Use the pipe operator: `type Status = "pending" | "active" | "completed"`. This creates a type that only accepts those exact values.

**Q3: What is `as const`?**

> `as const` assertion creates the narrowest possible type, converting values to literal types and making objects/arrays readonly.

**Q4: Why use literal types over enums?**

> Literal types have no runtime cost, work naturally with JavaScript, and provide better type inference. Enums generate runtime code and can have unexpected behavior with numeric values.

**Q5: What are template literal types?**

> Template literal types combine literal types with template string syntax to create pattern-based types like `` `on${string}` `` or `` `${number}px` ``.

---

## 🔑 Key Takeaways

> ### 💡 Summary
>
> - **Literal types** represent exact values
> - **String/number/boolean** literals are most common
> - **Union of literals** creates allowed value sets
> - **`as const`** prevents type widening
> - **Template literals** create pattern-based types
> - **Prefer literals** over enums for simple cases

---

**Next:** [12 - Type Narrowing](./12_Type_Narrowing.md) - Learn about type guards and narrowing!
