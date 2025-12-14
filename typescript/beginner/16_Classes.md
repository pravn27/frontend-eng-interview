# 16 - Classes

## 📖 Classes in TypeScript

TypeScript enhances JavaScript classes with type annotations, access modifiers, and other features.

```typescript
class Person {
  name: string;
  age: number;

  constructor(name: string, age: number) {
    this.name = name;
    this.age = age;
  }

  greet(): string {
    return `Hello, I'm ${this.name}`;
  }
}

const person = new Person("Alice", 30);
console.log(person.greet()); // "Hello, I'm Alice"
```

---

## 🎯 Access Modifiers

Control property and method visibility.

```typescript
class User {
  public name: string; // Accessible anywhere (default)
  private password: string; // Only within class
  protected email: string; // Within class and subclasses
  readonly id: number; // Can't be modified after construction

  constructor(name: string, email: string, password: string) {
    this.id = Date.now();
    this.name = name;
    this.email = email;
    this.password = password;
  }

  public getEmail(): string {
    return this.email;
  }

  private hashPassword(): string {
    return `hashed_${this.password}`;
  }
}

const user = new User("Alice", "alice@example.com", "secret");
console.log(user.name); // ✅ OK
console.log(user.password); // ❌ Error: private
console.log(user.email); // ❌ Error: protected
user.id = 123; // ❌ Error: readonly
```

---

## 📚 Parameter Properties

Shorthand for declaring and initializing properties.

```typescript
// Verbose way
class User {
  name: string;
  age: number;

  constructor(name: string, age: number) {
    this.name = name;
    this.age = age;
  }
}

// Parameter properties (shorthand)
class User {
  constructor(
    public name: string,
    public age: number,
    private password: string,
    readonly id: number = Date.now()
  ) {}
}

const user = new User("Alice", 30, "secret");
console.log(user.name); // "Alice"
console.log(user.age); // 30
```

---

## 🔷 Inheritance

```typescript
class Animal {
  constructor(public name: string) {}

  move(distance: number): void {
    console.log(`${this.name} moved ${distance}m`);
  }
}

class Dog extends Animal {
  constructor(name: string, public breed: string) {
    super(name); // Must call parent constructor
  }

  bark(): void {
    console.log("Woof!");
  }

  // Override parent method
  move(distance: number): void {
    console.log("Running...");
    super.move(distance); // Call parent method
  }
}

const dog = new Dog("Rex", "German Shepherd");
dog.bark(); // "Woof!"
dog.move(10); // "Running..." then "Rex moved 10m"
```

---

## 🏛️ Abstract Classes

Define base classes that can't be instantiated directly.

```typescript
abstract class Shape {
  abstract getArea(): number; // Must be implemented by subclasses

  printArea(): void {
    console.log(`Area: ${this.getArea()}`);
  }
}

class Circle extends Shape {
  constructor(private radius: number) {
    super();
  }

  getArea(): number {
    return Math.PI * this.radius ** 2;
  }
}

class Rectangle extends Shape {
  constructor(private width: number, private height: number) {
    super();
  }

  getArea(): number {
    return this.width * this.height;
  }
}

// const shape = new Shape(); // ❌ Error: Cannot instantiate abstract class
const circle = new Circle(5);
circle.printArea(); // "Area: 78.54..."
```

---

## 🔗 Implementing Interfaces

```typescript
interface Printable {
  print(): void;
}

interface Saveable {
  save(): Promise<void>;
}

class Document implements Printable, Saveable {
  constructor(public title: string, public content: string) {}

  print(): void {
    console.log(`${this.title}\n${this.content}`);
  }

  async save(): Promise<void> {
    console.log(`Saving ${this.title}...`);
    // Save logic
  }
}

// Interface can define class structure
interface ClockInterface {
  currentTime: Date;
  setTime(d: Date): void;
}

class Clock implements ClockInterface {
  currentTime: Date = new Date();

  setTime(d: Date): void {
    this.currentTime = d;
  }
}
```

---

## 💻 Practical Examples

### 1. User Management

```typescript
interface UserData {
  id: number;
  name: string;
  email: string;
}

class User {
  private static nextId = 1;

  readonly id: number;
  public name: string;
  private _email: string;

  constructor(name: string, email: string) {
    this.id = User.nextId++;
    this.name = name;
    this._email = email;
  }

  // Getter
  get email(): string {
    return this._email;
  }

  // Setter with validation
  set email(value: string) {
    if (!value.includes("@")) {
      throw new Error("Invalid email");
    }
    this._email = value;
  }

  toJSON(): UserData {
    return {
      id: this.id,
      name: this.name,
      email: this.email,
    };
  }

  static fromJSON(data: UserData): User {
    const user = new User(data.name, data.email);
    return user;
  }
}

const user = new User("Alice", "alice@example.com");
user.email = "new@example.com"; // Uses setter
console.log(user.email); // Uses getter
```

### 2. Generic Class

```typescript
class Stack<T> {
  private items: T[] = [];

  push(item: T): void {
    this.items.push(item);
  }

  pop(): T | undefined {
    return this.items.pop();
  }

  peek(): T | undefined {
    return this.items[this.items.length - 1];
  }

  isEmpty(): boolean {
    return this.items.length === 0;
  }

  get size(): number {
    return this.items.length;
  }
}

const numberStack = new Stack<number>();
numberStack.push(1);
numberStack.push(2);
console.log(numberStack.pop()); // 2

const stringStack = new Stack<string>();
stringStack.push("hello");
stringStack.push("world");
```

### 3. Singleton Pattern

```typescript
class Database {
  private static instance: Database;
  private connection: string | null = null;

  private constructor() {
    // Private constructor prevents direct instantiation
  }

  static getInstance(): Database {
    if (!Database.instance) {
      Database.instance = new Database();
    }
    return Database.instance;
  }

  connect(connectionString: string): void {
    this.connection = connectionString;
    console.log("Connected to:", connectionString);
  }

  query(sql: string): void {
    if (!this.connection) {
      throw new Error("Not connected");
    }
    console.log("Executing:", sql);
  }
}

const db1 = Database.getInstance();
const db2 = Database.getInstance();
console.log(db1 === db2); // true - same instance
```

---

## 🔒 Static Members

```typescript
class MathUtils {
  static PI = 3.14159;

  static add(a: number, b: number): number {
    return a + b;
  }

  static multiply(a: number, b: number): number {
    return a * b;
  }
}

// Access without instantiation
console.log(MathUtils.PI); // 3.14159
console.log(MathUtils.add(2, 3)); // 5
```

---

## ⚠️ Common Mistakes

### 1. Forgetting super() in Subclass

```typescript
class Parent {
  constructor(public name: string) {}
}

// ❌ Error: Must call super()
class Child extends Parent {
  constructor(name: string, public age: number) {
    // Missing super(name)!
    this.age = age;
  }
}

// ✅ Correct
class Child extends Parent {
  constructor(name: string, public age: number) {
    super(name);
  }
}
```

### 2. Private vs JavaScript #private

```typescript
class Example {
  private tsPrivate = "TypeScript private";
  #jsPrivate = "JavaScript private";
}

// TypeScript private: checked at compile time only
// JavaScript #private: enforced at runtime too
```

---

## 🏋️ Practice Exercises

### Exercise 1: Bank Account

Create a BankAccount class with deposit, withdraw, and balance checking.

### Exercise 2: Shape Hierarchy

Create abstract Shape class with Circle, Rectangle, and Triangle subclasses.

---

## ❓ Interview Questions

**Q1: What are access modifiers in TypeScript?**

> `public` (default): accessible anywhere. `private`: only within class. `protected`: within class and subclasses. `readonly`: can't be modified after construction.

**Q2: What are parameter properties?**

> Adding access modifiers to constructor parameters automatically creates and assigns class properties: `constructor(public name: string)`.

**Q3: Abstract class vs Interface?**

> Abstract classes can have implementation and state; interfaces are purely structural. Classes can extend one abstract class but implement multiple interfaces.

**Q4: What is a getter/setter?**

> Special methods that look like properties. Getters (`get prop()`) for reading, setters (`set prop(value)`) for writing with optional validation.

**Q5: Static members vs Instance members?**

> Static members belong to the class itself, accessed without instantiation. Instance members belong to each object created from the class.

---

## 🔑 Key Takeaways

> ### 💡 Summary
>
> - **Access modifiers**: public, private, protected, readonly
> - **Parameter properties**: shorthand in constructor
> - **Inheritance**: extends for classes, implements for interfaces
> - **Abstract classes**: can't be instantiated directly
> - **Static members**: belong to class, not instances
> - **Getters/setters**: computed properties with logic

---

**Next:** [17 - Modules](./17_Modules.md) - Learn about import/export and module systems!
