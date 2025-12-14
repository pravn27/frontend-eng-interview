# 06 - useMemo & useCallback

## 📖 What are useMemo and useCallback?

These hooks are **memoization** tools that cache values and functions between renders to prevent unnecessary recalculations and re-renders.

```jsx
import { useMemo, useCallback } from "react";

function ExpensiveComponent({ items, onItemClick }) {
  // useMemo: Cache computed value
  const sortedItems = useMemo(() => {
    return [...items].sort((a, b) => a.name.localeCompare(b.name));
  }, [items]);

  // useCallback: Cache function reference
  const handleClick = useCallback(
    (id) => {
      onItemClick(id);
    },
    [onItemClick]
  );

  return (
    <ul>
      {sortedItems.map((item) => (
        <li key={item.id} onClick={() => handleClick(item.id)}>
          {item.name}
        </li>
      ))}
    </ul>
  );
}
```

---

## 🎯 Why Memoization?

### The Problem: Unnecessary Work

```jsx
function App() {
  const [count, setCount] = useState(0);
  const [items, setItems] = useState([1, 2, 3, 4, 5]);

  // ❌ Recalculated on EVERY render, even when only count changes
  const expensiveCalculation = items.reduce((sum, item) => {
    console.log("Computing..."); // Logs on every render!
    return sum + item * 2;
  }, 0);

  return (
    <div>
      <p>Count: {count}</p>
      <p>Expensive: {expensiveCalculation}</p>
      <button onClick={() => setCount(count + 1)}>Increment</button>
    </div>
  );
}
```

### The Solution: useMemo

```jsx
function App() {
  const [count, setCount] = useState(0);
  const [items, setItems] = useState([1, 2, 3, 4, 5]);

  // ✅ Only recalculated when items change
  const expensiveCalculation = useMemo(() => {
    console.log("Computing..."); // Only logs when items change
    return items.reduce((sum, item) => sum + item * 2, 0);
  }, [items]);

  return (
    <div>
      <p>Count: {count}</p>
      <p>Expensive: {expensiveCalculation}</p>
      <button onClick={() => setCount(count + 1)}>Increment</button>
    </div>
  );
}
```

---

## 📚 useMemo

### Syntax

```jsx
const memoizedValue = useMemo(() => computeExpensiveValue(a, b), [a, b]);
```

### When to Use useMemo

1. **Expensive calculations**
2. **Referential equality for objects/arrays**
3. **Avoiding prop changes that trigger child re-renders**

### Example 1: Expensive Calculation

```jsx
function SearchResults({ items, query }) {
  // Expensive filtering and sorting
  const filteredItems = useMemo(() => {
    console.log("Filtering items...");
    return items
      .filter((item) => item.name.toLowerCase().includes(query.toLowerCase()))
      .sort((a, b) => a.name.localeCompare(b.name));
  }, [items, query]);

  return (
    <ul>
      {filteredItems.map((item) => (
        <li key={item.id}>{item.name}</li>
      ))}
    </ul>
  );
}
```

### Example 2: Referential Equality

```jsx
function Parent() {
  const [count, setCount] = useState(0);

  // ❌ New object every render - Child always re-renders
  const config = { theme: "dark", size: "large" };

  // ✅ Same reference unless dependencies change
  const config = useMemo(() => ({ theme: "dark", size: "large" }), []);

  return <Child config={config} />;
}

// Child wrapped with React.memo won't re-render unnecessarily
const Child = React.memo(function Child({ config }) {
  console.log("Child rendered");
  return <div>Theme: {config.theme}</div>;
});
```

### Example 3: Derived State

```jsx
function UserDashboard({ users }) {
  // Memoize derived data
  const stats = useMemo(() => {
    const activeUsers = users.filter((u) => u.isActive);
    const inactiveUsers = users.filter((u) => !u.isActive);

    return {
      total: users.length,
      active: activeUsers.length,
      inactive: inactiveUsers.length,
      activePercentage: ((activeUsers.length / users.length) * 100).toFixed(1),
    };
  }, [users]);

  return (
    <div>
      <p>Total: {stats.total}</p>
      <p>
        Active: {stats.active} ({stats.activePercentage}%)
      </p>
      <p>Inactive: {stats.inactive}</p>
    </div>
  );
}
```

---

## 📚 useCallback

### Syntax

```jsx
const memoizedCallback = useCallback(() => {
  doSomething(a, b);
}, [a, b]);
```

### When to Use useCallback

1. **Passing callbacks to memoized children**
2. **Callbacks as effect dependencies**
3. **Avoiding effect re-runs**

### Example 1: Preventing Child Re-renders

```jsx
function Parent() {
  const [count, setCount] = useState(0);
  const [items, setItems] = useState([]);

  // ❌ New function every render - Child always re-renders
  const handleClick = (id) => {
    console.log("Clicked:", id);
  };

  // ✅ Same function reference between renders
  const handleClick = useCallback((id) => {
    console.log("Clicked:", id);
  }, []);

  return (
    <>
      <button onClick={() => setCount(count + 1)}>Count: {count}</button>
      <ItemList items={items} onItemClick={handleClick} />
    </>
  );
}

const ItemList = React.memo(function ItemList({ items, onItemClick }) {
  console.log("ItemList rendered");
  return (
    <ul>
      {items.map((item) => (
        <li key={item.id} onClick={() => onItemClick(item.id)}>
          {item.name}
        </li>
      ))}
    </ul>
  );
});
```

### Example 2: Effect Dependencies

```jsx
function SearchComponent({ onSearch }) {
  const [query, setQuery] = useState("");

  // ❌ Effect re-runs every render if onSearch isn't memoized
  useEffect(() => {
    if (query) {
      onSearch(query);
    }
  }, [query, onSearch]);

  return <input value={query} onChange={(e) => setQuery(e.target.value)} />;
}

// Parent should memoize the callback
function Parent() {
  const handleSearch = useCallback((query) => {
    console.log("Searching:", query);
  }, []);

  return <SearchComponent onSearch={handleSearch} />;
}
```

### Example 3: With State Updates

```jsx
function TodoApp() {
  const [todos, setTodos] = useState([]);

  // ✅ Memoized callbacks for child components
  const addTodo = useCallback((text) => {
    setTodos((prev) => [...prev, { id: Date.now(), text, done: false }]);
  }, []);

  const toggleTodo = useCallback((id) => {
    setTodos((prev) =>
      prev.map((todo) =>
        todo.id === id ? { ...todo, done: !todo.done } : todo
      )
    );
  }, []);

  const deleteTodo = useCallback((id) => {
    setTodos((prev) => prev.filter((todo) => todo.id !== id));
  }, []);

  return (
    <div>
      <AddTodo onAdd={addTodo} />
      <TodoList todos={todos} onToggle={toggleTodo} onDelete={deleteTodo} />
    </div>
  );
}
```

---

## 💻 Practical Examples

### 1. Data Table with Sorting and Filtering

```jsx
function DataTable({ data, columns }) {
  const [sortColumn, setSortColumn] = useState(null);
  const [sortDirection, setSortDirection] = useState("asc");
  const [filter, setFilter] = useState("");

  // Memoize filtered and sorted data
  const processedData = useMemo(() => {
    console.log("Processing data...");

    let result = [...data];

    // Filter
    if (filter) {
      result = result.filter((row) =>
        Object.values(row).some((val) =>
          String(val).toLowerCase().includes(filter.toLowerCase())
        )
      );
    }

    // Sort
    if (sortColumn) {
      result.sort((a, b) => {
        const aVal = a[sortColumn];
        const bVal = b[sortColumn];
        const comparison = aVal < bVal ? -1 : aVal > bVal ? 1 : 0;
        return sortDirection === "asc" ? comparison : -comparison;
      });
    }

    return result;
  }, [data, filter, sortColumn, sortDirection]);

  // Memoize column handlers
  const handleSort = useCallback((column) => {
    setSortColumn((prev) => {
      if (prev === column) {
        setSortDirection((d) => (d === "asc" ? "desc" : "asc"));
        return column;
      }
      setSortDirection("asc");
      return column;
    });
  }, []);

  return (
    <div>
      <input
        placeholder="Filter..."
        value={filter}
        onChange={(e) => setFilter(e.target.value)}
      />

      <table>
        <thead>
          <tr>
            {columns.map((col) => (
              <th key={col.key} onClick={() => handleSort(col.key)}>
                {col.label}
                {sortColumn === col.key &&
                  (sortDirection === "asc" ? " ↑" : " ↓")}
              </th>
            ))}
          </tr>
        </thead>
        <tbody>
          {processedData.map((row, i) => (
            <tr key={i}>
              {columns.map((col) => (
                <td key={col.key}>{row[col.key]}</td>
              ))}
            </tr>
          ))}
        </tbody>
      </table>

      <p>
        Showing {processedData.length} of {data.length} rows
      </p>
    </div>
  );
}
```

### 2. Form with Validation

```jsx
function RegistrationForm() {
  const [formData, setFormData] = useState({
    username: "",
    email: "",
    password: "",
  });

  // Memoize validation results
  const validation = useMemo(() => {
    const errors = {};

    if (formData.username.length < 3) {
      errors.username = "Username must be at least 3 characters";
    }

    if (!/^[^\s@]+@[^\s@]+\.[^\s@]+$/.test(formData.email)) {
      errors.email = "Invalid email address";
    }

    if (formData.password.length < 8) {
      errors.password = "Password must be at least 8 characters";
    }

    return {
      errors,
      isValid: Object.keys(errors).length === 0,
    };
  }, [formData]);

  // Memoize handlers
  const handleChange = useCallback((e) => {
    const { name, value } = e.target;
    setFormData((prev) => ({ ...prev, [name]: value }));
  }, []);

  const handleSubmit = useCallback(
    (e) => {
      e.preventDefault();
      if (validation.isValid) {
        console.log("Submitting:", formData);
      }
    },
    [formData, validation.isValid]
  );

  return (
    <form onSubmit={handleSubmit}>
      <div>
        <input
          name="username"
          value={formData.username}
          onChange={handleChange}
          placeholder="Username"
        />
        {validation.errors.username && (
          <span className="error">{validation.errors.username}</span>
        )}
      </div>

      <div>
        <input
          name="email"
          value={formData.email}
          onChange={handleChange}
          placeholder="Email"
        />
        {validation.errors.email && (
          <span className="error">{validation.errors.email}</span>
        )}
      </div>

      <div>
        <input
          name="password"
          type="password"
          value={formData.password}
          onChange={handleChange}
          placeholder="Password"
        />
        {validation.errors.password && (
          <span className="error">{validation.errors.password}</span>
        )}
      </div>

      <button type="submit" disabled={!validation.isValid}>
        Register
      </button>
    </form>
  );
}
```

### 3. Virtual List with Row Callbacks

```jsx
function VirtualList({ items, height, rowHeight }) {
  const [scrollTop, setScrollTop] = useState(0);

  // Memoize visible items calculation
  const visibleItems = useMemo(() => {
    const startIndex = Math.floor(scrollTop / rowHeight);
    const endIndex = Math.min(
      startIndex + Math.ceil(height / rowHeight) + 1,
      items.length
    );

    return items.slice(startIndex, endIndex).map((item, index) => ({
      ...item,
      index: startIndex + index,
      top: (startIndex + index) * rowHeight,
    }));
  }, [items, scrollTop, height, rowHeight]);

  // Memoize scroll handler
  const handleScroll = useCallback((e) => {
    setScrollTop(e.target.scrollTop);
  }, []);

  // Memoize row click handler
  const handleRowClick = useCallback((item) => {
    console.log("Clicked:", item);
  }, []);

  return (
    <div style={{ height, overflow: "auto" }} onScroll={handleScroll}>
      <div style={{ height: items.length * rowHeight, position: "relative" }}>
        {visibleItems.map((item) => (
          <div
            key={item.id}
            onClick={() => handleRowClick(item)}
            style={{
              position: "absolute",
              top: item.top,
              height: rowHeight,
              width: "100%",
            }}
          >
            {item.name}
          </div>
        ))}
      </div>
    </div>
  );
}
```

---

## ⚠️ Common Mistakes

### 1. Overusing Memoization

```jsx
// ❌ Unnecessary - simple calculations are fast
const double = useMemo(() => count * 2, [count]);
const fullName = useMemo(() => `${first} ${last}`, [first, last]);

// ✅ Just compute directly
const double = count * 2;
const fullName = `${first} ${last}`;

// ✅ Do use for expensive operations
const sortedItems = useMemo(
  () => [...items].sort((a, b) => complexSort(a, b)),
  [items]
);
```

### 2. Wrong Dependencies

```jsx
// ❌ Missing dependency
const handleClick = useCallback(() => {
  console.log(count); // Uses count but not in deps
}, []); // Missing count!

// ✅ Correct dependencies
const handleClick = useCallback(() => {
  console.log(count);
}, [count]);

// ✅ Or use functional update to avoid dependency
const handleClick = useCallback(() => {
  setCount((c) => c + 1); // No count dependency needed
}, []);
```

### 3. Creating New References in Deps

```jsx
// ❌ New array every render - useMemo recalculates every time
const result = useMemo(
  () => expensiveCalculation(data),
  [data, [1, 2, 3]] // New array = new reference each render!
);

// ✅ Use stable references
const options = useMemo(() => [1, 2, 3], []);
const result = useMemo(
  () => expensiveCalculation(data, options),
  [data, options]
);
```

### 4. Memoizing Inline Objects

```jsx
// ❌ New object in dependency - defeats purpose
const memoizedValue = useMemo(
  () => calculate(config),
  [{ setting: true }] // New object every render!
);

// ✅ Memoize the config too
const config = useMemo(() => ({ setting: true }), []);
const memoizedValue = useMemo(() => calculate(config), [config]);
```

---

## 🔧 Best Practices

### 1. Profile Before Optimizing

```jsx
// Don't guess - measure!
// Use React DevTools Profiler to identify slow components

// Only memoize when:
// - Profiler shows the component is slow
// - The computation is genuinely expensive
// - The component re-renders often with same props
```

### 2. useMemo vs useCallback

```jsx
// useCallback(fn, deps) is equivalent to useMemo(() => fn, deps)

// Use useCallback for functions
const handleClick = useCallback(() => {
  doSomething();
}, []);

// Use useMemo for computed values
const sortedList = useMemo(() => {
  return list.sort();
}, [list]);
```

### 3. When NOT to Use

```jsx
// ❌ Don't memoize cheap calculations
const total = useMemo(() => a + b, [a, b]); // Overkill

// ❌ Don't memoize if component doesn't re-render often
// ❌ Don't memoize if children aren't memoized anyway
// ❌ Don't memoize primitives (they're already stable)
```

### 4. Combine with React.memo

```jsx
// Parent
function Parent() {
  const handleClick = useCallback(() => {}, []);
  const data = useMemo(() => processData(raw), [raw]);

  return <Child onClick={handleClick} data={data} />;
}

// Child - memo makes useCallback/useMemo worthwhile
const Child = React.memo(function Child({ onClick, data }) {
  return <button onClick={onClick}>{data.label}</button>;
});
```

---

## 🏋️ Practice Exercises

### Exercise 1: Search with Debounce

Build a search component with debounced API calls using useMemo and useCallback.

### Exercise 2: Canvas Animation

Create a canvas component with memoized draw functions for smooth animation.

### Exercise 3: Data Grid

Build a sortable, filterable data grid with optimized performance.

---

## ❓ Interview Questions

**Q1: What's the difference between useMemo and useCallback?**

> useMemo memoizes a computed value, useCallback memoizes a function. useCallback(fn, deps) is equivalent to useMemo(() => fn, deps). Use useMemo for expensive calculations, useCallback for stable function references.

**Q2: When should you NOT use useMemo/useCallback?**

> Don't use for cheap calculations (simple math, string concatenation), primitives (already stable), components that rarely re-render, or when children aren't memoized. The overhead of memoization can exceed the cost it's trying to avoid.

**Q3: Does useMemo guarantee the cached value won't be recalculated?**

> No, React may discard cached values under memory pressure. Treat memoization as a performance optimization, not a semantic guarantee. Code should work correctly without it.

**Q4: How do useMemo/useCallback relate to React.memo?**

> React.memo prevents re-renders when props haven't changed. useMemo/useCallback maintain stable references so React.memo comparisons pass. Without stable references, memoized children still re-render.

**Q5: What happens if you omit dependencies?**

> Empty deps `[]`: value/function cached once. Missing deps: stale closures, bugs. Wrong deps: either too many recalculations or stale values. ESLint's exhaustive-deps rule helps catch issues.

---

## 🔑 Key Takeaways

> ### 💡 Summary
>
> - **useMemo** caches computed values
> - **useCallback** caches function references
> - **Profile first** - don't prematurely optimize
> - **Use with React.memo** for child components
> - **List all dependencies** - ESLint helps
> - **Not a guarantee** - React may recalculate
> - **Simple operations** don't need memoization

---

**Next:** [07 - React.memo](./07_React_Memo.md) - Learn about component memoization!
