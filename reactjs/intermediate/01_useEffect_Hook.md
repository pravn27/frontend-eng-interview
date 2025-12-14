# 01 - useEffect Hook

## 📖 What is useEffect?

`useEffect` is React's hook for performing **side effects** in functional components. Side effects are operations that reach outside the React component to interact with the outside world.

```jsx
import { useState, useEffect } from "react";

function UserProfile({ userId }) {
  const [user, setUser] = useState(null);

  useEffect(() => {
    // Side effect: Fetch data from API
    fetch(`/api/users/${userId}`)
      .then((res) => res.json())
      .then((data) => setUser(data));
  }, [userId]); // Dependency array

  return user ? <h1>{user.name}</h1> : <p>Loading...</p>;
}
```

---

## 🎯 Why useEffect?

### What Are Side Effects?

Operations that affect something outside the component's render:

- ✅ **Data fetching** - API calls
- ✅ **Subscriptions** - WebSocket, event listeners
- ✅ **Timers** - setTimeout, setInterval
- ✅ **DOM manipulation** - Document title, focus
- ✅ **Logging** - Analytics, debugging
- ✅ **Local storage** - Reading/writing

### Why Not Do This in the Component Body?

```jsx
// ❌ WRONG - Side effect during render
function BadComponent({ userId }) {
  // This runs on EVERY render!
  fetch(`/api/users/${userId}`); // Infinite loop risk!

  return <div>User</div>;
}

// ✅ CORRECT - Side effect in useEffect
function GoodComponent({ userId }) {
  useEffect(() => {
    fetch(`/api/users/${userId}`);
  }, [userId]); // Only when userId changes

  return <div>User</div>;
}
```

---

## 📚 Core Concepts

### 1. Basic Syntax

```jsx
useEffect(() => {
  // Effect code runs AFTER render

  return () => {
    // Cleanup function (optional)
    // Runs before next effect or unmount
  };
}, [dependency1, dependency2]); // Dependency array
```

### 2. Dependency Array Behaviors

```jsx
// 1. No dependency array - runs after EVERY render
useEffect(() => {
  console.log("Runs after every render");
});

// 2. Empty array - runs ONCE after initial render
useEffect(() => {
  console.log("Runs once on mount");
}, []);

// 3. With dependencies - runs when dependencies change
useEffect(() => {
  console.log("Runs when count changes");
}, [count]);

// 4. Multiple dependencies - runs when ANY dependency changes
useEffect(() => {
  console.log("Runs when count OR name changes");
}, [count, name]);
```

### 3. Effect Lifecycle

```
┌─────────────────────────────────────────────────────────────┐
│                    EFFECT LIFECYCLE                          │
├─────────────────────────────────────────────────────────────┤
│                                                              │
│   Component Mounts                                           │
│         │                                                    │
│         ▼                                                    │
│   ┌─────────────┐                                           │
│   │   Render    │                                           │
│   └─────────────┘                                           │
│         │                                                    │
│         ▼                                                    │
│   ┌─────────────┐                                           │
│   │   Effect    │ ◄── Runs AFTER render, NOT during         │
│   │   Runs      │                                           │
│   └─────────────┘                                           │
│         │                                                    │
│         ▼                                                    │
│   Props/State Changes ──────────┐                           │
│         │                       │                           │
│         ▼                       ▼                           │
│   ┌─────────────┐        ┌─────────────┐                    │
│   │  Re-render  │        │   Cleanup   │ ◄── Previous       │
│   └─────────────┘        │   (if any)  │     effect cleanup │
│         │                └─────────────┘                    │
│         ▼                       │                           │
│   ┌─────────────┐               │                           │
│   │   Effect    │◄──────────────┘                           │
│   │   Runs      │                                           │
│   └─────────────┘                                           │
│         │                                                    │
│         ▼                                                    │
│   Component Unmounts                                         │
│         │                                                    │
│         ▼                                                    │
│   ┌─────────────┐                                           │
│   │   Cleanup   │ ◄── Final cleanup                         │
│   └─────────────┘                                           │
│                                                              │
└─────────────────────────────────────────────────────────────┘
```

---

## 💻 Practical Examples

### 1. Data Fetching

```jsx
import { useState, useEffect } from "react";

function UserList() {
  const [users, setUsers] = useState([]);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);

  useEffect(() => {
    // Flag to handle cleanup
    let isCancelled = false;

    const fetchUsers = async () => {
      try {
        setLoading(true);
        setError(null);

        const response = await fetch("/api/users");
        if (!response.ok) throw new Error("Failed to fetch");

        const data = await response.json();

        // Only update if not cancelled
        if (!isCancelled) {
          setUsers(data);
          setLoading(false);
        }
      } catch (err) {
        if (!isCancelled) {
          setError(err.message);
          setLoading(false);
        }
      }
    };

    fetchUsers();

    // Cleanup function
    return () => {
      isCancelled = true;
    };
  }, []); // Empty array = run once on mount

  if (loading) return <p>Loading...</p>;
  if (error) return <p>Error: {error}</p>;

  return (
    <ul>
      {users.map((user) => (
        <li key={user.id}>{user.name}</li>
      ))}
    </ul>
  );
}
```

### 2. Document Title

```jsx
function PageTitle({ title }) {
  useEffect(() => {
    // Store previous title for cleanup
    const previousTitle = document.title;

    // Set new title
    document.title = title;

    // Cleanup: restore previous title
    return () => {
      document.title = previousTitle;
    };
  }, [title]);

  return null; // This component just manages the title
}

// Usage
function App() {
  const [count, setCount] = useState(0);

  return (
    <div>
      <PageTitle title={`Count: ${count}`} />
      <button onClick={() => setCount((c) => c + 1)}>Increment</button>
    </div>
  );
}
```

### 3. Event Listeners

```jsx
function WindowSize() {
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

    // Add listener
    window.addEventListener("resize", handleResize);

    // Cleanup: remove listener
    return () => {
      window.removeEventListener("resize", handleResize);
    };
  }, []); // Empty array - set up once

  return (
    <p>
      Window: {size.width} x {size.height}
    </p>
  );
}
```

### 4. Timer / Interval

```jsx
function Timer() {
  const [seconds, setSeconds] = useState(0);
  const [isRunning, setIsRunning] = useState(false);

  useEffect(() => {
    if (!isRunning) return; // Don't set up timer if not running

    const intervalId = setInterval(() => {
      setSeconds((s) => s + 1);
    }, 1000);

    // Cleanup: clear interval
    return () => {
      clearInterval(intervalId);
    };
  }, [isRunning]); // Re-run when isRunning changes

  return (
    <div>
      <p>Seconds: {seconds}</p>
      <button onClick={() => setIsRunning(!isRunning)}>
        {isRunning ? "Pause" : "Start"}
      </button>
      <button onClick={() => setSeconds(0)}>Reset</button>
    </div>
  );
}
```

### 5. Local Storage Sync

```jsx
function useLocalStorage(key, initialValue) {
  const [value, setValue] = useState(() => {
    const stored = localStorage.getItem(key);
    return stored ? JSON.parse(stored) : initialValue;
  });

  useEffect(() => {
    localStorage.setItem(key, JSON.stringify(value));
  }, [key, value]);

  return [value, setValue];
}

// Usage
function App() {
  const [theme, setTheme] = useLocalStorage("theme", "light");

  return (
    <div className={theme}>
      <button onClick={() => setTheme(theme === "light" ? "dark" : "light")}>
        Toggle Theme
      </button>
    </div>
  );
}
```

### 6. WebSocket Connection

```jsx
function ChatRoom({ roomId }) {
  const [messages, setMessages] = useState([]);

  useEffect(() => {
    // Create WebSocket connection
    const ws = new WebSocket(`wss://chat.example.com/rooms/${roomId}`);

    ws.onopen = () => {
      console.log("Connected to room:", roomId);
    };

    ws.onmessage = (event) => {
      const message = JSON.parse(event.data);
      setMessages((prev) => [...prev, message]);
    };

    ws.onerror = (error) => {
      console.error("WebSocket error:", error);
    };

    // Cleanup: close connection
    return () => {
      console.log("Disconnecting from room:", roomId);
      ws.close();
    };
  }, [roomId]); // Re-connect when room changes

  return (
    <div>
      <h2>Room: {roomId}</h2>
      <ul>
        {messages.map((msg, i) => (
          <li key={i}>{msg.text}</li>
        ))}
      </ul>
    </div>
  );
}
```

### 7. Debounced Search

```jsx
function SearchInput() {
  const [query, setQuery] = useState("");
  const [results, setResults] = useState([]);
  const [loading, setLoading] = useState(false);

  useEffect(() => {
    // Don't search for empty query
    if (!query.trim()) {
      setResults([]);
      return;
    }

    // Debounce: wait 500ms before searching
    const timeoutId = setTimeout(async () => {
      setLoading(true);
      try {
        const response = await fetch(
          `/api/search?q=${encodeURIComponent(query)}`
        );
        const data = await response.json();
        setResults(data);
      } finally {
        setLoading(false);
      }
    }, 500);

    // Cleanup: cancel timeout if query changes
    return () => {
      clearTimeout(timeoutId);
    };
  }, [query]);

  return (
    <div>
      <input
        value={query}
        onChange={(e) => setQuery(e.target.value)}
        placeholder="Search..."
      />
      {loading && <p>Searching...</p>}
      <ul>
        {results.map((item) => (
          <li key={item.id}>{item.name}</li>
        ))}
      </ul>
    </div>
  );
}
```

---

## 🧩 Cleanup Patterns

### Why Cleanup is Essential

```jsx
// ❌ Memory leak - no cleanup
function BadTimer() {
  const [count, setCount] = useState(0);

  useEffect(() => {
    setInterval(() => {
      setCount((c) => c + 1);
    }, 1000);
    // Interval keeps running even after unmount!
  }, []);

  return <p>{count}</p>;
}

// ✅ Proper cleanup
function GoodTimer() {
  const [count, setCount] = useState(0);

  useEffect(() => {
    const id = setInterval(() => {
      setCount((c) => c + 1);
    }, 1000);

    return () => clearInterval(id); // Cleanup!
  }, []);

  return <p>{count}</p>;
}
```

### Common Cleanup Scenarios

```jsx
// 1. Event listeners
useEffect(() => {
  const handler = (e) => console.log(e);
  window.addEventListener("scroll", handler);
  return () => window.removeEventListener("scroll", handler);
}, []);

// 2. Timers
useEffect(() => {
  const timeoutId = setTimeout(callback, 1000);
  return () => clearTimeout(timeoutId);
}, []);

// 3. Subscriptions
useEffect(() => {
  const subscription = observable.subscribe(callback);
  return () => subscription.unsubscribe();
}, []);

// 4. AbortController for fetch
useEffect(() => {
  const controller = new AbortController();

  fetch(url, { signal: controller.signal })
    .then((res) => res.json())
    .then(setData);

  return () => controller.abort();
}, [url]);

// 5. WebSocket
useEffect(() => {
  const ws = new WebSocket(url);
  ws.onmessage = handleMessage;
  return () => ws.close();
}, [url]);
```

---

## ⚠️ Common Mistakes

### 1. Missing Dependencies

```jsx
// ❌ Bug: uses count but doesn't list it as dependency
function Counter() {
  const [count, setCount] = useState(0);

  useEffect(() => {
    const id = setInterval(() => {
      console.log(count); // Always logs 0!
    }, 1000);
    return () => clearInterval(id);
  }, []); // Missing count dependency
}

// ✅ Fix 1: Add dependency
useEffect(() => {
  const id = setInterval(() => {
    console.log(count);
  }, 1000);
  return () => clearInterval(id);
}, [count]); // Correct dependency

// ✅ Fix 2: Use functional update (preferred for this case)
useEffect(() => {
  const id = setInterval(() => {
    setCount((c) => c + 1); // Doesn't need count as dependency
  }, 1000);
  return () => clearInterval(id);
}, []);
```

### 2. Object/Array Dependencies

```jsx
// ❌ Bug: new object every render = infinite loop
function Component({ userId }) {
  const options = { userId, includeDetails: true }; // New object each render!

  useEffect(() => {
    fetchUser(options);
  }, [options]); // Triggers every render!
}

// ✅ Fix 1: Primitive dependencies
useEffect(() => {
  fetchUser({ userId, includeDetails: true });
}, [userId]); // Only primitive

// ✅ Fix 2: useMemo for complex objects
const options = useMemo(() => ({ userId, includeDetails: true }), [userId]);

useEffect(() => {
  fetchUser(options);
}, [options]);
```

### 3. Async Effect Functions

```jsx
// ❌ Wrong: Effect can't be async directly
useEffect(async () => {
  const data = await fetchData();
  setData(data);
}, []);

// ✅ Correct: Define async function inside
useEffect(() => {
  const fetchData = async () => {
    const data = await fetch("/api/data");
    setData(await data.json());
  };

  fetchData();
}, []);

// ✅ Also correct: IIFE
useEffect(() => {
  (async () => {
    const data = await fetch("/api/data");
    setData(await data.json());
  })();
}, []);
```

### 4. Race Conditions

```jsx
// ❌ Bug: Race condition with fast prop changes
function UserProfile({ userId }) {
  const [user, setUser] = useState(null);

  useEffect(() => {
    fetch(`/api/users/${userId}`)
      .then((res) => res.json())
      .then(setUser); // May set stale data!
  }, [userId]);
}

// ✅ Fix: Track if effect is current
function UserProfile({ userId }) {
  const [user, setUser] = useState(null);

  useEffect(() => {
    let isCurrent = true;

    fetch(`/api/users/${userId}`)
      .then((res) => res.json())
      .then((data) => {
        if (isCurrent) setUser(data);
      });

    return () => {
      isCurrent = false;
    };
  }, [userId]);
}

// ✅ Better: AbortController
function UserProfile({ userId }) {
  const [user, setUser] = useState(null);

  useEffect(() => {
    const controller = new AbortController();

    fetch(`/api/users/${userId}`, { signal: controller.signal })
      .then((res) => res.json())
      .then(setUser)
      .catch((err) => {
        if (err.name !== "AbortError") throw err;
      });

    return () => controller.abort();
  }, [userId]);
}
```

### 5. Infinite Loops

```jsx
// ❌ Infinite loop: setState in effect without proper dependencies
function Component() {
  const [data, setData] = useState([]);

  useEffect(() => {
    setData([...data, "new item"]); // Triggers re-render, effect runs again!
  }); // No dependency array = runs every render
}

// ✅ Fix: Proper dependencies or functional update
useEffect(() => {
  setData((prev) => [...prev, "new item"]);
}, []); // Only once
```

---

## 🔧 Best Practices

### 1. One Effect Per Concern

```jsx
// ❌ Mixed concerns
useEffect(() => {
  document.title = `User: ${user.name}`;
  fetchUserPosts(user.id).then(setPosts);
  analytics.track("profile_view", user.id);
}, [user]);

// ✅ Separate effects
useEffect(() => {
  document.title = `User: ${user.name}`;
}, [user.name]);

useEffect(() => {
  fetchUserPosts(user.id).then(setPosts);
}, [user.id]);

useEffect(() => {
  analytics.track("profile_view", user.id);
}, [user.id]);
```

### 2. Extract Custom Hooks

```jsx
// ✅ Reusable hook
function useFetch(url) {
  const [data, setData] = useState(null);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);

  useEffect(() => {
    const controller = new AbortController();

    setLoading(true);
    fetch(url, { signal: controller.signal })
      .then((res) => {
        if (!res.ok) throw new Error("Failed to fetch");
        return res.json();
      })
      .then(setData)
      .catch((err) => {
        if (err.name !== "AbortError") setError(err.message);
      })
      .finally(() => setLoading(false));

    return () => controller.abort();
  }, [url]);

  return { data, loading, error };
}

// Usage
function UserProfile({ userId }) {
  const { data: user, loading, error } = useFetch(`/api/users/${userId}`);

  if (loading) return <p>Loading...</p>;
  if (error) return <p>Error: {error}</p>;
  return <h1>{user.name}</h1>;
}
```

### 3. Keep Effects Simple

```jsx
// ❌ Complex effect
useEffect(() => {
  // Lots of complex logic here...
}, [deps]);

// ✅ Extract logic to functions
const handleDataFetch = async (signal) => {
  const res = await fetch(url, { signal });
  return res.json();
};

useEffect(() => {
  const controller = new AbortController();
  handleDataFetch(controller.signal).then(setData);
  return () => controller.abort();
}, [url]);
```

---

## 📊 Complete Demo: Real-Time Dashboard

```jsx
import { useState, useEffect, useCallback } from "react";

function RealTimeDashboard() {
  const [stats, setStats] = useState(null);
  const [notifications, setNotifications] = useState([]);
  const [isOnline, setIsOnline] = useState(navigator.onLine);
  const [lastUpdate, setLastUpdate] = useState(null);

  // 1. Online/Offline status
  useEffect(() => {
    const handleOnline = () => setIsOnline(true);
    const handleOffline = () => setIsOnline(false);

    window.addEventListener("online", handleOnline);
    window.addEventListener("offline", handleOffline);

    return () => {
      window.removeEventListener("online", handleOnline);
      window.removeEventListener("offline", handleOffline);
    };
  }, []);

  // 2. Fetch initial stats
  useEffect(() => {
    const controller = new AbortController();

    fetch("/api/dashboard/stats", { signal: controller.signal })
      .then((res) => res.json())
      .then((data) => {
        setStats(data);
        setLastUpdate(new Date());
      })
      .catch((err) => {
        if (err.name !== "AbortError") {
          console.error("Failed to fetch stats:", err);
        }
      });

    return () => controller.abort();
  }, []);

  // 3. Real-time updates via WebSocket
  useEffect(() => {
    if (!isOnline) return;

    const ws = new WebSocket("wss://api.example.com/realtime");

    ws.onmessage = (event) => {
      const update = JSON.parse(event.data);

      if (update.type === "stats") {
        setStats(update.data);
        setLastUpdate(new Date());
      } else if (update.type === "notification") {
        setNotifications((prev) => [update.data, ...prev].slice(0, 10));
      }
    };

    ws.onerror = (error) => {
      console.error("WebSocket error:", error);
    };

    return () => {
      ws.close();
    };
  }, [isOnline]);

  // 4. Update document title with notification count
  useEffect(() => {
    const unreadCount = notifications.filter((n) => !n.read).length;
    document.title =
      unreadCount > 0 ? `(${unreadCount}) Dashboard` : "Dashboard";
  }, [notifications]);

  // 5. Auto-refresh every 30 seconds as fallback
  useEffect(() => {
    if (!isOnline) return;

    const intervalId = setInterval(() => {
      fetch("/api/dashboard/stats")
        .then((res) => res.json())
        .then((data) => {
          setStats(data);
          setLastUpdate(new Date());
        });
    }, 30000);

    return () => clearInterval(intervalId);
  }, [isOnline]);

  const markAllRead = useCallback(() => {
    setNotifications((prev) => prev.map((n) => ({ ...n, read: true })));
  }, []);

  return (
    <div className="dashboard">
      <header>
        <h1>Dashboard</h1>
        <div className="status">
          <span className={isOnline ? "online" : "offline"}>
            {isOnline ? "🟢 Online" : "🔴 Offline"}
          </span>
          {lastUpdate && (
            <span>Last update: {lastUpdate.toLocaleTimeString()}</span>
          )}
        </div>
      </header>

      <main>
        {stats ? (
          <div className="stats-grid">
            <StatCard title="Users" value={stats.users} />
            <StatCard title="Revenue" value={`$${stats.revenue}`} />
            <StatCard title="Orders" value={stats.orders} />
            <StatCard title="Conversion" value={`${stats.conversion}%`} />
          </div>
        ) : (
          <p>Loading stats...</p>
        )}
      </main>

      <aside>
        <div className="notifications-header">
          <h2>Notifications</h2>
          <button onClick={markAllRead}>Mark all read</button>
        </div>
        <ul className="notifications">
          {notifications.map((notification, index) => (
            <li
              key={notification.id || index}
              className={notification.read ? "read" : "unread"}
            >
              {notification.message}
            </li>
          ))}
        </ul>
      </aside>
    </div>
  );
}

function StatCard({ title, value }) {
  return (
    <div className="stat-card">
      <h3>{title}</h3>
      <p className="value">{value}</p>
    </div>
  );
}

export default RealTimeDashboard;
```

---

## 🏋️ Practice Exercises

### Exercise 1: Auto-Save Form

Create a form that auto-saves to localStorage after the user stops typing for 1 second.

### Exercise 2: Infinite Scroll

Implement infinite scroll that loads more items when the user reaches the bottom.

### Exercise 3: Online Status

Build a component that shows different UI when the user goes offline/online.

### Exercise 4: Keyboard Shortcuts

Create a component that responds to keyboard shortcuts (e.g., Ctrl+S to save).

---

## ❓ Interview Questions

**Q1: What is useEffect and when would you use it?**

> useEffect is a hook for performing side effects in functional components. Use it for data fetching, subscriptions, timers, DOM manipulation, or any operation that interacts with something outside the component. It runs after the render is committed to the screen.

**Q2: What is the cleanup function in useEffect?**

> The cleanup function is returned from the effect and runs before the component unmounts or before the effect re-runs (if dependencies change). It's used to clean up subscriptions, timers, event listeners, or cancel pending requests to prevent memory leaks.

**Q3: What's the difference between an empty dependency array and no array?**

> Empty array `[]`: Effect runs once on mount, cleanup runs once on unmount. No array: Effect runs after every render. With dependencies: Effect runs when any dependency changes.

**Q4: How do you prevent race conditions in useEffect?**

> Use a cleanup flag (`isCurrent`) or AbortController to cancel pending requests when the component unmounts or dependencies change before the request completes.

**Q5: Can you make useEffect async?**

> No, the effect function itself cannot be async (it must return cleanup or undefined, not a Promise). Instead, define an async function inside the effect and call it.

---

## 🔑 Key Takeaways

> ### 💡 Summary
>
> - **useEffect** runs side effects after render
> - **Dependency array** controls when the effect runs
> - **Cleanup function** prevents memory leaks
> - **Always clean up** subscriptions, timers, and listeners
> - **Handle race conditions** with flags or AbortController
> - **Keep effects focused** - one concern per effect
> - **Extract to custom hooks** for reusability

---

**Next:** [02 - useRef Hook](./02_useRef_Hook.md) - Learn about refs for DOM access and persisting values!
