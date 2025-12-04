# 08. State and useState

> Managing dynamic data within components - making your UI interactive.

---

## 🎯 What is State?

**State** is data that changes over time and affects what's rendered. Unlike props (which come from parents), state is owned and managed by the component itself.

```jsx
import { useState } from "react";

function Counter() {
  const [count, setCount] = useState(0);

  return (
    <div>
      <p>Count: {count}</p>
      <button onClick={() => setCount(count + 1)}>Increment</button>
    </div>
  );
}
```

---

## 🤔 Why Do We Need State?

### Without State (The Problem)

```jsx
// ❌ This won't work - regular variables don't trigger re-renders
function BrokenCounter() {
  let count = 0;

  const handleClick = () => {
    count++; // Updates variable, but UI doesn't change!
    console.log(count); // Shows updated value
  };

  return (
    <div>
      <p>Count: {count}</p> {/* Always shows 0 */}
      <button onClick={handleClick}>Add</button>
    </div>
  );
}
```

**Why it doesn't work:**

1. Local variables don't persist between renders
2. Changing local variables doesn't trigger a re-render

### With State (The Solution)

```jsx
// ✅ State persists and triggers re-renders
function WorkingCounter() {
  const [count, setCount] = useState(0);

  const handleClick = () => {
    setCount(count + 1); // Triggers re-render with new value
  };

  return (
    <div>
      <p>Count: {count}</p> {/* Updates! */}
      <button onClick={handleClick}>Add</button>
    </div>
  );
}
```

---

## 📚 Core Concepts

### 1. The useState Hook

```jsx
import { useState } from "react";

function Component() {
  // Syntax: const [value, setValue] = useState(initialValue);
  const [count, setCount] = useState(0);
  //      ↑       ↑                    ↑
  //   current  updater           initial
  //   value    function          value

  return <p>{count}</p>;
}
```

**useState Returns an Array:**

1. **Current state value** - The data to display
2. **Setter function** - Function to update the state

### 2. Multiple State Variables

```jsx
function UserForm() {
  // Each piece of state is independent
  const [name, setName] = useState("");
  const [email, setEmail] = useState("");
  const [age, setAge] = useState(0);
  const [isSubscribed, setIsSubscribed] = useState(false);

  return (
    <form>
      <input value={name} onChange={(e) => setName(e.target.value)} />
      <input value={email} onChange={(e) => setEmail(e.target.value)} />
      <input
        type="number"
        value={age}
        onChange={(e) => setAge(Number(e.target.value))}
      />
      <input
        type="checkbox"
        checked={isSubscribed}
        onChange={(e) => setIsSubscribed(e.target.checked)}
      />
    </form>
  );
}
```

### 3. Initial State Types

```jsx
function StateTypesDemo() {
  // String
  const [name, setName] = useState("");
  const [text, setText] = useState("Hello");

  // Number
  const [count, setCount] = useState(0);
  const [price, setPrice] = useState(19.99);

  // Boolean
  const [isOpen, setIsOpen] = useState(false);
  const [isLoading, setIsLoading] = useState(true);

  // Array
  const [items, setItems] = useState([]);
  const [users, setUsers] = useState(["Alice", "Bob"]);

  // Object
  const [user, setUser] = useState({ name: "", email: "" });
  const [settings, setSettings] = useState({
    theme: "dark",
    notifications: true,
  });

  // null/undefined
  const [selectedItem, setSelectedItem] = useState(null);
  const [data, setData] = useState(undefined);

  // ... render
}
```

### 4. Lazy Initial State

For expensive computations, use a function to set initial state:

```jsx
// ❌ Bad - runs on every render
function ExpensiveComponent() {
  const [data, setData] = useState(expensiveCalculation());
}

// ✅ Good - runs only on initial render
function ExpensiveComponent() {
  const [data, setData] = useState(() => expensiveCalculation());
}

// Example: Reading from localStorage
function App() {
  const [theme, setTheme] = useState(() => {
    const saved = localStorage.getItem("theme");
    return saved || "light";
  });
}
```

### 5. Updating State

#### Direct Updates

```jsx
function Counter() {
  const [count, setCount] = useState(0);

  return (
    <div>
      <p>{count}</p>
      <button onClick={() => setCount(count + 1)}>Add 1</button>
      <button onClick={() => setCount(count - 1)}>Subtract 1</button>
      <button onClick={() => setCount(0)}>Reset</button>
    </div>
  );
}
```

#### Functional Updates (Recommended)

When new state depends on previous state, use the functional form:

```jsx
function Counter() {
  const [count, setCount] = useState(0);

  const handleTripleIncrement = () => {
    // ❌ Bad - all use the same stale count
    setCount(count + 1);
    setCount(count + 1);
    setCount(count + 1);
    // Result: count + 1 (not count + 3!)
  };

  const handleTripleIncrementFixed = () => {
    // ✅ Good - each uses the latest value
    setCount((c) => c + 1);
    setCount((c) => c + 1);
    setCount((c) => c + 1);
    // Result: count + 3
  };

  return (
    <div>
      <p>{count}</p>
      <button onClick={handleTripleIncrementFixed}>Add 3</button>
    </div>
  );
}
```

### 6. State vs Props Comparison

| Feature            | State            | Props                  |
| ------------------ | ---------------- | ---------------------- |
| **Ownership**      | Component itself | Parent component       |
| **Mutability**     | Can be changed   | Read-only              |
| **Purpose**        | Internal data    | External configuration |
| **Updates**        | setState/setter  | Parent re-renders      |
| **Initialization** | useState()       | JSX attributes         |

```jsx
function Parent() {
  const [name, setName] = useState("Alice"); // State

  return (
    <Child
      name={name} // Passed as prop
      onNameChange={setName} // Pass setter as prop
    />
  );
}

function Child({ name, onNameChange }) {
  // name is a PROP here (read-only from Child's perspective)
  // But Child can request changes via onNameChange

  return <input value={name} onChange={(e) => onNameChange(e.target.value)} />;
}
```

### 7. Lifting State Up

When siblings need to share state, lift it to their common parent:

```jsx
// Before: State in each component (can't sync)
function TemperatureInput({ scale }) {
  const [temperature, setTemperature] = useState('');
  return <input value={temperature} onChange={...} />;
}

// After: State lifted to parent
function Calculator() {
  const [temperature, setTemperature] = useState('');
  const [scale, setScale] = useState('c');

  const celsius = scale === 'c' ? temperature : toCelsius(temperature);
  const fahrenheit = scale === 'f' ? temperature : toFahrenheit(temperature);

  return (
    <div>
      <TemperatureInput
        scale="c"
        temperature={celsius}
        onTemperatureChange={(t) => {
          setScale('c');
          setTemperature(t);
        }}
      />
      <TemperatureInput
        scale="f"
        temperature={fahrenheit}
        onTemperatureChange={(t) => {
          setScale('f');
          setTemperature(t);
        }}
      />
    </div>
  );
}
```

---

## 💻 Demo Code

### Complete Example: Todo Application

```jsx
import { useState } from "react";

function TodoApp() {
  // State for the list of todos
  const [todos, setTodos] = useState([
    { id: 1, text: "Learn React", completed: true },
    { id: 2, text: "Build a project", completed: false },
  ]);

  // State for the input field
  const [newTodo, setNewTodo] = useState("");

  // State for filter
  const [filter, setFilter] = useState("all"); // 'all', 'active', 'completed'

  // Add new todo
  const addTodo = () => {
    if (newTodo.trim() === "") return;

    setTodos((prevTodos) => [
      ...prevTodos,
      {
        id: Date.now(),
        text: newTodo.trim(),
        completed: false,
      },
    ]);
    setNewTodo(""); // Clear input
  };

  // Toggle todo completion
  const toggleTodo = (id) => {
    setTodos((prevTodos) =>
      prevTodos.map((todo) =>
        todo.id === id ? { ...todo, completed: !todo.completed } : todo
      )
    );
  };

  // Delete todo
  const deleteTodo = (id) => {
    setTodos((prevTodos) => prevTodos.filter((todo) => todo.id !== id));
  };

  // Edit todo
  const editTodo = (id, newText) => {
    setTodos((prevTodos) =>
      prevTodos.map((todo) =>
        todo.id === id ? { ...todo, text: newText } : todo
      )
    );
  };

  // Clear completed
  const clearCompleted = () => {
    setTodos((prevTodos) => prevTodos.filter((todo) => !todo.completed));
  };

  // Filter todos
  const filteredTodos = todos.filter((todo) => {
    if (filter === "active") return !todo.completed;
    if (filter === "completed") return todo.completed;
    return true;
  });

  // Statistics
  const totalCount = todos.length;
  const completedCount = todos.filter((t) => t.completed).length;
  const activeCount = totalCount - completedCount;

  return (
    <div className="todo-app">
      <h1>My Todos</h1>

      {/* Add Todo Form */}
      <div className="add-todo">
        <input
          type="text"
          value={newTodo}
          onChange={(e) => setNewTodo(e.target.value)}
          onKeyPress={(e) => e.key === "Enter" && addTodo()}
          placeholder="What needs to be done?"
        />
        <button onClick={addTodo}>Add</button>
      </div>

      {/* Filter Buttons */}
      <div className="filters">
        <button
          className={filter === "all" ? "active" : ""}
          onClick={() => setFilter("all")}
        >
          All ({totalCount})
        </button>
        <button
          className={filter === "active" ? "active" : ""}
          onClick={() => setFilter("active")}
        >
          Active ({activeCount})
        </button>
        <button
          className={filter === "completed" ? "active" : ""}
          onClick={() => setFilter("completed")}
        >
          Completed ({completedCount})
        </button>
      </div>

      {/* Todo List */}
      <ul className="todo-list">
        {filteredTodos.map((todo) => (
          <TodoItem
            key={todo.id}
            todo={todo}
            onToggle={() => toggleTodo(todo.id)}
            onDelete={() => deleteTodo(todo.id)}
            onEdit={(text) => editTodo(todo.id, text)}
          />
        ))}
      </ul>

      {/* Footer */}
      {completedCount > 0 && (
        <button onClick={clearCompleted}>Clear Completed</button>
      )}
    </div>
  );
}

function TodoItem({ todo, onToggle, onDelete, onEdit }) {
  const [isEditing, setIsEditing] = useState(false);
  const [editText, setEditText] = useState(todo.text);

  const handleSave = () => {
    if (editText.trim()) {
      onEdit(editText.trim());
      setIsEditing(false);
    }
  };

  const handleCancel = () => {
    setEditText(todo.text);
    setIsEditing(false);
  };

  if (isEditing) {
    return (
      <li className="editing">
        <input
          type="text"
          value={editText}
          onChange={(e) => setEditText(e.target.value)}
          onKeyPress={(e) => e.key === "Enter" && handleSave()}
          onKeyDown={(e) => e.key === "Escape" && handleCancel()}
          autoFocus
        />
        <button onClick={handleSave}>Save</button>
        <button onClick={handleCancel}>Cancel</button>
      </li>
    );
  }

  return (
    <li className={todo.completed ? "completed" : ""}>
      <input type="checkbox" checked={todo.completed} onChange={onToggle} />
      <span onDoubleClick={() => setIsEditing(true)}>{todo.text}</span>
      <button onClick={onDelete}>Delete</button>
    </li>
  );
}

export default TodoApp;
```

### Example: Shopping Cart State

```jsx
import { useState } from "react";

function ShoppingCart() {
  const [cart, setCart] = useState([]);

  const addToCart = (product) => {
    setCart((prevCart) => {
      const existingItem = prevCart.find((item) => item.id === product.id);

      if (existingItem) {
        // Increase quantity
        return prevCart.map((item) =>
          item.id === product.id
            ? { ...item, quantity: item.quantity + 1 }
            : item
        );
      }

      // Add new item
      return [...prevCart, { ...product, quantity: 1 }];
    });
  };

  const removeFromCart = (productId) => {
    setCart((prevCart) => prevCart.filter((item) => item.id !== productId));
  };

  const updateQuantity = (productId, quantity) => {
    if (quantity <= 0) {
      removeFromCart(productId);
      return;
    }

    setCart((prevCart) =>
      prevCart.map((item) =>
        item.id === productId ? { ...item, quantity } : item
      )
    );
  };

  const clearCart = () => {
    setCart([]);
  };

  // Computed values
  const totalItems = cart.reduce((sum, item) => sum + item.quantity, 0);
  const totalPrice = cart.reduce(
    (sum, item) => sum + item.price * item.quantity,
    0
  );

  return (
    <div>
      <h2>Shopping Cart ({totalItems} items)</h2>

      {cart.length === 0 ? (
        <p>Your cart is empty</p>
      ) : (
        <>
          {cart.map((item) => (
            <div key={item.id} className="cart-item">
              <span>{item.name}</span>
              <span>${item.price}</span>
              <button
                onClick={() => updateQuantity(item.id, item.quantity - 1)}
              >
                -
              </button>
              <span>{item.quantity}</span>
              <button
                onClick={() => updateQuantity(item.id, item.quantity + 1)}
              >
                +
              </button>
              <button onClick={() => removeFromCart(item.id)}>Remove</button>
            </div>
          ))}

          <div className="cart-total">
            <strong>Total: ${totalPrice.toFixed(2)}</strong>
          </div>

          <button onClick={clearCart}>Clear Cart</button>
        </>
      )}
    </div>
  );
}
```

---

## ✅ Best Practices

### 1. Use Functional Updates for Dependent State

```jsx
// ✅ Always use functional form when state depends on previous value
const increment = () => setCount((c) => c + 1);
const addItem = (item) => setItems((prev) => [...prev, item]);
```

### 2. Keep State Minimal

```jsx
// ❌ Bad - redundant state
const [items, setItems] = useState([]);
const [count, setCount] = useState(0); // Can be derived!

// ✅ Good - derive count
const [items, setItems] = useState([]);
const count = items.length; // Computed, not state
```

### 3. Group Related State

```jsx
// ✅ For related values, consider an object
const [formData, setFormData] = useState({
  name: "",
  email: "",
  phone: "",
});

// Update one field
setFormData((prev) => ({ ...prev, name: "Alice" }));
```

### 4. Don't Mirror Props in State

```jsx
// ❌ Bad - props mirrored in state
function Message({ text }) {
  const [message, setMessage] = useState(text); // Don't do this!
  return <p>{message}</p>;
}

// ✅ Good - use props directly
function Message({ text }) {
  return <p>{text}</p>;
}
```

---

## ❌ Common Mistakes

### 1. Direct State Mutation

```jsx
// ❌ Wrong - mutating state directly
const [user, setUser] = useState({ name: "Alice", age: 25 });
user.age = 26; // WRONG!
setUser(user); // Won't trigger re-render properly

// ✅ Correct - create new object
setUser({ ...user, age: 26 });
setUser((prev) => ({ ...prev, age: 26 }));
```

### 2. Stale State in Closures

```jsx
// ❌ Bug - stale closure
function Timer() {
  const [count, setCount] = useState(0);

  useEffect(() => {
    const interval = setInterval(() => {
      setCount(count + 1); // Always uses initial count!
    }, 1000);
    return () => clearInterval(interval);
  }, []); // Empty deps = stale count
}

// ✅ Fix - functional update
useEffect(() => {
  const interval = setInterval(() => {
    setCount((c) => c + 1); // Always gets latest
  }, 1000);
  return () => clearInterval(interval);
}, []);
```

### 3. Forgetting State is Async

```jsx
// ❌ Wrong - state doesn't update immediately
const handleClick = () => {
  setCount(count + 1);
  console.log(count); // Still old value!
};

// ✅ Use useEffect to react to state changes
useEffect(() => {
  console.log("Count changed:", count);
}, [count]);
```

---

## 🏋️ Practice Exercises

### Exercise 1: Toggle Button

Create a component with a button that toggles between "ON" and "OFF".

### Exercise 2: Counter with Min/Max

Build a counter that:

- Can't go below 0
- Can't go above 10
- Shows different colors at extremes

### Exercise 3: Form with Multiple Fields

Create a signup form with name, email, password fields, all managed with state.

---

## 🎤 Interview Q&A

**Q1: What is useState and how does it work?**

> useState is a Hook that lets you add state to functional components. It returns an array with the current state value and a function to update it. When you call the setter, React schedules a re-render with the new state.

**Q2: Why use functional updates with setState?**

> Functional updates (setCount(c => c + 1)) ensure you're working with the latest state value. Direct updates (setCount(count + 1)) may use stale values in closures or when batching multiple updates.

**Q3: Can you call useState conditionally?**

> No! Hooks must be called in the same order every render. Conditional calls would break this rule. Instead, manage the condition inside the component logic.

**Q4: What happens when you call setState with the same value?**

> React uses Object.is comparison. If the new value is the same, React may skip re-rendering as an optimization. For objects/arrays, you must create new references to trigger updates.

**Q5: When should you lift state up?**

> Lift state when multiple components need to share it, or when a parent needs to coordinate between siblings. The state should live in the closest common ancestor of all components that need it.

---

## 📎 References

- [React Docs - useState](https://react.dev/reference/react/useState)
- [React Docs - State: A Component's Memory](https://react.dev/learn/state-a-components-memory)
- [React Docs - Sharing State Between Components](https://react.dev/learn/sharing-state-between-components)

---

**Previous Topic**: [← 07. Props](./07_Props.md)

**Next Topic**: [09. State as Snapshot →](./09_State_as_Snapshot.md)
