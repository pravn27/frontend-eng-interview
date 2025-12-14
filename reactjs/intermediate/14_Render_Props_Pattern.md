# 14 - Render Props Pattern

## 📖 What is Render Props?

Render Props is a pattern where a component receives a function as a prop (or children) and calls it to determine what to render. It's a way to **share code between components** using a prop whose value is a function.

```jsx
// Component with render prop
function Mouse({ render }) {
  const [position, setPosition] = useState({ x: 0, y: 0 });

  const handleMouseMove = (e) => {
    setPosition({ x: e.clientX, y: e.clientY });
  };

  return <div onMouseMove={handleMouseMove}>{render(position)}</div>;
}

// Usage
<Mouse
  render={({ x, y }) => (
    <p>
      Mouse position: {x}, {y}
    </p>
  )}
/>;
```

---

## 🎯 Why Render Props?

### Sharing Behavior Without Dictating UI

```jsx
// The Mouse component provides behavior (tracking)
// But lets the consumer decide the UI

// Consumer 1: Simple text
<Mouse render={({ x, y }) => (
  <p>Position: {x}, {y}</p>
)} />

// Consumer 2: Following cursor
<Mouse render={({ x, y }) => (
  <div style={{ position: 'fixed', left: x, top: y }}>
    🐱
  </div>
)} />

// Consumer 3: Drawing app
<Mouse render={({ x, y }) => (
  <Canvas drawPoint={{ x, y }} />
)} />
```

---

## 📚 Core Concepts

### 1. Render Prop

```jsx
function DataProvider({ render }) {
  const [data, setData] = useState(null);

  // ... fetch data logic

  return render(data);
}

<DataProvider render={(data) => <Display data={data} />} />;
```

### 2. Children as Function

```jsx
function DataProvider({ children }) {
  const [data, setData] = useState(null);

  // ... fetch data logic

  return children(data);
}

<DataProvider>{(data) => <Display data={data} />}</DataProvider>;
```

### 3. Multiple Render Props

```jsx
function Layout({ header, sidebar, content, footer }) {
  return (
    <div className="layout">
      <header>{header()}</header>
      <aside>{sidebar()}</aside>
      <main>{content()}</main>
      <footer>{footer()}</footer>
    </div>
  );
}

<Layout
  header={() => <h1>Title</h1>}
  sidebar={() => <Navigation />}
  content={() => <Article />}
  footer={() => <Copyright />}
/>;
```

---

## 💻 Practical Examples

### 1. Toggle Component

```jsx
function Toggle({ children }) {
  const [on, setOn] = useState(false);

  const toggle = () => setOn(!on);

  return children({ on, toggle });
}

// Usage
<Toggle>
  {({ on, toggle }) => (
    <div>
      <button onClick={toggle}>{on ? "Hide" : "Show"}</button>
      {on && <div>Content is visible!</div>}
    </div>
  )}
</Toggle>;
```

### 2. Mouse Tracker

```jsx
function MouseTracker({ children }) {
  const [position, setPosition] = useState({ x: 0, y: 0 });

  useEffect(() => {
    const handleMove = (e) => {
      setPosition({ x: e.clientX, y: e.clientY });
    };

    window.addEventListener("mousemove", handleMove);
    return () => window.removeEventListener("mousemove", handleMove);
  }, []);

  return children(position);
}

// Usage 1: Display coordinates
<MouseTracker>
  {({ x, y }) => (
    <p>Mouse: {x}, {y}</p>
  )}
</MouseTracker>

// Usage 2: Cursor follower
<MouseTracker>
  {({ x, y }) => (
    <div
      style={{
        position: "fixed",
        left: x - 10,
        top: y - 10,
        width: 20,
        height: 20,
        borderRadius: "50%",
        background: "red",
        pointerEvents: "none",
      }}
    />
  )}
</MouseTracker>
```

### 3. Fetch Component

```jsx
function Fetch({ url, children }) {
  const [state, setState] = useState({
    data: null,
    loading: true,
    error: null,
  });

  useEffect(() => {
    setState({ data: null, loading: true, error: null });

    fetch(url)
      .then((res) => res.json())
      .then((data) => setState({ data, loading: false, error: null }))
      .catch((error) => setState({ data: null, loading: false, error }));
  }, [url]);

  return children(state);
}

// Usage
<Fetch url="/api/users">
  {({ data, loading, error }) => {
    if (loading) return <Spinner />;
    if (error) return <Error message={error.message} />;
    return (
      <ul>
        {data.map((user) => (
          <li key={user.id}>{user.name}</li>
        ))}
      </ul>
    );
  }}
</Fetch>;
```

### 4. Form State Manager

```jsx
function Form({ initialValues, validate, children }) {
  const [values, setValues] = useState(initialValues);
  const [errors, setErrors] = useState({});
  const [touched, setTouched] = useState({});

  const handleChange = (name) => (e) => {
    const value =
      e.target.type === "checkbox" ? e.target.checked : e.target.value;
    setValues((prev) => ({ ...prev, [name]: value }));
  };

  const handleBlur = (name) => () => {
    setTouched((prev) => ({ ...prev, [name]: true }));
    if (validate) {
      setErrors(validate(values));
    }
  };

  const handleSubmit = (onSubmit) => (e) => {
    e.preventDefault();
    const validationErrors = validate ? validate(values) : {};
    setErrors(validationErrors);

    if (Object.keys(validationErrors).length === 0) {
      onSubmit(values);
    }
  };

  const getFieldProps = (name) => ({
    name,
    value: values[name],
    onChange: handleChange(name),
    onBlur: handleBlur(name),
  });

  return children({
    values,
    errors,
    touched,
    handleChange,
    handleBlur,
    handleSubmit,
    getFieldProps,
  });
}

// Usage
<Form
  initialValues={{ email: "", password: "" }}
  validate={(values) => {
    const errors = {};
    if (!values.email) errors.email = "Required";
    if (!values.password) errors.password = "Required";
    return errors;
  }}
>
  {({ values, errors, touched, getFieldProps, handleSubmit }) => (
    <form onSubmit={handleSubmit(console.log)}>
      <input {...getFieldProps("email")} type="email" placeholder="Email" />
      {touched.email && errors.email && <span>{errors.email}</span>}

      <input
        {...getFieldProps("password")}
        type="password"
        placeholder="Password"
      />
      {touched.password && errors.password && <span>{errors.password}</span>}

      <button type="submit">Submit</button>
    </form>
  )}
</Form>;
```

### 5. Dropdown with Render Props

```jsx
function Dropdown({ items, children }) {
  const [isOpen, setIsOpen] = useState(false);
  const [selectedItem, setSelectedItem] = useState(null);
  const containerRef = useRef(null);

  useEffect(() => {
    const handleClickOutside = (e) => {
      if (containerRef.current && !containerRef.current.contains(e.target)) {
        setIsOpen(false);
      }
    };
    document.addEventListener("mousedown", handleClickOutside);
    return () => document.removeEventListener("mousedown", handleClickOutside);
  }, []);

  const toggle = () => setIsOpen(!isOpen);
  const select = (item) => {
    setSelectedItem(item);
    setIsOpen(false);
  };

  return (
    <div ref={containerRef}>
      {children({
        isOpen,
        selectedItem,
        toggle,
        select,
        items,
      })}
    </div>
  );
}

// Usage
<Dropdown items={["Apple", "Banana", "Cherry"]}>
  {({ isOpen, selectedItem, toggle, select, items }) => (
    <div className="dropdown">
      <button onClick={toggle}>{selectedItem || "Select fruit"} ▼</button>
      {isOpen && (
        <ul className="dropdown-menu">
          {items.map((item) => (
            <li key={item} onClick={() => select(item)}>
              {item}
            </li>
          ))}
        </ul>
      )}
    </div>
  )}
</Dropdown>;
```

### 6. Infinite Scroll

```jsx
function InfiniteScroll({ fetchMore, hasMore, threshold = 100, children }) {
  const [items, setItems] = useState([]);
  const [loading, setLoading] = useState(false);
  const containerRef = useRef(null);

  const loadMore = async () => {
    if (loading || !hasMore) return;

    setLoading(true);
    const newItems = await fetchMore(items.length);
    setItems((prev) => [...prev, ...newItems]);
    setLoading(false);
  };

  useEffect(() => {
    loadMore();
  }, []);

  useEffect(() => {
    const container = containerRef.current;

    const handleScroll = () => {
      if (!container) return;

      const { scrollTop, scrollHeight, clientHeight } = container;
      if (scrollHeight - scrollTop - clientHeight < threshold) {
        loadMore();
      }
    };

    container?.addEventListener("scroll", handleScroll);
    return () => container?.removeEventListener("scroll", handleScroll);
  }, [loading, hasMore]);

  return (
    <div ref={containerRef} style={{ overflow: "auto", height: "100%" }}>
      {children({ items, loading, hasMore })}
    </div>
  );
}

// Usage
<InfiniteScroll
  fetchMore={(offset) => fetchItems(offset, 20)}
  hasMore={hasMore}
>
  {({ items, loading }) => (
    <>
      {items.map((item) => (
        <ItemCard key={item.id} item={item} />
      ))}
      {loading && <Spinner />}
    </>
  )}
</InfiniteScroll>;
```

---

## 🧩 Patterns

### 1. Combining Render Props

```jsx
<MouseTracker>
  {(mouse) => (
    <WindowSize>
      {(size) => (
        <div>
          Mouse: {mouse.x}, {mouse.y}
          <br />
          Window: {size.width} x {size.height}
        </div>
      )}
    </WindowSize>
  )}
</MouseTracker>
```

### 2. Render Props with HOC

```jsx
// Convert render prop to HOC
function withMouse(Component) {
  return function WithMouse(props) {
    return (
      <MouseTracker>
        {(mouse) => <Component {...props} mouse={mouse} />}
      </MouseTracker>
    );
  };
}
```

---

## ⚠️ Common Mistakes

### 1. Inline Functions Causing Re-renders

```jsx
// ❌ New function every render
<MouseTracker>{(mouse) => <Expensive mouse={mouse} />}</MouseTracker>;

// ✅ Memoize if expensive
const MemoizedExpensive = memo(Expensive);

<MouseTracker>{(mouse) => <MemoizedExpensive mouse={mouse} />}</MouseTracker>;
```

### 2. Forgetting to Call Children

```jsx
// ❌ Wrong - children is a function, not JSX
function Provider({ children, data }) {
  return children; // Error!
}

// ✅ Correct - call children with data
function Provider({ children, data }) {
  return children(data);
}
```

---

## 🔧 Render Props vs Hooks

Modern React prefers hooks for most use cases:

```jsx
// Render Props
<MouseTracker>
  {({ x, y }) => (
    <p>
      {x}, {y}
    </p>
  )}
</MouseTracker>;

// Hook (preferred)
function Component() {
  const { x, y } = useMouse();
  return (
    <p>
      {x}, {y}
    </p>
  );
}
```

### When to Use Render Props

- When you need to **control the rendered output**
- When integrating with **class components**
- For **compound components** that share implicit state

---

## 🏋️ Practice Exercises

### Exercise 1: Countdown Timer

Create a render prop component that provides countdown functionality.

### Exercise 2: Intersection Observer

Build a component that tracks element visibility.

### Exercise 3: Drag and Drop

Create a draggable component using render props.

---

## ❓ Interview Questions

**Q1: What is the Render Props pattern?**

> Render Props is a pattern where a component accepts a function prop that returns React elements. The component calls this function, passing its internal state or logic, letting the consumer control the rendered output.

**Q2: Children as function vs render prop?**

> They're equivalent. Children as function (`<C>{(x) => ...}</C>`) is often cleaner, while named render props (`render`, `renderItem`) can be more explicit and allow multiple render points.

**Q3: What problem does Render Props solve?**

> It solves code reuse and logic sharing between components while giving consumers full control over what gets rendered. It separates behavior from presentation.

**Q4: Render Props vs Hooks?**

> Hooks are generally preferred in modern React as they're simpler and don't add nesting. Render Props are still useful when you need to control rendered output or work with compound components.

**Q5: Performance considerations with Render Props?**

> Inline render functions create new functions each render, which can cause child re-renders. Use React.memo on rendered children or extract stable functions when performance is critical.

---

## 🔑 Key Takeaways

> ### 💡 Summary
>
> - **Render Props** share logic via function props
> - **Children as function** is a common pattern
> - **Separates behavior from UI** - provider gives data, consumer decides rendering
> - **Prefer Hooks** for new code
> - **Still useful** for compound components and controlled rendering
> - **Watch performance** with inline functions

---

**Next:** [15 - Compound Components](./15_Compound_Components.md) - Learn about flexible component APIs!
