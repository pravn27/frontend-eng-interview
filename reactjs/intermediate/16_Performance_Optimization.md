# 16 - Performance Optimization

## 📖 React Performance Fundamentals

React is fast by default, but there are patterns and techniques to optimize when needed. The key is to **measure first**, then optimize.

```jsx
// Before optimizing, ask:
// 1. Is there actually a performance problem?
// 2. What specifically is slow?
// 3. Can I measure the impact of my optimization?
```

---

## 🎯 Performance Optimization Strategies

### 1. Reduce Re-renders

```jsx
// React.memo - prevent re-render when props don't change
const ExpensiveList = memo(function ExpensiveList({ items }) {
  return items.map((item) => <Item key={item.id} item={item} />);
});

// useCallback - stable function references
const handleClick = useCallback((id) => {
  setSelected(id);
}, []);

// useMemo - memoize expensive calculations
const sortedItems = useMemo(() => {
  return [...items].sort((a, b) => a.name.localeCompare(b.name));
}, [items]);
```

### 2. Reduce Work Per Render

```jsx
// Virtualization for long lists
import { FixedSizeList } from "react-window";

function VirtualList({ items }) {
  return (
    <FixedSizeList height={400} itemCount={items.length} itemSize={35}>
      {({ index, style }) => <div style={style}>{items[index].name}</div>}
    </FixedSizeList>
  );
}
```

### 3. Reduce Bundle Size

```jsx
// Code splitting
const Dashboard = lazy(() => import("./Dashboard"));

// Tree shaking - import only what you need
import { debounce } from "lodash-es"; // Not: import _ from 'lodash'
```

---

## 📚 React DevTools Profiler

### Using the Profiler

1. Open React DevTools
2. Go to "Profiler" tab
3. Click "Record"
4. Perform the action you want to measure
5. Click "Stop"
6. Analyze the flamegraph

### What to Look For

- **Long render times** (> 16ms for 60fps)
- **Frequent re-renders** of the same component
- **Components re-rendering** when their props haven't changed
- **Expensive calculations** during render

---

## 💻 Optimization Techniques

### 1. Memoization

```jsx
import { memo, useMemo, useCallback } from "react";

// React.memo for component memoization
const TodoItem = memo(function TodoItem({ todo, onToggle }) {
  console.log("TodoItem rendered:", todo.id);
  return (
    <li onClick={() => onToggle(todo.id)}>
      {todo.completed ? "✓" : "○"} {todo.text}
    </li>
  );
});

// useMemo for expensive calculations
function TodoList({ todos, filter }) {
  const filteredTodos = useMemo(() => {
    console.log("Filtering todos...");
    return todos.filter((todo) => {
      if (filter === "active") return !todo.completed;
      if (filter === "completed") return todo.completed;
      return true;
    });
  }, [todos, filter]);

  // useCallback for stable handlers
  const handleToggle = useCallback((id) => {
    setTodos((prev) =>
      prev.map((todo) =>
        todo.id === id ? { ...todo, completed: !todo.completed } : todo
      )
    );
  }, []);

  return (
    <ul>
      {filteredTodos.map((todo) => (
        <TodoItem key={todo.id} todo={todo} onToggle={handleToggle} />
      ))}
    </ul>
  );
}
```

### 2. Virtualization

```jsx
import { FixedSizeList, VariableSizeList } from "react-window";

// Fixed size items
function FixedVirtualList({ items }) {
  const Row = ({ index, style }) => (
    <div style={style} className="row">
      {items[index].name}
    </div>
  );

  return (
    <FixedSizeList
      height={500}
      itemCount={items.length}
      itemSize={50}
      width="100%"
    >
      {Row}
    </FixedSizeList>
  );
}

// Variable size items
function VariableVirtualList({ items }) {
  const getItemSize = (index) => items[index].height || 50;

  return (
    <VariableSizeList
      height={500}
      itemCount={items.length}
      itemSize={getItemSize}
      width="100%"
    >
      {({ index, style }) => <div style={style}>{items[index].content}</div>}
    </VariableSizeList>
  );
}
```

### 3. Debouncing and Throttling

```jsx
import { useState, useMemo, useCallback } from "react";
import { debounce, throttle } from "lodash-es";

// Debounced search
function SearchInput({ onSearch }) {
  const [value, setValue] = useState("");

  const debouncedSearch = useMemo(
    () => debounce((query) => onSearch(query), 300),
    [onSearch]
  );

  const handleChange = (e) => {
    setValue(e.target.value);
    debouncedSearch(e.target.value);
  };

  return <input value={value} onChange={handleChange} />;
}

// Throttled scroll handler
function ScrollTracker() {
  const [scrollY, setScrollY] = useState(0);

  useEffect(() => {
    const handleScroll = throttle(() => {
      setScrollY(window.scrollY);
    }, 100);

    window.addEventListener("scroll", handleScroll);
    return () => {
      window.removeEventListener("scroll", handleScroll);
      handleScroll.cancel();
    };
  }, []);

  return <div>Scroll position: {scrollY}</div>;
}
```

### 4. Lazy Loading Images

```jsx
function LazyImage({ src, alt, placeholder }) {
  const [isLoaded, setIsLoaded] = useState(false);
  const [isInView, setIsInView] = useState(false);
  const imgRef = useRef(null);

  useEffect(() => {
    const observer = new IntersectionObserver(
      ([entry]) => {
        if (entry.isIntersecting) {
          setIsInView(true);
          observer.disconnect();
        }
      },
      { rootMargin: "50px" }
    );

    if (imgRef.current) {
      observer.observe(imgRef.current);
    }

    return () => observer.disconnect();
  }, []);

  return (
    <div ref={imgRef} className="lazy-image-container">
      {isInView ? (
        <img
          src={src}
          alt={alt}
          onLoad={() => setIsLoaded(true)}
          style={{ opacity: isLoaded ? 1 : 0 }}
        />
      ) : (
        placeholder || <div className="placeholder" />
      )}
    </div>
  );
}
```

### 5. State Colocation

```jsx
// ❌ Bad - state too high, causes unnecessary re-renders
function App() {
  const [searchQuery, setSearchQuery] = useState("");
  const [isModalOpen, setIsModalOpen] = useState(false);

  return (
    <div>
      <Header />
      <SearchBar value={searchQuery} onChange={setSearchQuery} />
      <ProductList query={searchQuery} />
      <Footer />
      <Modal isOpen={isModalOpen} />
    </div>
  );
}

// ✅ Good - state colocated with where it's used
function App() {
  return (
    <div>
      <Header />
      <SearchSection /> {/* Owns its own search state */}
      <Footer />
    </div>
  );
}

function SearchSection() {
  const [searchQuery, setSearchQuery] = useState("");

  return (
    <>
      <SearchBar value={searchQuery} onChange={setSearchQuery} />
      <ProductList query={searchQuery} />
    </>
  );
}
```

### 6. Avoiding Inline Objects/Functions

```jsx
// ❌ Bad - new object/function every render
function Parent() {
  return (
    <Child
      style={{ color: "red" }} // New object each render
      onClick={() => console.log("clicked")} // New function each render
    />
  );
}

// ✅ Good - stable references
const childStyle = { color: "red" };

function Parent() {
  const handleClick = useCallback(() => {
    console.log("clicked");
  }, []);

  return <Child style={childStyle} onClick={handleClick} />;
}
```

### 7. useTransition for Non-Urgent Updates

```jsx
import { useState, useTransition } from "react";

function SearchResults() {
  const [query, setQuery] = useState("");
  const [results, setResults] = useState([]);
  const [isPending, startTransition] = useTransition();

  const handleChange = (e) => {
    const value = e.target.value;
    setQuery(value); // Urgent - update input immediately

    startTransition(() => {
      // Non-urgent - can be interrupted
      setResults(filterLargeDataset(value));
    });
  };

  return (
    <div>
      <input value={query} onChange={handleChange} />
      {isPending && <Spinner />}
      <ResultList results={results} style={{ opacity: isPending ? 0.7 : 1 }} />
    </div>
  );
}
```

### 8. useDeferredValue for Expensive Renders

```jsx
import { useDeferredValue, memo } from "react";

function Search({ query }) {
  // Deferred value lags behind during typing
  const deferredQuery = useDeferredValue(query);
  const isStale = query !== deferredQuery;

  return (
    <div style={{ opacity: isStale ? 0.7 : 1 }}>
      <ExpensiveResults query={deferredQuery} />
    </div>
  );
}

const ExpensiveResults = memo(function ExpensiveResults({ query }) {
  // Expensive filtering/rendering
  const results = filterThousandsOfItems(query);
  return (
    <ul>
      {results.map((item) => (
        <li key={item.id}>{item.name}</li>
      ))}
    </ul>
  );
});
```

---

## 🧩 Performance Patterns

### 1. Keys for List Optimization

```jsx
// ❌ Bad - index as key causes issues with reordering
{
  items.map((item, index) => <Item key={index} item={item} />);
}

// ✅ Good - stable, unique key
{
  items.map((item) => <Item key={item.id} item={item} />);
}
```

### 2. Component Splitting

```jsx
// ❌ Everything in one component
function Dashboard() {
  const [count, setCount] = useState(0);

  return (
    <div>
      <ExpensiveChart data={data} /> {/* Re-renders on count change! */}
      <button onClick={() => setCount((c) => c + 1)}>Count: {count}</button>
    </div>
  );
}

// ✅ Split into separate components
function Dashboard() {
  return (
    <div>
      <ChartSection />
      <CounterSection />
    </div>
  );
}

function ChartSection() {
  return <ExpensiveChart data={data} />;
}

function CounterSection() {
  const [count, setCount] = useState(0);
  return <button onClick={() => setCount((c) => c + 1)}>Count: {count}</button>;
}
```

### 3. Context Splitting

```jsx
// ❌ One context for everything
const AppContext = createContext({
  user: null,
  theme: "light",
  notifications: [],
});

// ✅ Split contexts to prevent unnecessary re-renders
const UserContext = createContext(null);
const ThemeContext = createContext("light");
const NotificationContext = createContext([]);
```

---

## ⚠️ Common Performance Mistakes

### 1. Premature Optimization

```jsx
// ❌ Over-optimization without measuring
const value = useMemo(() => 1 + 1, []); // Pointless!
const handleClick = useCallback(() => {}, []); // May not be needed

// ✅ Measure first, then optimize
// Use React DevTools Profiler to identify actual bottlenecks
```

### 2. Wrong Dependency Arrays

```jsx
// ❌ Missing dependencies
const handleClick = useCallback(() => {
  console.log(count); // Stale value!
}, []); // Missing count

// ❌ Object in dependencies (new reference each render)
const options = { limit: 10 };
const data = useMemo(() => fetchData(options), [options]); // Recalculates every render!

// ✅ Correct
const data = useMemo(() => fetchData({ limit: 10 }), []); // Or memoize options
```

---

## 🔧 Performance Checklist

1. **Profile first** - Use React DevTools Profiler
2. **Identify bottlenecks** - Find slow components
3. **Memoize appropriately** - React.memo, useMemo, useCallback
4. **Virtualize long lists** - react-window, react-virtualized
5. **Code split** - React.lazy for routes/heavy components
6. **Colocate state** - Keep state close to where it's used
7. **Avoid inline objects** - Move to variables or useMemo
8. **Use proper keys** - Stable, unique identifiers
9. **Split contexts** - Prevent unnecessary re-renders
10. **Debounce/throttle** - For frequent events

---

## 🏋️ Practice Exercises

### Exercise 1: Optimize a List

Profile and optimize a list of 10,000 items.

### Exercise 2: Search Optimization

Build a search with debouncing and virtualized results.

### Exercise 3: Dashboard Performance

Optimize a dashboard with multiple widgets and charts.

---

## ❓ Interview Questions

**Q1: How do you approach performance optimization in React?**

> First, measure using React DevTools Profiler to identify actual bottlenecks. Then apply targeted optimizations: memoization for expensive calculations, React.memo for components that re-render with same props, virtualization for long lists, and code splitting for large bundles.

**Q2: When should you use useMemo vs useCallback?**

> useMemo memoizes computed values (calculations, filtered/sorted data). useCallback memoizes functions (event handlers passed to memoized children). Both are for optimization - don't use them everywhere.

**Q3: What causes unnecessary re-renders?**

> Parent re-renders, inline objects/functions creating new references, context updates, missing memoization, and state updates in parent that don't affect child props.

**Q4: How does virtualization improve performance?**

> Instead of rendering all list items, virtualization only renders visible items plus a small buffer. This dramatically reduces DOM nodes and React work for large lists.

**Q5: What is useTransition and when would you use it?**

> useTransition marks state updates as non-urgent, allowing React to keep the UI responsive during expensive updates. Use it for filtering large lists, navigating, or any update that might cause lag.

---

## 🔑 Key Takeaways

> ### 💡 Summary
>
> - **Measure first** - Don't optimize blindly
> - **React is fast** - Only optimize when needed
> - **Memoize strategically** - memo, useMemo, useCallback
> - **Virtualize lists** - For 100+ items
> - **Code split** - Reduce initial bundle
> - **Colocate state** - Minimize re-render scope
> - **Profile regularly** - Part of development workflow

---

**Next:** [17 - Data Fetching Patterns](./17_Data_Fetching_Patterns.md) - Learn about API integration!
