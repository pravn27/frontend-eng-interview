# 11. Event Handling

> Making your UI interactive - responding to user actions.

---

## 🎯 What is Event Handling?

Event handling in React lets you respond to user interactions like clicks, keyboard input, form submissions, and more. React uses **Synthetic Events** - a cross-browser wrapper around native browser events.

```jsx
function Button() {
  const handleClick = () => {
    alert("Button clicked!");
  };

  return <button onClick={handleClick}>Click me</button>;
}
```

---

## 🤔 Why Synthetic Events?

React's Synthetic Events provide:

- ✅ **Cross-browser consistency** - Same API across all browsers
- ✅ **Performance** - Event pooling and delegation
- ✅ **Familiar interface** - Similar to native DOM events
- ✅ **Integration with React** - Works with React's rendering cycle

---

## 📚 Core Concepts

### 1. Basic Event Handlers

```jsx
function EventExamples() {
  // Click event
  const handleClick = () => {
    console.log("Clicked!");
  };

  // Click with event object
  const handleClickWithEvent = (e) => {
    console.log("Event:", e);
    console.log("Target:", e.target);
    console.log("Current Target:", e.currentTarget);
  };

  return (
    <div>
      {/* Method 1: Reference to function */}
      <button onClick={handleClick}>Click Me</button>

      {/* Method 2: Inline arrow function */}
      <button onClick={() => console.log("Inline click")}>
        Inline Handler
      </button>

      {/* Method 3: With event object */}
      <button onClick={handleClickWithEvent}>With Event</button>
    </div>
  );
}
```

### 2. Common Event Types

```jsx
function CommonEvents() {
  return (
    <div>
      {/* Mouse Events */}
      <button onClick={(e) => console.log("click", e)}>Click</button>
      <button onDoubleClick={(e) => console.log("double click", e)}>
        Double Click
      </button>
      <div onMouseEnter={(e) => console.log("mouse enter", e)}>Hover me</div>
      <div onMouseLeave={(e) => console.log("mouse leave", e)}>Leave me</div>
      <div onMouseMove={(e) => console.log(e.clientX, e.clientY)}>
        Track mouse
      </div>
      <div onMouseDown={(e) => console.log("mouse down", e)}>Mouse down</div>
      <div onMouseUp={(e) => console.log("mouse up", e)}>Mouse up</div>
      {/* Keyboard Events */}
      <input onKeyDown={(e) => console.log("key down:", e.key)} />
      <input onKeyUp={(e) => console.log("key up:", e.key)} />
      <input onKeyPress={(e) => console.log("key press:", e.key)} /> {/* Deprecated */}
      {/* Form Events */}
      <input onChange={(e) => console.log("changed:", e.target.value)} />
      <input onFocus={(e) => console.log("focused", e)} />
      <input onBlur={(e) => console.log("blurred", e)} />
      <form
        onSubmit={(e) => {
          e.preventDefault();
          console.log("submitted");
        }}
      >
        <button type="submit">Submit</button>
      </form>
      <input onInput={(e) => console.log("input:", e.target.value)} />
      {/* Focus Events */}
      <input onFocus={() => console.log("focused")} />
      <input onBlur={() => console.log("blurred")} />
      {/* Clipboard Events */}
      <input onCopy={(e) => console.log("copied", e)} />
      <input onPaste={(e) => console.log("pasted", e)} />
      <input onCut={(e) => console.log("cut", e)} />
      {/* Drag Events */}
      <div draggable onDragStart={(e) => console.log("drag start", e)}>
        Drag me
      </div>
      <div
        onDrop={(e) => console.log("dropped", e)}
        onDragOver={(e) => e.preventDefault()}
      >
        Drop here
      </div>
      {/* Scroll Event */}
      <div onScroll={(e) => console.log("scrolled", e.target.scrollTop)}>
        Scrollable content
      </div>
      {/* Touch Events (mobile) */}
      <div onTouchStart={(e) => console.log("touch start", e)}>Touch me</div>
      <div onTouchEnd={(e) => console.log("touch end", e)}>Touch end</div>
      <div onTouchMove={(e) => console.log("touch move", e)}>Touch move</div>
    </div>
  );
}
```

### 3. The Event Object

```jsx
function EventObject() {
  const handleClick = (e) => {
    // Common properties
    console.log(e.type); // 'click'
    console.log(e.target); // Element that triggered event
    console.log(e.currentTarget); // Element with handler attached
    console.log(e.timeStamp); // When event occurred

    // Mouse position
    console.log(e.clientX, e.clientY); // Relative to viewport
    console.log(e.pageX, e.pageY); // Relative to document
    console.log(e.screenX, e.screenY); // Relative to screen

    // Modifier keys
    console.log(e.shiftKey); // Shift held?
    console.log(e.ctrlKey); // Ctrl held?
    console.log(e.altKey); // Alt held?
    console.log(e.metaKey); // Cmd/Win held?

    // Keyboard events
    console.log(e.key); // 'Enter', 'a', 'Escape', etc.
    console.log(e.code); // 'Enter', 'KeyA', 'Escape', etc.

    // Native event (if needed)
    console.log(e.nativeEvent);
  };

  return <button onClick={handleClick}>Click for event info</button>;
}
```

### 4. Passing Arguments to Event Handlers

```jsx
function ArgumentExamples() {
  const handleDelete = (id) => {
    console.log("Deleting item:", id);
  };

  const handleAction = (id, action, e) => {
    console.log(`Action: ${action} on item: ${id}`);
    console.log("Event:", e);
  };

  const items = [
    { id: 1, name: "Item 1" },
    { id: 2, name: "Item 2" },
    { id: 3, name: "Item 3" },
  ];

  return (
    <ul>
      {items.map((item) => (
        <li key={item.id}>
          {item.name}

          {/* Method 1: Arrow function (most common) */}
          <button onClick={() => handleDelete(item.id)}>Delete</button>

          {/* Method 2: Arrow function with event */}
          <button onClick={(e) => handleAction(item.id, "edit", e)}>
            Edit
          </button>

          {/* Method 3: bind (less common) */}
          <button onClick={handleDelete.bind(null, item.id)}>Remove</button>
        </li>
      ))}
    </ul>
  );
}
```

### 5. Preventing Default Behavior

```jsx
function PreventDefault() {
  // Prevent form submission page reload
  const handleSubmit = (e) => {
    e.preventDefault();
    console.log("Form submitted without page reload");
  };

  // Prevent link navigation
  const handleLinkClick = (e) => {
    e.preventDefault();
    console.log("Link clicked but not navigated");
  };

  // Prevent right-click context menu
  const handleContextMenu = (e) => {
    e.preventDefault();
    console.log("Custom context menu could appear here");
  };

  return (
    <div>
      <form onSubmit={handleSubmit}>
        <input type="text" name="query" />
        <button type="submit">Search</button>
      </form>

      <a href="https://google.com" onClick={handleLinkClick}>
        Click me (won't navigate)
      </a>

      <div onContextMenu={handleContextMenu}>Right-click me</div>
    </div>
  );
}
```

### 6. Event Bubbling and Propagation

```jsx
function EventBubbling() {
  const handleOuterClick = () => {
    console.log("Outer clicked");
  };

  const handleInnerClick = (e) => {
    console.log("Inner clicked");
    // Stop event from bubbling to outer
    e.stopPropagation();
  };

  const handleCaptureClick = () => {
    console.log("Capture phase");
  };

  return (
    <div onClick={handleOuterClick}>
      <p>Click anywhere in this outer div</p>

      {/* Without stopPropagation: both handlers fire */}
      {/* With stopPropagation: only inner fires */}
      <button onClick={handleInnerClick}>Inner Button</button>

      {/* Capture phase handler (runs before bubbling) */}
      <div onClickCapture={handleCaptureClick}>
        <button onClick={() => console.log("Target clicked")}>
          With Capture
        </button>
      </div>
    </div>
  );
}
```

```
Event Flow:

┌─────────────────────────────────────────────┐
│                   CAPTURE                    │
│  (onClickCapture) - Top to bottom           │
│     ↓                                       │
│  ┌─────────────────────────────────────┐   │
│  │              TARGET                  │   │
│  │  (onClick on actual element)         │   │
│  └─────────────────────────────────────┘   │
│     ↓                                       │
│                   BUBBLE                     │
│  (onClick) - Bottom to top                  │
│  e.stopPropagation() stops bubbling         │
└─────────────────────────────────────────────┘
```

### 7. Event Handlers as Props

```jsx
// Child component receives handler as prop
function Button({ onClick, children }) {
  return <button onClick={onClick}>{children}</button>;
}

// More flexible: pass event to parent
function ActionButton({ onAction, actionId, children }) {
  const handleClick = (e) => {
    onAction(actionId, e);
  };

  return <button onClick={handleClick}>{children}</button>;
}

// Parent component
function Parent() {
  const handleButtonClick = () => {
    console.log("Button was clicked");
  };

  const handleAction = (id, event) => {
    console.log(`Action ${id} triggered`, event);
  };

  return (
    <div>
      <Button onClick={handleButtonClick}>Simple Button</Button>

      <ActionButton onAction={handleAction} actionId="save">
        Save
      </ActionButton>

      <ActionButton onAction={handleAction} actionId="delete">
        Delete
      </ActionButton>
    </div>
  );
}
```

---

## 💻 Demo Code

### Example 1: Interactive Counter

```jsx
import { useState } from "react";

function InteractiveCounter() {
  const [count, setCount] = useState(0);
  const [step, setStep] = useState(1);

  const handleIncrement = () => setCount((c) => c + step);
  const handleDecrement = () => setCount((c) => c - step);
  const handleReset = () => setCount(0);

  const handleKeyDown = (e) => {
    if (e.key === "ArrowUp") {
      e.preventDefault();
      handleIncrement();
    } else if (e.key === "ArrowDown") {
      e.preventDefault();
      handleDecrement();
    } else if (e.key === "r" || e.key === "R") {
      handleReset();
    }
  };

  const handleDoubleClick = () => {
    setCount((c) => c * 2);
  };

  return (
    <div
      tabIndex={0}
      onKeyDown={handleKeyDown}
      style={{ outline: "none", padding: "20px" }}
    >
      <h2 onDoubleClick={handleDoubleClick} title="Double-click to double">
        Count: {count}
      </h2>

      <div>
        <label>
          Step:
          <input
            type="number"
            value={step}
            onChange={(e) => setStep(Number(e.target.value))}
            min="1"
          />
        </label>
      </div>

      <div>
        <button onClick={handleDecrement}>-{step}</button>
        <button onClick={handleReset}>Reset</button>
        <button onClick={handleIncrement}>+{step}</button>
      </div>

      <p>
        <small>
          Use Arrow keys to increment/decrement, R to reset. Double-click count
          to double it.
        </small>
      </p>
    </div>
  );
}
```

### Example 2: Form with Various Events

```jsx
import { useState } from "react";

function FormExample() {
  const [formData, setFormData] = useState({
    username: "",
    email: "",
    password: "",
    bio: "",
    agreeToTerms: false,
  });
  const [errors, setErrors] = useState({});
  const [focused, setFocused] = useState("");

  const handleChange = (e) => {
    const { name, value, type, checked } = e.target;
    setFormData((prev) => ({
      ...prev,
      [name]: type === "checkbox" ? checked : value,
    }));

    // Clear error on change
    if (errors[name]) {
      setErrors((prev) => ({ ...prev, [name]: "" }));
    }
  };

  const handleFocus = (e) => {
    setFocused(e.target.name);
  };

  const handleBlur = (e) => {
    setFocused("");
    validateField(e.target.name, e.target.value);
  };

  const validateField = (name, value) => {
    let error = "";

    switch (name) {
      case "username":
        if (value.length < 3) {
          error = "Username must be at least 3 characters";
        }
        break;
      case "email":
        if (!/\S+@\S+\.\S+/.test(value)) {
          error = "Please enter a valid email";
        }
        break;
      case "password":
        if (value.length < 8) {
          error = "Password must be at least 8 characters";
        }
        break;
      default:
        break;
    }

    setErrors((prev) => ({ ...prev, [name]: error }));
    return !error;
  };

  const handleSubmit = (e) => {
    e.preventDefault();

    // Validate all fields
    const isValid = Object.keys(formData).every((key) => {
      if (key === "agreeToTerms") {
        if (!formData[key]) {
          setErrors((prev) => ({ ...prev, [key]: "You must agree to terms" }));
          return false;
        }
        return true;
      }
      return validateField(key, formData[key]);
    });

    if (isValid) {
      console.log("Form submitted:", formData);
      alert("Form submitted successfully!");
    }
  };

  const handleKeyDown = (e) => {
    // Submit on Ctrl/Cmd + Enter
    if (e.key === "Enter" && (e.ctrlKey || e.metaKey)) {
      handleSubmit(e);
    }
  };

  return (
    <form onSubmit={handleSubmit} onKeyDown={handleKeyDown}>
      <h2>Sign Up</h2>

      <div className={`field ${focused === "username" ? "focused" : ""}`}>
        <label htmlFor="username">Username</label>
        <input
          id="username"
          name="username"
          value={formData.username}
          onChange={handleChange}
          onFocus={handleFocus}
          onBlur={handleBlur}
          placeholder="Enter username"
        />
        {errors.username && <span className="error">{errors.username}</span>}
      </div>

      <div className={`field ${focused === "email" ? "focused" : ""}`}>
        <label htmlFor="email">Email</label>
        <input
          id="email"
          name="email"
          type="email"
          value={formData.email}
          onChange={handleChange}
          onFocus={handleFocus}
          onBlur={handleBlur}
          placeholder="Enter email"
        />
        {errors.email && <span className="error">{errors.email}</span>}
      </div>

      <div className={`field ${focused === "password" ? "focused" : ""}`}>
        <label htmlFor="password">Password</label>
        <input
          id="password"
          name="password"
          type="password"
          value={formData.password}
          onChange={handleChange}
          onFocus={handleFocus}
          onBlur={handleBlur}
          placeholder="Enter password"
        />
        {errors.password && <span className="error">{errors.password}</span>}
      </div>

      <div className={`field ${focused === "bio" ? "focused" : ""}`}>
        <label htmlFor="bio">Bio</label>
        <textarea
          id="bio"
          name="bio"
          value={formData.bio}
          onChange={handleChange}
          onFocus={handleFocus}
          onBlur={handleBlur}
          placeholder="Tell us about yourself"
          rows={4}
        />
      </div>

      <div className="field">
        <label>
          <input
            type="checkbox"
            name="agreeToTerms"
            checked={formData.agreeToTerms}
            onChange={handleChange}
          />
          I agree to the terms and conditions
        </label>
        {errors.agreeToTerms && (
          <span className="error">{errors.agreeToTerms}</span>
        )}
      </div>

      <button type="submit">Sign Up</button>
      <p>
        <small>Tip: Press Ctrl+Enter to submit</small>
      </p>
    </form>
  );
}
```

### Example 3: Drag and Drop

```jsx
import { useState } from "react";

function DragDropExample() {
  const [items, setItems] = useState([
    { id: 1, text: "Item 1" },
    { id: 2, text: "Item 2" },
    { id: 3, text: "Item 3" },
    { id: 4, text: "Item 4" },
  ]);
  const [draggedItem, setDraggedItem] = useState(null);

  const handleDragStart = (e, item) => {
    setDraggedItem(item);
    e.target.style.opacity = "0.5";
  };

  const handleDragEnd = (e) => {
    e.target.style.opacity = "1";
    setDraggedItem(null);
  };

  const handleDragOver = (e) => {
    e.preventDefault();
  };

  const handleDrop = (e, targetItem) => {
    e.preventDefault();

    if (!draggedItem || draggedItem.id === targetItem.id) return;

    const newItems = [...items];
    const draggedIndex = items.findIndex((i) => i.id === draggedItem.id);
    const targetIndex = items.findIndex((i) => i.id === targetItem.id);

    // Remove dragged item and insert at target position
    newItems.splice(draggedIndex, 1);
    newItems.splice(targetIndex, 0, draggedItem);

    setItems(newItems);
  };

  return (
    <div>
      <h2>Drag to Reorder</h2>
      <ul style={{ listStyle: "none", padding: 0 }}>
        {items.map((item) => (
          <li
            key={item.id}
            draggable
            onDragStart={(e) => handleDragStart(e, item)}
            onDragEnd={handleDragEnd}
            onDragOver={handleDragOver}
            onDrop={(e) => handleDrop(e, item)}
            style={{
              padding: "10px",
              margin: "5px 0",
              backgroundColor: "#f0f0f0",
              cursor: "grab",
              borderRadius: "4px",
            }}
          >
            {item.text}
          </li>
        ))}
      </ul>
    </div>
  );
}
```

---

## ✅ Best Practices

### 1. Define Handlers Outside JSX

```jsx
// ✅ Good - handler defined outside
function Button() {
  const handleClick = () => {
    console.log("clicked");
  };

  return <button onClick={handleClick}>Click</button>;
}

// ❌ Avoid - inline for complex logic
function Button() {
  return (
    <button
      onClick={() => {
        // lots of code here
        console.log("clicked");
        doSomething();
        doAnotherThing();
      }}
    >
      Click
    </button>
  );
}
```

### 2. Name Handlers Clearly

```jsx
// ✅ Good naming
const handleSubmit = () => {};
const handleInputChange = () => {};
const handleDeleteClick = () => {};

// ❌ Unclear naming
const click = () => {};
const do1 = () => {};
const fn = () => {};
```

### 3. Use Functional Updates for State

```jsx
// ✅ Good - functional update
const handleIncrement = () => {
  setCount((c) => c + 1);
};

// ❌ Risky - may have stale closure
const handleIncrement = () => {
  setCount(count + 1);
};
```

---

## ❌ Common Mistakes

### 1. Calling Functions Instead of Passing

```jsx
// ❌ Wrong - calls immediately on render!
<button onClick={handleClick()}>Click</button>

// ✅ Correct - passes function reference
<button onClick={handleClick}>Click</button>

// ✅ Correct - arrow function that calls
<button onClick={() => handleClick()}>Click</button>
```

### 2. Forgetting preventDefault

```jsx
// ❌ Form will reload page
<form onSubmit={handleSubmit}>

// ✅ Prevent default behavior
const handleSubmit = (e) => {
  e.preventDefault();
  // ... handle form
};
```

### 3. Event Handler Returns

```jsx
// ❌ Returning false doesn't work in React
<a href="#" onClick={() => false}>Link</a>

// ✅ Must use preventDefault
<a href="#" onClick={(e) => e.preventDefault()}>Link</a>
```

---

## 🏋️ Practice Exercises

### Exercise 1: Click Counter with Keyboard

Create a counter that:

- Increments on click
- Increments on spacebar
- Resets on Escape key

### Exercise 2: Input with Character Count

Build an input that:

- Shows remaining characters (max 100)
- Changes color when < 20 remaining
- Shows warning at < 10

### Exercise 3: Image Carousel

Create a carousel with:

- Next/Previous buttons
- Keyboard navigation (arrows)
- Click on thumbnails to select

---

## 🎤 Interview Q&A

**Q1: What are Synthetic Events in React?**

> Synthetic Events are React's cross-browser wrapper around native browser events. They provide a consistent interface regardless of browser, and are pooled for performance.

**Q2: How do you pass arguments to event handlers?**

> Use an arrow function: `onClick={() => handleClick(arg)}` or bind: `onClick={handleClick.bind(null, arg)}`. Arrow functions are more common.

**Q3: What's the difference between `onClick={handleClick}` and `onClick={handleClick()}`?**

> `onClick={handleClick}` passes the function reference - it will be called when clicked. `onClick={handleClick()}` calls the function immediately during render and passes its return value.

**Q4: How do you prevent event bubbling?**

> Call `e.stopPropagation()` in the event handler. This stops the event from bubbling up to parent elements.

**Q5: Why do we need `e.preventDefault()` in form submissions?**

> By default, form submission causes a page reload. `e.preventDefault()` stops this behavior, allowing React to handle the form data without losing application state.

---

## 📎 References

- [React Docs - Responding to Events](https://react.dev/learn/responding-to-events)
- [React Docs - SyntheticEvent](https://react.dev/reference/react-dom/components/common#react-event-object)
- [MDN - Event Reference](https://developer.mozilla.org/en-US/docs/Web/Events)

---

**Previous Topic**: [← 10. Updating Objects and Arrays](./10_Updating_Objects_Arrays_in_State.md)

**Next Topic**: [12. Conditional Rendering →](./12_Conditional_Rendering.md)
