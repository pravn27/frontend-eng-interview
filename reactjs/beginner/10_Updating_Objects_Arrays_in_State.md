# 10. Updating Objects and Arrays in State

> The #1 beginner mistake - learning to update state immutably.

---

## 🎯 What's the Problem?

State should be treated as **immutable** (unchangeable). When you have objects or arrays in state, you must create **new copies** instead of modifying the existing ones.

```jsx
// ❌ WRONG - Mutating state directly
const [user, setUser] = useState({ name: "Alice", age: 25 });
user.age = 26; // Mutation!
setUser(user); // Same reference, React won't detect change!

// ✅ CORRECT - Creating a new object
setUser({ ...user, age: 26 }); // New object with updated age
```

---

## 🤔 Why Can't We Mutate State?

### The Technical Reason

React uses **reference equality** to detect changes:

```jsx
const oldUser = { name: "Alice" };
const newUser = oldUser;
oldUser.name = "Bob";

// Are they the same?
console.log(oldUser === newUser); // true! Same reference

// React sees: "Same reference? No change. Skip re-render."
```

### The Practical Problems

1. **React won't re-render** - Changes aren't detected
2. **Bugs in time-travel debugging** - React DevTools show wrong history
3. **Optimization breaks** - React.memo, useMemo rely on reference checks
4. **Unpredictable behavior** - Race conditions, stale closures

---

## 📚 Core Concepts

### 1. Updating Objects

#### Basic Object Update

```jsx
const [person, setPerson] = useState({
  name: "Alice",
  age: 25,
  email: "alice@example.com",
});

// ❌ Wrong - mutation
person.age = 26;
setPerson(person);

// ✅ Correct - spread operator creates new object
setPerson({
  ...person, // Copy all existing properties
  age: 26, // Override specific property
});

// ✅ Also correct - Object.assign
setPerson(Object.assign({}, person, { age: 26 }));
```

#### Updating Multiple Properties

```jsx
setPerson({
  ...person,
  name: "Alicia",
  age: 26,
  email: "alicia@example.com",
});
```

#### Computed Property Names

```jsx
const handleChange = (field, value) => {
  setPerson({
    ...person,
    [field]: value, // Dynamic property name
  });
};

// Usage
handleChange("name", "Bob");
handleChange("age", 30);
```

### 2. Updating Nested Objects

```jsx
const [user, setUser] = useState({
  name: "Alice",
  address: {
    street: "123 Main St",
    city: "New York",
    zip: "10001",
  },
  preferences: {
    theme: "dark",
    notifications: {
      email: true,
      push: false,
    },
  },
});

// ❌ Wrong - nested mutation
user.address.city = "Boston"; // Mutates nested object!

// ✅ Correct - spread at each level
setUser({
  ...user,
  address: {
    ...user.address,
    city: "Boston",
  },
});

// ✅ Deep nested update
setUser({
  ...user,
  preferences: {
    ...user.preferences,
    notifications: {
      ...user.preferences.notifications,
      push: true,
    },
  },
});
```

### 3. Updating Arrays

#### Adding Items

```jsx
const [items, setItems] = useState(["Apple", "Banana"]);

// ❌ Wrong - push mutates array
items.push("Cherry");
setItems(items);

// ✅ Correct - spread creates new array
setItems([...items, "Cherry"]); // Add to end

// ✅ Add to beginning
setItems(["Cherry", ...items]);

// ✅ Add at specific index
const index = 1;
setItems([...items.slice(0, index), "Cherry", ...items.slice(index)]);
```

#### Removing Items

```jsx
const [todos, setTodos] = useState([
  { id: 1, text: "Learn React" },
  { id: 2, text: "Build project" },
  { id: 3, text: "Deploy app" },
]);

// ❌ Wrong - splice mutates array
todos.splice(index, 1);
setTodos(todos);

// ✅ Correct - filter creates new array
setTodos(todos.filter((todo) => todo.id !== idToRemove));

// ✅ Remove by index
setTodos(todos.filter((_, index) => index !== indexToRemove));
```

#### Updating Items

```jsx
// ❌ Wrong - direct mutation
todos[index].text = "New text";
setTodos(todos);

// ✅ Correct - map creates new array with new object
setTodos(
  todos.map((todo) =>
    todo.id === idToUpdate ? { ...todo, text: "New text" } : todo
  )
);

// ✅ Toggle a boolean property
setTodos(
  todos.map((todo) =>
    todo.id === idToToggle ? { ...todo, completed: !todo.completed } : todo
  )
);
```

#### Replacing an Item

```jsx
// ✅ Replace item at index
setItems([...items.slice(0, index), newItem, ...items.slice(index + 1)]);

// ✅ Using map (cleaner for objects)
setItems(items.map((item, i) => (i === index ? newItem : item)));
```

#### Sorting and Reversing

```jsx
// ❌ Wrong - sort and reverse mutate the array
items.sort();
items.reverse();

// ✅ Correct - copy first, then sort/reverse
setItems([...items].sort());
setItems([...items].reverse());

// ✅ Custom sort
setItems([...items].sort((a, b) => a.name.localeCompare(b.name)));
```

### 4. Common Array Operations Summary

| Operation     | Mutates (❌ Avoid)       | Immutable (✅ Use)                 |
| ------------- | ------------------------ | ---------------------------------- |
| **Add**       | `push`, `unshift`        | `[...arr, item]`, `[item, ...arr]` |
| **Remove**    | `pop`, `shift`, `splice` | `filter`, `slice`                  |
| **Replace**   | `arr[i] = x`             | `map`                              |
| **Sort**      | `sort`, `reverse`        | `[...arr].sort()`                  |
| **Transform** | -                        | `map`                              |
| **Find**      | -                        | `find`, `findIndex`                |

### 5. Updating Arrays of Objects

```jsx
const [users, setUsers] = useState([
  { id: 1, name: "Alice", active: true },
  { id: 2, name: "Bob", active: false },
  { id: 3, name: "Charlie", active: true },
]);

// Add user
setUsers([...users, { id: 4, name: "Diana", active: true }]);

// Remove user
setUsers(users.filter((user) => user.id !== 2));

// Update user
setUsers(
  users.map((user) => (user.id === 1 ? { ...user, name: "Alicia" } : user))
);

// Toggle user active status
setUsers(
  users.map((user) =>
    user.id === 2 ? { ...user, active: !user.active } : user
  )
);

// Update multiple users
setUsers(
  users.map((user) => (user.active ? { ...user, role: "member" } : user))
);
```

---

## 💻 Demo Code

### Example 1: Form with Object State

```jsx
import { useState } from "react";

function UserProfileForm() {
  const [profile, setProfile] = useState({
    firstName: "",
    lastName: "",
    email: "",
    phone: "",
    address: {
      street: "",
      city: "",
      state: "",
      zip: "",
    },
    preferences: {
      newsletter: false,
      notifications: true,
    },
  });

  // Generic handler for top-level fields
  const handleChange = (e) => {
    const { name, value, type, checked } = e.target;
    setProfile({
      ...profile,
      [name]: type === "checkbox" ? checked : value,
    });
  };

  // Handler for address fields
  const handleAddressChange = (e) => {
    const { name, value } = e.target;
    setProfile({
      ...profile,
      address: {
        ...profile.address,
        [name]: value,
      },
    });
  };

  // Handler for preference toggles
  const handlePreferenceChange = (e) => {
    const { name, checked } = e.target;
    setProfile({
      ...profile,
      preferences: {
        ...profile.preferences,
        [name]: checked,
      },
    });
  };

  const handleSubmit = (e) => {
    e.preventDefault();
    console.log("Submitting:", profile);
  };

  return (
    <form onSubmit={handleSubmit}>
      <h2>User Profile</h2>

      {/* Basic Info */}
      <fieldset>
        <legend>Basic Information</legend>
        <input
          name="firstName"
          value={profile.firstName}
          onChange={handleChange}
          placeholder="First Name"
        />
        <input
          name="lastName"
          value={profile.lastName}
          onChange={handleChange}
          placeholder="Last Name"
        />
        <input
          name="email"
          type="email"
          value={profile.email}
          onChange={handleChange}
          placeholder="Email"
        />
        <input
          name="phone"
          value={profile.phone}
          onChange={handleChange}
          placeholder="Phone"
        />
      </fieldset>

      {/* Address */}
      <fieldset>
        <legend>Address</legend>
        <input
          name="street"
          value={profile.address.street}
          onChange={handleAddressChange}
          placeholder="Street"
        />
        <input
          name="city"
          value={profile.address.city}
          onChange={handleAddressChange}
          placeholder="City"
        />
        <input
          name="state"
          value={profile.address.state}
          onChange={handleAddressChange}
          placeholder="State"
        />
        <input
          name="zip"
          value={profile.address.zip}
          onChange={handleAddressChange}
          placeholder="ZIP"
        />
      </fieldset>

      {/* Preferences */}
      <fieldset>
        <legend>Preferences</legend>
        <label>
          <input
            type="checkbox"
            name="newsletter"
            checked={profile.preferences.newsletter}
            onChange={handlePreferenceChange}
          />
          Subscribe to newsletter
        </label>
        <label>
          <input
            type="checkbox"
            name="notifications"
            checked={profile.preferences.notifications}
            onChange={handlePreferenceChange}
          />
          Enable notifications
        </label>
      </fieldset>

      <button type="submit">Save Profile</button>
    </form>
  );
}
```

### Example 2: Todo List with Array State

```jsx
import { useState } from "react";

function TodoList() {
  const [todos, setTodos] = useState([
    { id: 1, text: "Learn React", completed: true, priority: "high" },
    { id: 2, text: "Build project", completed: false, priority: "medium" },
    { id: 3, text: "Write tests", completed: false, priority: "low" },
  ]);
  const [newTodo, setNewTodo] = useState("");
  const [filter, setFilter] = useState("all");

  // Add new todo
  const addTodo = () => {
    if (!newTodo.trim()) return;

    setTodos([
      ...todos,
      {
        id: Date.now(),
        text: newTodo.trim(),
        completed: false,
        priority: "medium",
      },
    ]);
    setNewTodo("");
  };

  // Toggle completion
  const toggleTodo = (id) => {
    setTodos(
      todos.map((todo) =>
        todo.id === id ? { ...todo, completed: !todo.completed } : todo
      )
    );
  };

  // Delete todo
  const deleteTodo = (id) => {
    setTodos(todos.filter((todo) => todo.id !== id));
  };

  // Update text
  const updateTodoText = (id, text) => {
    setTodos(todos.map((todo) => (todo.id === id ? { ...todo, text } : todo)));
  };

  // Update priority
  const updatePriority = (id, priority) => {
    setTodos(
      todos.map((todo) => (todo.id === id ? { ...todo, priority } : todo))
    );
  };

  // Reorder (move up)
  const moveUp = (index) => {
    if (index === 0) return;
    const newTodos = [...todos];
    [newTodos[index - 1], newTodos[index]] = [
      newTodos[index],
      newTodos[index - 1],
    ];
    setTodos(newTodos);
  };

  // Reorder (move down)
  const moveDown = (index) => {
    if (index === todos.length - 1) return;
    const newTodos = [...todos];
    [newTodos[index], newTodos[index + 1]] = [
      newTodos[index + 1],
      newTodos[index],
    ];
    setTodos(newTodos);
  };

  // Clear completed
  const clearCompleted = () => {
    setTodos(todos.filter((todo) => !todo.completed));
  };

  // Sort by priority
  const sortByPriority = () => {
    const priorityOrder = { high: 0, medium: 1, low: 2 };
    setTodos(
      [...todos].sort(
        (a, b) => priorityOrder[a.priority] - priorityOrder[b.priority]
      )
    );
  };

  // Filtered todos
  const filteredTodos = todos.filter((todo) => {
    if (filter === "active") return !todo.completed;
    if (filter === "completed") return todo.completed;
    return true;
  });

  return (
    <div className="todo-list">
      <h1>Todos</h1>

      {/* Add todo */}
      <div className="add-todo">
        <input
          value={newTodo}
          onChange={(e) => setNewTodo(e.target.value)}
          onKeyPress={(e) => e.key === "Enter" && addTodo()}
          placeholder="What needs to be done?"
        />
        <button onClick={addTodo}>Add</button>
      </div>

      {/* Filters */}
      <div className="filters">
        {["all", "active", "completed"].map((f) => (
          <button
            key={f}
            className={filter === f ? "active" : ""}
            onClick={() => setFilter(f)}
          >
            {f.charAt(0).toUpperCase() + f.slice(1)}
          </button>
        ))}
        <button onClick={sortByPriority}>Sort by Priority</button>
      </div>

      {/* Todo items */}
      <ul>
        {filteredTodos.map((todo, index) => (
          <li key={todo.id} className={todo.completed ? "completed" : ""}>
            <input
              type="checkbox"
              checked={todo.completed}
              onChange={() => toggleTodo(todo.id)}
            />
            <input
              type="text"
              value={todo.text}
              onChange={(e) => updateTodoText(todo.id, e.target.value)}
            />
            <select
              value={todo.priority}
              onChange={(e) => updatePriority(todo.id, e.target.value)}
            >
              <option value="high">High</option>
              <option value="medium">Medium</option>
              <option value="low">Low</option>
            </select>
            <button onClick={() => moveUp(index)}>↑</button>
            <button onClick={() => moveDown(index)}>↓</button>
            <button onClick={() => deleteTodo(todo.id)}>🗑️</button>
          </li>
        ))}
      </ul>

      {/* Footer */}
      <div className="footer">
        <span>{todos.filter((t) => !t.completed).length} items left</span>
        <button onClick={clearCompleted}>Clear Completed</button>
      </div>
    </div>
  );
}

export default TodoList;
```

### Example 3: Shopping Cart

```jsx
import { useState } from "react";

function ShoppingCart() {
  const [cart, setCart] = useState({
    items: [],
    couponCode: "",
    discount: 0,
  });

  // Add item to cart
  const addItem = (product) => {
    const existingItem = cart.items.find((item) => item.id === product.id);

    if (existingItem) {
      // Update quantity
      setCart({
        ...cart,
        items: cart.items.map((item) =>
          item.id === product.id
            ? { ...item, quantity: item.quantity + 1 }
            : item
        ),
      });
    } else {
      // Add new item
      setCart({
        ...cart,
        items: [...cart.items, { ...product, quantity: 1 }],
      });
    }
  };

  // Remove item
  const removeItem = (productId) => {
    setCart({
      ...cart,
      items: cart.items.filter((item) => item.id !== productId),
    });
  };

  // Update quantity
  const updateQuantity = (productId, quantity) => {
    if (quantity <= 0) {
      removeItem(productId);
      return;
    }

    setCart({
      ...cart,
      items: cart.items.map((item) =>
        item.id === productId ? { ...item, quantity } : item
      ),
    });
  };

  // Apply coupon
  const applyCoupon = (code) => {
    const discounts = {
      SAVE10: 10,
      SAVE20: 20,
      HALF: 50,
    };

    setCart({
      ...cart,
      couponCode: code,
      discount: discounts[code] || 0,
    });
  };

  // Calculate totals
  const subtotal = cart.items.reduce(
    (sum, item) => sum + item.price * item.quantity,
    0
  );
  const discountAmount = subtotal * (cart.discount / 100);
  const total = subtotal - discountAmount;

  return (
    <div className="shopping-cart">
      <h2>Shopping Cart</h2>

      {cart.items.length === 0 ? (
        <p>Your cart is empty</p>
      ) : (
        <>
          {cart.items.map((item) => (
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
              <span>${(item.price * item.quantity).toFixed(2)}</span>
              <button onClick={() => removeItem(item.id)}>Remove</button>
            </div>
          ))}

          <div className="coupon">
            <input
              value={cart.couponCode}
              onChange={(e) => setCart({ ...cart, couponCode: e.target.value })}
              placeholder="Coupon code"
            />
            <button onClick={() => applyCoupon(cart.couponCode)}>Apply</button>
          </div>

          <div className="totals">
            <p>Subtotal: ${subtotal.toFixed(2)}</p>
            {cart.discount > 0 && (
              <p>
                Discount ({cart.discount}%): -${discountAmount.toFixed(2)}
              </p>
            )}
            <p>
              <strong>Total: ${total.toFixed(2)}</strong>
            </p>
          </div>
        </>
      )}
    </div>
  );
}
```

---

## ✅ Best Practices

### 1. Always Use Spread or Array Methods

```jsx
// ✅ Objects: spread operator
setUser({ ...user, name: "New Name" });

// ✅ Arrays: spread, map, filter
setItems([...items, newItem]);
setItems(items.filter((i) => i.id !== id));
setItems(items.map((i) => (i.id === id ? { ...i, done: true } : i)));
```

### 2. Consider Immer for Complex Updates

```jsx
import { produce } from "immer";

// Without Immer (verbose)
setUser({
  ...user,
  address: {
    ...user.address,
    city: "Boston",
  },
});

// With Immer (cleaner)
setUser(
  produce((draft) => {
    draft.address.city = "Boston";
  })
);
```

### 3. Extract Update Logic

```jsx
// ✅ Keep update logic in helper functions
const addTodo = (todos, text) => [
  ...todos,
  { id: Date.now(), text, completed: false },
];

const toggleTodo = (todos, id) =>
  todos.map((todo) =>
    todo.id === id ? { ...todo, completed: !todo.completed } : todo
  );

// Use in component
setTodos((prev) => addTodo(prev, newText));
setTodos((prev) => toggleTodo(prev, todoId));
```

---

## ❌ Common Mistakes

### 1. Direct Mutation

```jsx
// ❌ All of these are mutations
user.name = "Bob"; // Object mutation
items.push(newItem); // Array mutation
items[0].done = true; // Nested mutation
items.sort(); // Array mutation

// ✅ Create new references
setUser({ ...user, name: "Bob" });
setItems([...items, newItem]);
setItems(items.map((item, i) => (i === 0 ? { ...item, done: true } : item)));
setItems([...items].sort());
```

### 2. Shallow vs Deep Copy

```jsx
// ❌ Shallow copy doesn't protect nested objects
const newUser = { ...user };
newUser.address.city = "Boston"; // Still mutates original!

// ✅ Copy nested objects too
const newUser = {
  ...user,
  address: { ...user.address, city: "Boston" },
};
```

### 3. Forgetting to Return New Array

```jsx
// ❌ forEach doesn't return new array
setItems(items.forEach((item) => item.count++)); // Wrong!

// ✅ map returns new array
setItems(items.map((item) => ({ ...item, count: item.count + 1 })));
```

---

## 🏋️ Practice Exercises

### Exercise 1: Update Nested Object

Given this state, write code to update the user's street:

```jsx
const [data, setData] = useState({
  user: {
    name: "Alice",
    address: {
      street: "123 Main St",
      city: "NYC",
    },
  },
});
```

### Exercise 2: Array Operations

Implement these functions:

- `addItem(id, name)` - Add item to array
- `removeItem(id)` - Remove item by id
- `updateItem(id, newName)` - Update item's name
- `toggleItem(id)` - Toggle item's `active` property

### Exercise 3: Complex Update

Build a contact list that can:

- Add contacts with name, email, phone
- Edit any contact's information
- Add tags to contacts (array within object)
- Remove tags from contacts

---

## 🎤 Interview Q&A

**Q1: Why can't we mutate state directly in React?**

> React uses reference equality to detect changes. If you mutate an object, the reference stays the same, so React doesn't know it changed and won't re-render. Additionally, it breaks features like time-travel debugging and memoization.

**Q2: How do you update a nested object in state?**

> You need to spread at each level of nesting: `setState({ ...state, nested: { ...state.nested, property: newValue } })`. Each level needs a new object reference for React to detect the change.

**Q3: What's the difference between `[...arr].sort()` and `arr.sort()`?**

> `arr.sort()` mutates the original array. `[...arr].sort()` creates a copy first, then sorts the copy, leaving the original unchanged. Always use the spread copy for state updates.

**Q4: When would you use Immer?**

> Immer is useful for deeply nested state updates where the spread syntax becomes verbose and error-prone. It lets you write "mutating" code that produces immutable updates under the hood.

**Q5: How do you add an item to a specific position in an array?**

> Use slice: `setItems([...items.slice(0, index), newItem, ...items.slice(index)])`. This creates a new array with the item inserted at the specified index.

---

## 📎 References

- [React Docs - Updating Objects in State](https://react.dev/learn/updating-objects-in-state)
- [React Docs - Updating Arrays in State](https://react.dev/learn/updating-arrays-in-state)
- [Immer Documentation](https://immerjs.github.io/immer/)
- [Immutability in React](https://react.dev/learn/updating-objects-in-state#why-is-mutating-state-not-recommended-in-react)

---

**Previous Topic**: [← 09. State as Snapshot](./09_State_as_Snapshot.md)

**Next Topic**: [11. Event Handling →](./11_Event_Handling.md)
