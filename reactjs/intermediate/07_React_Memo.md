# 07 - React.memo

## 📖 What is React.memo?

`React.memo` is a higher-order component that memoizes a component, preventing re-renders when props haven't changed. It performs a **shallow comparison** of props.

```jsx
import { memo } from "react";

// Without memo - re-renders every time parent re-renders
function ExpensiveComponent({ data }) {
  console.log("Rendering ExpensiveComponent");
  return <div>{data.name}</div>;
}

// With memo - only re-renders when props change
const MemoizedComponent = memo(function ExpensiveComponent({ data }) {
  console.log("Rendering ExpensiveComponent");
  return <div>{data.name}</div>;
});
```

---

## 🎯 Why React.memo?

### The Problem: Unnecessary Re-renders

```jsx
function Parent() {
  const [count, setCount] = useState(0);
  const [items] = useState(["a", "b", "c"]);

  return (
    <div>
      <button onClick={() => setCount(count + 1)}>Count: {count}</button>
      {/* ❌ Re-renders every time count changes, even though items didn't */}
      <ExpensiveList items={items} />
    </div>
  );
}

function ExpensiveList({ items }) {
  console.log("ExpensiveList rendered"); // Logs on every count change!
  return (
    <ul>
      {items.map((item) => (
        <li key={item}>{item}</li>
      ))}
    </ul>
  );
}
```

### The Solution: React.memo

```jsx
function Parent() {
  const [count, setCount] = useState(0);
  const [items] = useState(["a", "b", "c"]);

  return (
    <div>
      <button onClick={() => setCount(count + 1)}>Count: {count}</button>
      {/* ✅ Only re-renders when items change */}
      <MemoizedList items={items} />
    </div>
  );
}

const MemoizedList = memo(function ExpensiveList({ items }) {
  console.log("ExpensiveList rendered"); // Only logs when items change
  return (
    <ul>
      {items.map((item) => (
        <li key={item}>{item}</li>
      ))}
    </ul>
  );
});
```

---

## 📚 Core Concepts

### 1. Basic Usage

```jsx
// Method 1: Wrap function component
const MemoizedComponent = memo(function MyComponent(props) {
  return <div>{props.value}</div>;
});

// Method 2: Wrap arrow function
const MemoizedComponent = memo((props) => {
  return <div>{props.value}</div>;
});

// Method 3: Wrap existing component
function MyComponent(props) {
  return <div>{props.value}</div>;
}
const MemoizedComponent = memo(MyComponent);
```

### 2. Shallow Comparison

```jsx
// memo performs shallow comparison:
// - Primitives: compared by value
// - Objects/Arrays: compared by reference

// ✅ Works - primitives
<MemoizedComponent count={5} name="Alice" />

// ⚠️ May not work - new object every render
<MemoizedComponent config={{ theme: "dark" }} />

// ✅ Fix - stable reference
const config = useMemo(() => ({ theme: "dark" }), []);
<MemoizedComponent config={config} />
```

### 3. Custom Comparison Function

```jsx
const MemoizedComponent = memo(
  function MyComponent({ user, onClick }) {
    return <div onClick={onClick}>{user.name}</div>;
  },
  // Custom comparison function
  (prevProps, nextProps) => {
    // Return true if props are equal (don't re-render)
    // Return false if props are different (do re-render)
    return (
      prevProps.user.id === nextProps.user.id &&
      prevProps.user.name === nextProps.user.name
    );
  }
);
```

---

## 💻 Practical Examples

### 1. List with Memoized Items

```jsx
const TodoItem = memo(function TodoItem({ todo, onToggle, onDelete }) {
  console.log("Rendering TodoItem:", todo.id);

  return (
    <li>
      <input
        type="checkbox"
        checked={todo.completed}
        onChange={() => onToggle(todo.id)}
      />
      <span
        style={{ textDecoration: todo.completed ? "line-through" : "none" }}
      >
        {todo.text}
      </span>
      <button onClick={() => onDelete(todo.id)}>Delete</button>
    </li>
  );
});

function TodoList() {
  const [todos, setTodos] = useState([
    { id: 1, text: "Learn React", completed: false },
    { id: 2, text: "Build app", completed: false },
  ]);

  // ✅ Memoized callbacks prevent TodoItem re-renders
  const handleToggle = useCallback((id) => {
    setTodos((prev) =>
      prev.map((todo) =>
        todo.id === id ? { ...todo, completed: !todo.completed } : todo
      )
    );
  }, []);

  const handleDelete = useCallback((id) => {
    setTodos((prev) => prev.filter((todo) => todo.id !== id));
  }, []);

  return (
    <ul>
      {todos.map((todo) => (
        <TodoItem
          key={todo.id}
          todo={todo}
          onToggle={handleToggle}
          onDelete={handleDelete}
        />
      ))}
    </ul>
  );
}
```

### 2. Dashboard with Multiple Widgets

```jsx
const StatsWidget = memo(function StatsWidget({ title, value, change }) {
  console.log("Rendering StatsWidget:", title);

  return (
    <div className="widget">
      <h3>{title}</h3>
      <p className="value">{value}</p>
      <p className={change >= 0 ? "positive" : "negative"}>
        {change >= 0 ? "+" : ""}
        {change}%
      </p>
    </div>
  );
});

const ChartWidget = memo(function ChartWidget({ data, type }) {
  console.log("Rendering ChartWidget:", type);

  return (
    <div className="chart">
      {/* Expensive chart rendering */}
      <Chart data={data} type={type} />
    </div>
  );
});

function Dashboard() {
  const [refreshKey, setRefreshKey] = useState(0);
  const [stats, setStats] = useState({
    revenue: { value: 50000, change: 12 },
    users: { value: 1200, change: 8 },
    orders: { value: 340, change: -3 },
  });

  // Only ChartWidget with matching data will re-render
  const chartData = useMemo(
    () => ({
      revenue: generateChartData(stats.revenue),
      users: generateChartData(stats.users),
    }),
    [stats]
  );

  return (
    <div className="dashboard">
      <button onClick={() => setRefreshKey((k) => k + 1)}>
        Refresh Key: {refreshKey}
      </button>

      <div className="widgets">
        <StatsWidget
          title="Revenue"
          value={`$${stats.revenue.value}`}
          change={stats.revenue.change}
        />
        <StatsWidget
          title="Users"
          value={stats.users.value}
          change={stats.users.change}
        />
        <StatsWidget
          title="Orders"
          value={stats.orders.value}
          change={stats.orders.change}
        />
      </div>

      <div className="charts">
        <ChartWidget data={chartData.revenue} type="revenue" />
        <ChartWidget data={chartData.users} type="users" />
      </div>
    </div>
  );
}
```

### 3. Form with Memoized Fields

```jsx
const FormField = memo(function FormField({
  name,
  label,
  value,
  error,
  onChange,
  onBlur,
}) {
  console.log("Rendering FormField:", name);

  return (
    <div className="form-field">
      <label htmlFor={name}>{label}</label>
      <input
        id={name}
        name={name}
        value={value}
        onChange={onChange}
        onBlur={onBlur}
      />
      {error && <span className="error">{error}</span>}
    </div>
  );
});

function RegistrationForm() {
  const [formData, setFormData] = useState({
    username: "",
    email: "",
    password: "",
  });
  const [errors, setErrors] = useState({});

  // Single memoized handler that works for all fields
  const handleChange = useCallback((e) => {
    const { name, value } = e.target;
    setFormData((prev) => ({ ...prev, [name]: value }));
  }, []);

  const handleBlur = useCallback((e) => {
    const { name, value } = e.target;
    const error = validateField(name, value);
    setErrors((prev) => ({ ...prev, [name]: error }));
  }, []);

  return (
    <form>
      <FormField
        name="username"
        label="Username"
        value={formData.username}
        error={errors.username}
        onChange={handleChange}
        onBlur={handleBlur}
      />
      <FormField
        name="email"
        label="Email"
        value={formData.email}
        error={errors.email}
        onChange={handleChange}
        onBlur={handleBlur}
      />
      <FormField
        name="password"
        label="Password"
        value={formData.password}
        error={errors.password}
        onChange={handleChange}
        onBlur={handleBlur}
      />
    </form>
  );
}
```

### 4. Custom Comparison for Deep Objects

```jsx
const UserCard = memo(
  function UserCard({ user, onSelect }) {
    console.log("Rendering UserCard:", user.id);

    return (
      <div className="user-card" onClick={() => onSelect(user.id)}>
        <img src={user.avatar} alt={user.name} />
        <h3>{user.name}</h3>
        <p>{user.email}</p>
        <span className={user.isOnline ? "online" : "offline"}>
          {user.isOnline ? "Online" : "Offline"}
        </span>
      </div>
    );
  },
  (prevProps, nextProps) => {
    // Deep comparison for user object
    return (
      prevProps.user.id === nextProps.user.id &&
      prevProps.user.name === nextProps.user.name &&
      prevProps.user.email === nextProps.user.email &&
      prevProps.user.avatar === nextProps.user.avatar &&
      prevProps.user.isOnline === nextProps.user.isOnline &&
      prevProps.onSelect === nextProps.onSelect
    );
  }
);
```

---

## 🧩 Patterns

### 1. Combining with useCallback

```jsx
function Parent() {
  const [count, setCount] = useState(0);

  // ✅ Stable function reference
  const handleClick = useCallback(() => {
    console.log("Clicked!");
  }, []);

  return (
    <div>
      <button onClick={() => setCount((c) => c + 1)}>Count: {count}</button>
      <MemoizedChild onClick={handleClick} />
    </div>
  );
}

const MemoizedChild = memo(function Child({ onClick }) {
  console.log("Child rendered");
  return <button onClick={onClick}>Click me</button>;
});
```

### 2. Combining with useMemo

```jsx
function Parent() {
  const [count, setCount] = useState(0);
  const [items] = useState([1, 2, 3]);

  // ✅ Stable object reference
  const config = useMemo(
    () => ({
      items,
      settings: { theme: "dark" },
    }),
    [items]
  );

  return (
    <div>
      <button onClick={() => setCount((c) => c + 1)}>Count: {count}</button>
      <MemoizedChild config={config} />
    </div>
  );
}

const MemoizedChild = memo(function Child({ config }) {
  console.log("Child rendered");
  return <div>Items: {config.items.length}</div>;
});
```

### 3. Selective Memoization

```jsx
// Only memoize components that:
// 1. Render often
// 2. Render with same props
// 3. Are expensive to render

// ✅ Good candidate - expensive list
const ProductList = memo(function ProductList({ products }) {
  return products.map((p) => <ProductCard key={p.id} product={p} />);
});

// ✅ Good candidate - rarely changing data
const Header = memo(function Header({ user }) {
  return <header>Welcome, {user.name}</header>;
});

// ❌ Bad candidate - always different props
const TimeDisplay = memo(function TimeDisplay({ time }) {
  return <span>{time.toISOString()}</span>; // time changes every render
});
```

---

## ⚠️ Common Mistakes

### 1. Forgetting to Memoize Callbacks

```jsx
function Parent() {
  const [count, setCount] = useState(0);

  // ❌ New function every render - defeats memo
  const handleClick = () => console.log("clicked");

  return <MemoizedChild onClick={handleClick} />;
}

// ✅ Fix
function Parent() {
  const [count, setCount] = useState(0);

  const handleClick = useCallback(() => console.log("clicked"), []);

  return <MemoizedChild onClick={handleClick} />;
}
```

### 2. Passing Inline Objects

```jsx
// ❌ New object every render - defeats memo
<MemoizedChild style={{ color: "red" }} />
<MemoizedChild data={{ id: 1 }} />

// ✅ Fix - stable references
const style = useMemo(() => ({ color: "red" }), []);
const data = useMemo(() => ({ id: 1 }), []);

<MemoizedChild style={style} />
<MemoizedChild data={data} />
```

### 3. Memoizing Everything

```jsx
// ❌ Over-memoization - adds overhead without benefit
const SimpleText = memo(function SimpleText({ text }) {
  return <span>{text}</span>; // Trivial component
});

// ✅ Only memoize when there's measurable benefit
function SimpleText({ text }) {
  return <span>{text}</span>;
}
```

### 4. Wrong Custom Comparison

```jsx
// ❌ Wrong - always returns true, never re-renders
const BadMemo = memo(Component, () => true);

// ❌ Wrong - always returns false, always re-renders
const BadMemo = memo(Component, () => false);

// ✅ Correct - compare relevant props
const GoodMemo = memo(Component, (prev, next) => {
  return prev.id === next.id && prev.name === next.name;
});
```

---

## 🔧 Best Practices

### 1. Profile Before Memoizing

```jsx
// Use React DevTools Profiler to:
// 1. Identify slow-rendering components
// 2. See which components re-render unnecessarily
// 3. Measure the impact of memoization
```

### 2. Memoize from the Bottom Up

```jsx
// Start by memoizing leaf components
const ListItem = memo(function ListItem({ item }) {
  return <li>{item.name}</li>;
});

// Then parent components if needed
const List = memo(function List({ items }) {
  return (
    <ul>
      {items.map((item) => (
        <ListItem key={item.id} item={item} />
      ))}
    </ul>
  );
});
```

### 3. Keep Props Simple

```jsx
// ✅ Simple, stable props
<UserCard
  id={user.id}
  name={user.name}
  avatar={user.avatar}
/>

// ❌ Complex, unstable props
<UserCard
  user={user}
  config={{ theme: "dark" }}
  onClick={() => selectUser(user.id)}
/>
```

---

## 🏋️ Practice Exercises

### Exercise 1: Optimize a List

Take a slow list component and optimize it with memo, useCallback, and useMemo.

### Exercise 2: Dashboard Widgets

Build a dashboard where only affected widgets re-render on data changes.

### Exercise 3: Form Optimization

Create a large form where field changes only re-render the affected field.

---

## ❓ Interview Questions

**Q1: What is React.memo and when would you use it?**

> React.memo is a HOC that memoizes a component, preventing re-renders when props haven't changed. Use it for components that render often with the same props, or expensive components where re-renders are costly.

**Q2: How does React.memo compare props?**

> By default, React.memo performs a shallow comparison of props. For objects and arrays, it compares references, not contents. You can provide a custom comparison function as the second argument.

**Q3: Does React.memo guarantee no re-renders?**

> No. React.memo only prevents re-renders from parent updates when props are equal. Components still re-render when their own state changes or when consuming context that updates.

**Q4: What's the relationship between memo, useCallback, and useMemo?**

> They work together: memo prevents re-renders when props are equal, useCallback creates stable function references, and useMemo creates stable object references. Without useCallback/useMemo, memo may be ineffective for non-primitive props.

**Q5: When should you NOT use React.memo?**

> Don't use memo for: components that almost always receive new props, components that are cheap to render, components with only primitive props, or when it adds complexity without measurable benefit.

---

## 🔑 Key Takeaways

> ### 💡 Summary
>
> - **React.memo** prevents re-renders when props haven't changed
> - **Shallow comparison** by default - objects compared by reference
> - **Combine with useCallback/useMemo** for function/object props
> - **Profile first** - don't prematurely optimize
> - **Custom comparison** for complex objects
> - **Not a guarantee** - state/context changes still trigger re-renders
> - **Measure impact** - memoization has overhead

---

**Next:** [08 - Error Boundaries](./08_Error_Boundaries.md) - Learn about handling errors gracefully!
