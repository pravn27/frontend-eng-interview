# 09 - Type Aliases

## 📖 What are Type Aliases?

Type aliases create custom names for types, making code more readable and reusable.

```typescript
// Create a type alias
type UserID = string;
type Age = number;
type Coordinates = [number, number];

// Use the alias
let userId: UserID = "user_123";
let userAge: Age = 30;
let position: Coordinates = [10, 20];
```

---

## 🎯 Basic Type Aliases

### Primitive Aliases

```typescript
// Semantic naming for primitives
type Email = string;
type Password = string;
type Timestamp = number;
type UUID = string;

// Usage
function sendEmail(to: Email, subject: string): void {
  console.log(`Sending to ${to}`);
}

function validatePassword(password: Password): boolean {
  return password.length >= 8;
}
```

### Object Type Aliases

```typescript
// Object type alias
type User = {
  id: number;
  name: string;
  email: string;
  isActive: boolean;
};

// Usage
const user: User = {
  id: 1,
  name: "Alice",
  email: "alice@example.com",
  isActive: true,
};

// Function with type alias
function createUser(name: string, email: string): User {
  return {
    id: Date.now(),
    name,
    email,
    isActive: true,
  };
}
```

### Array and Tuple Aliases

```typescript
// Array type alias
type NumberArray = number[];
type StringList = Array<string>;

// Tuple type alias
type Point = [x: number, y: number];
type RGB = [red: number, green: number, blue: number];
type NameAge = [name: string, age: number];

// Usage
const colors: RGB = [255, 128, 0];
const person: NameAge = ["Alice", 30];
```

---

## 📚 Function Type Aliases

```typescript
// Function type alias
type Callback = () => void;
type Predicate<T> = (value: T) => boolean;
type Comparator<T> = (a: T, b: T) => number;
type AsyncOperation<T> = () => Promise<T>;

// Usage
const onClick: Callback = () => console.log("Clicked");

const isPositive: Predicate<number> = (n) => n > 0;

const compareNumbers: Comparator<number> = (a, b) => a - b;

// Higher-order function with type alias
function filter<T>(arr: T[], predicate: Predicate<T>): T[] {
  return arr.filter(predicate);
}
```

---

## 🔷 Union and Intersection Aliases

```typescript
// Union type alias
type Status = "pending" | "active" | "completed" | "cancelled";
type Result<T> = T | null;
type Input = string | number;

// Intersection type alias
type WithTimestamps = {
  createdAt: Date;
  updatedAt: Date;
};

type UserWithTimestamps = User & WithTimestamps;

// Complex union
type ApiResponse<T> =
  | { status: "success"; data: T }
  | { status: "error"; message: string }
  | { status: "loading" };
```

---

## 💻 Practical Examples

### 1. API Types

```typescript
type HttpMethod = "GET" | "POST" | "PUT" | "DELETE" | "PATCH";
type StatusCode = 200 | 201 | 400 | 401 | 403 | 404 | 500;

type RequestConfig = {
  url: string;
  method: HttpMethod;
  headers?: Record<string, string>;
  body?: unknown;
  timeout?: number;
};

type ApiResponse<T> = {
  data: T;
  status: StatusCode;
  message: string;
};

async function fetchApi<T>(config: RequestConfig): Promise<ApiResponse<T>> {
  const response = await fetch(config.url, {
    method: config.method,
    headers: config.headers,
    body: config.body ? JSON.stringify(config.body) : undefined,
  });

  return {
    data: await response.json(),
    status: response.status as StatusCode,
    message: response.statusText,
  };
}
```

### 2. Form Types

```typescript
type FormField<T> = {
  value: T;
  error: string | null;
  touched: boolean;
  dirty: boolean;
};

type FormState<T> = {
  [K in keyof T]: FormField<T[K]>;
};

type LoginFormData = {
  email: string;
  password: string;
  rememberMe: boolean;
};

type LoginFormState = FormState<LoginFormData>;

// Result:
// {
//   email: FormField<string>;
//   password: FormField<string>;
//   rememberMe: FormField<boolean>;
// }
```

### 3. Event Types

```typescript
type EventType = "click" | "hover" | "focus" | "blur" | "submit";

type EventHandler<T = void> = (event: Event) => T;

type EventMap = {
  [K in EventType]: EventHandler;
};

type ComponentEvents = Partial<EventMap>;

const events: ComponentEvents = {
  click: (e) => console.log("Clicked"),
  hover: (e) => console.log("Hovered"),
};
```

### 4. Utility Type Aliases

```typescript
// Common utility patterns
type Nullable<T> = T | null;
type Optional<T> = T | undefined;
type Maybe<T> = T | null | undefined;

type DeepPartial<T> = {
  [K in keyof T]?: T[K] extends object ? DeepPartial<T[K]> : T[K];
};

type ReadonlyDeep<T> = {
  readonly [K in keyof T]: T[K] extends object ? ReadonlyDeep<T[K]> : T[K];
};

// Usage
type User = {
  id: number;
  name: string;
  profile: {
    bio: string;
    avatar: string;
  };
};

type PartialUser = DeepPartial<User>;
// Can now have partial nested objects
```

---

## 🔄 Type Alias vs Interface

| Feature             | Type Alias | Interface |
| ------------------- | ---------- | --------- |
| Object shapes       | ✅         | ✅        |
| Union types         | ✅         | ❌        |
| Intersection types  | ✅         | ✅        |
| Tuple types         | ✅         | ❌        |
| Primitive aliases   | ✅         | ❌        |
| Declaration merging | ❌         | ✅        |
| Extends/implements  | Partial    | ✅        |
| Computed properties | ✅         | ❌        |

```typescript
// Use type for:
type Status = "active" | "inactive"; // Unions
type Point = [number, number]; // Tuples
type ID = string; // Primitive aliases
type Transform<T> = (value: T) => T; // Function types

// Use interface for:
interface User {
  name: string;
} // Object shapes
interface Animal {
  species: string;
}
interface Dog extends Animal {
  breed: string;
} // Inheritance
```

---

## ⚠️ Common Mistakes

### 1. Overusing Type Aliases

```typescript
// ❌ Unnecessary alias - doesn't add clarity
type MyString = string;
type MyNumber = number;

// ✅ Meaningful aliases
type UserID = string;
type Timestamp = number;
type EmailAddress = string;
```

### 2. Circular References

```typescript
// ❌ Error: Type alias circularly references itself
type BadType = BadType[];

// ✅ OK with interfaces (for recursive structures)
interface TreeNode {
  value: number;
  children: TreeNode[];
}

// ✅ Or use type with object wrapper
type TreeNodeType = {
  value: number;
  children: TreeNodeType[];
};
```

### 3. Confusing Type Parameters

```typescript
// ❌ Unclear type parameters
type T<A, B, C> = { a: A; b: B; c: C };

// ✅ Descriptive names
type ApiResult<TData, TError> = {
  data: TData;
  error: TError;
};
```

---

## 🏋️ Practice Exercises

### Exercise 1: Create Domain Types

Create type aliases for an e-commerce domain:

- ProductID, CategoryID (strings)
- Price (number)
- Product (object with id, name, price, category)
- Cart (array of products with quantities)

### Exercise 2: Function Types

Create type aliases for:

- Event handler: takes Event, returns void
- Validator: takes string, returns boolean
- Transformer: takes T, returns T

---

## ❓ Interview Questions

**Q1: What is a type alias?**

> A type alias creates a new name for any type - primitives, objects, unions, tuples, functions, etc. It improves code readability and reusability.

**Q2: Type alias vs interface?**

> Both can define object shapes. Type aliases can also define unions, tuples, and primitives. Interfaces support declaration merging and extends. Use types for complex types, interfaces for object shapes.

**Q3: Can type aliases be extended?**

> Type aliases can be combined using intersection (`&`), but can't use `extends` like interfaces. `type Extended = Base & { newProp: string }`.

**Q4: Are type aliases just for objects?**

> No, type aliases work with any type: primitives, unions, tuples, functions, generics, etc. They're more versatile than interfaces.

**Q5: Do type aliases exist at runtime?**

> No, like all TypeScript types, aliases are erased during compilation. They're purely a compile-time construct.

---

## 🔑 Key Takeaways

> ### 💡 Summary
>
> - **Type aliases** create reusable, named types
> - **Work with** any type: primitives, objects, unions, functions
> - **Use descriptive names** for clarity
> - **Prefer interfaces** for object shapes (can be extended)
> - **Use types** for unions, tuples, and complex types
> - **Types are erased** at runtime

---

**Next:** [10 - Enums](./10_Enums.md) - Learn about enumerated types!
