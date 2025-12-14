# 02 - useRef Hook

## 📖 What is useRef?

`useRef` is a hook that lets you reference a value that's **not needed for rendering**. Unlike state, changing a ref doesn't trigger a re-render.

```jsx
import { useRef } from "react";

function TextInput() {
  const inputRef = useRef(null);

  const focusInput = () => {
    inputRef.current.focus(); // Direct DOM access
  };

  return (
    <div>
      <input ref={inputRef} type="text" />
      <button onClick={focusInput}>Focus Input</button>
    </div>
  );
}
```

---

## 🎯 Two Main Use Cases

### 1. Accessing DOM Elements

```jsx
function VideoPlayer() {
  const videoRef = useRef(null);

  const handlePlay = () => videoRef.current.play();
  const handlePause = () => videoRef.current.pause();

  return (
    <div>
      <video ref={videoRef} src="movie.mp4" />
      <button onClick={handlePlay}>Play</button>
      <button onClick={handlePause}>Pause</button>
    </div>
  );
}
```

### 2. Persisting Values Between Renders

```jsx
function Timer() {
  const [count, setCount] = useState(0);
  const intervalRef = useRef(null); // Persists across renders

  const start = () => {
    intervalRef.current = setInterval(() => {
      setCount((c) => c + 1);
    }, 1000);
  };

  const stop = () => {
    clearInterval(intervalRef.current);
  };

  return (
    <div>
      <p>Count: {count}</p>
      <button onClick={start}>Start</button>
      <button onClick={stop}>Stop</button>
    </div>
  );
}
```

---

## 📚 Core Concepts

### 1. Ref Object Structure

```jsx
const myRef = useRef(initialValue);

// Returns:
// { current: initialValue }

// Access/modify:
console.log(myRef.current);
myRef.current = newValue;
```

### 2. Refs vs State

```jsx
import { useState, useRef } from "react";

function CompareRefAndState() {
  const [stateCount, setStateCount] = useState(0);
  const refCount = useRef(0);

  const incrementBoth = () => {
    setStateCount(stateCount + 1); // Triggers re-render
    refCount.current += 1; // Does NOT trigger re-render
  };

  console.log("Rendered!"); // Only logs when state changes

  return (
    <div>
      <p>State: {stateCount}</p>
      <p>Ref: {refCount.current}</p>
      <button onClick={incrementBoth}>Increment Both</button>
    </div>
  );
}
```

### Comparison Table

| Feature           | useState               | useRef                      |
| ----------------- | ---------------------- | --------------------------- |
| **Re-render**     | Triggers re-render     | Does NOT trigger re-render  |
| **Value storage** | Current render only    | Persists across renders     |
| **Mutable**       | Immutable (use setter) | Mutable (direct assignment) |
| **Access**        | Direct value           | `.current` property         |
| **Use for**       | Data that affects UI   | Values that don't affect UI |

---

## 💻 Practical Examples

### 1. Focus Management

```jsx
function LoginForm() {
  const emailRef = useRef(null);
  const passwordRef = useRef(null);

  useEffect(() => {
    // Focus email input on mount
    emailRef.current.focus();
  }, []);

  const handleEmailKeyDown = (e) => {
    if (e.key === "Enter") {
      passwordRef.current.focus();
    }
  };

  return (
    <form>
      <input
        ref={emailRef}
        type="email"
        placeholder="Email"
        onKeyDown={handleEmailKeyDown}
      />
      <input ref={passwordRef} type="password" placeholder="Password" />
      <button type="submit">Login</button>
    </form>
  );
}
```

### 2. Scroll to Element

```jsx
function ScrollToSection() {
  const section1Ref = useRef(null);
  const section2Ref = useRef(null);
  const section3Ref = useRef(null);

  const scrollTo = (ref) => {
    ref.current.scrollIntoView({ behavior: "smooth" });
  };

  return (
    <div>
      <nav>
        <button onClick={() => scrollTo(section1Ref)}>Section 1</button>
        <button onClick={() => scrollTo(section2Ref)}>Section 2</button>
        <button onClick={() => scrollTo(section3Ref)}>Section 3</button>
      </nav>

      <section ref={section1Ref} style={{ height: "100vh" }}>
        Section 1
      </section>
      <section ref={section2Ref} style={{ height: "100vh" }}>
        Section 2
      </section>
      <section ref={section3Ref} style={{ height: "100vh" }}>
        Section 3
      </section>
    </div>
  );
}
```

### 3. Previous Value Tracking

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
        Now: {count}, Before: {prevCount}
      </p>
      <button onClick={() => setCount((c) => c + 1)}>Increment</button>
    </div>
  );
}
```

### 4. Timer with Controls

```jsx
function Stopwatch() {
  const [time, setTime] = useState(0);
  const [isRunning, setIsRunning] = useState(false);
  const intervalRef = useRef(null);
  const startTimeRef = useRef(null);

  const start = () => {
    if (isRunning) return;

    setIsRunning(true);
    startTimeRef.current = Date.now() - time;

    intervalRef.current = setInterval(() => {
      setTime(Date.now() - startTimeRef.current);
    }, 10);
  };

  const stop = () => {
    setIsRunning(false);
    clearInterval(intervalRef.current);
  };

  const reset = () => {
    stop();
    setTime(0);
  };

  // Cleanup on unmount
  useEffect(() => {
    return () => clearInterval(intervalRef.current);
  }, []);

  const formatTime = (ms) => {
    const minutes = Math.floor(ms / 60000);
    const seconds = Math.floor((ms % 60000) / 1000);
    const centiseconds = Math.floor((ms % 1000) / 10);
    return `${minutes.toString().padStart(2, "0")}:${seconds
      .toString()
      .padStart(2, "0")}.${centiseconds.toString().padStart(2, "0")}`;
  };

  return (
    <div>
      <h1>{formatTime(time)}</h1>
      <button onClick={start} disabled={isRunning}>
        Start
      </button>
      <button onClick={stop} disabled={!isRunning}>
        Stop
      </button>
      <button onClick={reset}>Reset</button>
    </div>
  );
}
```

### 5. Click Outside Detection

```jsx
function useClickOutside(ref, handler) {
  useEffect(() => {
    const listener = (event) => {
      // Do nothing if clicking ref's element or descendent elements
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

  useClickOutside(dropdownRef, () => setIsOpen(false));

  return (
    <div ref={dropdownRef}>
      <button onClick={() => setIsOpen(!isOpen)}>Toggle Menu</button>
      {isOpen && (
        <ul className="dropdown-menu">
          <li>Option 1</li>
          <li>Option 2</li>
          <li>Option 3</li>
        </ul>
      )}
    </div>
  );
}
```

### 6. Counting Renders

```jsx
function RenderCounter() {
  const [count, setCount] = useState(0);
  const renderCount = useRef(0);

  // Increment on every render (not in useEffect to show immediate value)
  renderCount.current += 1;

  return (
    <div>
      <p>Count: {count}</p>
      <p>This component has rendered {renderCount.current} times</p>
      <button onClick={() => setCount((c) => c + 1)}>Increment</button>
    </div>
  );
}
```

### 7. Debounced Callback

```jsx
function useDebounce(callback, delay) {
  const timeoutRef = useRef(null);

  useEffect(() => {
    // Cleanup on unmount
    return () => {
      if (timeoutRef.current) {
        clearTimeout(timeoutRef.current);
      }
    };
  }, []);

  const debouncedCallback = (...args) => {
    if (timeoutRef.current) {
      clearTimeout(timeoutRef.current);
    }

    timeoutRef.current = setTimeout(() => {
      callback(...args);
    }, delay);
  };

  return debouncedCallback;
}

// Usage
function SearchInput() {
  const [query, setQuery] = useState("");
  const [results, setResults] = useState([]);

  const search = useDebounce((searchTerm) => {
    console.log("Searching for:", searchTerm);
    // API call here
    fetch(`/api/search?q=${searchTerm}`)
      .then((res) => res.json())
      .then(setResults);
  }, 500);

  const handleChange = (e) => {
    setQuery(e.target.value);
    search(e.target.value);
  };

  return (
    <div>
      <input value={query} onChange={handleChange} placeholder="Search..." />
      <ul>
        {results.map((item) => (
          <li key={item.id}>{item.name}</li>
        ))}
      </ul>
    </div>
  );
}
```

### 8. Canvas Drawing

```jsx
function DrawingCanvas() {
  const canvasRef = useRef(null);
  const isDrawingRef = useRef(false);
  const lastPosRef = useRef({ x: 0, y: 0 });

  useEffect(() => {
    const canvas = canvasRef.current;
    const ctx = canvas.getContext("2d");

    ctx.strokeStyle = "#000";
    ctx.lineWidth = 2;
    ctx.lineCap = "round";

    const getPos = (e) => {
      const rect = canvas.getBoundingClientRect();
      return {
        x: e.clientX - rect.left,
        y: e.clientY - rect.top,
      };
    };

    const startDrawing = (e) => {
      isDrawingRef.current = true;
      lastPosRef.current = getPos(e);
    };

    const draw = (e) => {
      if (!isDrawingRef.current) return;

      const pos = getPos(e);
      ctx.beginPath();
      ctx.moveTo(lastPosRef.current.x, lastPosRef.current.y);
      ctx.lineTo(pos.x, pos.y);
      ctx.stroke();
      lastPosRef.current = pos;
    };

    const stopDrawing = () => {
      isDrawingRef.current = false;
    };

    canvas.addEventListener("mousedown", startDrawing);
    canvas.addEventListener("mousemove", draw);
    canvas.addEventListener("mouseup", stopDrawing);
    canvas.addEventListener("mouseout", stopDrawing);

    return () => {
      canvas.removeEventListener("mousedown", startDrawing);
      canvas.removeEventListener("mousemove", draw);
      canvas.removeEventListener("mouseup", stopDrawing);
      canvas.removeEventListener("mouseout", stopDrawing);
    };
  }, []);

  const clearCanvas = () => {
    const canvas = canvasRef.current;
    const ctx = canvas.getContext("2d");
    ctx.clearRect(0, 0, canvas.width, canvas.height);
  };

  return (
    <div>
      <canvas
        ref={canvasRef}
        width={400}
        height={300}
        style={{ border: "1px solid #ccc" }}
      />
      <button onClick={clearCanvas}>Clear</button>
    </div>
  );
}
```

---

## 🧩 Ref Callback Pattern

For more control over when refs are attached/detached:

```jsx
function MeasureElement() {
  const [height, setHeight] = useState(0);

  // Callback ref - called when element is attached/detached
  const measureRef = useCallback((node) => {
    if (node !== null) {
      setHeight(node.getBoundingClientRect().height);
    }
  }, []);

  return (
    <div>
      <div ref={measureRef} style={{ padding: "20px" }}>
        <p>This element's height is being measured</p>
        <p>Resize the window to see changes</p>
      </div>
      <p>Height: {height}px</p>
    </div>
  );
}
```

---

## ⚠️ Common Mistakes

### 1. Using Ref for Rendering Data

```jsx
// ❌ Wrong: UI won't update when ref changes
function BadCounter() {
  const countRef = useRef(0);

  const increment = () => {
    countRef.current += 1;
    // UI doesn't update!
  };

  return (
    <div>
      <p>Count: {countRef.current}</p>
      <button onClick={increment}>+</button>
    </div>
  );
}

// ✅ Correct: Use state for rendering data
function GoodCounter() {
  const [count, setCount] = useState(0);

  return (
    <div>
      <p>Count: {count}</p>
      <button onClick={() => setCount((c) => c + 1)}>+</button>
    </div>
  );
}
```

### 2. Accessing Ref During Render

```jsx
// ❌ Wrong: ref might not be attached yet
function BadComponent() {
  const inputRef = useRef(null);

  // This runs during render - ref is null!
  console.log(inputRef.current.value); // Error!

  return <input ref={inputRef} />;
}

// ✅ Correct: Access ref in effect or event handler
function GoodComponent() {
  const inputRef = useRef(null);

  useEffect(() => {
    // Ref is attached after render
    console.log(inputRef.current.value);
  }, []);

  return <input ref={inputRef} />;
}
```

### 3. Forgetting Cleanup

```jsx
// ❌ Wrong: Interval keeps running after unmount
function BadTimer() {
  const intervalRef = useRef(null);

  const start = () => {
    intervalRef.current = setInterval(() => {
      console.log("tick");
    }, 1000);
  };

  return <button onClick={start}>Start</button>;
}

// ✅ Correct: Clean up in useEffect
function GoodTimer() {
  const intervalRef = useRef(null);

  useEffect(() => {
    return () => {
      if (intervalRef.current) {
        clearInterval(intervalRef.current);
      }
    };
  }, []);

  const start = () => {
    intervalRef.current = setInterval(() => {
      console.log("tick");
    }, 1000);
  };

  return <button onClick={start}>Start</button>;
}
```

### 4. Overusing Refs

```jsx
// ❌ Wrong: Using ref when state would be better
function BadForm() {
  const nameRef = useRef("");
  const emailRef = useRef("");

  // Can't easily validate or show values
  return (
    <form>
      <input onChange={(e) => (nameRef.current = e.target.value)} />
      <input onChange={(e) => (emailRef.current = e.target.value)} />
    </form>
  );
}

// ✅ Correct: Use controlled inputs for forms
function GoodForm() {
  const [name, setName] = useState("");
  const [email, setEmail] = useState("");

  return (
    <form>
      <input value={name} onChange={(e) => setName(e.target.value)} />
      <input value={email} onChange={(e) => setEmail(e.target.value)} />
    </form>
  );
}
```

---

## 🔧 Best Practices

### 1. Use State for UI, Refs for Side Effects

```jsx
// ✅ State for what's displayed
const [isPlaying, setIsPlaying] = useState(false);

// ✅ Ref for DOM access / side effects
const videoRef = useRef(null);

const togglePlay = () => {
  if (isPlaying) {
    videoRef.current.pause();
  } else {
    videoRef.current.play();
  }
  setIsPlaying(!isPlaying);
};
```

### 2. Initialize Refs Properly

```jsx
// ✅ For DOM elements
const inputRef = useRef(null);

// ✅ For values with known initial value
const countRef = useRef(0);
const intervalRef = useRef(null);

// ✅ For lazy initialization
const expensiveRef = useRef(null);
if (expensiveRef.current === null) {
  expensiveRef.current = createExpensiveObject();
}
```

### 3. Consider Callback Refs for Dynamic Elements

```jsx
function DynamicList() {
  const [items, setItems] = useState([]);
  const itemRefs = useRef(new Map());

  const setItemRef = (id, node) => {
    if (node) {
      itemRefs.current.set(id, node);
    } else {
      itemRefs.current.delete(id);
    }
  };

  const scrollToItem = (id) => {
    const node = itemRefs.current.get(id);
    if (node) {
      node.scrollIntoView({ behavior: "smooth" });
    }
  };

  return (
    <ul>
      {items.map((item) => (
        <li key={item.id} ref={(node) => setItemRef(item.id, node)}>
          {item.name}
        </li>
      ))}
    </ul>
  );
}
```

---

## 🏋️ Practice Exercises

### Exercise 1: Auto-Focus First Error

Create a form that auto-focuses the first field with an error after submission.

### Exercise 2: Intersection Observer

Build a component that lazy-loads images when they scroll into view using IntersectionObserver.

### Exercise 3: Copy to Clipboard

Create a code block component with a "Copy" button that copies content to clipboard.

### Exercise 4: Drag and Drop

Implement a simple drag-and-drop list using refs to track positions.

---

## ❓ Interview Questions

**Q1: What is useRef and when would you use it?**

> useRef returns a mutable ref object whose `.current` property persists across renders without causing re-renders. Use it for: (1) accessing DOM elements directly, (2) storing mutable values that shouldn't trigger re-renders, (3) storing previous values, (4) keeping references to timers/intervals.

**Q2: What's the difference between useRef and useState?**

> useState triggers a re-render when updated and returns the current value. useRef does not trigger re-renders and stores value in `.current`. Use state for values that affect rendering, refs for values that don't.

**Q3: When is the ref attached to the DOM element?**

> The ref is attached after the component mounts (after the first render) and is available in useEffect and event handlers. It's null during the initial render.

**Q4: What is a callback ref and when would you use it?**

> A callback ref is a function passed to the ref prop. It's called with the DOM node when attached and null when detached. Use it when you need to react to ref changes or measure elements.

**Q5: Can you use multiple refs on the same element?**

> No, an element can only have one ref. To use multiple refs, you can combine them with a callback ref or use a library like `useMergeRefs`.

---

## 🔑 Key Takeaways

> ### 💡 Summary
>
> - **useRef** creates a persistent mutable container
> - **Does not trigger** re-renders when changed
> - **Two main uses**: DOM access and persisting values
> - **Access via** `.current` property
> - **Use state** for UI data, **refs** for non-rendering values
> - **Clean up** timers/intervals stored in refs
> - **Callback refs** for dynamic element tracking

---

**Next:** [03 - useContext Hook](./03_useContext_Hook.md) - Learn about sharing state deeply with Context!
