# 06. Keeping Components Pure

> Understanding purity in React components - the foundation for predictable UIs.

---

## 🎯 What is a Pure Component?

A **pure component** is like a mathematical function: given the same inputs (props), it always returns the same output (JSX). It doesn't change any objects or variables that existed before it was called.

```jsx
// Pure function - same input, same output
function double(number) {
  return number * 2;
}

double(5); // Always returns 10
double(5); // Always returns 10

// Pure component - same props, same JSX
function Greeting({ name }) {
  return <h1>Hello, {name}!</h1>;
}

<Greeting name="Alice" />  // Always renders "Hello, Alice!"
<Greeting name="Alice" />  // Always renders "Hello, Alice!"
```

---

## 🤔 Why Purity Matters

### The Problems with Impure Components

```jsx
let guestCount = 0;  // External variable

// ❌ IMPURE - Modifies external variable
function GuestCounter({ name }) {
  guestCount++;  // Side effect!
  return <p>Guest #{guestCount}: {name}</p>;
}

// Using it:
<GuestCounter name="Alice" />  // Guest #1: Alice
<GuestCounter name="Bob" />    // Guest #2: Bob
<GuestCounter name="Alice" />  // Guest #3: Alice  (not #1!)
```

**Problems:**

- ❌ Unpredictable output - same props give different results
- ❌ Order-dependent - results change based on call order
- ❌ Hard to debug - hidden dependencies
- ❌ Can't safely re-render - each render changes state

### The Benefits of Pure Components

```jsx
// ✅ PURE - Same input, same output
function GuestCard({ name, guestNumber }) {
  return <p>Guest #{guestNumber}: {name}</p>;
}

// Using it:
<GuestCard name="Alice" guestNumber={1} />  // Always: Guest #1: Alice
<GuestCard name="Alice" guestNumber={1} />  // Always: Guest #1: Alice
```

**Benefits:**

- ✅ Predictable - same props always produce same output
- ✅ Cacheable - React can skip re-renders when props don't change
- ✅ Safe to re-render - no hidden side effects
- ✅ Server-compatible - can render on server without issues
- ✅ Easier to test - input/output relationship is clear

---

## 📚 Core Concepts

### 1. The Rules of Purity

```
┌─────────────────────────────────────────────────────────────┐
│                 RULES OF PURE COMPONENTS                     │
├─────────────────────────────────────────────────────────────┤
│                                                              │
│  1. SAME INPUT → SAME OUTPUT                                │
│     Given the same props, always return the same JSX        │
│                                                              │
│  2. NO SIDE EFFECTS DURING RENDER                           │
│     Don't modify variables outside the component            │
│     Don't make network requests                             │
│     Don't modify the DOM directly                           │
│                                                              │
│  3. DON'T MUTATE INPUTS                                     │
│     Props and state should be treated as read-only          │
│     Never modify them directly                              │
│                                                              │
└─────────────────────────────────────────────────────────────┘
```

### 2. Identifying Side Effects

```jsx
// ❌ Side Effect: Modifying external variable
let count = 0;
function Counter() {
  count++; // BAD!
  return <p>{count}</p>;
}

// ❌ Side Effect: Modifying passed object
function Profile({ user }) {
  user.name = "Modified"; // BAD!
  return <p>{user.name}</p>;
}

// ❌ Side Effect: DOM manipulation
function Title({ text }) {
  document.title = text; // BAD during render!
  return <h1>{text}</h1>;
}

// ❌ Side Effect: Network request
function UserData({ userId }) {
  fetch(`/api/users/${userId}`); // BAD during render!
  return <div>Loading...</div>;
}

// ❌ Side Effect: Generating random values
function RandomColor() {
  const color = Math.random() > 0.5 ? "red" : "blue"; // BAD!
  return <div style={{ color }}>Text</div>;
}
```

### 3. Local Mutation is OK

It's fine to modify variables and objects you've just created during rendering:

```jsx
// ✅ OK - Modifying locally created variables
function ProductList({ products }) {
  const items = []; // Created inside component

  for (let product of products) {
    items.push(<li key={product.id}>{product.name}</li>);
  }

  return <ul>{items}</ul>;
}

// ✅ OK - Modifying locally created object
function createGreeting(name) {
  const greeting = {}; // Created here
  greeting.message = `Hello, ${name}!`;
  greeting.timestamp = Date.now();
  return greeting;
}

// ✅ OK - Building up JSX
function Table({ rows }) {
  const tableRows = rows.map((row) => (
    <tr key={row.id}>
      <td>{row.name}</td>
      <td>{row.value}</td>
    </tr>
  ));

  return (
    <table>
      <tbody>{tableRows}</tbody>
    </table>
  );
}
```

### 4. Where Side Effects Belong

Side effects should happen **outside of rendering** - in event handlers or useEffect:

```jsx
import { useState, useEffect } from "react";

function UserProfile({ userId }) {
  const [user, setUser] = useState(null);

  // ✅ Side effect in useEffect (after render)
  useEffect(() => {
    fetch(`/api/users/${userId}`)
      .then((res) => res.json())
      .then((data) => setUser(data));
  }, [userId]);

  // ✅ Side effect in event handler (user action)
  const handleSave = () => {
    fetch(`/api/users/${userId}`, {
      method: "PUT",
      body: JSON.stringify(user),
    });
  };

  // Pure render - no side effects
  if (!user) return <p>Loading...</p>;

  return (
    <div>
      <h1>{user.name}</h1>
      <button onClick={handleSave}>Save</button>
    </div>
  );
}
```

```
┌─────────────────────────────────────────────────────────────┐
│              WHERE SIDE EFFECTS BELONG                       │
├─────────────────────────────────────────────────────────────┤
│                                                              │
│  RENDERING (Component body)                                  │
│  └── ❌ NO side effects allowed                             │
│                                                              │
│  EVENT HANDLERS (onClick, onSubmit, etc.)                   │
│  └── ✅ Side effects OK (user triggered)                    │
│                                                              │
│  useEffect                                                   │
│  └── ✅ Side effects OK (after render)                      │
│                                                              │
└─────────────────────────────────────────────────────────────┘
```

### 5. React StrictMode

React's StrictMode helps catch purity violations by double-invoking your components:

```jsx
// main.jsx
import { StrictMode } from "react";
import { createRoot } from "react-dom/client";

createRoot(document.getElementById("root")).render(
  <StrictMode>
    <App />
  </StrictMode>
);
```

```jsx
let renderCount = 0;

// With StrictMode, React calls this TWICE
function ImpureComponent() {
  renderCount++; // First call: 1, Second call: 2
  console.log("Render count:", renderCount);
  return <p>Count: {renderCount}</p>;
}

// You'll see inconsistent behavior, revealing the impurity!
```

**StrictMode Benefits:**

- Double-renders in development to catch impure renders
- Double-runs effects to check cleanup
- Warns about deprecated APIs
- Only runs in development (no production impact)

---

## 💻 Demo Code

### Pure vs Impure Examples

```jsx
import { useState, useEffect } from "react";

// ========================================
// EXAMPLE 1: Counter Component
// ========================================

// ❌ IMPURE VERSION
let globalCount = 0;

function ImpureCounter() {
  globalCount++; // Mutating external variable
  return <p>Count: {globalCount}</p>;
}

// ✅ PURE VERSION
function PureCounter({ count }) {
  return <p>Count: {count}</p>; // Only uses props
}

// ========================================
// EXAMPLE 2: Time Display
// ========================================

// ❌ IMPURE VERSION
function ImpureTime() {
  const now = new Date(); // Different result each render
  return <p>Time: {now.toLocaleTimeString()}</p>;
}

// ✅ PURE VERSION
function PureTime({ time }) {
  return <p>Time: {time.toLocaleTimeString()}</p>;
}

// Side effect handled separately
function TimeContainer() {
  const [time, setTime] = useState(new Date());

  useEffect(() => {
    const interval = setInterval(() => {
      setTime(new Date());
    }, 1000);
    return () => clearInterval(interval);
  }, []);

  return <PureTime time={time} />;
}

// ========================================
// EXAMPLE 3: User List
// ========================================

// ❌ IMPURE VERSION
function ImpureUserList({ users }) {
  users.sort((a, b) => a.name.localeCompare(b.name)); // Mutates prop!
  return (
    <ul>
      {users.map((user) => (
        <li key={user.id}>{user.name}</li>
      ))}
    </ul>
  );
}

// ✅ PURE VERSION
function PureUserList({ users }) {
  const sortedUsers = [...users].sort((a, b) => a.name.localeCompare(b.name)); // Creates new array

  return (
    <ul>
      {sortedUsers.map((user) => (
        <li key={user.id}>{user.name}</li>
      ))}
    </ul>
  );
}

// ========================================
// EXAMPLE 4: Shopping Cart Item
// ========================================

// ❌ IMPURE VERSION
function ImpureCartItem({ item, cart }) {
  item.total = item.price * item.quantity; // Mutates item!
  cart.items.push(item); // Mutates cart!

  return <div>${item.total}</div>;
}

// ✅ PURE VERSION
function PureCartItem({ item }) {
  const total = item.price * item.quantity; // Computed value

  return (
    <div className="cart-item">
      <span>{item.name}</span>
      <span>
        {item.quantity} × ${item.price}
      </span>
      <span>${total}</span>
    </div>
  );
}
```

### Complete Example: Todo App

```jsx
import { useState } from "react";

// ✅ All components are pure

function TodoItem({ todo, onToggle, onDelete }) {
  // Pure: renders based only on props
  return (
    <li className={todo.completed ? "completed" : ""}>
      <input
        type="checkbox"
        checked={todo.completed}
        onChange={() => onToggle(todo.id)} // Event handler, not render
      />
      <span>{todo.text}</span>
      <button onClick={() => onDelete(todo.id)}>Delete</button>
    </li>
  );
}

function TodoList({ todos, onToggle, onDelete }) {
  // Pure: same todos = same list
  return (
    <ul>
      {todos.map((todo) => (
        <TodoItem
          key={todo.id}
          todo={todo}
          onToggle={onToggle}
          onDelete={onDelete}
        />
      ))}
    </ul>
  );
}

function TodoStats({ todos }) {
  // Pure: computations are fine
  const total = todos.length;
  const completed = todos.filter((t) => t.completed).length;
  const pending = total - completed;

  return (
    <div className="stats">
      <span>Total: {total}</span>
      <span>Completed: {completed}</span>
      <span>Pending: {pending}</span>
    </div>
  );
}

function TodoApp() {
  const [todos, setTodos] = useState([
    { id: 1, text: "Learn React", completed: true },
    { id: 2, text: "Build project", completed: false },
  ]);
  const [newTodo, setNewTodo] = useState("");

  // Event handlers contain side effects (state changes)
  const handleAdd = () => {
    if (!newTodo.trim()) return;

    setTodos([...todos, { id: Date.now(), text: newTodo, completed: false }]);
    setNewTodo("");
  };

  const handleToggle = (id) => {
    setTodos(
      todos.map((todo) =>
        todo.id === id ? { ...todo, completed: !todo.completed } : todo
      )
    );
  };

  const handleDelete = (id) => {
    setTodos(todos.filter((todo) => todo.id !== id));
  };

  // Render is pure
  return (
    <div className="todo-app">
      <h1>My Todos</h1>

      <div className="add-todo">
        <input
          value={newTodo}
          onChange={(e) => setNewTodo(e.target.value)}
          placeholder="Add new todo..."
        />
        <button onClick={handleAdd}>Add</button>
      </div>

      <TodoStats todos={todos} />
      <TodoList todos={todos} onToggle={handleToggle} onDelete={handleDelete} />
    </div>
  );
}

export default TodoApp;
```

---

## ✅ Best Practices

### 1. Keep Rendering Pure

```jsx
// ✅ Good - Pure render
function Greeting({ name, time }) {
  const greeting = time < 12 ? "Good morning" : "Good afternoon";
  return (
    <h1>
      {greeting}, {name}!
    </h1>
  );
}

// ❌ Bad - Impure render
function Greeting({ name }) {
  const time = new Date().getHours(); // Different each render
  const greeting = time < 12 ? "Good morning" : "Good afternoon";
  return (
    <h1>
      {greeting}, {name}!
    </h1>
  );
}
```

### 2. Don't Mutate Props or State

```jsx
// ❌ Bad - Mutating props
function SortedList({ items }) {
  items.sort(); // Mutates original array!
  return (
    <ul>
      {items.map((i) => (
        <li key={i}>{i}</li>
      ))}
    </ul>
  );
}

// ✅ Good - Create new array
function SortedList({ items }) {
  const sorted = [...items].sort(); // New array
  return (
    <ul>
      {sorted.map((i) => (
        <li key={i}>{i}</li>
      ))}
    </ul>
  );
}
```

### 3. Use Event Handlers for Side Effects

```jsx
function Form() {
  const [data, setData] = useState({});

  // ✅ Side effects in event handler
  const handleSubmit = async (e) => {
    e.preventDefault();
    await fetch("/api/submit", {
      method: "POST",
      body: JSON.stringify(data),
    });
  };

  return <form onSubmit={handleSubmit}>...</form>;
}
```

### 4. Use useEffect for Synchronization

```jsx
function DocumentTitle({ title }) {
  // ✅ Side effect in useEffect
  useEffect(() => {
    document.title = title;
  }, [title]);

  return null; // Or some JSX
}
```

---

## ❌ Common Mistakes

### 1. Modifying Objects/Arrays

```jsx
// ❌ Bad
function addItem(items, newItem) {
  items.push(newItem); // Mutates!
  return items;
}

// ✅ Good
function addItem(items, newItem) {
  return [...items, newItem]; // New array
}
```

### 2. Using Random/Time in Render

```jsx
// ❌ Bad
function RandomBackground() {
  const color = `hsl(${Math.random() * 360}, 50%, 50%)`;
  return <div style={{ background: color }}>Content</div>;
}

// ✅ Good - Use state
function RandomBackground() {
  const [color] = useState(() => `hsl(${Math.random() * 360}, 50%, 50%)`);
  return <div style={{ background: color }}>Content</div>;
}
```

### 3. API Calls in Render

```jsx
// ❌ Bad
function UserProfile({ userId }) {
  const user = fetch(`/api/users/${userId}`); // Called every render!
  return <div>{user.name}</div>;
}

// ✅ Good
function UserProfile({ userId }) {
  const [user, setUser] = useState(null);

  useEffect(() => {
    fetch(`/api/users/${userId}`)
      .then((res) => res.json())
      .then(setUser);
  }, [userId]);

  return user ? <div>{user.name}</div> : <p>Loading...</p>;
}
```

---

## 🏋️ Practice Exercises

### Exercise 1: Spot the Impurity

Which of these components are impure? Fix them.

```jsx
// Component A
let messageCount = 0;
function Message({ text }) {
  messageCount++;
  return (
    <p>
      Message #{messageCount}: {text}
    </p>
  );
}

// Component B
function Timestamp({ date }) {
  return <p>Date: {date.toLocaleDateString()}</p>;
}

// Component C
function RandomGreeting({ name }) {
  const greetings = ["Hello", "Hi", "Hey"];
  const greeting = greetings[Math.floor(Math.random() * 3)];
  return (
    <p>
      {greeting}, {name}!
    </p>
  );
}
```

### Exercise 2: Refactor to Pure

Convert this impure component to pure:

```jsx
function ShoppingCart({ items }) {
  let total = 0;

  items.forEach((item) => {
    item.subtotal = item.price * item.quantity;
    total += item.subtotal;
  });

  return (
    <div>
      {items.map((item) => (
        <div key={item.id}>
          {item.name}: ${item.subtotal}
        </div>
      ))}
      <p>Total: ${total}</p>
    </div>
  );
}
```

### Exercise 3: Build a Pure Component

Create a pure `PriceDisplay` component that:

- Takes `amount` and `currency` as props
- Formats the price correctly
- Handles different currencies (USD, EUR, GBP)

---

## 🎤 Interview Q&A

**Q1: What is a pure component in React?**

> A pure component is one that always returns the same output for the same props, without modifying anything outside of itself during rendering. It has no side effects during the render phase.

**Q2: Why is purity important in React?**

> Purity enables React to safely re-render components, skip unnecessary renders through memoization, render on the server, and maintain predictable application behavior. It also makes components easier to test and debug.

**Q3: What are side effects and where should they go?**

> Side effects are operations that affect something outside the component (API calls, DOM manipulation, subscriptions). They should go in event handlers (user-triggered actions) or useEffect (synchronization with external systems), not in the render phase.

**Q4: What is React.StrictMode and how does it help with purity?**

> StrictMode is a development tool that double-invokes component functions to detect impure renders. If a component produces different results on the second call, it reveals a purity violation. It only runs in development.

**Q5: Is it okay to mutate variables inside a component?**

> Yes, if you create the variable during the current render. It's fine to build up arrays or objects locally. What's not okay is mutating props, state, or variables created outside the component.

---

## 📎 References

- [React Docs - Keeping Components Pure](https://react.dev/learn/keeping-components-pure)
- [React Docs - Side Effects](https://react.dev/learn/synchronizing-with-effects)
- [React Docs - StrictMode](https://react.dev/reference/react/StrictMode)
- [Pure Functions in JavaScript](https://www.freecodecamp.org/news/what-is-a-pure-function-in-javascript-acb887375dfe/)

---

**Previous Topic**: [← 05. Components](./05_Components.md)

**Next Topic**: [07. Props →](./07_Props.md)
