# 09. State as a Snapshot

> Understanding how state behaves in renders - the key to avoiding common bugs.

---

## 🎯 What Does "State as Snapshot" Mean?

When React renders a component, it takes a **"snapshot"** of the state at that moment. That snapshot is used for the entire render cycle. State isn't a live variable that updates instantly - it's a fixed value for each render.

```jsx
function Counter() {
  const [count, setCount] = useState(0);

  const handleClick = () => {
    setCount(count + 1);
    console.log(count); // Still 0! The snapshot hasn't changed yet
  };

  return <button onClick={handleClick}>Count: {count}</button>;
}
```

---

## 🤔 Why This Concept Matters

Understanding state as a snapshot helps you:

- ✅ Avoid bugs with stale state values
- ✅ Understand why updates don't happen "immediately"
- ✅ Use functional updates correctly
- ✅ Debug unexpected behavior
- ✅ Write more predictable code

---

## 📚 Core Concepts

### 1. State is Fixed Per Render

Each render has its own "version" of state:

```jsx
function MessageSender() {
  const [message, setMessage] = useState("Hello");

  const handleSend = () => {
    // This uses the message from THIS render's snapshot
    setTimeout(() => {
      alert(`Sending: ${message}`); // Uses snapshot, not latest
    }, 3000);
  };

  return (
    <div>
      <input value={message} onChange={(e) => setMessage(e.target.value)} />
      <button onClick={handleSend}>Send (3s delay)</button>
    </div>
  );
}

// Try this:
// 1. Type "Hello"
// 2. Click Send
// 3. Quickly change to "Goodbye"
// 4. Alert shows "Hello" (the snapshot when clicked!)
```

### 2. Rendering is Like Taking a Photo

```
┌────────────────────────────────────────────────────────────────┐
│                  RENDERING = TAKING A SNAPSHOT                  │
├────────────────────────────────────────────────────────────────┤
│                                                                 │
│  Render 1 (count = 0)                                          │
│  ┌─────────────────────────────────┐                           │
│  │ 📸 Snapshot: count = 0         │                           │
│  │ All code in this render sees 0  │                           │
│  └─────────────────────────────────┘                           │
│                    ↓ setCount(1)                               │
│  Render 2 (count = 1)                                          │
│  ┌─────────────────────────────────┐                           │
│  │ 📸 Snapshot: count = 1         │                           │
│  │ All code in this render sees 1  │                           │
│  └─────────────────────────────────┘                           │
│                    ↓ setCount(2)                               │
│  Render 3 (count = 2)                                          │
│  ┌─────────────────────────────────┐                           │
│  │ 📸 Snapshot: count = 2         │                           │
│  │ All code in this render sees 2  │                           │
│  └─────────────────────────────────┘                           │
│                                                                 │
└────────────────────────────────────────────────────────────────┘
```

### 3. Multiple setStates in One Event

When you call setState multiple times, each call uses the same snapshot:

```jsx
function Counter() {
  const [count, setCount] = useState(0);

  const handleClick = () => {
    // All three use count = 0 (the snapshot)
    setCount(count + 1); // 0 + 1 = 1
    setCount(count + 1); // 0 + 1 = 1
    setCount(count + 1); // 0 + 1 = 1
    // Result: count = 1, not 3!
  };

  return (
    <div>
      <p>Count: {count}</p>
      <button onClick={handleClick}>Add 3 (broken)</button>
    </div>
  );
}
```

**The Fix: Functional Updates**

```jsx
function Counter() {
  const [count, setCount] = useState(0);

  const handleClick = () => {
    // Each uses the PENDING state, not the snapshot
    setCount((c) => c + 1); // 0 + 1 = 1
    setCount((c) => c + 1); // 1 + 1 = 2
    setCount((c) => c + 1); // 2 + 1 = 3
    // Result: count = 3! ✅
  };

  return (
    <div>
      <p>Count: {count}</p>
      <button onClick={handleClick}>Add 3 (fixed)</button>
    </div>
  );
}
```

### 4. State in Event Handlers

Event handlers capture the state snapshot from when they were created:

```jsx
function Chat() {
  const [message, setMessage] = useState("");

  const handleSend = () => {
    // This 'message' is the snapshot from when onClick happened
    sendToServer(message);
    setMessage(""); // Clear input
  };

  return (
    <div>
      <input value={message} onChange={(e) => setMessage(e.target.value)} />
      <button onClick={handleSend}>Send</button>
    </div>
  );
}
```

### 5. State in Async Callbacks

Async code (setTimeout, fetch callbacks, promises) captures the snapshot too:

```jsx
function Profile() {
  const [user, setUser] = useState({ name: "Alice" });

  const handleClick = () => {
    // Start with user.name = 'Alice'

    setTimeout(() => {
      // 3 seconds later, this still sees 'Alice'
      // even if user changed in the meantime!
      alert(user.name);
    }, 3000);

    // Imagine user changes to 'Bob' right after clicking
    setUser({ name: "Bob" });
  };

  return <button onClick={handleClick}>Check Name</button>;
}
```

### 6. Visualizing State Through Renders

```jsx
// Render 1: number = 0
// Click button...
function Counter() {
  const [number, setNumber] = useState(0);

  return (
    <>
      <h1>{number}</h1> {/* Shows 0 */}
      <button
        onClick={() => {
          setNumber(number + 5); // Schedule update to 5

          setTimeout(() => {
            alert(number); // Shows 0! (snapshot)
          }, 3000);
        }}
      >
        +5
      </button>
    </>
  );
}

// After clicking, React schedules Render 2: number = 5
// But the setTimeout callback still has number = 0 captured
```

---

## 💻 Demo Code

### Example 1: Traffic Light

```jsx
import { useState } from "react";

function TrafficLight() {
  const [color, setColor] = useState("red");

  const handleWalk = () => {
    // Snapshot: color = current value

    // This alert will show the snapshot value
    // even if we change color before 3 seconds
    setTimeout(() => {
      alert(`You clicked when light was ${color}`);
    }, 3000);

    // Change the light immediately
    setColor("green");
  };

  return (
    <div>
      <div
        style={{
          width: 100,
          height: 100,
          borderRadius: "50%",
          backgroundColor: color,
        }}
      />
      <p>Current: {color}</p>
      <button onClick={handleWalk}>Walk</button>
      <button onClick={() => setColor("red")}>Red</button>
      <button onClick={() => setColor("yellow")}>Yellow</button>
      <button onClick={() => setColor("green")}>Green</button>
    </div>
  );
}
```

### Example 2: Message Queue

```jsx
import { useState } from "react";

function MessageQueue() {
  const [messages, setMessages] = useState([]);
  const [input, setInput] = useState("");

  const handleSend = () => {
    if (!input.trim()) return;

    // Capture current input in snapshot
    const currentMessage = input;

    // Simulate sending with delay
    setTimeout(() => {
      // This uses currentMessage from the snapshot
      // Even if input changed, we send the right message
      setMessages((prev) => [
        ...prev,
        {
          id: Date.now(),
          text: currentMessage,
          status: "sent",
        },
      ]);
    }, 1000);

    // Clear input immediately (UI feels responsive)
    setInput("");

    // Add pending message
    setMessages((prev) => [
      ...prev,
      {
        id: Date.now(),
        text: currentMessage,
        status: "pending",
      },
    ]);
  };

  return (
    <div>
      <ul>
        {messages.map((msg) => (
          <li
            key={msg.id}
            style={{
              opacity: msg.status === "pending" ? 0.5 : 1,
            }}
          >
            {msg.text} {msg.status === "pending" && "(sending...)"}
          </li>
        ))}
      </ul>
      <input
        value={input}
        onChange={(e) => setInput(e.target.value)}
        onKeyPress={(e) => e.key === "Enter" && handleSend()}
      />
      <button onClick={handleSend}>Send</button>
    </div>
  );
}
```

### Example 3: Form with Snapshot Demo

```jsx
import { useState } from "react";

function SnapshotDemo() {
  const [name, setName] = useState("Taylor");

  const handleClick = () => {
    // Snapshot: name = 'Taylor'

    setName("Alice");
    // Still 'Taylor' in this render's snapshot!
    console.log("After first setName:", name); // 'Taylor'

    setName("Bob");
    // Still 'Taylor'!
    console.log("After second setName:", name); // 'Taylor'

    // The scheduled update will be 'Bob' (last one wins)
  };

  console.log("Rendering with name:", name);

  return (
    <div>
      <p>Name: {name}</p>
      <button onClick={handleClick}>Change Name (watch console)</button>
    </div>
  );
}
```

### Example 4: Counter Comparison

```jsx
import { useState } from "react";

function CounterComparison() {
  const [count, setCount] = useState(0);

  const addThreeBroken = () => {
    // All use snapshot count = 0
    setCount(count + 1);
    setCount(count + 1);
    setCount(count + 1);
    // Result: 1
  };

  const addThreeFixed = () => {
    // Each uses pending state
    setCount((c) => c + 1);
    setCount((c) => c + 1);
    setCount((c) => c + 1);
    // Result: 3
  };

  const addThreeDelayed = () => {
    // All three timeouts capture count = current snapshot
    setTimeout(() => setCount(count + 1), 1000);
    setTimeout(() => setCount(count + 1), 2000);
    setTimeout(() => setCount(count + 1), 3000);
    // Result: 1 (all add 1 to same snapshot)
  };

  const addThreeDelayedFixed = () => {
    // All three use functional updates
    setTimeout(() => setCount((c) => c + 1), 1000);
    setTimeout(() => setCount((c) => c + 1), 2000);
    setTimeout(() => setCount((c) => c + 1), 3000);
    // Result: 3 (each adds to latest)
  };

  return (
    <div>
      <h2>Count: {count}</h2>
      <button onClick={() => setCount(0)}>Reset</button>
      <hr />
      <button onClick={addThreeBroken}>
        Add 3 (Broken: {count} + 1 + 1 + 1 = ?)
      </button>
      <button onClick={addThreeFixed}>Add 3 (Fixed: +1 +1 +1 = 3)</button>
      <hr />
      <button onClick={addThreeDelayed}>Add 3 Delayed (Broken)</button>
      <button onClick={addThreeDelayedFixed}>Add 3 Delayed (Fixed)</button>
    </div>
  );
}
```

---

## ✅ Best Practices

### 1. Use Functional Updates for Dependent State

```jsx
// ✅ Always use functional updates when new state depends on old
setCount((c) => c + 1);
setItems((prev) => [...prev, newItem]);
setUser((prev) => ({ ...prev, name: "New Name" }));
```

### 2. Capture Values for Async Operations

```jsx
const handleSubmit = () => {
  // Capture current values
  const currentData = { name, email, message };

  // Use captured values in async code
  fetch("/api/submit", {
    method: "POST",
    body: JSON.stringify(currentData),
  });
};
```

### 3. Use useEffect for Reacting to State Changes

```jsx
// ✅ React to state changes in useEffect
useEffect(() => {
  console.log("Count changed to:", count);
  document.title = `Count: ${count}`;
}, [count]);
```

---

## ❌ Common Mistakes

### 1. Expecting Immediate Updates

```jsx
// ❌ Wrong expectation
const handleClick = () => {
  setCount(count + 1);
  console.log(count); // Still old value!

  if (count > 5) {
    // Uses old value!
    doSomething();
  }
};

// ✅ Correct approach
const handleClick = () => {
  const newCount = count + 1;
  setCount(newCount);

  if (newCount > 5) {
    doSomething();
  }
};
```

### 2. Stale Closures in Callbacks

```jsx
// ❌ Bug: stale closure
useEffect(() => {
  const interval = setInterval(() => {
    setCount(count + 1); // Always uses initial count!
  }, 1000);
  return () => clearInterval(interval);
}, []);

// ✅ Fix: functional update
useEffect(() => {
  const interval = setInterval(() => {
    setCount((c) => c + 1); // Always uses latest
  }, 1000);
  return () => clearInterval(interval);
}, []);
```

### 3. Multiple Updates Using Snapshot

```jsx
// ❌ Only increments by 1
const handleClick = () => {
  setCount(count + 1);
  setCount(count + 1);
};

// ✅ Increments by 2
const handleClick = () => {
  setCount((c) => c + 1);
  setCount((c) => c + 1);
};
```

---

## 🏋️ Practice Exercises

### Exercise 1: Predict the Output

What will be alerted?

```jsx
function Quiz() {
  const [number, setNumber] = useState(0);

  return (
    <button
      onClick={() => {
        setNumber(number + 5);
        setNumber((n) => n + 1);
        setTimeout(() => {
          alert(number);
        }, 1000);
      }}
    >
      Click me
    </button>
  );
}
```

<details>
<summary>Answer</summary>

Alert shows `0`.

- `setNumber(number + 5)` schedules update to `0 + 5 = 5`
- `setNumber(n => n + 1)` schedules update to `5 + 1 = 6`
- But `setTimeout` captures `number = 0` from the snapshot
- After render, count will be 6, but alert shows 0
</details>

### Exercise 2: Fix This Code

The counter should increment by 3 when clicked:

```jsx
function Counter() {
  const [count, setCount] = useState(0);

  const handleClick = () => {
    setCount(count + 1);
    setCount(count + 1);
    setCount(count + 1);
  };

  return <button onClick={handleClick}>Count: {count}</button>;
}
```

### Exercise 3: Build a Stopwatch

Create a stopwatch that:

- Starts/stops with a button
- Shows elapsed time
- Uses setInterval correctly (hint: functional updates!)

---

## 🎤 Interview Q&A

**Q1: What does "state as a snapshot" mean in React?**

> Each render has its own fixed version of state. When you read state in an event handler or effect, you get the value from that particular render's snapshot, not a "live" value that updates immediately.

**Q2: Why doesn't state update immediately after calling setState?**

> State updates trigger a re-render, but the current render already has its snapshot. The new value is only available in the next render. This is why console.log after setState shows the old value.

**Q3: How do you fix multiple setState calls that all read the same value?**

> Use functional updates: `setCount(c => c + 1)` instead of `setCount(count + 1)`. Functional updates receive the pending state, not the snapshot.

**Q4: Why do setTimeout callbacks sometimes have stale state values?**

> Callbacks capture variables from when they were created (closure). If created during a render, they capture that render's snapshot. Use functional updates or refs to access current values.

**Q5: What's the difference between `setCount(count + 1)` and `setCount(c => c + 1)`?**

> `setCount(count + 1)` uses the snapshot value from the current render. `setCount(c => c + 1)` uses the pending state, accounting for any queued updates. The functional form is safer when the new value depends on the previous one.

---

## 📎 References

- [React Docs - State as a Snapshot](https://react.dev/learn/state-as-a-snapshot)
- [React Docs - Queueing a Series of State Updates](https://react.dev/learn/queueing-a-series-of-state-updates)
- [React Docs - useState Reference](https://react.dev/reference/react/useState)

---

**Previous Topic**: [← 08. State and useState](./08_State_and_useState.md)

**Next Topic**: [10. Updating Objects and Arrays in State →](./10_Updating_Objects_Arrays_in_State.md)
