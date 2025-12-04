# 13. Lists and Keys

> Rendering collections of data efficiently and correctly.

---

## 🎯 What Are Lists and Keys?

In React, you often need to display lists of data - products, users, messages, etc. **Keys** are special attributes that help React identify which items have changed, been added, or removed.

```jsx
function TodoList({ todos }) {
  return (
    <ul>
      {todos.map((todo) => (
        <li key={todo.id}>{todo.text}</li>
      ))}
    </ul>
  );
}
```

---

## 🤔 Why Keys Matter

Keys are crucial for:

- ✅ **Performance** - React can skip re-rendering unchanged items
- ✅ **Correctness** - State is preserved for the right items
- ✅ **Preventing bugs** - Wrong keys cause subtle, hard-to-find bugs

---

## 📚 Core Concepts

### 1. Rendering Lists with map()

The `map()` function transforms an array of data into an array of elements:

```jsx
const products = [
  { id: 1, name: "Apple", price: 1.5 },
  { id: 2, name: "Banana", price: 0.75 },
  { id: 3, name: "Cherry", price: 3.0 },
];

function ProductList() {
  return (
    <ul>
      {products.map((product) => (
        <li key={product.id}>
          {product.name}: ${product.price}
        </li>
      ))}
    </ul>
  );
}
```

### 2. Understanding Keys

Keys help React identify elements across renders:

```jsx
// Without keys - React doesn't know which item is which
// ❌ Warning: Each child should have a unique "key" prop

// With keys - React can track each item
{
  items.map((item) => <Item key={item.id} data={item} />);
}
```

**What makes a good key:**

- ✅ **Unique among siblings** - Not globally, just in this list
- ✅ **Stable** - Same item always gets same key
- ✅ **Predictable** - Derived from the data, not random

### 3. Valid Key Sources

```jsx
// ✅ Best: Unique ID from your data
{
  users.map((user) => <User key={user.id} user={user} />);
}

// ✅ Good: Unique identifier from API
{
  posts.map((post) => <Post key={post.slug} post={post} />);
}

// ✅ Good: Combination for uniqueness
{
  items.map((item) => <Item key={`${item.category}-${item.id}`} item={item} />);
}

// ⚠️ Acceptable only for static lists that never change
{
  staticOptions.map((option, index) => (
    <option key={index} value={option}>
      {option}
    </option>
  ));
}

// ❌ Bad: Index for dynamic lists
{
  todos.map((todo, index) => <Todo key={index} todo={todo} />);
}

// ❌ Bad: Random values
{
  items.map((item) => <Item key={Math.random()} item={item} />);
}
```

### 4. Why Index as Key is Problematic

```jsx
// Initial render
<ul>
  <li key={0}>Apple</li>   {/* index 0 */}
  <li key={1}>Banana</li>  {/* index 1 */}
  <li key={2}>Cherry</li>  {/* index 2 */}
</ul>

// After removing Banana
<ul>
  <li key={0}>Apple</li>   {/* index 0 - same */}
  <li key={1}>Cherry</li>  {/* index 1 - WAS 2! React reuses wrong DOM */}
</ul>

// Problems:
// 1. Cherry gets Banana's old DOM node and potentially state
// 2. Input values, focus, animations may be wrong
// 3. Performance is worse (unnecessary re-renders)
```

### 5. Generating Keys

When your data doesn't have IDs:

```jsx
// Method 1: Generate ID when creating data
const addTodo = (text) => {
  setTodos([
    ...todos,
    {
      id: crypto.randomUUID(), // or Date.now()
      text,
      completed: false,
    },
  ]);
};

// Method 2: Use a library
import { nanoid } from "nanoid";

const addTodo = (text) => {
  setTodos([...todos, { id: nanoid(), text, completed: false }]);
};

// Method 3: Use content as key (if unique)
{
  tags.map((tag) => <Tag key={tag} name={tag} />);
}
```

### 6. Keys Must Be Unique Among Siblings

Keys only need to be unique within the same array:

```jsx
function App() {
  const fruits = [
    { id: 1, name: "Apple" },
    { id: 2, name: "Banana" },
  ];
  const veggies = [
    { id: 1, name: "Carrot" },
    { id: 2, name: "Broccoli" },
  ];

  return (
    <div>
      {/* These can have same IDs - different lists */}
      <ul>
        {fruits.map((f) => (
          <li key={f.id}>{f.name}</li>
        ))}
      </ul>
      <ul>
        {veggies.map((v) => (
          <li key={v.id}>{v.name}</li>
        ))}
      </ul>
    </div>
  );
}
```

### 7. Where to Put Keys

Keys go on the outermost element returned from map():

```jsx
// ✅ Correct - key on the mapped component
{
  items.map((item) => <ListItem key={item.id} item={item} />);
}

// ✅ Correct - key on Fragment when needed
{
  items.map((item) => (
    <Fragment key={item.id}>
      <dt>{item.term}</dt>
      <dd>{item.definition}</dd>
    </Fragment>
  ));
}

// ❌ Wrong - key inside the component
function ListItem({ item }) {
  return <li key={item.id}>{item.name}</li>; // Wrong place!
}
```

### 8. Keys Are Not Props

Keys aren't passed to components - use a different prop name if needed:

```jsx
// ❌ Can't access key inside component
function Item({ key }) {
  console.log(key); // undefined!
}

// ✅ Pass as separate prop
{
  items.map((item) => (
    <Item
      key={item.id} // For React's internal use
      itemId={item.id} // Available inside component
      item={item}
    />
  ));
}

function Item({ itemId, item }) {
  console.log(itemId); // Works!
}
```

---

## 💻 Demo Code

### Example 1: Basic List Operations

```jsx
import { useState } from "react";

function TodoApp() {
  const [todos, setTodos] = useState([
    { id: 1, text: "Learn React", completed: true },
    { id: 2, text: "Build a project", completed: false },
    { id: 3, text: "Deploy to production", completed: false },
  ]);
  const [newTodo, setNewTodo] = useState("");

  const addTodo = () => {
    if (!newTodo.trim()) return;
    setTodos([
      ...todos,
      {
        id: Date.now(), // Unique ID
        text: newTodo.trim(),
        completed: false,
      },
    ]);
    setNewTodo("");
  };

  const toggleTodo = (id) => {
    setTodos(
      todos.map((todo) =>
        todo.id === id ? { ...todo, completed: !todo.completed } : todo
      )
    );
  };

  const deleteTodo = (id) => {
    setTodos(todos.filter((todo) => todo.id !== id));
  };

  const moveTodoUp = (index) => {
    if (index === 0) return;
    const newTodos = [...todos];
    [newTodos[index - 1], newTodos[index]] = [
      newTodos[index],
      newTodos[index - 1],
    ];
    setTodos(newTodos);
  };

  return (
    <div>
      <h1>My Todos</h1>

      <div className="add-todo">
        <input
          value={newTodo}
          onChange={(e) => setNewTodo(e.target.value)}
          onKeyPress={(e) => e.key === "Enter" && addTodo()}
          placeholder="Add new todo..."
        />
        <button onClick={addTodo}>Add</button>
      </div>

      <ul className="todo-list">
        {todos.map((todo, index) => (
          <li key={todo.id} className={todo.completed ? "completed" : ""}>
            <input
              type="checkbox"
              checked={todo.completed}
              onChange={() => toggleTodo(todo.id)}
            />
            <span>{todo.text}</span>
            <button onClick={() => moveTodoUp(index)} disabled={index === 0}>
              ↑
            </button>
            <button onClick={() => deleteTodo(todo.id)}>🗑️</button>
          </li>
        ))}
      </ul>

      <p>{todos.filter((t) => !t.completed).length} items left</p>
    </div>
  );
}
```

### Example 2: Filtering Lists

```jsx
import { useState } from "react";

const products = [
  { id: 1, name: "iPhone", category: "electronics", price: 999, inStock: true },
  {
    id: 2,
    name: "MacBook",
    category: "electronics",
    price: 1299,
    inStock: false,
  },
  { id: 3, name: "T-Shirt", category: "clothing", price: 29, inStock: true },
  { id: 4, name: "Jeans", category: "clothing", price: 59, inStock: true },
  { id: 5, name: "Coffee Maker", category: "home", price: 79, inStock: false },
  {
    id: 6,
    name: "Headphones",
    category: "electronics",
    price: 199,
    inStock: true,
  },
];

function ProductCatalog() {
  const [filters, setFilters] = useState({
    category: "all",
    inStockOnly: false,
    maxPrice: Infinity,
    searchQuery: "",
  });

  const filteredProducts = products.filter((product) => {
    // Category filter
    if (filters.category !== "all" && product.category !== filters.category) {
      return false;
    }
    // Stock filter
    if (filters.inStockOnly && !product.inStock) {
      return false;
    }
    // Price filter
    if (product.price > filters.maxPrice) {
      return false;
    }
    // Search filter
    if (
      filters.searchQuery &&
      !product.name.toLowerCase().includes(filters.searchQuery.toLowerCase())
    ) {
      return false;
    }
    return true;
  });

  const categories = ["all", ...new Set(products.map((p) => p.category))];

  return (
    <div className="catalog">
      {/* Filters */}
      <div className="filters">
        <input
          type="text"
          placeholder="Search products..."
          value={filters.searchQuery}
          onChange={(e) =>
            setFilters({ ...filters, searchQuery: e.target.value })
          }
        />

        <select
          value={filters.category}
          onChange={(e) => setFilters({ ...filters, category: e.target.value })}
        >
          {categories.map((cat) => (
            <option key={cat} value={cat}>
              {cat.charAt(0).toUpperCase() + cat.slice(1)}
            </option>
          ))}
        </select>

        <label>
          <input
            type="checkbox"
            checked={filters.inStockOnly}
            onChange={(e) =>
              setFilters({ ...filters, inStockOnly: e.target.checked })
            }
          />
          In Stock Only
        </label>

        <select
          value={filters.maxPrice}
          onChange={(e) =>
            setFilters({ ...filters, maxPrice: Number(e.target.value) })
          }
        >
          <option value={Infinity}>Any Price</option>
          <option value={50}>Under $50</option>
          <option value={100}>Under $100</option>
          <option value={500}>Under $500</option>
        </select>
      </div>

      {/* Results count */}
      <p>
        {filteredProducts.length} of {products.length} products
      </p>

      {/* Product list */}
      {filteredProducts.length === 0 ? (
        <p className="no-results">No products match your filters.</p>
      ) : (
        <div className="product-grid">
          {filteredProducts.map((product) => (
            <ProductCard key={product.id} product={product} />
          ))}
        </div>
      )}
    </div>
  );
}

function ProductCard({ product }) {
  return (
    <div className={`product-card ${!product.inStock ? "out-of-stock" : ""}`}>
      <h3>{product.name}</h3>
      <p className="category">{product.category}</p>
      <p className="price">${product.price}</p>
      <p className={product.inStock ? "in-stock" : "out-of-stock"}>
        {product.inStock ? "✓ In Stock" : "✗ Out of Stock"}
      </p>
    </div>
  );
}
```

### Example 3: Nested Lists

```jsx
function MessageThread({ thread }) {
  return (
    <div className="thread">
      {thread.messages.map((message) => (
        <Message key={message.id} message={message}>
          {message.replies?.length > 0 && (
            <div className="replies">
              {message.replies.map((reply) => (
                <Reply key={reply.id} reply={reply} />
              ))}
            </div>
          )}
        </Message>
      ))}
    </div>
  );
}

function Message({ message, children }) {
  return (
    <div className="message">
      <div className="message-header">
        <strong>{message.author}</strong>
        <span>{message.timestamp}</span>
      </div>
      <p>{message.content}</p>
      {children}
    </div>
  );
}

function Reply({ reply }) {
  return (
    <div className="reply">
      <strong>{reply.author}</strong>: {reply.content}
    </div>
  );
}
```

### Example 4: Dynamic List with State Preservation

```jsx
import { useState } from "react";

function InputList() {
  const [items, setItems] = useState([{ id: 1 }, { id: 2 }, { id: 3 }]);

  const addItem = () => {
    setItems([...items, { id: Date.now() }]);
  };

  const removeItem = (id) => {
    setItems(items.filter((item) => item.id !== id));
  };

  const insertAtBeginning = () => {
    setItems([{ id: Date.now() }, ...items]);
  };

  return (
    <div>
      <h2>Input List Demo</h2>
      <p>Type in inputs, then add/remove items. Watch state preservation!</p>

      <button onClick={insertAtBeginning}>Add at Beginning</button>
      <button onClick={addItem}>Add at End</button>

      <ul>
        {items.map((item) => (
          <li key={item.id}>
            <ItemInput />
            <button onClick={() => removeItem(item.id)}>Remove</button>
            <small>ID: {item.id}</small>
          </li>
        ))}
      </ul>
    </div>
  );
}

function ItemInput() {
  const [value, setValue] = useState("");

  return (
    <input
      value={value}
      onChange={(e) => setValue(e.target.value)}
      placeholder="Type something..."
    />
  );
}

// Try the same with index as key to see the bug!
```

---

## ✅ Best Practices

### 1. Always Use Stable, Unique IDs

```jsx
// ✅ Best practice
const addItem = (name) => {
  setItems((prev) => [
    ...prev,
    {
      id: crypto.randomUUID(), // Generate unique ID
      name,
    },
  ]);
};
```

### 2. Keep Keys Stable

```jsx
// ❌ Bad - key changes every render
{
  items.map((item) => <Item key={Math.random()} item={item} />);
}

// ✅ Good - key is stable
{
  items.map((item) => <Item key={item.id} item={item} />);
}
```

### 3. Extract List Items to Components

```jsx
// ✅ Good - clean and maintainable
function UserList({ users }) {
  return (
    <ul>
      {users.map((user) => (
        <UserItem key={user.id} user={user} />
      ))}
    </ul>
  );
}

function UserItem({ user }) {
  return <li>{user.name}</li>;
}
```

### 4. Handle Empty Lists

```jsx
function ItemList({ items }) {
  if (items.length === 0) {
    return <p>No items found.</p>;
  }

  return (
    <ul>
      {items.map((item) => (
        <li key={item.id}>{item.name}</li>
      ))}
    </ul>
  );
}
```

---

## ❌ Common Mistakes

### 1. Using Index as Key for Dynamic Lists

```jsx
// ❌ Wrong - causes bugs when list changes
{
  todos.map((todo, index) => <TodoItem key={index} todo={todo} />);
}

// ✅ Correct - use stable ID
{
  todos.map((todo) => <TodoItem key={todo.id} todo={todo} />);
}
```

### 2. Random Keys

```jsx
// ❌ Terrible - creates new key every render
{
  items.map((item) => <Item key={Math.random()} />);
}

// ✅ Correct - stable key
{
  items.map((item) => <Item key={item.id} />);
}
```

### 3. Duplicate Keys

```jsx
// ❌ Wrong - duplicate keys
const items = [
  { id: 1, name: "A" },
  { id: 1, name: "B" }, // Same ID!
];

// ✅ Fix - ensure uniqueness
const items = [
  { id: 1, name: "A" },
  { id: 2, name: "B" },
];
```

### 4. Forgetting Keys

```jsx
// ❌ Warning: Each child should have a unique "key" prop
{
  items.map((item) => <li>{item}</li>);
}

// ✅ Correct
{
  items.map((item) => <li key={item}>{item}</li>);
}
```

---

## 🏋️ Practice Exercises

### Exercise 1: Sortable List

Create a list that can be sorted by name or date, with proper keys.

### Exercise 2: Shopping Cart

Build a cart where items can be added, removed, and quantities changed.

### Exercise 3: Kanban Board

Create a simple board with columns where cards can be added/removed.

---

## 🎤 Interview Q&A

**Q1: Why do we need keys in React lists?**

> Keys help React identify which items have changed, been added, or removed. They enable efficient updates by allowing React to match elements between renders and preserve state correctly.

**Q2: Why shouldn't you use array index as a key?**

> Index keys cause problems when list order changes or items are added/removed. React may reuse the wrong DOM nodes, leading to incorrect state, broken animations, and performance issues.

**Q3: What makes a good key?**

> A good key is unique among siblings, stable (same item always gets same key), and derived from the data itself (like database IDs). It should not be random or calculated each render.

**Q4: Where should the key prop be placed?**

> Keys should be on the outermost element returned from map(), which is usually the component being rendered. Keys on elements inside the component don't work.

**Q5: Can two lists have items with the same key?**

> Yes! Keys only need to be unique among siblings within the same list. Different lists can reuse the same keys.

---

## 📎 References

- [React Docs - Rendering Lists](https://react.dev/learn/rendering-lists)
- [React Docs - Preserving and Resetting State](https://react.dev/learn/preserving-and-resetting-state)
- [Why Keys Matter](https://react.dev/learn/rendering-lists#why-does-react-need-keys)

---

**Previous Topic**: [← 12. Conditional Rendering](./12_Conditional_Rendering.md)

**Next Topic**: [14. Forms in React →](./14_Forms_in_React.md)
