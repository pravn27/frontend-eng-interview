# 06 - Objects and Interfaces

## 📖 Object Types in TypeScript

TypeScript provides multiple ways to define the shape of objects.

```typescript
// Inline object type
let user: { name: string; age: number } = {
  name: "Alice",
  age: 30,
};

// Interface (recommended)
interface User {
  name: string;
  age: number;
}

let user: User = {
  name: "Alice",
  age: 30,
};
```

---

## 🎯 Inline Object Types

For one-time use or simple cases.

```typescript
// Inline object type
function printUser(user: { name: string; age: number }): void {
  console.log(`${user.name} is ${user.age} years old`);
}

// Variable with inline type
let point: { x: number; y: number } = { x: 10, y: 20 };

// Function returning inline object type
function createPoint(x: number, y: number): { x: number; y: number } {
  return { x, y };
}
```

---

## 📚 Interfaces

Interfaces define the structure of objects. They're the primary way to type objects in TypeScript.

### Basic Interface

```typescript
interface User {
  id: number;
  name: string;
  email: string;
}

const user: User = {
  id: 1,
  name: "Alice",
  email: "alice@example.com",
};

// ❌ Error: Missing required property
const badUser: User = {
  id: 2,
  name: "Bob",
  // Missing email!
};

// ❌ Error: Extra property
const extraUser: User = {
  id: 3,
  name: "Charlie",
  email: "charlie@example.com",
  age: 30, // Error: Object literal may only specify known properties
};
```

### Optional Properties

```typescript
interface User {
  id: number;
  name: string;
  email: string;
  age?: number; // Optional
  bio?: string; // Optional
}

const user1: User = {
  id: 1,
  name: "Alice",
  email: "alice@example.com",
};

const user2: User = {
  id: 2,
  name: "Bob",
  email: "bob@example.com",
  age: 25,
  bio: "Developer",
};
```

### Readonly Properties

```typescript
interface Config {
  readonly apiKey: string;
  readonly baseUrl: string;
  timeout: number;
}

const config: Config = {
  apiKey: "abc123",
  baseUrl: "https://api.example.com",
  timeout: 5000,
};

config.timeout = 10000; // ✅ OK
config.apiKey = "newKey"; // ❌ Error: Cannot assign to 'apiKey'
```

### Index Signatures

```typescript
// For objects with dynamic keys
interface StringDictionary {
  [key: string]: string;
}

const dict: StringDictionary = {
  hello: "world",
  foo: "bar",
};

dict.newKey = "newValue"; // ✅ OK

// Mixed known and dynamic properties
interface UserSettings {
  theme: string;
  language: string;
  [key: string]: string; // Additional settings
}
```

---

## 🔷 Extending Interfaces

Interfaces can extend other interfaces.

```typescript
interface Person {
  name: string;
  age: number;
}

interface Employee extends Person {
  employeeId: number;
  department: string;
}

const employee: Employee = {
  name: "Alice",
  age: 30,
  employeeId: 12345,
  department: "Engineering",
};

// Multiple inheritance
interface Manager extends Employee {
  team: string[];
}

// Extend multiple interfaces
interface AdminEmployee extends Person, Employee {
  adminLevel: number;
}
```

---

## 💻 Practical Examples

### 1. API Response Types

```typescript
interface ApiResponse<T> {
  data: T;
  status: number;
  message: string;
  timestamp: string;
}

interface User {
  id: number;
  name: string;
  email: string;
}

interface PaginatedResponse<T> {
  items: T[];
  total: number;
  page: number;
  pageSize: number;
  hasNext: boolean;
}

// Usage
const userResponse: ApiResponse<User> = {
  data: { id: 1, name: "Alice", email: "alice@example.com" },
  status: 200,
  message: "Success",
  timestamp: "2025-01-01T00:00:00Z",
};

const usersResponse: ApiResponse<PaginatedResponse<User>> = {
  data: {
    items: [{ id: 1, name: "Alice", email: "alice@example.com" }],
    total: 100,
    page: 1,
    pageSize: 10,
    hasNext: true,
  },
  status: 200,
  message: "Success",
  timestamp: "2025-01-01T00:00:00Z",
};
```

### 2. Form Data

```typescript
interface LoginForm {
  email: string;
  password: string;
  rememberMe?: boolean;
}

interface RegistrationForm extends LoginForm {
  name: string;
  confirmPassword: string;
  acceptTerms: boolean;
}

function validateLoginForm(form: LoginForm): boolean {
  return form.email.includes("@") && form.password.length >= 8;
}

const loginData: LoginForm = {
  email: "user@example.com",
  password: "password123",
  rememberMe: true,
};
```

### 3. Configuration Objects

```typescript
interface DatabaseConfig {
  host: string;
  port: number;
  database: string;
  username: string;
  password: string;
  ssl?: boolean;
  poolSize?: number;
}

interface AppConfig {
  readonly env: "development" | "staging" | "production";
  readonly version: string;
  database: DatabaseConfig;
  features: {
    darkMode: boolean;
    analytics: boolean;
    [key: string]: boolean;
  };
}

const config: AppConfig = {
  env: "development",
  version: "1.0.0",
  database: {
    host: "localhost",
    port: 5432,
    database: "myapp",
    username: "admin",
    password: "secret",
    ssl: false,
  },
  features: {
    darkMode: true,
    analytics: false,
  },
};
```

### 4. Function Types in Interfaces

```typescript
interface Calculator {
  add(a: number, b: number): number;
  subtract(a: number, b: number): number;
  multiply(a: number, b: number): number;
  divide(a: number, b: number): number;
}

const calculator: Calculator = {
  add: (a, b) => a + b,
  subtract: (a, b) => a - b,
  multiply: (a, b) => a * b,
  divide: (a, b) => (b !== 0 ? a / b : 0),
};

// Alternative: callable interface
interface Greeter {
  (name: string): string;
  language: string;
}

const greet: Greeter = (name: string) => `Hello, ${name}!`;
greet.language = "en";
```

---

## 🔄 Interface vs Type Alias

Both can define object shapes, but with differences.

```typescript
// Interface
interface UserInterface {
  name: string;
  age: number;
}

// Type alias
type UserType = {
  name: string;
  age: number;
};

// Extending
interface AdminInterface extends UserInterface {
  role: string;
}

type AdminType = UserType & {
  role: string;
};

// Declaration merging (only interfaces)
interface User {
  name: string;
}

interface User {
  email: string;
}
// User now has both name and email

// Type aliases can't be merged
type Person = { name: string };
type Person = { age: number }; // ❌ Error: Duplicate identifier
```

### When to Use Which?

| Use Interface When...     | Use Type Alias When...            |
| ------------------------- | --------------------------------- |
| Defining object shapes    | Creating union types              |
| Need declaration merging  | Creating intersection types       |
| Defining class contracts  | Creating tuple types              |
| Working with OOP patterns | Creating mapped/conditional types |
| Building public APIs      | Need any complex type             |

---

## ⚠️ Common Mistakes

### 1. Excess Property Checks

```typescript
interface User {
  name: string;
}

// ❌ Error: Object literal may only specify known properties
const user: User = {
  name: "Alice",
  age: 30, // Error!
};

// ✅ Workaround: Assign to variable first
const data = { name: "Alice", age: 30 };
const user: User = data; // OK - no excess property check
```

### 2. Forgetting Optional Checks

```typescript
interface User {
  name: string;
  email?: string;
}

function sendEmail(user: User) {
  // ❌ Error: email might be undefined
  console.log(user.email.toLowerCase());

  // ✅ Check first
  if (user.email) {
    console.log(user.email.toLowerCase());
  }

  // Or use optional chaining
  console.log(user.email?.toLowerCase());
}
```

### 3. Mutable Readonly Properties

```typescript
interface Config {
  readonly settings: { theme: string };
}

const config: Config = {
  settings: { theme: "dark" },
};

config.settings = { theme: "light" }; // ❌ Error
config.settings.theme = "light"; // ✅ Works! Only reference is readonly

// For deep readonly, use utility type
type DeepReadonly<T> = {
  readonly [K in keyof T]: DeepReadonly<T[K]>;
};
```

---

## 🏋️ Practice Exercises

### Exercise 1: Define Interfaces

Create interfaces for an e-commerce system:

- Product (id, name, price, description?, inStock)
- CartItem (product, quantity)
- Order (items, total, customer, shippingAddress)

### Exercise 2: Extend Interfaces

Create a hierarchy: Person → Employee → Manager with appropriate properties.

---

## ❓ Interview Questions

**Q1: What's the difference between interface and type?**

> Interfaces are for object shapes and support declaration merging and extends. Types are more flexible, supporting unions, intersections, and complex types. Use interfaces for object shapes, types for everything else.

**Q2: What are optional properties?**

> Properties marked with `?` that may or may not exist. `name?: string` means the property is `string | undefined`.

**Q3: What are index signatures?**

> Allow objects with dynamic keys: `[key: string]: value`. Useful for dictionaries or objects with unknown property names.

**Q4: Can interfaces have methods?**

> Yes, interfaces can define method signatures: `greet(name: string): string`. The implementing object must have matching methods.

**Q5: What is declaration merging?**

> When multiple interface declarations with the same name are combined into one. This allows extending interfaces across files or adding to library types.

---

## 🔑 Key Takeaways

> ### 💡 Summary
>
> - **Interfaces** define object shapes and contracts
> - **Optional properties** use `?` suffix
> - **Readonly** prevents reassignment (shallow)
> - **Index signatures** allow dynamic keys
> - **extends** creates interface hierarchies
> - **Declaration merging** combines same-name interfaces
> - **Prefer interfaces** for object types

---

**Next:** [07 - Functions](./07_Functions.md) - Learn about typed functions!
