# 08 - Union & Intersection Types

## 📖 Combining Types

TypeScript allows you to create new types by combining existing ones using **union** (`|`) and **intersection** (`&`) operators.

```typescript
// Union: Either type A OR type B
type StringOrNumber = string | number;

// Intersection: Both type A AND type B
type Combined = TypeA & TypeB;
```

---

## 🎯 Union Types (`|`)

A union type represents a value that can be **one of several types**.

### Basic Unions

```typescript
// Value can be string or number
let id: string | number;

id = "abc123"; // ✅ OK
id = 12345; // ✅ OK
id = true; // ❌ Error

// Function parameter union
function printId(id: string | number): void {
  console.log(`ID: ${id}`);
}

printId("abc"); // ✅ OK
printId(123); // ✅ OK
```

### Union with Literals

```typescript
// Status can only be these specific values
type Status = "pending" | "approved" | "rejected";

let orderStatus: Status = "pending"; // ✅ OK
orderStatus = "shipped"; // ❌ Error: not in union

// Direction type
type Direction = "up" | "down" | "left" | "right";

function move(direction: Direction): void {
  console.log(`Moving ${direction}`);
}
```

### Working with Union Types

```typescript
function printId(id: string | number): void {
  // Type narrowing required for type-specific methods
  if (typeof id === "string") {
    console.log(id.toUpperCase()); // OK - TypeScript knows it's string
  } else {
    console.log(id.toFixed(2)); // OK - TypeScript knows it's number
  }
}

// Common members work without narrowing
function getLength(value: string | string[]): number {
  return value.length; // Both string and array have .length
}
```

---

## 📚 Intersection Types (`&`)

An intersection type combines multiple types into one, requiring **all properties**.

### Basic Intersections

```typescript
interface Person {
  name: string;
  age: number;
}

interface Employee {
  employeeId: number;
  department: string;
}

// Must have ALL properties from both types
type EmployeePerson = Person & Employee;

const employee: EmployeePerson = {
  name: "Alice",
  age: 30,
  employeeId: 12345,
  department: "Engineering",
};
```

### Extending Functionality

```typescript
interface Timestamps {
  createdAt: Date;
  updatedAt: Date;
}

interface User {
  id: number;
  name: string;
  email: string;
}

// User with timestamps
type UserWithTimestamps = User & Timestamps;

const user: UserWithTimestamps = {
  id: 1,
  name: "Alice",
  email: "alice@example.com",
  createdAt: new Date(),
  updatedAt: new Date(),
};
```

### Mixins Pattern

```typescript
interface Loggable {
  log(): void;
}

interface Serializable {
  serialize(): string;
}

type LoggableSerializable = Loggable & Serializable;

const obj: LoggableSerializable = {
  log() {
    console.log("Logged");
  },
  serialize() {
    return JSON.stringify(this);
  },
};
```

---

## 💻 Practical Examples

### 1. API Response Handling

```typescript
// Success or error response
type ApiResponse<T> =
  | { success: true; data: T }
  | { success: false; error: string };

function handleResponse(response: ApiResponse<User>): void {
  if (response.success) {
    console.log(`User: ${response.data.name}`);
  } else {
    console.log(`Error: ${response.error}`);
  }
}
```

### 2. Form Input Types

```typescript
// Different input configurations
type TextInput = {
  type: "text";
  placeholder: string;
  maxLength?: number;
};

type NumberInput = {
  type: "number";
  min?: number;
  max?: number;
};

type SelectInput = {
  type: "select";
  options: string[];
};

type FormInput = TextInput | NumberInput | SelectInput;

function renderInput(input: FormInput): void {
  switch (input.type) {
    case "text":
      console.log(`Text input: ${input.placeholder}`);
      break;
    case "number":
      console.log(`Number input: ${input.min} - ${input.max}`);
      break;
    case "select":
      console.log(`Select options: ${input.options.join(", ")}`);
      break;
  }
}
```

### 3. Extending Base Types

```typescript
// Base entity
interface BaseEntity {
  id: number;
  createdAt: Date;
  updatedAt: Date;
}

// Specific entities
interface Product {
  name: string;
  price: number;
  category: string;
}

interface Order {
  items: string[];
  total: number;
  status: string;
}

// Combined types
type ProductEntity = BaseEntity & Product;
type OrderEntity = BaseEntity & Order;

const product: ProductEntity = {
  id: 1,
  createdAt: new Date(),
  updatedAt: new Date(),
  name: "Laptop",
  price: 999,
  category: "Electronics",
};
```

### 4. Optional Combinations

```typescript
// User with optional admin capabilities
interface User {
  id: number;
  name: string;
}

interface AdminCapabilities {
  canDelete: boolean;
  canEdit: boolean;
  accessLevel: number;
}

type UserOrAdmin = User | (User & AdminCapabilities);

function checkAccess(user: UserOrAdmin): void {
  if ("accessLevel" in user) {
    console.log(`Admin level: ${user.accessLevel}`);
  } else {
    console.log(`Regular user: ${user.name}`);
  }
}
```

---

## 🔄 Discriminated Unions

Use a common property (discriminant) to distinguish between union members.

```typescript
// Shape types with discriminant 'kind'
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

// TypeScript ensures exhaustive checking
const circle: Circle = { kind: "circle", radius: 5 };
console.log(getArea(circle)); // 78.54...
```

---

## ⚠️ Common Mistakes

### 1. Accessing Non-Common Properties

```typescript
type Pet =
  | { name: string; bark?: () => void }
  | { name: string; meow?: () => void };

function getPet(pet: Pet) {
  pet.name; // ✅ OK - common to both
  pet.bark(); // ❌ Error - not on all union members
}

// ✅ Use type narrowing
function getPet(pet: Pet) {
  if ("bark" in pet && pet.bark) {
    pet.bark();
  }
}
```

### 2. Conflicting Intersection Properties

```typescript
type A = { x: number };
type B = { x: string };

type C = A & B; // x is never (number & string = never)

const c: C = { x: 1 }; // ❌ Error - impossible type
```

### 3. Forgetting Exhaustive Checks

```typescript
type Status = "pending" | "active" | "completed";

function getStatusMessage(status: Status): string {
  switch (status) {
    case "pending":
      return "Waiting";
    case "active":
      return "In progress";
    // Missing "completed" case!
  }
}

// ✅ Add exhaustive check
function getStatusMessage(status: Status): string {
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
```

---

## 🏋️ Practice Exercises

### Exercise 1: Create Union Types

Create types for a notification system:

- NotificationType: "info", "warning", "error", "success"
- Create notification objects with type-specific properties

### Exercise 2: Intersection Types

Create a type that combines:

- Identifiable (id, createdAt)
- Nameable (firstName, lastName)
- Contactable (email, phone)

---

## ❓ Interview Questions

**Q1: What's the difference between union and intersection?**

> Union (`|`) means the value is one of the types. Intersection (`&`) means the value has all properties of all types.

**Q2: What is a discriminated union?**

> A union where each member has a common literal property (discriminant) that TypeScript uses to narrow the type in switch statements or if checks.

**Q3: How do you access properties on union types?**

> You can only access properties common to all union members. For type-specific properties, use type narrowing (typeof, in, instanceof, or discriminant).

**Q4: Can intersections conflict?**

> Yes, if the same property has incompatible types in both types, the intersection becomes `never` for that property.

**Q5: When would you use intersection over interface extends?**

> Use intersection for combining types (not interfaces), for inline combinations, or when you need to combine types that can't use extends.

---

## 🔑 Key Takeaways

> ### 💡 Summary
>
> - **Union (`|`)**: Value is one of several types
> - **Intersection (`&`)**: Value has all properties of all types
> - **Discriminated unions**: Use common property to distinguish
> - **Type narrowing**: Required to access union-specific properties
> - **Exhaustive checks**: Ensure all union cases are handled
> - **Avoid conflicts**: Intersecting same property with different types

---

**Next:** [09 - Type Aliases](./09_Type_Aliases.md) - Learn to create custom types!
