# 09 - Refs & ForwardRef

## 📖 What is ForwardRef?

`forwardRef` lets a component expose a DOM node or internal ref to its parent component. It "forwards" the ref from parent to child.

```jsx
import { forwardRef, useRef } from "react";

// Component that forwards ref to its input
const CustomInput = forwardRef(function CustomInput(props, ref) {
  return <input ref={ref} {...props} />;
});

// Parent can access the input DOM node
function Form() {
  const inputRef = useRef(null);

  const focusInput = () => {
    inputRef.current.focus();
  };

  return (
    <>
      <CustomInput ref={inputRef} placeholder="Enter text" />
      <button onClick={focusInput}>Focus Input</button>
    </>
  );
}
```

---

## 🎯 Why ForwardRef?

### The Problem: Refs Don't Pass Through

```jsx
// ❌ This doesn't work - ref doesn't reach the input
function CustomInput(props) {
  return <input {...props} />; // ref is lost!
}

function Form() {
  const inputRef = useRef(null);
  return <CustomInput ref={inputRef} />; // ref is not passed to input
}
```

### The Solution: ForwardRef

```jsx
// ✅ forwardRef passes ref to the input
const CustomInput = forwardRef(function CustomInput(props, ref) {
  return <input ref={ref} {...props} />;
});

function Form() {
  const inputRef = useRef(null);
  return <CustomInput ref={inputRef} />; // Works!
}
```

---

## 📚 Core Concepts

### 1. Basic ForwardRef Syntax

```jsx
import { forwardRef } from "react";

// Method 1: Named function
const MyComponent = forwardRef(function MyComponent(props, ref) {
  return <div ref={ref}>{props.children}</div>;
});

// Method 2: Arrow function
const MyComponent = forwardRef((props, ref) => {
  return <div ref={ref}>{props.children}</div>;
});

// With TypeScript
const MyComponent = forwardRef<HTMLDivElement, Props>((props, ref) => {
  return <div ref={ref}>{props.children}</div>;
});
```

### 2. useImperativeHandle

Customize what the ref exposes to the parent:

```jsx
import { forwardRef, useRef, useImperativeHandle } from "react";

const CustomInput = forwardRef(function CustomInput(props, ref) {
  const inputRef = useRef(null);

  // Expose custom methods instead of the DOM node
  useImperativeHandle(ref, () => ({
    focus: () => inputRef.current.focus(),
    clear: () => (inputRef.current.value = ""),
    getValue: () => inputRef.current.value,
    scrollIntoView: () => inputRef.current.scrollIntoView(),
  }));

  return <input ref={inputRef} {...props} />;
});

// Parent uses custom API
function Form() {
  const inputRef = useRef(null);

  const handleSubmit = () => {
    console.log(inputRef.current.getValue());
    inputRef.current.clear();
  };

  return (
    <>
      <CustomInput ref={inputRef} />
      <button onClick={() => inputRef.current.focus()}>Focus</button>
      <button onClick={handleSubmit}>Submit</button>
    </>
  );
}
```

---

## 💻 Practical Examples

### 1. Reusable Input Component

```jsx
const Input = forwardRef(function Input(
  { label, error, className, ...props },
  ref
) {
  const id = useId();

  return (
    <div className={`input-wrapper ${className || ""}`}>
      {label && <label htmlFor={id}>{label}</label>}
      <input
        ref={ref}
        id={id}
        className={error ? "input-error" : ""}
        {...props}
      />
      {error && <span className="error-message">{error}</span>}
    </div>
  );
});

// Usage
function LoginForm() {
  const emailRef = useRef(null);
  const passwordRef = useRef(null);

  useEffect(() => {
    emailRef.current.focus();
  }, []);

  const handleEmailKeyDown = (e) => {
    if (e.key === "Enter") {
      passwordRef.current.focus();
    }
  };

  return (
    <form>
      <Input
        ref={emailRef}
        label="Email"
        type="email"
        onKeyDown={handleEmailKeyDown}
      />
      <Input ref={passwordRef} label="Password" type="password" />
    </form>
  );
}
```

### 2. Modal with Focus Trap

```jsx
const Modal = forwardRef(function Modal({ isOpen, onClose, children }, ref) {
  const modalRef = useRef(null);
  const previousFocusRef = useRef(null);

  useImperativeHandle(ref, () => ({
    focusFirst: () => {
      const focusable = modalRef.current.querySelectorAll(
        'button, [href], input, select, textarea, [tabindex]:not([tabindex="-1"])'
      );
      focusable[0]?.focus();
    },
  }));

  useEffect(() => {
    if (isOpen) {
      previousFocusRef.current = document.activeElement;
      modalRef.current?.querySelector("button, input")?.focus();
    } else {
      previousFocusRef.current?.focus();
    }
  }, [isOpen]);

  if (!isOpen) return null;

  return (
    <div className="modal-overlay" onClick={onClose}>
      <div
        ref={modalRef}
        className="modal"
        onClick={(e) => e.stopPropagation()}
        role="dialog"
        aria-modal="true"
      >
        {children}
        <button onClick={onClose}>Close</button>
      </div>
    </div>
  );
});
```

### 3. Video Player Component

```jsx
const VideoPlayer = forwardRef(function VideoPlayer(
  { src, poster, onTimeUpdate },
  ref
) {
  const videoRef = useRef(null);

  useImperativeHandle(ref, () => ({
    play: () => videoRef.current.play(),
    pause: () => videoRef.current.pause(),
    togglePlay: () => {
      if (videoRef.current.paused) {
        videoRef.current.play();
      } else {
        videoRef.current.pause();
      }
    },
    seek: (time) => {
      videoRef.current.currentTime = time;
    },
    getCurrentTime: () => videoRef.current.currentTime,
    getDuration: () => videoRef.current.duration,
    setVolume: (volume) => {
      videoRef.current.volume = Math.max(0, Math.min(1, volume));
    },
    toggleMute: () => {
      videoRef.current.muted = !videoRef.current.muted;
    },
  }));

  return (
    <video
      ref={videoRef}
      src={src}
      poster={poster}
      onTimeUpdate={onTimeUpdate}
      className="video-player"
    />
  );
});

// Usage
function VideoPage() {
  const playerRef = useRef(null);

  return (
    <div>
      <VideoPlayer ref={playerRef} src="/video.mp4" />
      <div className="controls">
        <button onClick={() => playerRef.current.togglePlay()}>
          Play/Pause
        </button>
        <button onClick={() => playerRef.current.seek(0)}>Restart</button>
        <input
          type="range"
          min="0"
          max="1"
          step="0.1"
          onChange={(e) => playerRef.current.setVolume(e.target.value)}
        />
      </div>
    </div>
  );
}
```

### 4. Form with Field Access

```jsx
const FormField = forwardRef(function FormField(
  { name, label, type = "text", validate },
  ref
) {
  const [value, setValue] = useState("");
  const [error, setError] = useState("");
  const inputRef = useRef(null);

  useImperativeHandle(ref, () => ({
    getValue: () => value,
    setValue: (v) => setValue(v),
    validate: () => {
      const err = validate?.(value) || "";
      setError(err);
      return !err;
    },
    focus: () => inputRef.current.focus(),
    clear: () => {
      setValue("");
      setError("");
    },
    hasError: () => !!error,
  }));

  return (
    <div className="form-field">
      <label>{label}</label>
      <input
        ref={inputRef}
        type={type}
        value={value}
        onChange={(e) => setValue(e.target.value)}
      />
      {error && <span className="error">{error}</span>}
    </div>
  );
});

function RegistrationForm() {
  const usernameRef = useRef(null);
  const emailRef = useRef(null);
  const passwordRef = useRef(null);

  const handleSubmit = (e) => {
    e.preventDefault();

    const isUsernameValid = usernameRef.current.validate();
    const isEmailValid = emailRef.current.validate();
    const isPasswordValid = passwordRef.current.validate();

    if (isUsernameValid && isEmailValid && isPasswordValid) {
      const data = {
        username: usernameRef.current.getValue(),
        email: emailRef.current.getValue(),
        password: passwordRef.current.getValue(),
      };
      console.log("Submit:", data);
    } else {
      // Focus first field with error
      [usernameRef, emailRef, passwordRef]
        .find((ref) => ref.current.hasError())
        ?.current.focus();
    }
  };

  return (
    <form onSubmit={handleSubmit}>
      <FormField
        ref={usernameRef}
        name="username"
        label="Username"
        validate={(v) => (v.length < 3 ? "Min 3 characters" : "")}
      />
      <FormField
        ref={emailRef}
        name="email"
        label="Email"
        type="email"
        validate={(v) => (!v.includes("@") ? "Invalid email" : "")}
      />
      <FormField
        ref={passwordRef}
        name="password"
        label="Password"
        type="password"
        validate={(v) => (v.length < 8 ? "Min 8 characters" : "")}
      />
      <button type="submit">Register</button>
    </form>
  );
}
```

### 5. Scroll Container

```jsx
const ScrollContainer = forwardRef(function ScrollContainer(
  { children, className },
  ref
) {
  const containerRef = useRef(null);

  useImperativeHandle(ref, () => ({
    scrollToTop: () => {
      containerRef.current.scrollTo({ top: 0, behavior: "smooth" });
    },
    scrollToBottom: () => {
      containerRef.current.scrollTo({
        top: containerRef.current.scrollHeight,
        behavior: "smooth",
      });
    },
    scrollToElement: (selector) => {
      const element = containerRef.current.querySelector(selector);
      element?.scrollIntoView({ behavior: "smooth" });
    },
    getScrollPosition: () => ({
      top: containerRef.current.scrollTop,
      left: containerRef.current.scrollLeft,
    }),
    isAtBottom: () => {
      const { scrollTop, scrollHeight, clientHeight } = containerRef.current;
      return scrollTop + clientHeight >= scrollHeight - 10;
    },
  }));

  return (
    <div ref={containerRef} className={`scroll-container ${className || ""}`}>
      {children}
    </div>
  );
});
```

---

## 🧩 Patterns

### 1. Merging Refs

When a component needs both a local ref and a forwarded ref:

```jsx
import { forwardRef, useRef, useImperativeHandle } from "react";

const Input = forwardRef(function Input(props, forwardedRef) {
  const localRef = useRef(null);

  // Merge local ref with forwarded ref
  useImperativeHandle(forwardedRef, () => localRef.current);

  // Now you can use localRef internally
  useEffect(() => {
    console.log("Input mounted:", localRef.current);
  }, []);

  return <input ref={localRef} {...props} />;
});
```

### 2. Callback Ref with ForwardRef

```jsx
const MeasuredInput = forwardRef(function MeasuredInput(props, ref) {
  const [width, setWidth] = useState(0);

  const measureRef = useCallback((node) => {
    if (node !== null) {
      setWidth(node.getBoundingClientRect().width);
    }
  }, []);

  // Combine callback ref with forwarded ref
  const combinedRef = useCallback(
    (node) => {
      measureRef(node);
      if (ref) {
        if (typeof ref === "function") {
          ref(node);
        } else {
          ref.current = node;
        }
      }
    },
    [ref, measureRef]
  );

  return (
    <div>
      <input ref={combinedRef} {...props} />
      <span>Width: {width}px</span>
    </div>
  );
});
```

### 3. Generic Forwarded Component

```jsx
// Utility to forward ref to any element
function forwardRefWithAs(Component) {
  return forwardRef(function ForwardedComponent({ as: Element = "div", ...props }, ref) {
    return <Element ref={ref} {...props} />;
  });
}

// Usage
const Box = forwardRefWithAs(({ children, ...props }) => (
  <div {...props}>{children}</div>
));

<Box as="section" ref={sectionRef}>Content</Box>
<Box as="article" ref={articleRef}>Content</Box>
```

---

## ⚠️ Common Mistakes

### 1. Forgetting to Forward Ref

```jsx
// ❌ ref is not forwarded
function Button({ children, ...props }) {
  return <button {...props}>{children}</button>;
}

// ✅ ref is forwarded
const Button = forwardRef(function Button({ children, ...props }, ref) {
  return (
    <button ref={ref} {...props}>
      {children}
    </button>
  );
});
```

### 2. Exposing Too Much

```jsx
// ❌ Exposing entire DOM node
useImperativeHandle(ref, () => inputRef.current);

// ✅ Expose only what's needed
useImperativeHandle(ref, () => ({
  focus: () => inputRef.current.focus(),
  getValue: () => inputRef.current.value,
}));
```

### 3. Incorrect Dependency Array

```jsx
// ❌ Missing dependencies
useImperativeHandle(ref, () => ({
  getCount: () => count, // count might be stale
})); // Missing dependency array!

// ✅ Include dependencies
useImperativeHandle(
  ref,
  () => ({
    getCount: () => count,
  }),
  [count]
);
```

---

## 🔧 Best Practices

### 1. Limit Exposed API

```jsx
// ✅ Expose minimal, intentional API
useImperativeHandle(ref, () => ({
  focus: () => inputRef.current.focus(),
  scrollIntoView: () => inputRef.current.scrollIntoView(),
}));

// ❌ Don't expose everything
useImperativeHandle(ref, () => inputRef.current);
```

### 2. Document the Ref API

```jsx
/**
 * @ref {Object} ref
 * @ref.focus {Function} Focus the input
 * @ref.clear {Function} Clear the input value
 * @ref.getValue {Function} Get current value
 */
const CustomInput = forwardRef(function CustomInput(props, ref) {
  // ...
});
```

### 3. Consider Alternatives

```jsx
// Sometimes callbacks are better than imperative handles
// ❌ Imperative
const inputRef = useRef();
<CustomInput ref={inputRef} />
<button onClick={() => inputRef.current.clear()}>Clear</button>

// ✅ Declarative with callback
<CustomInput onClear={() => setValue("")} />
```

---

## 🏋️ Practice Exercises

### Exercise 1: Autocomplete Input

Build an autocomplete input that exposes `open()`, `close()`, and `selectItem(index)` methods.

### Exercise 2: Carousel Component

Create a carousel that exposes `next()`, `prev()`, `goTo(index)` methods.

### Exercise 3: Accordion

Build an accordion that exposes `openAll()`, `closeAll()`, `toggle(index)` methods.

---

## ❓ Interview Questions

**Q1: What is forwardRef and when would you use it?**

> forwardRef creates a component that can pass a ref to a child element. Use it when building reusable components where parents need direct access to DOM elements or imperative methods.

**Q2: What is useImperativeHandle?**

> useImperativeHandle customizes what a ref exposes to parent components. Instead of exposing the DOM node, you can expose a custom API with specific methods.

**Q3: Why can't regular function components receive refs?**

> Refs are a special prop that React handles differently. Function components don't have instances, so there's nothing for the ref to attach to. forwardRef explicitly tells React to pass the ref as a second argument.

**Q4: When should you use useImperativeHandle vs exposing the DOM node?**

> Use useImperativeHandle when you want to limit or customize the API, abstract implementation details, or provide semantic methods. Expose the DOM node when parents need full DOM access.

**Q5: How do you combine multiple refs?**

> Use useImperativeHandle to forward the local ref, or create a combined callback ref that updates both refs when the element mounts.

---

## 🔑 Key Takeaways

> ### 💡 Summary
>
> - **forwardRef** passes ref from parent to child element
> - **useImperativeHandle** customizes exposed ref API
> - **Limit exposure** - only expose what's needed
> - **Include dependencies** in useImperativeHandle
> - **Consider alternatives** - callbacks may be simpler
> - **Document the API** for component consumers

---

**Next:** [10 - React Portals](./10_React_Portals.md) - Learn about rendering outside the DOM hierarchy!
