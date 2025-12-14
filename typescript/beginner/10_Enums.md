# 10 - Enums

## 📖 What are Enums?

Enums (enumerations) define a set of named constants, making code more readable and maintainable.

```typescript
// Define an enum
enum Direction {
  Up,
  Down,
  Left,
  Right,
}

// Use the enum
let direction: Direction = Direction.Up;

function move(dir: Direction): void {
  console.log(`Moving: ${Direction[dir]}`);
}

move(Direction.Left); // "Moving: Left"
```

---

## 🎯 Numeric Enums

By default, enums are **numeric**, starting from 0.

```typescript
enum Status {
  Pending, // 0
  Active, // 1
  Completed, // 2
  Cancelled, // 3
}

console.log(Status.Pending); // 0
console.log(Status.Active); // 1
console.log(Status[0]); // "Pending" (reverse mapping)

// Custom starting value
enum Priority {
  Low = 1,
  Medium = 2,
  High = 3,
  Critical = 4,
}

// Auto-increment from last value
enum HttpStatus {
  OK = 200,
  Created = 201,
  BadRequest = 400,
  Unauthorized, // 401
  Forbidden, // 402
  NotFound = 404,
}
```

---

## 📚 String Enums

String enums have **explicit string values** - more readable and debuggable.

```typescript
enum Color {
  Red = "RED",
  Green = "GREEN",
  Blue = "BLUE",
}

console.log(Color.Red); // "RED"

// No reverse mapping for string enums
console.log(Color["RED"]); // undefined

// Common use: API status values
enum OrderStatus {
  Pending = "pending",
  Processing = "processing",
  Shipped = "shipped",
  Delivered = "delivered",
  Cancelled = "cancelled",
}

function getStatusMessage(status: OrderStatus): string {
  switch (status) {
    case OrderStatus.Pending:
      return "Order is pending";
    case OrderStatus.Processing:
      return "Order is being processed";
    case OrderStatus.Shipped:
      return "Order has been shipped";
    case OrderStatus.Delivered:
      return "Order delivered";
    case OrderStatus.Cancelled:
      return "Order was cancelled";
  }
}
```

---

## 🔷 Const Enums

`const enum` is completely removed during compilation, inlining values for better performance.

```typescript
const enum Direction {
  Up = "UP",
  Down = "DOWN",
  Left = "LEFT",
  Right = "RIGHT",
}

let dir = Direction.Up;

// Compiled to:
// let dir = "UP";

// ⚠️ No reverse mapping available
// ⚠️ Can't iterate over const enum
```

### When to Use Const Enums

```typescript
// ✅ Use const enum for:
// - Performance-critical code
// - When you only need the values, not names
// - When you won't iterate over enum

// ❌ Avoid const enum when:
// - You need reverse mapping
// - You need to iterate over values
// - Publishing a library (can cause issues)
```

---

## 💻 Practical Examples

### 1. User Roles

```typescript
enum UserRole {
  Guest = "guest",
  User = "user",
  Moderator = "moderator",
  Admin = "admin",
  SuperAdmin = "super_admin",
}

interface User {
  id: number;
  name: string;
  role: UserRole;
}

function hasAdminAccess(user: User): boolean {
  return user.role === UserRole.Admin || user.role === UserRole.SuperAdmin;
}

function canModerate(user: User): boolean {
  const moderatorRoles = [
    UserRole.Moderator,
    UserRole.Admin,
    UserRole.SuperAdmin,
  ];
  return moderatorRoles.includes(user.role);
}
```

### 2. HTTP Methods

```typescript
enum HttpMethod {
  GET = "GET",
  POST = "POST",
  PUT = "PUT",
  PATCH = "PATCH",
  DELETE = "DELETE",
}

interface RequestConfig {
  url: string;
  method: HttpMethod;
  body?: unknown;
}

async function request(config: RequestConfig): Promise<Response> {
  return fetch(config.url, {
    method: config.method,
    body: config.body ? JSON.stringify(config.body) : undefined,
  });
}

// Usage
request({
  url: "/api/users",
  method: HttpMethod.GET,
});
```

### 3. State Machine

```typescript
enum GameState {
  Menu = "MENU",
  Loading = "LOADING",
  Playing = "PLAYING",
  Paused = "PAUSED",
  GameOver = "GAME_OVER",
}

class Game {
  private state: GameState = GameState.Menu;

  start(): void {
    if (this.state === GameState.Menu) {
      this.state = GameState.Loading;
      this.loadAssets().then(() => {
        this.state = GameState.Playing;
      });
    }
  }

  pause(): void {
    if (this.state === GameState.Playing) {
      this.state = GameState.Paused;
    }
  }

  resume(): void {
    if (this.state === GameState.Paused) {
      this.state = GameState.Playing;
    }
  }

  private async loadAssets(): Promise<void> {
    // Load game assets
  }
}
```

### 4. Logging Levels

```typescript
enum LogLevel {
  Debug = 0,
  Info = 1,
  Warn = 2,
  Error = 3,
  Fatal = 4,
}

class Logger {
  private level: LogLevel;

  constructor(level: LogLevel = LogLevel.Info) {
    this.level = level;
  }

  debug(message: string): void {
    this.log(LogLevel.Debug, message);
  }

  info(message: string): void {
    this.log(LogLevel.Info, message);
  }

  warn(message: string): void {
    this.log(LogLevel.Warn, message);
  }

  error(message: string): void {
    this.log(LogLevel.Error, message);
  }

  private log(level: LogLevel, message: string): void {
    if (level >= this.level) {
      console.log(`[${LogLevel[level]}] ${message}`);
    }
  }
}
```

---

## 🔄 Enum vs Union Types

```typescript
// Enum
enum Direction {
  Up = "UP",
  Down = "DOWN",
  Left = "LEFT",
  Right = "RIGHT",
}

// Union type (often preferred)
type Direction = "UP" | "DOWN" | "LEFT" | "RIGHT";

// Comparison
// Enum advantages:
// - Reverse mapping (numeric enums)
// - Can iterate over values
// - Namespace for related constants

// Union advantages:
// - No runtime code generated
// - Simpler and more predictable
// - Better tree-shaking
// - Works with as const
```

### When to Use Each

```typescript
// ✅ Use Enums when:
// - You need reverse mapping
// - You need to iterate over values
// - Values need to be computed
// - Working with flags (bitwise operations)

// ✅ Use Union Types when:
// - You want zero runtime cost
// - Values are simple strings/numbers
// - You're defining API contracts
// - You want better TypeScript inference
```

---

## 🚩 Bit Flags with Enums

```typescript
enum Permission {
  None = 0,
  Read = 1 << 0, // 1
  Write = 1 << 1, // 2
  Execute = 1 << 2, // 4
  Delete = 1 << 3, // 8
  All = Read | Write | Execute | Delete, // 15
}

function hasPermission(userPerms: Permission, required: Permission): boolean {
  return (userPerms & required) === required;
}

function addPermission(current: Permission, add: Permission): Permission {
  return current | add;
}

function removePermission(current: Permission, remove: Permission): Permission {
  return current & ~remove;
}

// Usage
let perms = Permission.Read | Permission.Write;
console.log(hasPermission(perms, Permission.Read)); // true
console.log(hasPermission(perms, Permission.Execute)); // false

perms = addPermission(perms, Permission.Execute);
console.log(hasPermission(perms, Permission.Execute)); // true
```

---

## ⚠️ Common Mistakes

### 1. Expecting String Values from Numeric Enums

```typescript
enum Status {
  Active,
  Inactive,
}

// ❌ Wrong expectation
console.log(Status.Active); // 0, not "Active"

// ✅ Use reverse mapping
console.log(Status[Status.Active]); // "Active"

// ✅ Or use string enum
enum Status {
  Active = "active",
  Inactive = "inactive",
}
```

### 2. Mixing Enum Types

```typescript
enum Color {
  Red,
  Green,
  Blue,
}

enum Size {
  Small,
  Medium,
  Large,
}

// ❌ TypeScript allows this (numeric enums)
let color: Color = Size.Small; // No error!

// ✅ String enums prevent this
enum Color {
  Red = "red",
}
enum Size {
  Small = "small",
}

let color: Color = Size.Small; // Error!
```

---

## 🏋️ Practice Exercises

### Exercise 1: Create Enums

Create enums for:

- Days of the week
- HTTP status codes (common ones)
- File types (PDF, DOC, XLS, etc.)

### Exercise 2: State Machine

Create an enum-based state machine for a traffic light (Red, Yellow, Green).

---

## ❓ Interview Questions

**Q1: What are enums in TypeScript?**

> Enums define a set of named constants. They make code more readable by giving meaningful names to values and can be numeric or string-based.

**Q2: Numeric vs String enums?**

> Numeric enums auto-increment from 0 and support reverse mapping. String enums require explicit values, are more readable, and don't have reverse mapping.

**Q3: What is a const enum?**

> A const enum is completely inlined during compilation (values are substituted), resulting in no runtime code. They offer better performance but can't be iterated over.

**Q4: Enum vs Union type?**

> Enums exist at runtime and support features like reverse mapping. Union types have zero runtime cost and are often preferred for simple string/number constants.

**Q5: Can you iterate over enum values?**

> Yes for regular enums: `Object.values(MyEnum)` or `Object.keys(MyEnum)`. No for const enums (they're removed at compile time).

---

## 🔑 Key Takeaways

> ### 💡 Summary
>
> - **Enums** define named constants
> - **Numeric enums** auto-increment, support reverse mapping
> - **String enums** are explicit, more readable
> - **Const enums** are inlined for performance
> - **Consider union types** for simple cases
> - **Use bit flags** for combinable permissions

---

**Next:** [11 - Literal Types](./11_Literal_Types.md) - Learn about exact value types!
