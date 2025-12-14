# 17 - Modules

## 📖 What are Modules?

Modules are files that export code (functions, classes, types) for use in other files. Each file with imports/exports is a module.

```typescript
// math.ts - Module file
export function add(a: number, b: number): number {
  return a + b;
}

export function multiply(a: number, b: number): number {
  return a * b;
}

// main.ts - Import and use
import { add, multiply } from "./math";

console.log(add(2, 3)); // 5
console.log(multiply(2, 3)); // 6
```

---

## 🎯 Export Syntax

### Named Exports

```typescript
// utils.ts

// Export individual declarations
export const PI = 3.14159;

export function calculateArea(radius: number): number {
  return PI * radius ** 2;
}

export interface Circle {
  radius: number;
  x: number;
  y: number;
}

export class Calculator {
  add(a: number, b: number): number {
    return a + b;
  }
}

// Export list at end of file
const SECRET = "hidden";
function helper() {}

export { SECRET, helper };

// Rename exports
export { SECRET as API_KEY, helper as utilHelper };
```

### Default Export

```typescript
// User.ts
export default class User {
  constructor(public name: string, public email: string) {}
}

// Or for functions
export default function greet(name: string): string {
  return `Hello, ${name}!`;
}

// Import default export (any name works)
import User from "./User";
import myGreetFunction from "./greet";
```

### Mixed Exports

```typescript
// api.ts
export default class API {
  static baseUrl = "https://api.example.com";
}

export interface RequestConfig {
  method: string;
  headers: Record<string, string>;
}

export function createRequest(config: RequestConfig): Request {
  return new Request(API.baseUrl, config);
}

// Import both
import API, { RequestConfig, createRequest } from "./api";
```

---

## 📚 Import Syntax

### Named Imports

```typescript
// Import specific exports
import { add, multiply } from "./math";

// Rename imports
import { add as sum, multiply as mul } from "./math";

// Import all as namespace
import * as MathUtils from "./math";
MathUtils.add(1, 2);
MathUtils.multiply(3, 4);
```

### Default Imports

```typescript
// Import default (any name works)
import User from "./User";
import MyUser from "./User"; // Same thing, different name

// Import default and named together
import User, { UserRole, createUser } from "./User";
```

### Type-Only Imports

```typescript
// Import only types (removed at compile time)
import type { User, UserRole } from "./types";

// Or inline
import { createUser, type User } from "./user";

// Prevents accidentally importing values
```

---

## 🔷 Re-exports

Create barrel files to simplify imports.

```typescript
// models/User.ts
export interface User {
  id: number;
  name: string;
}

// models/Product.ts
export interface Product {
  id: number;
  name: string;
  price: number;
}

// models/index.ts (barrel file)
export { User } from "./User";
export { Product } from "./Product";

// Re-export with rename
export { User as UserModel } from "./User";

// Re-export everything
export * from "./User";
export * from "./Product";

// Usage - single import
import { User, Product } from "./models";
```

---

## 💻 Practical Examples

### 1. Feature Module Structure

```
src/
├── features/
│   └── auth/
│       ├── index.ts          # Barrel file
│       ├── types.ts          # Type definitions
│       ├── AuthService.ts    # Service class
│       └── useAuth.ts        # React hook
├── shared/
│   ├── utils/
│   │   ├── index.ts
│   │   └── format.ts
│   └── types/
│       └── index.ts
└── main.ts
```

```typescript
// features/auth/types.ts
export interface User {
  id: number;
  email: string;
  name: string;
}

export interface AuthState {
  user: User | null;
  isAuthenticated: boolean;
  loading: boolean;
}

// features/auth/AuthService.ts
import type { User } from "./types";

export class AuthService {
  async login(email: string, password: string): Promise<User> {
    // Login logic
  }

  async logout(): Promise<void> {
    // Logout logic
  }
}

// features/auth/index.ts
export type { User, AuthState } from "./types";
export { AuthService } from "./AuthService";
export { useAuth } from "./useAuth";

// main.ts - Clean import
import { AuthService, User, useAuth } from "./features/auth";
```

### 2. Utility Functions Module

```typescript
// utils/string.ts
export function capitalize(str: string): string {
  return str.charAt(0).toUpperCase() + str.slice(1);
}

export function truncate(str: string, length: number): string {
  return str.length > length ? str.slice(0, length) + "..." : str;
}

// utils/number.ts
export function formatCurrency(amount: number, currency = "USD"): string {
  return new Intl.NumberFormat("en-US", {
    style: "currency",
    currency,
  }).format(amount);
}

export function clamp(value: number, min: number, max: number): number {
  return Math.min(Math.max(value, min), max);
}

// utils/index.ts
export * from "./string";
export * from "./number";

// Usage
import { capitalize, formatCurrency } from "./utils";
```

### 3. Constants Module

```typescript
// constants/api.ts
export const API_BASE_URL = "https://api.example.com";
export const API_VERSION = "v1";
export const API_TIMEOUT = 5000;

// constants/routes.ts
export const ROUTES = {
  HOME: "/",
  LOGIN: "/login",
  DASHBOARD: "/dashboard",
  PROFILE: "/profile",
  SETTINGS: "/settings",
} as const;

export type Route = (typeof ROUTES)[keyof typeof ROUTES];

// constants/index.ts
export * from "./api";
export * from "./routes";
```

---

## 🔧 Module Resolution

### TypeScript Configuration

```json
// tsconfig.json
{
  "compilerOptions": {
    "module": "ESNext",
    "moduleResolution": "bundler",
    "baseUrl": "./src",
    "paths": {
      "@/*": ["./*"],
      "@components/*": ["./components/*"],
      "@utils/*": ["./utils/*"]
    }
  }
}
```

```typescript
// With path aliases
import { Button } from "@components/Button";
import { formatDate } from "@utils/date";
```

---

## ⚠️ Common Mistakes

### 1. Circular Dependencies

```typescript
// ❌ Circular dependency
// a.ts
import { B } from "./b";
export class A {
  b = new B();
}

// b.ts
import { A } from "./a";
export class B {
  a = new A();
}

// ✅ Fix: Refactor to break the cycle
// shared.ts
export interface Shared {}

// a.ts
import { Shared } from "./shared";
```

### 2. Missing File Extensions

```typescript
// ❌ May fail depending on module resolution
import { utils } from "./utils";

// ✅ Explicit for Node.js ESM
import { utils } from "./utils.js";
```

### 3. Default Export Issues

```typescript
// ❌ Confusing - different names for same thing
import User from "./User";
import Person from "./User"; // Same class!

// ✅ Prefer named exports for clarity
export class User {}
import { User } from "./User";
```

---

## 🏋️ Practice Exercises

### Exercise 1: Create Module Structure

Create a module structure for a todo app with:

- Types (Todo, TodoList)
- Service (TodoService)
- Utilities (formatDate, generateId)

### Exercise 2: Barrel Files

Create barrel files to simplify imports across your project.

---

## ❓ Interview Questions

**Q1: Named vs Default exports?**

> Named exports are explicit and require exact names. Default exports can be imported with any name. Prefer named exports for clarity and better refactoring.

**Q2: What is a barrel file?**

> An index.ts that re-exports from multiple files, creating a single import point: `export * from './User'`. Simplifies imports.

**Q3: What is type-only import?**

> `import type { X }` imports only types, ensuring they're removed at compile time. Useful for avoiding circular dependencies and reducing bundle size.

**Q4: How do you handle circular dependencies?**

> Refactor shared code to a third module, use type-only imports, or restructure the dependency graph.

**Q5: ESM vs CommonJS?**

> ESM uses `import/export`, is the standard, and supports tree-shaking. CommonJS uses `require/module.exports`, is older, and is common in Node.js.

---

## 🔑 Key Takeaways

> ### 💡 Summary
>
> - **Named exports**: explicit, refactor-friendly
> - **Default exports**: one per file, flexible naming
> - **Barrel files**: simplify imports with re-exports
> - **Type-only imports**: for types without runtime cost
> - **Path aliases**: cleaner imports with tsconfig paths
> - **Avoid circular dependencies**: refactor to shared modules

---

**Next:** [18 - Working with DOM](./18_Working_with_DOM.md) - Learn to type DOM manipulations!
