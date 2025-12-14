# 10 - React Portals

## 📖 What are Portals?

Portals let you render children into a DOM node that exists **outside** the parent component's DOM hierarchy, while maintaining React's component tree structure.

```jsx
import { createPortal } from "react-dom";

function Modal({ children }) {
  return createPortal(
    <div className="modal">{children}</div>,
    document.getElementById("modal-root") // Renders here, not in parent
  );
}

// In index.html
// <div id="root"></div>
// <div id="modal-root"></div>
```

---

## 🎯 Why Portals?

### The Problem: CSS Stacking Context

```jsx
// ❌ Modal is trapped by parent's CSS
function Card() {
  return (
    <div style={{ overflow: "hidden", position: "relative" }}>
      <h1>Card</h1>
      <Modal>
        {" "}
        {/* Modal gets cut off by overflow: hidden */}
        <p>Modal content</p>
      </Modal>
    </div>
  );
}
```

### The Solution: Portals

```jsx
// ✅ Modal renders outside parent, not affected by CSS
function Card() {
  return (
    <div style={{ overflow: "hidden", position: "relative" }}>
      <h1>Card</h1>
      <Modal>
        {" "}
        {/* Renders to #modal-root, outside overflow */}
        <p>Modal content</p>
      </Modal>
    </div>
  );
}
```

---

## 📚 Core Concepts

### 1. createPortal Syntax

```jsx
import { createPortal } from "react-dom";

createPortal(children, domNode, key?)

// children: React elements to render
// domNode: DOM element to render into
// key: Optional key for the portal
```

### 2. Event Bubbling Still Works

Even though the DOM is separate, events bubble through the React tree:

```jsx
function Parent() {
  const handleClick = () => console.log("Parent clicked!");

  return (
    <div onClick={handleClick}>
      <Portal>
        <button>Click me</button> {/* Click bubbles to Parent! */}
      </Portal>
    </div>
  );
}
```

### 3. Context Works Through Portals

```jsx
const ThemeContext = createContext("light");

function App() {
  return (
    <ThemeContext.Provider value="dark">
      <Portal>
        <ThemedModal /> {/* Can access ThemeContext */}
      </Portal>
    </ThemeContext.Provider>
  );
}

function ThemedModal() {
  const theme = useContext(ThemeContext); // "dark"
  return <div className={theme}>Modal</div>;
}
```

---

## 💻 Practical Examples

### 1. Modal Component

```jsx
import { createPortal } from "react-dom";
import { useEffect, useRef } from "react";

function Modal({ isOpen, onClose, title, children }) {
  const previousFocusRef = useRef(null);

  useEffect(() => {
    if (isOpen) {
      previousFocusRef.current = document.activeElement;
      document.body.style.overflow = "hidden";
    }

    return () => {
      document.body.style.overflow = "";
      previousFocusRef.current?.focus();
    };
  }, [isOpen]);

  useEffect(() => {
    const handleEscape = (e) => {
      if (e.key === "Escape") onClose();
    };

    if (isOpen) {
      document.addEventListener("keydown", handleEscape);
      return () => document.removeEventListener("keydown", handleEscape);
    }
  }, [isOpen, onClose]);

  if (!isOpen) return null;

  return createPortal(
    <div className="modal-overlay" onClick={onClose}>
      <div
        className="modal-content"
        onClick={(e) => e.stopPropagation()}
        role="dialog"
        aria-modal="true"
        aria-labelledby="modal-title"
      >
        <header className="modal-header">
          <h2 id="modal-title">{title}</h2>
          <button onClick={onClose} aria-label="Close modal">
            ×
          </button>
        </header>
        <div className="modal-body">{children}</div>
      </div>
    </div>,
    document.getElementById("modal-root")
  );
}

// Usage
function App() {
  const [showModal, setShowModal] = useState(false);

  return (
    <div>
      <button onClick={() => setShowModal(true)}>Open Modal</button>
      <Modal
        isOpen={showModal}
        onClose={() => setShowModal(false)}
        title="My Modal"
      >
        <p>Modal content here!</p>
      </Modal>
    </div>
  );
}
```

### 2. Tooltip Component

```jsx
import { createPortal } from "react-dom";
import { useState, useRef, useEffect } from "react";

function Tooltip({ children, content }) {
  const [show, setShow] = useState(false);
  const [position, setPosition] = useState({ top: 0, left: 0 });
  const triggerRef = useRef(null);

  useEffect(() => {
    if (show && triggerRef.current) {
      const rect = triggerRef.current.getBoundingClientRect();
      setPosition({
        top: rect.top - 8,
        left: rect.left + rect.width / 2,
      });
    }
  }, [show]);

  return (
    <>
      <span
        ref={triggerRef}
        onMouseEnter={() => setShow(true)}
        onMouseLeave={() => setShow(false)}
      >
        {children}
      </span>

      {show &&
        createPortal(
          <div
            className="tooltip"
            style={{
              position: "fixed",
              top: position.top,
              left: position.left,
              transform: "translate(-50%, -100%)",
            }}
          >
            {content}
          </div>,
          document.body
        )}
    </>
  );
}

// Usage
function App() {
  return (
    <p>
      Hover over this{" "}
      <Tooltip content="I'm a tooltip!">
        <strong>text</strong>
      </Tooltip>{" "}
      to see tooltip.
    </p>
  );
}
```

### 3. Toast Notifications

```jsx
import { createPortal } from "react-dom";
import { createContext, useContext, useState, useCallback } from "react";

const ToastContext = createContext(null);

function ToastProvider({ children }) {
  const [toasts, setToasts] = useState([]);

  const addToast = useCallback((message, type = "info", duration = 3000) => {
    const id = Date.now();
    setToasts((prev) => [...prev, { id, message, type }]);

    setTimeout(() => {
      setToasts((prev) => prev.filter((t) => t.id !== id));
    }, duration);

    return id;
  }, []);

  const removeToast = useCallback((id) => {
    setToasts((prev) => prev.filter((t) => t.id !== id));
  }, []);

  return (
    <ToastContext.Provider value={{ addToast, removeToast }}>
      {children}
      {createPortal(
        <div className="toast-container">
          {toasts.map((toast) => (
            <div key={toast.id} className={`toast toast-${toast.type}`}>
              <span>{toast.message}</span>
              <button onClick={() => removeToast(toast.id)}>×</button>
            </div>
          ))}
        </div>,
        document.body
      )}
    </ToastContext.Provider>
  );
}

function useToast() {
  const context = useContext(ToastContext);
  if (!context) throw new Error("useToast must be within ToastProvider");
  return context;
}

// Usage
function App() {
  return (
    <ToastProvider>
      <NotificationButton />
    </ToastProvider>
  );
}

function NotificationButton() {
  const { addToast } = useToast();

  return (
    <div>
      <button onClick={() => addToast("Success!", "success")}>
        Show Success
      </button>
      <button onClick={() => addToast("Error!", "error")}>Show Error</button>
    </div>
  );
}
```

### 4. Dropdown Menu

```jsx
import { createPortal } from "react-dom";
import { useState, useRef, useEffect } from "react";

function Dropdown({ trigger, children }) {
  const [isOpen, setIsOpen] = useState(false);
  const [position, setPosition] = useState({ top: 0, left: 0 });
  const triggerRef = useRef(null);
  const menuRef = useRef(null);

  useEffect(() => {
    if (isOpen && triggerRef.current) {
      const rect = triggerRef.current.getBoundingClientRect();
      setPosition({
        top: rect.bottom + 4,
        left: rect.left,
      });
    }
  }, [isOpen]);

  useEffect(() => {
    const handleClickOutside = (e) => {
      if (
        menuRef.current &&
        !menuRef.current.contains(e.target) &&
        !triggerRef.current.contains(e.target)
      ) {
        setIsOpen(false);
      }
    };

    if (isOpen) {
      document.addEventListener("mousedown", handleClickOutside);
      return () =>
        document.removeEventListener("mousedown", handleClickOutside);
    }
  }, [isOpen]);

  return (
    <>
      <span ref={triggerRef} onClick={() => setIsOpen(!isOpen)}>
        {trigger}
      </span>

      {isOpen &&
        createPortal(
          <div
            ref={menuRef}
            className="dropdown-menu"
            style={{
              position: "fixed",
              top: position.top,
              left: position.left,
            }}
          >
            {children}
          </div>,
          document.body
        )}
    </>
  );
}

// Usage
function App() {
  return (
    <Dropdown trigger={<button>Menu</button>}>
      <ul>
        <li onClick={() => console.log("Edit")}>Edit</li>
        <li onClick={() => console.log("Delete")}>Delete</li>
        <li onClick={() => console.log("Share")}>Share</li>
      </ul>
    </Dropdown>
  );
}
```

### 5. Full-Screen Overlay

```jsx
function FullScreenPreview({ isOpen, onClose, children }) {
  useEffect(() => {
    if (isOpen) {
      document.body.style.overflow = "hidden";
    }
    return () => {
      document.body.style.overflow = "";
    };
  }, [isOpen]);

  if (!isOpen) return null;

  return createPortal(
    <div className="fullscreen-overlay">
      <button className="close-button" onClick={onClose}>
        ×
      </button>
      <div className="fullscreen-content">{children}</div>
    </div>,
    document.body
  );
}

// Usage
function ImageGallery() {
  const [selectedImage, setSelectedImage] = useState(null);

  return (
    <div>
      {images.map((img) => (
        <img
          key={img.id}
          src={img.thumbnail}
          onClick={() => setSelectedImage(img)}
        />
      ))}

      <FullScreenPreview
        isOpen={!!selectedImage}
        onClose={() => setSelectedImage(null)}
      >
        {selectedImage && <img src={selectedImage.full} alt="" />}
      </FullScreenPreview>
    </div>
  );
}
```

---

## 🧩 Patterns

### 1. Portal with Dynamic Container

```jsx
function DynamicPortal({ children }) {
  const [container, setContainer] = useState(null);

  useEffect(() => {
    const el = document.createElement("div");
    el.id = "dynamic-portal-root";
    document.body.appendChild(el);
    setContainer(el);

    return () => {
      document.body.removeChild(el);
    };
  }, []);

  if (!container) return null;

  return createPortal(children, container);
}
```

### 2. Portal Hook

```jsx
function usePortal(id = "portal-root") {
  const [container, setContainer] = useState(null);

  useEffect(() => {
    let el = document.getElementById(id);

    if (!el) {
      el = document.createElement("div");
      el.id = id;
      document.body.appendChild(el);
    }

    setContainer(el);
  }, [id]);

  return container;
}

// Usage
function Modal({ children }) {
  const container = usePortal("modal-root");

  if (!container) return null;

  return createPortal(<div className="modal">{children}</div>, container);
}
```

### 3. Nested Portals with Z-Index Management

```jsx
const PortalContext = createContext(0);

function PortalLayer({ children }) {
  const parentZIndex = useContext(PortalContext);
  const zIndex = parentZIndex + 100;

  return (
    <PortalContext.Provider value={zIndex}>
      {createPortal(
        <div style={{ position: "relative", zIndex }}>{children}</div>,
        document.body
      )}
    </PortalContext.Provider>
  );
}
```

---

## ⚠️ Common Mistakes

### 1. Forgetting to Create Target Element

```jsx
// ❌ Error if #modal-root doesn't exist
createPortal(content, document.getElementById("modal-root"));

// ✅ Check existence or create dynamically
const container = document.getElementById("modal-root") || document.body;
createPortal(content, container);
```

### 2. Not Handling Cleanup

```jsx
// ❌ Body overflow stays hidden after unmount
function Modal({ isOpen }) {
  if (isOpen) {
    document.body.style.overflow = "hidden";
  }
}

// ✅ Clean up in useEffect
function Modal({ isOpen }) {
  useEffect(() => {
    if (isOpen) {
      document.body.style.overflow = "hidden";
      return () => {
        document.body.style.overflow = "";
      };
    }
  }, [isOpen]);
}
```

### 3. Breaking Focus Management

```jsx
// ✅ Restore focus after portal closes
function Modal({ isOpen, onClose }) {
  const previousFocusRef = useRef(null);

  useEffect(() => {
    if (isOpen) {
      previousFocusRef.current = document.activeElement;
    }
    return () => {
      previousFocusRef.current?.focus();
    };
  }, [isOpen]);
}
```

---

## 🔧 Best Practices

### 1. Add ARIA Attributes

```jsx
<div
  role="dialog"
  aria-modal="true"
  aria-labelledby="modal-title"
  aria-describedby="modal-description"
>
```

### 2. Handle Escape Key

```jsx
useEffect(() => {
  const handleEscape = (e) => {
    if (e.key === "Escape") onClose();
  };
  document.addEventListener("keydown", handleEscape);
  return () => document.removeEventListener("keydown", handleEscape);
}, [onClose]);
```

### 3. Prevent Background Scroll

```jsx
useEffect(() => {
  document.body.style.overflow = "hidden";
  return () => {
    document.body.style.overflow = "";
  };
}, []);
```

---

## 🏋️ Practice Exercises

### Exercise 1: Confirmation Dialog

Build a confirmation dialog portal with "Confirm" and "Cancel" buttons.

### Exercise 2: Context Menu

Create a right-click context menu that appears via portal.

### Exercise 3: Image Lightbox

Build a lightbox gallery with keyboard navigation.

---

## ❓ Interview Questions

**Q1: What are React Portals and when would you use them?**

> Portals render children outside the parent DOM hierarchy while keeping them in the React tree. Use them for modals, tooltips, dropdowns, or anything that needs to visually "break out" of its parent's CSS constraints.

**Q2: Do events still bubble through portals?**

> Yes! Even though the DOM structure is different, events bubble through the React component tree. A click inside a portal will bubble to React ancestors.

**Q3: Does Context work through portals?**

> Yes, Context propagates through the React tree regardless of where the portal renders in the DOM.

**Q4: What's the difference between portal and regular rendering?**

> Regular rendering puts elements as children of the parent DOM node. Portals render to a different DOM node while maintaining the React parent-child relationship.

**Q5: How do you handle accessibility with portals?**

> Add appropriate ARIA attributes, manage focus (trap focus in modals, restore on close), handle keyboard events (Escape to close), and prevent background interaction.

---

## 🔑 Key Takeaways

> ### 💡 Summary
>
> - **Portals** render outside parent DOM but stay in React tree
> - **Events bubble** through React tree, not DOM tree
> - **Context works** through portals
> - **Use for** modals, tooltips, dropdowns, overlays
> - **Handle cleanup** - overflow, focus, event listeners
> - **Add accessibility** - ARIA, keyboard, focus management

---

**Next:** [11 - Code Splitting & Lazy Loading](./11_Code_Splitting_Lazy_Loading.md) - Learn about optimizing bundle size!
