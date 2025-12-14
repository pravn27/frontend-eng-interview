# 04 - Custom Hooks

## 📖 What are Custom Hooks?

Custom hooks are JavaScript functions that start with "use" and allow you to **extract and reuse stateful logic** between components. They can use other hooks inside them.

```jsx
// Custom hook
function useCounter(initialValue = 0) {
  const [count, setCount] = useState(initialValue);

  const increment = () => setCount((c) => c + 1);
  const decrement = () => setCount((c) => c - 1);
  const reset = () => setCount(initialValue);

  return { count, increment, decrement, reset };
}

// Usage in components
function Counter() {
  const { count, increment, decrement, reset } = useCounter(10);

  return (
    <div>
      <p>Count: {count}</p>
      <button onClick={increment}>+</button>
      <button onClick={decrement}>-</button>
      <button onClick={reset}>Reset</button>
    </div>
  );
}
```

---

## 🎯 Why Custom Hooks?

### Without Custom Hooks (Duplicated Logic)

```jsx
// ❌ Same logic repeated in multiple components
function UserProfile() {
  const [user, setUser] = useState(null);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);

  useEffect(() => {
    fetch("/api/user")
      .then((res) => res.json())
      .then(setUser)
      .catch(setError)
      .finally(() => setLoading(false));
  }, []);

  // ... render
}

function ProductList() {
  const [products, setProducts] = useState([]);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);

  useEffect(() => {
    fetch("/api/products")
      .then((res) => res.json())
      .then(setProducts)
      .catch(setError)
      .finally(() => setLoading(false));
  }, []);

  // ... render
}
```

### With Custom Hooks (Reusable Logic)

```jsx
// ✅ Extract to custom hook
function useFetch(url) {
  const [data, setData] = useState(null);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);

  useEffect(() => {
    const controller = new AbortController();

    fetch(url, { signal: controller.signal })
      .then((res) => res.json())
      .then(setData)
      .catch((err) => {
        if (err.name !== "AbortError") setError(err);
      })
      .finally(() => setLoading(false));

    return () => controller.abort();
  }, [url]);

  return { data, loading, error };
}

// Clean component usage
function UserProfile() {
  const { data: user, loading, error } = useFetch("/api/user");
  // ... render
}

function ProductList() {
  const { data: products, loading, error } = useFetch("/api/products");
  // ... render
}
```

---

## 📚 Rules for Custom Hooks

### 1. Name Must Start with "use"

```jsx
// ✅ Correct
function useLocalStorage(key, initialValue) {}
function useWindowSize() {}
function useDebounce(value, delay) {}

// ❌ Wrong - won't work with React's hook rules
function getLocalStorage(key) {}
function windowSize() {}
```

### 2. Can Call Other Hooks

```jsx
function useAuth() {
  const [user, setUser] = useState(null); // useState ✅
  const navigate = useNavigate(); // useNavigate ✅

  useEffect(() => {
    // useEffect ✅
    // Check authentication
  }, []);

  const { data } = useFetch("/api/me"); // Custom hook ✅

  return { user, login, logout };
}
```

### 3. Each Call Gets Its Own State

```jsx
function useCounter() {
  const [count, setCount] = useState(0);
  return { count, setCount };
}

function App() {
  const counter1 = useCounter(); // Independent state
  const counter2 = useCounter(); // Independent state

  // counter1.count and counter2.count are separate!
}
```

---

## 💻 Common Custom Hooks

### 1. useLocalStorage

```jsx
function useLocalStorage(key, initialValue) {
  // Get from local storage or use initial value
  const [storedValue, setStoredValue] = useState(() => {
    try {
      const item = window.localStorage.getItem(key);
      return item ? JSON.parse(item) : initialValue;
    } catch (error) {
      console.error(error);
      return initialValue;
    }
  });

  // Sync to local storage
  const setValue = (value) => {
    try {
      const valueToStore =
        value instanceof Function ? value(storedValue) : value;
      setStoredValue(valueToStore);
      window.localStorage.setItem(key, JSON.stringify(valueToStore));
    } catch (error) {
      console.error(error);
    }
  };

  return [storedValue, setValue];
}

// Usage
function Settings() {
  const [theme, setTheme] = useLocalStorage("theme", "light");
  const [fontSize, setFontSize] = useLocalStorage("fontSize", 16);

  return (
    <div>
      <select value={theme} onChange={(e) => setTheme(e.target.value)}>
        <option value="light">Light</option>
        <option value="dark">Dark</option>
      </select>
    </div>
  );
}
```

### 2. useDebounce

```jsx
function useDebounce(value, delay = 500) {
  const [debouncedValue, setDebouncedValue] = useState(value);

  useEffect(() => {
    const timer = setTimeout(() => {
      setDebouncedValue(value);
    }, delay);

    return () => clearTimeout(timer);
  }, [value, delay]);

  return debouncedValue;
}

// Usage
function SearchInput() {
  const [query, setQuery] = useState("");
  const debouncedQuery = useDebounce(query, 300);

  useEffect(() => {
    if (debouncedQuery) {
      // API call with debounced value
      searchAPI(debouncedQuery);
    }
  }, [debouncedQuery]);

  return (
    <input
      value={query}
      onChange={(e) => setQuery(e.target.value)}
      placeholder="Search..."
    />
  );
}
```

### 3. useWindowSize

```jsx
function useWindowSize() {
  const [size, setSize] = useState({
    width: window.innerWidth,
    height: window.innerHeight,
  });

  useEffect(() => {
    const handleResize = () => {
      setSize({
        width: window.innerWidth,
        height: window.innerHeight,
      });
    };

    window.addEventListener("resize", handleResize);
    return () => window.removeEventListener("resize", handleResize);
  }, []);

  return size;
}

// Usage
function ResponsiveComponent() {
  const { width } = useWindowSize();

  return <div>{width > 768 ? <DesktopLayout /> : <MobileLayout />}</div>;
}
```

### 4. useOnClickOutside

```jsx
function useOnClickOutside(ref, handler) {
  useEffect(() => {
    const listener = (event) => {
      if (!ref.current || ref.current.contains(event.target)) {
        return;
      }
      handler(event);
    };

    document.addEventListener("mousedown", listener);
    document.addEventListener("touchstart", listener);

    return () => {
      document.removeEventListener("mousedown", listener);
      document.removeEventListener("touchstart", listener);
    };
  }, [ref, handler]);
}

// Usage
function Dropdown() {
  const [isOpen, setIsOpen] = useState(false);
  const dropdownRef = useRef(null);

  useOnClickOutside(dropdownRef, () => setIsOpen(false));

  return (
    <div ref={dropdownRef}>
      <button onClick={() => setIsOpen(!isOpen)}>Toggle</button>
      {isOpen && <div className="menu">Menu content</div>}
    </div>
  );
}
```

### 5. useToggle

```jsx
function useToggle(initialValue = false) {
  const [value, setValue] = useState(initialValue);

  const toggle = useCallback(() => setValue((v) => !v), []);
  const setTrue = useCallback(() => setValue(true), []);
  const setFalse = useCallback(() => setValue(false), []);

  return { value, toggle, setTrue, setFalse };
}

// Usage
function Modal() {
  const { value: isOpen, toggle, setFalse: close } = useToggle();

  return (
    <>
      <button onClick={toggle}>Open Modal</button>
      {isOpen && (
        <div className="modal">
          <p>Modal Content</p>
          <button onClick={close}>Close</button>
        </div>
      )}
    </>
  );
}
```

### 6. useFetch

```jsx
function useFetch(url, options = {}) {
  const [data, setData] = useState(null);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);

  // Stable reference for options
  const optionsRef = useRef(options);
  optionsRef.current = options;

  const refetch = useCallback(() => {
    setLoading(true);
    setError(null);

    const controller = new AbortController();

    fetch(url, { ...optionsRef.current, signal: controller.signal })
      .then((res) => {
        if (!res.ok) throw new Error(`HTTP error! status: ${res.status}`);
        return res.json();
      })
      .then(setData)
      .catch((err) => {
        if (err.name !== "AbortError") {
          setError(err);
        }
      })
      .finally(() => setLoading(false));

    return () => controller.abort();
  }, [url]);

  useEffect(() => {
    const cleanup = refetch();
    return cleanup;
  }, [refetch]);

  return { data, loading, error, refetch };
}

// Usage
function UserList() {
  const { data: users, loading, error, refetch } = useFetch("/api/users");

  if (loading) return <Spinner />;
  if (error) return <Error message={error.message} onRetry={refetch} />;

  return (
    <ul>
      {users.map((user) => (
        <li key={user.id}>{user.name}</li>
      ))}
    </ul>
  );
}
```

### 7. useForm

```jsx
function useForm(initialValues, validate) {
  const [values, setValues] = useState(initialValues);
  const [errors, setErrors] = useState({});
  const [touched, setTouched] = useState({});
  const [isSubmitting, setIsSubmitting] = useState(false);

  const handleChange = (e) => {
    const { name, value, type, checked } = e.target;
    setValues((prev) => ({
      ...prev,
      [name]: type === "checkbox" ? checked : value,
    }));
  };

  const handleBlur = (e) => {
    const { name } = e.target;
    setTouched((prev) => ({ ...prev, [name]: true }));

    if (validate) {
      const validationErrors = validate(values);
      setErrors(validationErrors);
    }
  };

  const handleSubmit = (onSubmit) => async (e) => {
    e.preventDefault();

    // Mark all fields as touched
    const allTouched = Object.keys(values).reduce(
      (acc, key) => ({ ...acc, [key]: true }),
      {}
    );
    setTouched(allTouched);

    // Validate
    if (validate) {
      const validationErrors = validate(values);
      setErrors(validationErrors);
      if (Object.keys(validationErrors).length > 0) return;
    }

    setIsSubmitting(true);
    try {
      await onSubmit(values);
    } finally {
      setIsSubmitting(false);
    }
  };

  const reset = () => {
    setValues(initialValues);
    setErrors({});
    setTouched({});
  };

  return {
    values,
    errors,
    touched,
    isSubmitting,
    handleChange,
    handleBlur,
    handleSubmit,
    reset,
    setValues,
  };
}

// Usage
function LoginForm() {
  const validate = (values) => {
    const errors = {};
    if (!values.email) errors.email = "Email required";
    if (!values.password) errors.password = "Password required";
    return errors;
  };

  const { values, errors, touched, handleChange, handleBlur, handleSubmit } =
    useForm({ email: "", password: "" }, validate);

  const onSubmit = async (data) => {
    await loginAPI(data);
  };

  return (
    <form onSubmit={handleSubmit(onSubmit)}>
      <input
        name="email"
        value={values.email}
        onChange={handleChange}
        onBlur={handleBlur}
      />
      {touched.email && errors.email && <span>{errors.email}</span>}

      <input
        name="password"
        type="password"
        value={values.password}
        onChange={handleChange}
        onBlur={handleBlur}
      />
      {touched.password && errors.password && <span>{errors.password}</span>}

      <button type="submit">Login</button>
    </form>
  );
}
```

### 8. useInterval

```jsx
function useInterval(callback, delay) {
  const savedCallback = useRef(callback);

  // Remember the latest callback
  useEffect(() => {
    savedCallback.current = callback;
  }, [callback]);

  // Set up the interval
  useEffect(() => {
    if (delay === null) return;

    const id = setInterval(() => savedCallback.current(), delay);
    return () => clearInterval(id);
  }, [delay]);
}

// Usage
function Timer() {
  const [count, setCount] = useState(0);
  const [isRunning, setIsRunning] = useState(true);

  useInterval(
    () => setCount((c) => c + 1),
    isRunning ? 1000 : null // null pauses the interval
  );

  return (
    <div>
      <p>Count: {count}</p>
      <button onClick={() => setIsRunning(!isRunning)}>
        {isRunning ? "Pause" : "Resume"}
      </button>
    </div>
  );
}
```

### 9. usePrevious

```jsx
function usePrevious(value) {
  const ref = useRef();

  useEffect(() => {
    ref.current = value;
  }, [value]);

  return ref.current;
}

// Usage
function Counter() {
  const [count, setCount] = useState(0);
  const prevCount = usePrevious(count);

  return (
    <div>
      <p>
        Now: {count}, Before: {prevCount ?? "N/A"}
      </p>
      <button onClick={() => setCount((c) => c + 1)}>+</button>
    </div>
  );
}
```

### 10. useMediaQuery

```jsx
function useMediaQuery(query) {
  const [matches, setMatches] = useState(() => {
    return window.matchMedia(query).matches;
  });

  useEffect(() => {
    const mediaQuery = window.matchMedia(query);

    const handleChange = (e) => {
      setMatches(e.matches);
    };

    // Modern browsers
    if (mediaQuery.addEventListener) {
      mediaQuery.addEventListener("change", handleChange);
      return () => mediaQuery.removeEventListener("change", handleChange);
    }
    // Older browsers
    mediaQuery.addListener(handleChange);
    return () => mediaQuery.removeListener(handleChange);
  }, [query]);

  return matches;
}

// Usage
function ResponsiveLayout() {
  const isMobile = useMediaQuery("(max-width: 768px)");
  const isDarkMode = useMediaQuery("(prefers-color-scheme: dark)");

  return (
    <div className={isDarkMode ? "dark" : "light"}>
      {isMobile ? <MobileNav /> : <DesktopNav />}
    </div>
  );
}
```

---

## 🧩 Advanced Patterns

### 1. Hook Composition

```jsx
// Combine multiple hooks
function useUser(userId) {
  const { data: user, loading, error } = useFetch(`/api/users/${userId}`);
  const [isFollowing, setIsFollowing] = useLocalStorage(
    `following-${userId}`,
    false
  );

  const toggleFollow = async () => {
    setIsFollowing(!isFollowing);
    await updateFollowStatus(userId, !isFollowing);
  };

  return { user, loading, error, isFollowing, toggleFollow };
}
```

### 2. Hooks with Options

```jsx
function useFetch(url, options = {}) {
  const { immediate = true, initialData = null, onSuccess, onError } = options;

  const [data, setData] = useState(initialData);
  const [loading, setLoading] = useState(immediate);
  const [error, setError] = useState(null);

  const execute = useCallback(async () => {
    setLoading(true);
    try {
      const res = await fetch(url);
      const json = await res.json();
      setData(json);
      onSuccess?.(json);
    } catch (err) {
      setError(err);
      onError?.(err);
    } finally {
      setLoading(false);
    }
  }, [url, onSuccess, onError]);

  useEffect(() => {
    if (immediate) execute();
  }, [execute, immediate]);

  return { data, loading, error, execute };
}

// Usage with options
const { data, execute } = useFetch("/api/users", {
  immediate: false,
  onSuccess: (data) => console.log("Fetched:", data),
});
```

### 3. Returning Stable References

```jsx
function useCounter(initial = 0) {
  const [count, setCount] = useState(initial);

  // ✅ Memoize functions to maintain reference equality
  const increment = useCallback(() => setCount((c) => c + 1), []);
  const decrement = useCallback(() => setCount((c) => c - 1), []);
  const reset = useCallback(() => setCount(initial), [initial]);

  // ✅ Memoize return object
  return useMemo(
    () => ({ count, increment, decrement, reset }),
    [count, increment, decrement, reset]
  );
}
```

---

## ⚠️ Common Mistakes

### 1. Forgetting "use" Prefix

```jsx
// ❌ Wrong - React won't treat this as a hook
function localStorageValue(key) {
  const [value, setValue] = useState(localStorage.getItem(key));
  // ESLint won't check hook rules!
}

// ✅ Correct
function useLocalStorageValue(key) {
  const [value, setValue] = useState(localStorage.getItem(key));
}
```

### 2. Calling Hooks Conditionally

```jsx
// ❌ Wrong - hooks called conditionally
function useUserData(userId) {
  if (!userId) return null; // This breaks hook rules!

  const [user, setUser] = useState(null);
  // ...
}

// ✅ Correct - handle condition inside hook
function useUserData(userId) {
  const [user, setUser] = useState(null);

  useEffect(() => {
    if (!userId) return;
    fetchUser(userId).then(setUser);
  }, [userId]);

  return userId ? user : null;
}
```

### 3. Not Memoizing Returned Objects

```jsx
// ❌ Problem - new object every render
function useCounter() {
  const [count, setCount] = useState(0);
  return { count, increment: () => setCount((c) => c + 1) }; // New object!
}

// ✅ Fix - memoize if stability matters
function useCounter() {
  const [count, setCount] = useState(0);
  const increment = useCallback(() => setCount((c) => c + 1), []);

  return useMemo(() => ({ count, increment }), [count, increment]);
}
```

---

## 🔧 Best Practices

### 1. Keep Hooks Focused

```jsx
// ✅ Good - single responsibility
function useLocalStorage(key, initial) {}
function useDebounce(value, delay) {}
function useWindowSize() {}

// ❌ Bad - too many responsibilities
function useEverything() {
  // localStorage, debounce, window size, fetch, ...
}
```

### 2. Document Your Hooks

```jsx
/**
 * Hook for fetching data with loading and error states
 * @param {string} url - The URL to fetch
 * @param {Object} options - Fetch options
 * @returns {{ data: any, loading: boolean, error: Error | null, refetch: Function }}
 */
function useFetch(url, options = {}) {
  // ...
}
```

### 3. Test Your Hooks

```jsx
import { renderHook, act } from "@testing-library/react";
import { useCounter } from "./useCounter";

test("should increment counter", () => {
  const { result } = renderHook(() => useCounter(0));

  act(() => {
    result.current.increment();
  });

  expect(result.current.count).toBe(1);
});
```

---

## 🏋️ Practice Exercises

### Exercise 1: useAsync

Create a hook that handles async function execution with loading/error states.

### Exercise 2: useScrollPosition

Create a hook that tracks the scroll position of the page.

### Exercise 3: useCopyToClipboard

Create a hook that copies text to clipboard with success/error handling.

---

## ❓ Interview Questions

**Q1: What are custom hooks and why use them?**

> Custom hooks are functions starting with "use" that extract reusable stateful logic. They reduce code duplication, improve testing, and make components cleaner by separating concerns.

**Q2: How do custom hooks differ from regular functions?**

> Custom hooks can call other hooks (useState, useEffect, etc.) while regular functions cannot. The "use" prefix tells React to apply hook rules (no conditional calls, etc.).

**Q3: Do custom hooks share state between components?**

> No, each component calling a hook gets its own independent state. Hooks share logic, not state. To share state, use Context with a custom hook.

**Q4: When should you create a custom hook?**

> When you find the same stateful logic repeated across components, or when a component has complex logic that could be encapsulated and tested independently.

**Q5: How do you test custom hooks?**

> Use `@testing-library/react-hooks` or `renderHook` from React Testing Library. You can test hooks in isolation without needing a component.

---

## 🔑 Key Takeaways

> ### 💡 Summary
>
> - **Custom hooks** extract reusable stateful logic
> - **Must start with "use"** to follow React conventions
> - **Can use other hooks** (useState, useEffect, etc.)
> - **Each call gets its own state** - logic is shared, not state
> - **Keep hooks focused** on single responsibility
> - **Memoize** returned objects/functions if stability matters
> - **Test hooks** independently with renderHook

---

**Next:** [05 - useReducer Hook](./05_useReducer_Hook.md) - Learn about managing complex state!
