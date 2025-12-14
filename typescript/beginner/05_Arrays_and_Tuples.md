# 05 - Arrays and Tuples

## 📖 Arrays in TypeScript

Arrays hold multiple values of the same type. TypeScript provides two syntaxes for typing arrays.

```typescript
// Syntax 1: Type[]
let numbers: number[] = [1, 2, 3, 4, 5];
let names: string[] = ["Alice", "Bob", "Charlie"];

// Syntax 2: Array<Type>
let numbers: Array<number> = [1, 2, 3, 4, 5];
let names: Array<string> = ["Alice", "Bob", "Charlie"];
```

---

## 🎯 Basic Array Types

### Primitive Arrays

```typescript
// Number array
let scores: number[] = [95, 87, 92, 78];

// String array
let colors: string[] = ["red", "green", "blue"];

// Boolean array
let flags: boolean[] = [true, false, true];

// Type inference
let inferred = [1, 2, 3]; // number[]
let inferredStrings = ["a", "b"]; // string[]
```

### Mixed Type Arrays

```typescript
// Union type array
let mixed: (string | number)[] = [1, "two", 3, "four"];

// Alternative syntax
let mixed: Array<string | number> = [1, "two", 3, "four"];

// Any type (avoid if possible)
let anything: any[] = [1, "two", true, null];
```

### Object Arrays

```typescript
interface User {
  id: number;
  name: string;
}

let users: User[] = [
  { id: 1, name: "Alice" },
  { id: 2, name: "Bob" },
];

// Inline object type
let products: { id: number; name: string; price: number }[] = [
  { id: 1, name: "Laptop", price: 999 },
  { id: 2, name: "Mouse", price: 29 },
];
```

---

## 📚 Array Methods

TypeScript provides type checking for array methods.

```typescript
const numbers: number[] = [1, 2, 3, 4, 5];

// map - transforms each element
const doubled: number[] = numbers.map((n) => n * 2);
const strings: string[] = numbers.map((n) => n.toString());

// filter - keeps elements matching condition
const evens: number[] = numbers.filter((n) => n % 2 === 0);

// find - returns element or undefined
const found: number | undefined = numbers.find((n) => n > 3);

// reduce - accumulates to single value
const sum: number = numbers.reduce((acc, n) => acc + n, 0);

// some/every - boolean checks
const hasEven: boolean = numbers.some((n) => n % 2 === 0);
const allPositive: boolean = numbers.every((n) => n > 0);

// includes - check if element exists
const hasThree: boolean = numbers.includes(3);
```

---

## 🔷 Tuples

Tuples are **fixed-length arrays** where each element has a **specific type**.

```typescript
// Tuple: [type1, type2, ...]
let point: [number, number] = [10, 20];
let person: [string, number] = ["Alice", 30];

// Access by index
const x: number = point[0];
const y: number = point[1];

// ❌ Error: length mismatch
let badTuple: [number, number] = [1, 2, 3];

// ❌ Error: type mismatch
let badTypes: [string, number] = [30, "Alice"];
```

### Named Tuple Elements

```typescript
// Named tuples (TypeScript 4.0+)
type Coordinate = [x: number, y: number];
type UserTuple = [id: number, name: string, active: boolean];

const point: Coordinate = [10, 20];
const user: UserTuple = [1, "Alice", true];

// More readable in function parameters
function setCoordinate(coord: [x: number, y: number]) {
  console.log(`Setting to (${coord[0]}, ${coord[1]})`);
}
```

### Optional Tuple Elements

```typescript
// Optional elements at the end
type Point2DOrD = [number, number, number?];

const point2D: Point2DOrD = [10, 20];
const point3D: Point2DOrD = [10, 20, 30];

// Rest elements
type StringNumberRest = [string, ...number[]];
const data: StringNumberRest = ["values", 1, 2, 3, 4, 5];
```

---

## 💻 Practical Examples

### 1. Coordinate System

```typescript
type Point2D = [x: number, y: number];
type Point3D = [x: number, y: number, z: number];

function calculateDistance(p1: Point2D, p2: Point2D): number {
  const dx = p2[0] - p1[0];
  const dy = p2[1] - p1[1];
  return Math.sqrt(dx * dx + dy * dy);
}

const pointA: Point2D = [0, 0];
const pointB: Point2D = [3, 4];
console.log(calculateDistance(pointA, pointB)); // 5
```

### 2. RGB Colors

```typescript
type RGB = [red: number, green: number, blue: number];
type RGBA = [red: number, green: number, blue: number, alpha: number];

function rgbToHex(color: RGB): string {
  return (
    "#" +
    color
      .map((c) => {
        const hex = c.toString(16);
        return hex.length === 1 ? "0" + hex : hex;
      })
      .join("")
  );
}

const red: RGB = [255, 0, 0];
console.log(rgbToHex(red)); // "#ff0000"
```

### 3. API Response Handling

```typescript
// Tuple for [data, error] pattern
type Result<T> = [T, null] | [null, Error];

async function fetchUser(id: number): Promise<Result<User>> {
  try {
    const response = await fetch(`/api/users/${id}`);
    const user = await response.json();
    return [user, null];
  } catch (error) {
    return [null, error as Error];
  }
}

// Usage
const [user, error] = await fetchUser(1);
if (error) {
  console.error("Failed:", error.message);
} else {
  console.log("User:", user.name);
}
```

### 4. useState-like Return

```typescript
// React's useState returns a tuple
type UseStateReturn<T> = [T, (value: T) => void];

function useState<T>(initial: T): UseStateReturn<T> {
  let state = initial;
  const setState = (value: T) => {
    state = value;
  };
  return [state, setState];
}

const [count, setCount] = useState(0);
const [name, setName] = useState("Alice");
```

---

## 🔒 Readonly Arrays

Prevent modifications to arrays.

```typescript
// Readonly array
const numbers: readonly number[] = [1, 2, 3];
numbers.push(4); // ❌ Error: Property 'push' does not exist

// Alternative syntax
const numbers: ReadonlyArray<number> = [1, 2, 3];

// Readonly tuple
const point: readonly [number, number] = [10, 20];
point[0] = 5; // ❌ Error: Cannot assign to '0'

// as const creates readonly
const colors = ["red", "green", "blue"] as const;
// Type: readonly ["red", "green", "blue"]
```

---

## ⚠️ Common Mistakes

### 1. Empty Array Without Type

```typescript
// ❌ Inferred as never[]
const items = [];
items.push("test"); // Error!

// ✅ Provide type annotation
const items: string[] = [];
items.push("test"); // OK
```

### 2. Tuple Assignment Issues

```typescript
// ❌ Array is not automatically a tuple
const pair = [1, "one"]; // (string | number)[]
pair.push(2); // OK - but might not be what you want

// ✅ Explicitly type as tuple
const pair: [number, string] = [1, "one"];
pair.push(2); // ❌ Error with readonly, or push works but length issue
```

### 3. Accessing Out of Bounds

```typescript
const numbers: number[] = [1, 2, 3];

// TypeScript doesn't catch this at compile time
const value = numbers[10]; // undefined at runtime

// ✅ Use optional chaining or checks
const value = numbers[10]; // number (TypeScript assumes it exists)
if (value !== undefined) {
  console.log(value);
}

// Enable noUncheckedIndexedAccess for safer access
// Then: const value: number | undefined = numbers[10];
```

---

## 🔧 Best Practices

### 1. Prefer Type[] Over Array<Type>

```typescript
// ✅ More common and concise
let numbers: number[] = [1, 2, 3];

// Use Array<> for complex types
let data: Array<{ id: number; name: string }> = [];
```

### 2. Use Tuples for Fixed Structures

```typescript
// ✅ Tuple when structure is known
type Coordinate = [number, number];
type Response = [data: User, status: number];

// Use arrays for variable length
type Scores = number[];
```

### 3. Readonly by Default

```typescript
// ✅ Prevent accidental mutations
function processScores(scores: readonly number[]): number {
  // scores.push(100); // ❌ Error
  return scores.reduce((a, b) => a + b, 0);
}
```

---

## 🏋️ Practice Exercises

### Exercise 1: Type the Arrays

Add types to these declarations:

```typescript
const fibonacci = [0, 1, 1, 2, 3, 5, 8];
const users = [
  { name: "Alice", age: 30 },
  { name: "Bob", age: 25 },
];
const config = ["debug", true, 5000];
```

### Exercise 2: Create a Tuple Type

Create a tuple type for a function that returns success/failure:

```typescript
// Type should be: [success: boolean, message: string, data?: any]
function validate(input: string): ??? {
  if (input.length > 0) {
    return [true, "Valid", { input }];
  }
  return [false, "Empty input"];
}
```

---

## ❓ Interview Questions

**Q1: What's the difference between an array and a tuple?**

> Arrays can have any length with same type elements. Tuples have fixed length with specific types at each position. `number[]` vs `[number, string]`.

**Q2: How do you make an array readonly?**

> Use `readonly Type[]`, `ReadonlyArray<Type>`, or `as const`. Readonly arrays don't have mutating methods like push, pop, splice.

**Q3: What is `as const` assertion?**

> `as const` makes the value deeply readonly and uses literal types instead of widened types. `[1, 2] as const` becomes `readonly [1, 2]` instead of `number[]`.

**Q4: How do you type an array of objects?**

> Use an interface or type alias: `interface User { name: string }` then `User[]` or inline: `{ name: string }[]`.

**Q5: What's `noUncheckedIndexedAccess`?**

> A compiler option that makes array/object index access return `T | undefined` instead of `T`, forcing null checks.

---

## 🔑 Key Takeaways

> ### 💡 Summary
>
> - **Arrays**: `Type[]` or `Array<Type>` for variable-length collections
> - **Tuples**: `[Type1, Type2]` for fixed-length with specific types
> - **readonly**: Prevents mutations
> - **as const**: Creates readonly literal types
> - **Named tuples**: Improve readability
> - **Always type empty arrays**

---

**Next:** [06 - Objects and Interfaces](./06_Objects_and_Interfaces.md) - Learn about object types!
