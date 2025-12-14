# 05 - useReducer Hook

## 📖 What is useReducer?

`useReducer` is a hook for managing complex state logic. It's an alternative to `useState` that uses the **reducer pattern** from Redux - you dispatch actions to a reducer function that computes the new state.

```jsx
import { useReducer } from "react";

// Reducer function
function counterReducer(state, action) {
  switch (action.type) {
    case "INCREMENT":
      return { count: state.count + 1 };
    case "DECREMENT":
      return { count: state.count - 1 };
    case "RESET":
      return { count: 0 };
    default:
      return state;
  }
}

function Counter() {
  const [state, dispatch] = useReducer(counterReducer, { count: 0 });

  return (
    <div>
      <p>Count: {state.count}</p>
      <button onClick={() => dispatch({ type: "INCREMENT" })}>+</button>
      <button onClick={() => dispatch({ type: "DECREMENT" })}>-</button>
      <button onClick={() => dispatch({ type: "RESET" })}>Reset</button>
    </div>
  );
}
```

---

## 🎯 When to Use useReducer

### useState vs useReducer

| Use `useState` when...   | Use `useReducer` when... |
| ------------------------ | ------------------------ |
| Simple state (primitive) | Complex state object     |
| Independent updates      | Related state updates    |
| Few state transitions    | Many state transitions   |
| Simple logic             | Complex update logic     |

### Examples of When to Use useReducer

```jsx
// ✅ useState: Simple, independent values
const [name, setName] = useState("");
const [email, setEmail] = useState("");

// ✅ useReducer: Complex, related state
const [formState, dispatch] = useReducer(formReducer, {
  values: { name: "", email: "" },
  errors: {},
  touched: {},
  isSubmitting: false,
});

// ✅ useReducer: Many state transitions
const [todoState, dispatch] = useReducer(todoReducer, {
  todos: [],
  filter: "all",
  sortBy: "date",
});
```

---

## 📚 Core Concepts

### 1. Reducer Function

```jsx
// Pure function: (state, action) => newState
function reducer(state, action) {
  switch (action.type) {
    case "ACTION_TYPE":
      return { ...state /* updates */ };
    default:
      return state; // Always return state for unknown actions
  }
}
```

### 2. Actions

```jsx
// Action: object describing what happened
{ type: "INCREMENT" }
{ type: "SET_NAME", payload: "Alice" }
{ type: "ADD_TODO", payload: { id: 1, text: "Learn React" } }
{ type: "UPDATE_USER", payload: { field: "email", value: "a@b.com" } }
```

### 3. useReducer Syntax

```jsx
const [state, dispatch] = useReducer(reducer, initialState);
const [state, dispatch] = useReducer(reducer, initialArg, init);

// With lazy initialization
function init(initialCount) {
  return { count: initialCount };
}

const [state, dispatch] = useReducer(reducer, 0, init);
```

---

## 💻 Practical Examples

### 1. Todo List

```jsx
const initialState = {
  todos: [],
  filter: "all", // "all" | "active" | "completed"
};

function todoReducer(state, action) {
  switch (action.type) {
    case "ADD_TODO":
      return {
        ...state,
        todos: [
          ...state.todos,
          {
            id: Date.now(),
            text: action.payload,
            completed: false,
          },
        ],
      };

    case "TOGGLE_TODO":
      return {
        ...state,
        todos: state.todos.map((todo) =>
          todo.id === action.payload
            ? { ...todo, completed: !todo.completed }
            : todo
        ),
      };

    case "DELETE_TODO":
      return {
        ...state,
        todos: state.todos.filter((todo) => todo.id !== action.payload),
      };

    case "EDIT_TODO":
      return {
        ...state,
        todos: state.todos.map((todo) =>
          todo.id === action.payload.id
            ? { ...todo, text: action.payload.text }
            : todo
        ),
      };

    case "SET_FILTER":
      return {
        ...state,
        filter: action.payload,
      };

    case "CLEAR_COMPLETED":
      return {
        ...state,
        todos: state.todos.filter((todo) => !todo.completed),
      };

    case "TOGGLE_ALL":
      const allCompleted = state.todos.every((todo) => todo.completed);
      return {
        ...state,
        todos: state.todos.map((todo) => ({
          ...todo,
          completed: !allCompleted,
        })),
      };

    default:
      return state;
  }
}

function TodoApp() {
  const [state, dispatch] = useReducer(todoReducer, initialState);
  const [input, setInput] = useState("");

  const handleAdd = () => {
    if (input.trim()) {
      dispatch({ type: "ADD_TODO", payload: input.trim() });
      setInput("");
    }
  };

  const filteredTodos = state.todos.filter((todo) => {
    if (state.filter === "active") return !todo.completed;
    if (state.filter === "completed") return todo.completed;
    return true;
  });

  return (
    <div>
      <input value={input} onChange={(e) => setInput(e.target.value)} />
      <button onClick={handleAdd}>Add</button>

      <div>
        <button
          onClick={() => dispatch({ type: "SET_FILTER", payload: "all" })}
        >
          All
        </button>
        <button
          onClick={() => dispatch({ type: "SET_FILTER", payload: "active" })}
        >
          Active
        </button>
        <button
          onClick={() => dispatch({ type: "SET_FILTER", payload: "completed" })}
        >
          Completed
        </button>
      </div>

      <ul>
        {filteredTodos.map((todo) => (
          <li key={todo.id}>
            <input
              type="checkbox"
              checked={todo.completed}
              onChange={() =>
                dispatch({ type: "TOGGLE_TODO", payload: todo.id })
              }
            />
            <span
              style={{
                textDecoration: todo.completed ? "line-through" : "none",
              }}
            >
              {todo.text}
            </span>
            <button
              onClick={() =>
                dispatch({ type: "DELETE_TODO", payload: todo.id })
              }
            >
              Delete
            </button>
          </li>
        ))}
      </ul>

      <button onClick={() => dispatch({ type: "CLEAR_COMPLETED" })}>
        Clear Completed
      </button>
    </div>
  );
}
```

### 2. Form State Management

```jsx
const initialFormState = {
  values: { username: "", email: "", password: "" },
  errors: {},
  touched: {},
  isSubmitting: false,
  isValid: false,
};

function formReducer(state, action) {
  switch (action.type) {
    case "SET_FIELD":
      return {
        ...state,
        values: {
          ...state.values,
          [action.payload.field]: action.payload.value,
        },
      };

    case "SET_ERROR":
      return {
        ...state,
        errors: {
          ...state.errors,
          [action.payload.field]: action.payload.error,
        },
      };

    case "SET_ERRORS":
      return {
        ...state,
        errors: action.payload,
      };

    case "SET_TOUCHED":
      return {
        ...state,
        touched: {
          ...state.touched,
          [action.payload]: true,
        },
      };

    case "SET_SUBMITTING":
      return {
        ...state,
        isSubmitting: action.payload,
      };

    case "VALIDATE":
      const errors = validateForm(state.values);
      return {
        ...state,
        errors,
        isValid: Object.keys(errors).length === 0,
      };

    case "RESET":
      return initialFormState;

    default:
      return state;
  }
}

function RegistrationForm() {
  const [state, dispatch] = useReducer(formReducer, initialFormState);

  const handleChange = (e) => {
    const { name, value } = e.target;
    dispatch({ type: "SET_FIELD", payload: { field: name, value } });
  };

  const handleBlur = (e) => {
    dispatch({ type: "SET_TOUCHED", payload: e.target.name });
    dispatch({ type: "VALIDATE" });
  };

  const handleSubmit = async (e) => {
    e.preventDefault();
    dispatch({ type: "VALIDATE" });

    if (!state.isValid) return;

    dispatch({ type: "SET_SUBMITTING", payload: true });
    try {
      await submitForm(state.values);
      dispatch({ type: "RESET" });
    } finally {
      dispatch({ type: "SET_SUBMITTING", payload: false });
    }
  };

  return (
    <form onSubmit={handleSubmit}>
      <input
        name="username"
        value={state.values.username}
        onChange={handleChange}
        onBlur={handleBlur}
      />
      {state.touched.username && state.errors.username && (
        <span className="error">{state.errors.username}</span>
      )}

      <input
        name="email"
        value={state.values.email}
        onChange={handleChange}
        onBlur={handleBlur}
      />
      {state.touched.email && state.errors.email && (
        <span className="error">{state.errors.email}</span>
      )}

      <input
        name="password"
        type="password"
        value={state.values.password}
        onChange={handleChange}
        onBlur={handleBlur}
      />
      {state.touched.password && state.errors.password && (
        <span className="error">{state.errors.password}</span>
      )}

      <button type="submit" disabled={state.isSubmitting}>
        {state.isSubmitting ? "Submitting..." : "Register"}
      </button>
    </form>
  );
}
```

### 3. Shopping Cart

```jsx
const initialCart = {
  items: [],
  coupon: null,
  shippingMethod: "standard",
};

function cartReducer(state, action) {
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
      if (action.payload.quantity <= 0) {
        return {
          ...state,
          items: state.items.filter((item) => item.id !== action.payload.id),
        };
      }
      return {
        ...state,
        items: state.items.map((item) =>
          item.id === action.payload.id
            ? { ...item, quantity: action.payload.quantity }
            : item
        ),
      };

    case "APPLY_COUPON":
      return {
        ...state,
        coupon: action.payload,
      };

    case "REMOVE_COUPON":
      return {
        ...state,
        coupon: null,
      };

    case "SET_SHIPPING":
      return {
        ...state,
        shippingMethod: action.payload,
      };

    case "CLEAR_CART":
      return initialCart;

    default:
      return state;
  }
}

function useCart() {
  const [state, dispatch] = useReducer(cartReducer, initialCart);

  const addItem = (product) => {
    dispatch({ type: "ADD_ITEM", payload: product });
  };

  const removeItem = (id) => {
    dispatch({ type: "REMOVE_ITEM", payload: id });
  };

  const updateQuantity = (id, quantity) => {
    dispatch({ type: "UPDATE_QUANTITY", payload: { id, quantity } });
  };

  const applyCoupon = (coupon) => {
    dispatch({ type: "APPLY_COUPON", payload: coupon });
  };

  const clearCart = () => {
    dispatch({ type: "CLEAR_CART" });
  };

  // Computed values
  const subtotal = state.items.reduce(
    (sum, item) => sum + item.price * item.quantity,
    0
  );

  const discount = state.coupon ? subtotal * (state.coupon.discount / 100) : 0;

  const shipping =
    state.shippingMethod === "express"
      ? 15
      : state.shippingMethod === "standard"
      ? 5
      : 0;

  const total = subtotal - discount + shipping;

  return {
    ...state,
    subtotal,
    discount,
    shipping,
    total,
    addItem,
    removeItem,
    updateQuantity,
    applyCoupon,
    clearCart,
  };
}
```

### 4. Async Operations with useReducer

```jsx
const initialState = {
  data: null,
  loading: false,
  error: null,
};

function fetchReducer(state, action) {
  switch (action.type) {
    case "FETCH_START":
      return { ...state, loading: true, error: null };
    case "FETCH_SUCCESS":
      return { data: action.payload, loading: false, error: null };
    case "FETCH_ERROR":
      return { data: null, loading: false, error: action.payload };
    default:
      return state;
  }
}

function useFetch(url) {
  const [state, dispatch] = useReducer(fetchReducer, initialState);

  useEffect(() => {
    const controller = new AbortController();

    const fetchData = async () => {
      dispatch({ type: "FETCH_START" });

      try {
        const response = await fetch(url, { signal: controller.signal });
        if (!response.ok) throw new Error("Failed to fetch");
        const data = await response.json();
        dispatch({ type: "FETCH_SUCCESS", payload: data });
      } catch (error) {
        if (error.name !== "AbortError") {
          dispatch({ type: "FETCH_ERROR", payload: error.message });
        }
      }
    };

    fetchData();

    return () => controller.abort();
  }, [url]);

  return state;
}
```

---

## 🧩 useReducer with Context

Combine `useReducer` with Context for global state management:

```jsx
const TodoContext = createContext(null);
const TodoDispatchContext = createContext(null);

function TodoProvider({ children }) {
  const [state, dispatch] = useReducer(todoReducer, initialState);

  return (
    <TodoContext.Provider value={state}>
      <TodoDispatchContext.Provider value={dispatch}>
        {children}
      </TodoDispatchContext.Provider>
    </TodoContext.Provider>
  );
}

// Custom hooks for consuming
function useTodos() {
  const context = useContext(TodoContext);
  if (!context) throw new Error("useTodos must be used within TodoProvider");
  return context;
}

function useTodoDispatch() {
  const context = useContext(TodoDispatchContext);
  if (!context)
    throw new Error("useTodoDispatch must be used within TodoProvider");
  return context;
}

// Usage in components
function TodoList() {
  const { todos, filter } = useTodos();
  const dispatch = useTodoDispatch();

  return (
    <ul>
      {todos.map((todo) => (
        <li key={todo.id}>
          <span>{todo.text}</span>
          <button
            onClick={() => dispatch({ type: "DELETE_TODO", payload: todo.id })}
          >
            Delete
          </button>
        </li>
      ))}
    </ul>
  );
}
```

---

## ⚠️ Common Mistakes

### 1. Mutating State in Reducer

```jsx
// ❌ Wrong - mutating state
function reducer(state, action) {
  switch (action.type) {
    case "ADD_ITEM":
      state.items.push(action.payload); // Mutation!
      return state;
  }
}

// ✅ Correct - return new object
function reducer(state, action) {
  switch (action.type) {
    case "ADD_ITEM":
      return {
        ...state,
        items: [...state.items, action.payload],
      };
  }
}
```

### 2. Forgetting Default Case

```jsx
// ❌ Wrong - no default case
function reducer(state, action) {
  switch (action.type) {
    case "INCREMENT":
      return { count: state.count + 1 };
    // Missing default!
  }
}

// ✅ Correct
function reducer(state, action) {
  switch (action.type) {
    case "INCREMENT":
      return { count: state.count + 1 };
    default:
      return state;
  }
}
```

### 3. Side Effects in Reducer

```jsx
// ❌ Wrong - side effects in reducer
function reducer(state, action) {
  switch (action.type) {
    case "SAVE":
      localStorage.setItem("data", JSON.stringify(state)); // Side effect!
      return state;
  }
}

// ✅ Correct - side effects outside reducer
function Component() {
  const [state, dispatch] = useReducer(reducer, initialState);

  useEffect(() => {
    localStorage.setItem("data", JSON.stringify(state));
  }, [state]);
}
```

---

## 🔧 Best Practices

### 1. Define Action Types as Constants

```jsx
const ActionTypes = {
  ADD_TODO: "ADD_TODO",
  TOGGLE_TODO: "TOGGLE_TODO",
  DELETE_TODO: "DELETE_TODO",
};

function reducer(state, action) {
  switch (action.type) {
    case ActionTypes.ADD_TODO:
    // ...
  }
}
```

### 2. Create Action Creators

```jsx
const actions = {
  addTodo: (text) => ({ type: "ADD_TODO", payload: text }),
  toggleTodo: (id) => ({ type: "TOGGLE_TODO", payload: id }),
  deleteTodo: (id) => ({ type: "DELETE_TODO", payload: id }),
};

// Usage
dispatch(actions.addTodo("Learn React"));
```

### 3. Keep Reducers Pure

```jsx
// ✅ Pure reducer - predictable, testable
function reducer(state, action) {
  // No side effects
  // Same input always produces same output
  // Only calculates new state
}
```

---

## 🏋️ Practice Exercises

### Exercise 1: Undo/Redo

Implement a text editor with undo/redo using useReducer.

### Exercise 2: Multi-Step Form

Create a multi-step form wizard with useReducer managing step, data, and validation.

### Exercise 3: Kanban Board

Build a kanban board where cards can be moved between columns.

---

## ❓ Interview Questions

**Q1: When would you use useReducer instead of useState?**

> Use useReducer for complex state with multiple sub-values, when the next state depends on the previous one, when you have many different update operations, or when you want to centralize state update logic for testing.

**Q2: What are the parts of useReducer?**

> State (current value), Dispatch (function to send actions), Reducer (pure function that computes new state from current state and action), and Actions (objects describing what happened).

**Q3: How does useReducer work with Context?**

> Combine them for global state management. Put useReducer at the top of the tree, provide state and dispatch via separate contexts, and components can consume either based on what they need.

**Q4: Should reducers have side effects?**

> No, reducers must be pure functions. Side effects (API calls, localStorage, etc.) should happen in useEffect or event handlers, not in the reducer.

**Q5: How do you test reducers?**

> Reducers are pure functions, making them easy to test: call the reducer with a state and action, assert the returned state is correct. No mocking needed.

---

## 🔑 Key Takeaways

> ### 💡 Summary
>
> - **useReducer** manages complex state with actions
> - **Reducer** is a pure function: `(state, action) => newState`
> - **Actions** describe what happened, not how state changes
> - **Use over useState** for complex, related state
> - **Never mutate** state in reducer
> - **Combine with Context** for global state
> - **Keep reducers pure** - no side effects

---

**Next:** [06 - useMemo & useCallback](./06_useMemo_useCallback.md) - Learn about memoization for performance!
