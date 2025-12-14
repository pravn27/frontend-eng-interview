# 03 - useContext Hook

## 📖 What is useContext?

`useContext` is a hook that lets you read and subscribe to **context** from your component. Context provides a way to pass data through the component tree without having to pass props down manually at every level.

```jsx
import { createContext, useContext, useState } from "react";

// 1. Create context
const ThemeContext = createContext("light");

// 2. Provide context
function App() {
  const [theme, setTheme] = useState("light");

  return (
    <ThemeContext.Provider value={theme}>
      <Toolbar />
    </ThemeContext.Provider>
  );
}

// 3. Consume context
function Toolbar() {
  const theme = useContext(ThemeContext);
  return <div className={theme}>Toolbar</div>;
}
```

---

## 🎯 Why Context?

### The Prop Drilling Problem

```jsx
// ❌ Without Context - Prop drilling through many levels
function App() {
  const [user, setUser] = useState({ name: "Alice" });

  return <Header user={user} />;
}

function Header({ user }) {
  return <Navigation user={user} />;
}

function Navigation({ user }) {
  return <UserMenu user={user} />;
}

function UserMenu({ user }) {
  return <span>{user.name}</span>;
}
```

```jsx
// ✅ With Context - Direct access anywhere in tree
const UserContext = createContext(null);

function App() {
  const [user, setUser] = useState({ name: "Alice" });

  return (
    <UserContext.Provider value={user}>
      <Header />
    </UserContext.Provider>
  );
}

function Header() {
  return <Navigation />;
}

function Navigation() {
  return <UserMenu />;
}

function UserMenu() {
  const user = useContext(UserContext);
  return <span>{user.name}</span>;
}
```

---

## 📚 Core Concepts

### 1. Creating Context

```jsx
import { createContext } from "react";

// With default value
const ThemeContext = createContext("light");

// Without default value (null)
const UserContext = createContext(null);

// With object default
const SettingsContext = createContext({
  language: "en",
  currency: "USD",
});
```

### 2. Providing Context

```jsx
function App() {
  const [theme, setTheme] = useState("dark");

  return (
    <ThemeContext.Provider value={theme}>
      {/* All children can access theme */}
      <MainContent />
    </ThemeContext.Provider>
  );
}

// Providing multiple contexts
function App() {
  return (
    <ThemeContext.Provider value="dark">
      <UserContext.Provider value={user}>
        <SettingsContext.Provider value={settings}>
          <MainContent />
        </SettingsContext.Provider>
      </UserContext.Provider>
    </ThemeContext.Provider>
  );
}
```

### 3. Consuming Context

```jsx
import { useContext } from "react";

function ThemedButton() {
  const theme = useContext(ThemeContext);

  return <button className={`btn-${theme}`}>Current theme: {theme}</button>;
}
```

### 4. Context with State and Actions

```jsx
// Create context with both state and actions
const CounterContext = createContext(null);

function CounterProvider({ children }) {
  const [count, setCount] = useState(0);

  const increment = () => setCount((c) => c + 1);
  const decrement = () => setCount((c) => c - 1);
  const reset = () => setCount(0);

  const value = {
    count,
    increment,
    decrement,
    reset,
  };

  return (
    <CounterContext.Provider value={value}>{children}</CounterContext.Provider>
  );
}

// Custom hook for consuming
function useCounter() {
  const context = useContext(CounterContext);
  if (context === null) {
    throw new Error("useCounter must be used within CounterProvider");
  }
  return context;
}

// Usage
function CounterDisplay() {
  const { count } = useCounter();
  return <p>Count: {count}</p>;
}

function CounterButtons() {
  const { increment, decrement, reset } = useCounter();
  return (
    <div>
      <button onClick={decrement}>-</button>
      <button onClick={increment}>+</button>
      <button onClick={reset}>Reset</button>
    </div>
  );
}
```

---

## 💻 Practical Examples

### 1. Theme Context

```jsx
import { createContext, useContext, useState } from "react";

// Create context
const ThemeContext = createContext(null);

// Provider component
function ThemeProvider({ children }) {
  const [theme, setTheme] = useState("light");

  const toggleTheme = () => {
    setTheme((t) => (t === "light" ? "dark" : "light"));
  };

  const value = { theme, toggleTheme };

  return (
    <ThemeContext.Provider value={value}>{children}</ThemeContext.Provider>
  );
}

// Custom hook
function useTheme() {
  const context = useContext(ThemeContext);
  if (!context) {
    throw new Error("useTheme must be used within ThemeProvider");
  }
  return context;
}

// Components using theme
function App() {
  return (
    <ThemeProvider>
      <Layout />
    </ThemeProvider>
  );
}

function Layout() {
  const { theme } = useTheme();

  return (
    <div className={`layout ${theme}`}>
      <Header />
      <Main />
      <Footer />
    </div>
  );
}

function Header() {
  const { theme, toggleTheme } = useTheme();

  return (
    <header>
      <h1>My App</h1>
      <button onClick={toggleTheme}>
        Switch to {theme === "light" ? "Dark" : "Light"}
      </button>
    </header>
  );
}
```

### 2. Authentication Context

```jsx
import { createContext, useContext, useState, useEffect } from "react";

const AuthContext = createContext(null);

function AuthProvider({ children }) {
  const [user, setUser] = useState(null);
  const [loading, setLoading] = useState(true);

  useEffect(() => {
    // Check for existing session
    const token = localStorage.getItem("token");
    if (token) {
      fetchUser(token)
        .then(setUser)
        .finally(() => setLoading(false));
    } else {
      setLoading(false);
    }
  }, []);

  const login = async (email, password) => {
    const response = await fetch("/api/login", {
      method: "POST",
      body: JSON.stringify({ email, password }),
    });
    const { user, token } = await response.json();
    localStorage.setItem("token", token);
    setUser(user);
    return user;
  };

  const logout = () => {
    localStorage.removeItem("token");
    setUser(null);
  };

  const value = {
    user,
    loading,
    isAuthenticated: !!user,
    login,
    logout,
  };

  return <AuthContext.Provider value={value}>{children}</AuthContext.Provider>;
}

function useAuth() {
  const context = useContext(AuthContext);
  if (!context) {
    throw new Error("useAuth must be used within AuthProvider");
  }
  return context;
}

// Usage
function ProtectedRoute({ children }) {
  const { isAuthenticated, loading } = useAuth();

  if (loading) return <Spinner />;
  if (!isAuthenticated) return <Navigate to="/login" />;

  return children;
}

function UserProfile() {
  const { user, logout } = useAuth();

  return (
    <div>
      <h1>Welcome, {user.name}</h1>
      <button onClick={logout}>Logout</button>
    </div>
  );
}
```

### 3. Shopping Cart Context

```jsx
import { createContext, useContext, useReducer } from "react";

const CartContext = createContext(null);

const cartReducer = (state, action) => {
  switch (action.type) {
    case "ADD_ITEM": {
      const existingItem = state.items.find(
        (item) => item.id === action.payload.id
      );
      if (existingItem) {
        return {
          ...state,
          items: state.items.map((item) =>
            item.id === action.payload.id
              ? { ...item, quantity: item.quantity + 1 }
              : item
          ),
        };
      }
      return {
        ...state,
        items: [...state.items, { ...action.payload, quantity: 1 }],
      };
    }
    case "REMOVE_ITEM":
      return {
        ...state,
        items: state.items.filter((item) => item.id !== action.payload),
      };
    case "UPDATE_QUANTITY":
      return {
        ...state,
        items: state.items.map((item) =>
          item.id === action.payload.id
            ? { ...item, quantity: action.payload.quantity }
            : item
        ),
      };
    case "CLEAR_CART":
      return { ...state, items: [] };
    default:
      return state;
  }
};

function CartProvider({ children }) {
  const [state, dispatch] = useReducer(cartReducer, { items: [] });

  const addItem = (product) => {
    dispatch({ type: "ADD_ITEM", payload: product });
  };

  const removeItem = (productId) => {
    dispatch({ type: "REMOVE_ITEM", payload: productId });
  };

  const updateQuantity = (productId, quantity) => {
    if (quantity <= 0) {
      removeItem(productId);
    } else {
      dispatch({
        type: "UPDATE_QUANTITY",
        payload: { id: productId, quantity },
      });
    }
  };

  const clearCart = () => {
    dispatch({ type: "CLEAR_CART" });
  };

  const totalItems = state.items.reduce((sum, item) => sum + item.quantity, 0);
  const totalPrice = state.items.reduce(
    (sum, item) => sum + item.price * item.quantity,
    0
  );

  const value = {
    items: state.items,
    totalItems,
    totalPrice,
    addItem,
    removeItem,
    updateQuantity,
    clearCart,
  };

  return <CartContext.Provider value={value}>{children}</CartContext.Provider>;
}

function useCart() {
  const context = useContext(CartContext);
  if (!context) {
    throw new Error("useCart must be used within CartProvider");
  }
  return context;
}

// Usage
function CartIcon() {
  const { totalItems } = useCart();
  return (
    <div className="cart-icon">
      🛒 <span className="badge">{totalItems}</span>
    </div>
  );
}

function ProductCard({ product }) {
  const { addItem } = useCart();
  return (
    <div className="product-card">
      <h3>{product.name}</h3>
      <p>${product.price}</p>
      <button onClick={() => addItem(product)}>Add to Cart</button>
    </div>
  );
}

function Cart() {
  const { items, totalPrice, updateQuantity, removeItem, clearCart } =
    useCart();

  if (items.length === 0) {
    return <p>Your cart is empty</p>;
  }

  return (
    <div className="cart">
      {items.map((item) => (
        <div key={item.id} className="cart-item">
          <span>{item.name}</span>
          <span>${item.price}</span>
          <button onClick={() => updateQuantity(item.id, item.quantity - 1)}>
            -
          </button>
          <span>{item.quantity}</span>
          <button onClick={() => updateQuantity(item.id, item.quantity + 1)}>
            +
          </button>
          <button onClick={() => removeItem(item.id)}>Remove</button>
        </div>
      ))}
      <div className="cart-total">Total: ${totalPrice.toFixed(2)}</div>
      <button onClick={clearCart}>Clear Cart</button>
    </div>
  );
}
```

### 4. Notification Context

```jsx
import { createContext, useContext, useState, useCallback } from "react";

const NotificationContext = createContext(null);

function NotificationProvider({ children }) {
  const [notifications, setNotifications] = useState([]);

  const addNotification = useCallback((message, type = "info") => {
    const id = Date.now();
    setNotifications((prev) => [...prev, { id, message, type }]);

    // Auto-remove after 5 seconds
    setTimeout(() => {
      removeNotification(id);
    }, 5000);

    return id;
  }, []);

  const removeNotification = useCallback((id) => {
    setNotifications((prev) => prev.filter((n) => n.id !== id));
  }, []);

  const success = useCallback(
    (message) => addNotification(message, "success"),
    [addNotification]
  );
  const error = useCallback(
    (message) => addNotification(message, "error"),
    [addNotification]
  );
  const warning = useCallback(
    (message) => addNotification(message, "warning"),
    [addNotification]
  );
  const info = useCallback(
    (message) => addNotification(message, "info"),
    [addNotification]
  );

  const value = {
    notifications,
    addNotification,
    removeNotification,
    success,
    error,
    warning,
    info,
  };

  return (
    <NotificationContext.Provider value={value}>
      {children}
      <NotificationContainer />
    </NotificationContext.Provider>
  );
}

function useNotification() {
  const context = useContext(NotificationContext);
  if (!context) {
    throw new Error("useNotification must be used within NotificationProvider");
  }
  return context;
}

function NotificationContainer() {
  const { notifications, removeNotification } = useNotification();

  return (
    <div className="notification-container">
      {notifications.map((notification) => (
        <div
          key={notification.id}
          className={`notification notification-${notification.type}`}
        >
          <span>{notification.message}</span>
          <button onClick={() => removeNotification(notification.id)}>×</button>
        </div>
      ))}
    </div>
  );
}

// Usage
function SaveButton() {
  const { success, error } = useNotification();

  const handleSave = async () => {
    try {
      await saveData();
      success("Data saved successfully!");
    } catch (err) {
      error("Failed to save data");
    }
  };

  return <button onClick={handleSave}>Save</button>;
}
```

---

## 🧩 Advanced Patterns

### 1. Splitting Context for Performance

```jsx
// ❌ Problem: All consumers re-render when any value changes
const AppContext = createContext({
  user: null,
  theme: "light",
  language: "en",
});

// ✅ Solution: Split into separate contexts
const UserContext = createContext(null);
const ThemeContext = createContext("light");
const LanguageContext = createContext("en");

function App() {
  const [user, setUser] = useState(null);
  const [theme, setTheme] = useState("light");
  const [language, setLanguage] = useState("en");

  return (
    <UserContext.Provider value={{ user, setUser }}>
      <ThemeContext.Provider value={{ theme, setTheme }}>
        <LanguageContext.Provider value={{ language, setLanguage }}>
          <MainContent />
        </LanguageContext.Provider>
      </ThemeContext.Provider>
    </UserContext.Provider>
  );
}
```

### 2. Separate State and Dispatch Contexts

```jsx
const StateContext = createContext(null);
const DispatchContext = createContext(null);

function AppProvider({ children }) {
  const [state, dispatch] = useReducer(reducer, initialState);

  return (
    <StateContext.Provider value={state}>
      <DispatchContext.Provider value={dispatch}>
        {children}
      </DispatchContext.Provider>
    </StateContext.Provider>
  );
}

// Components that only dispatch don't re-render on state changes
function ActionButton() {
  const dispatch = useContext(DispatchContext);
  return <button onClick={() => dispatch({ type: "INCREMENT" })}>+</button>;
}

// Components that read state
function Display() {
  const state = useContext(StateContext);
  return <p>{state.count}</p>;
}
```

### 3. Composing Providers

```jsx
// Create a composed provider to avoid nesting
function AppProviders({ children }) {
  return (
    <AuthProvider>
      <ThemeProvider>
        <CartProvider>
          <NotificationProvider>{children}</NotificationProvider>
        </CartProvider>
      </ThemeProvider>
    </AuthProvider>
  );
}

// Usage
function App() {
  return (
    <AppProviders>
      <MainContent />
    </AppProviders>
  );
}
```

---

## ⚠️ Common Mistakes

### 1. Missing Provider

```jsx
// ❌ Error: Component outside provider
function App() {
  return (
    <div>
      <ThemeContext.Provider value="dark">
        <Sidebar />
      </ThemeContext.Provider>
      <Header /> {/* Error: No provider above this */}
    </div>
  );
}

// ✅ Correct: Provider wraps all consumers
function App() {
  return (
    <ThemeContext.Provider value="dark">
      <div>
        <Sidebar />
        <Header />
      </div>
    </ThemeContext.Provider>
  );
}
```

### 2. Object Identity Causing Re-renders

```jsx
// ❌ Problem: New object every render
function Provider({ children }) {
  const [user, setUser] = useState(null);

  return (
    <UserContext.Provider value={{ user, setUser }}>
      {" "}
      {/* New object each render */}
      {children}
    </UserContext.Provider>
  );
}

// ✅ Fix: useMemo to maintain identity
function Provider({ children }) {
  const [user, setUser] = useState(null);

  const value = useMemo(() => ({ user, setUser }), [user]);

  return <UserContext.Provider value={value}>{children}</UserContext.Provider>;
}
```

### 3. Overusing Context

```jsx
// ❌ Don't use context for everything
const ButtonColorContext = createContext("blue");
const FontSizeContext = createContext(16);
// Too granular!

// ✅ Use context for truly global state
// - Authentication
// - Theme
// - Locale/Language
// - Global notifications
```

---

## 🔧 Best Practices

### 1. Create Custom Hooks for Context

```jsx
// Always create a custom hook
function useTheme() {
  const context = useContext(ThemeContext);
  if (context === undefined) {
    throw new Error("useTheme must be used within a ThemeProvider");
  }
  return context;
}
```

### 2. Use TypeScript for Type Safety

```typescript
interface AuthContextType {
  user: User | null;
  login: (email: string, password: string) => Promise<void>;
  logout: () => void;
}

const AuthContext = createContext<AuthContextType | null>(null);

function useAuth(): AuthContextType {
  const context = useContext(AuthContext);
  if (!context) {
    throw new Error("useAuth must be used within AuthProvider");
  }
  return context;
}
```

### 3. Keep Context Focused

```jsx
// ✅ Good: Single responsibility
const AuthContext = createContext(null);
const ThemeContext = createContext(null);
const CartContext = createContext(null);

// ❌ Bad: Kitchen sink context
const AppContext = createContext({
  user: null,
  theme: "light",
  cart: [],
  notifications: [],
  // Too much!
});
```

---

## 🏋️ Practice Exercises

### Exercise 1: Language Context

Create a LanguageContext that provides translations and a language switcher.

### Exercise 2: Modal Context

Build a modal context that can show/hide modals from anywhere in the app.

### Exercise 3: Multi-step Form Context

Create context for a multi-step form wizard that tracks progress and form data.

---

## ❓ Interview Questions

**Q1: What is Context and when would you use it?**

> Context provides a way to pass data through the component tree without prop drilling. Use it for global data like themes, authentication, language, or any state needed by many components at different levels.

**Q2: What's the difference between Context and Redux?**

> Context is built into React and best for simple global state. Redux provides additional features like middleware, time-travel debugging, and is better for complex state logic. Context can cause performance issues with frequent updates; Redux is optimized for this.

**Q3: How do you prevent unnecessary re-renders with Context?**

> Split context into smaller pieces, separate state and dispatch contexts, use useMemo for context values, and consider using libraries like Zustand or Jotai for performance-critical state.

**Q4: Can Context replace prop drilling entirely?**

> No. Context is for truly global data. For state used by a few nearby components, props are simpler and more explicit. Overusing context makes components less reusable.

**Q5: What happens if you use useContext outside its Provider?**

> You get the default value passed to createContext. If no default was provided, you get undefined. Best practice is to throw an error in your custom hook if the context is missing.

---

## 🔑 Key Takeaways

> ### 💡 Summary
>
> - **Context** solves prop drilling for global data
> - **createContext** → **Provider** → **useContext**
> - **Create custom hooks** for type safety and error handling
> - **Split contexts** to prevent unnecessary re-renders
> - **Don't overuse** - props are often simpler
> - **Memoize values** to prevent re-renders
> - **Keep contexts focused** on single responsibility

---

**Next:** [04 - Custom Hooks](./04_Custom_Hooks.md) - Learn to create reusable hook logic!
