# 15 - Compound Components

## 📖 What are Compound Components?

Compound Components is a pattern where multiple components work together to form a complete UI, sharing implicit state. They provide a **flexible and expressive API** while keeping related components together.

```jsx
// Compound Components Pattern
<Select value={value} onChange={setValue}>
  <Select.Trigger>Select a fruit</Select.Trigger>
  <Select.Menu>
    <Select.Option value="apple">Apple</Select.Option>
    <Select.Option value="banana">Banana</Select.Option>
    <Select.Option value="cherry">Cherry</Select.Option>
  </Select.Menu>
</Select>
```

---

## 🎯 Why Compound Components?

### Traditional Props vs Compound Components

```jsx
// ❌ Props-heavy API
<Select
  value={value}
  onChange={setValue}
  placeholder="Select a fruit"
  options={[
    { value: "apple", label: "Apple", disabled: false },
    { value: "banana", label: "Banana", disabled: true },
    { value: "cherry", label: "Cherry", disabled: false },
  ]}
  renderOption={(option) => <span>{option.label}</span>}
  menuPosition="bottom"
  closeOnSelect={true}
/>

// ✅ Compound Components - flexible and readable
<Select value={value} onChange={setValue}>
  <Select.Trigger placeholder="Select a fruit" />
  <Select.Menu position="bottom">
    <Select.Option value="apple">🍎 Apple</Select.Option>
    <Select.Option value="banana" disabled>🍌 Banana</Select.Option>
    <Select.Option value="cherry">🍒 Cherry</Select.Option>
  </Select.Menu>
</Select>
```

---

## 📚 Implementation Patterns

### 1. Using Context

```jsx
import { createContext, useContext, useState } from "react";

// Create context
const AccordionContext = createContext(null);

// Main component
function Accordion({ children, allowMultiple = false }) {
  const [openItems, setOpenItems] = useState([]);

  const toggle = (id) => {
    setOpenItems((prev) => {
      if (prev.includes(id)) {
        return prev.filter((i) => i !== id);
      }
      return allowMultiple ? [...prev, id] : [id];
    });
  };

  const isOpen = (id) => openItems.includes(id);

  return (
    <AccordionContext.Provider value={{ toggle, isOpen }}>
      <div className="accordion">{children}</div>
    </AccordionContext.Provider>
  );
}

// Sub-components
function AccordionItem({ children, id }) {
  return (
    <div className="accordion-item" data-id={id}>
      {children}
    </div>
  );
}

function AccordionHeader({ children, id }) {
  const { toggle, isOpen } = useContext(AccordionContext);

  return (
    <button
      className="accordion-header"
      onClick={() => toggle(id)}
      aria-expanded={isOpen(id)}
    >
      {children}
      <span>{isOpen(id) ? "−" : "+"}</span>
    </button>
  );
}

function AccordionPanel({ children, id }) {
  const { isOpen } = useContext(AccordionContext);

  if (!isOpen(id)) return null;

  return <div className="accordion-panel">{children}</div>;
}

// Attach sub-components
Accordion.Item = AccordionItem;
Accordion.Header = AccordionHeader;
Accordion.Panel = AccordionPanel;

// Usage
<Accordion allowMultiple>
  <Accordion.Item id="1">
    <Accordion.Header id="1">Section 1</Accordion.Header>
    <Accordion.Panel id="1">Content 1</Accordion.Panel>
  </Accordion.Item>
  <Accordion.Item id="2">
    <Accordion.Header id="2">Section 2</Accordion.Header>
    <Accordion.Panel id="2">Content 2</Accordion.Panel>
  </Accordion.Item>
</Accordion>;
```

### 2. Using React.Children and cloneElement

```jsx
function Tabs({ children, defaultIndex = 0 }) {
  const [activeIndex, setActiveIndex] = useState(defaultIndex);

  return (
    <div className="tabs">
      {React.Children.map(children, (child, index) => {
        if (!React.isValidElement(child)) return null;

        return React.cloneElement(child, {
          isActive: index === activeIndex,
          onSelect: () => setActiveIndex(index),
          index,
        });
      })}
    </div>
  );
}

function Tab({ children, isActive, onSelect }) {
  return (
    <button className={`tab ${isActive ? "active" : ""}`} onClick={onSelect}>
      {children}
    </button>
  );
}

Tabs.Tab = Tab;

// Usage
<Tabs defaultIndex={0}>
  <Tabs.Tab>Home</Tabs.Tab>
  <Tabs.Tab>Profile</Tabs.Tab>
  <Tabs.Tab>Settings</Tabs.Tab>
</Tabs>;
```

---

## 💻 Practical Examples

### 1. Dropdown/Select Component

```jsx
const SelectContext = createContext(null);

function Select({ value, onChange, children }) {
  const [isOpen, setIsOpen] = useState(false);
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

  const select = (newValue) => {
    onChange(newValue);
    setIsOpen(false);
  };

  return (
    <SelectContext.Provider value={{ value, select, isOpen, setIsOpen }}>
      <div ref={containerRef} className="select">
        {children}
      </div>
    </SelectContext.Provider>
  );
}

function SelectTrigger({ placeholder, children }) {
  const { value, isOpen, setIsOpen } = useContext(SelectContext);

  return (
    <button
      className="select-trigger"
      onClick={() => setIsOpen(!isOpen)}
      aria-expanded={isOpen}
    >
      {children || value || placeholder}
      <span className="arrow">{isOpen ? "▲" : "▼"}</span>
    </button>
  );
}

function SelectMenu({ children }) {
  const { isOpen } = useContext(SelectContext);

  if (!isOpen) return null;

  return <ul className="select-menu">{children}</ul>;
}

function SelectOption({ value: optionValue, disabled, children }) {
  const { value, select } = useContext(SelectContext);
  const isSelected = value === optionValue;

  return (
    <li
      className={`select-option ${isSelected ? "selected" : ""} ${
        disabled ? "disabled" : ""
      }`}
      onClick={() => !disabled && select(optionValue)}
    >
      {children}
      {isSelected && <span>✓</span>}
    </li>
  );
}

Select.Trigger = SelectTrigger;
Select.Menu = SelectMenu;
Select.Option = SelectOption;

// Usage
function App() {
  const [fruit, setFruit] = useState("");

  return (
    <Select value={fruit} onChange={setFruit}>
      <Select.Trigger placeholder="Choose a fruit" />
      <Select.Menu>
        <Select.Option value="apple">🍎 Apple</Select.Option>
        <Select.Option value="banana">🍌 Banana</Select.Option>
        <Select.Option value="cherry" disabled>
          🍒 Cherry
        </Select.Option>
      </Select.Menu>
    </Select>
  );
}
```

### 2. Modal Component

```jsx
const ModalContext = createContext(null);

function Modal({ isOpen, onClose, children }) {
  useEffect(() => {
    if (isOpen) {
      document.body.style.overflow = "hidden";
    }
    return () => {
      document.body.style.overflow = "";
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
    <ModalContext.Provider value={{ onClose }}>
      <div className="modal-overlay" onClick={onClose}>
        <div className="modal-content" onClick={(e) => e.stopPropagation()}>
          {children}
        </div>
      </div>
    </ModalContext.Provider>,
    document.body
  );
}

function ModalHeader({ children }) {
  const { onClose } = useContext(ModalContext);

  return (
    <div className="modal-header">
      <h2>{children}</h2>
      <button onClick={onClose} aria-label="Close">
        ×
      </button>
    </div>
  );
}

function ModalBody({ children }) {
  return <div className="modal-body">{children}</div>;
}

function ModalFooter({ children }) {
  return <div className="modal-footer">{children}</div>;
}

Modal.Header = ModalHeader;
Modal.Body = ModalBody;
Modal.Footer = ModalFooter;

// Usage
<Modal isOpen={isOpen} onClose={close}>
  <Modal.Header>Confirm Action</Modal.Header>
  <Modal.Body>
    <p>Are you sure you want to proceed?</p>
  </Modal.Body>
  <Modal.Footer>
    <button onClick={close}>Cancel</button>
    <button onClick={confirm}>Confirm</button>
  </Modal.Footer>
</Modal>;
```

### 3. Card Component

```jsx
function Card({ children, className }) {
  return <div className={`card ${className || ""}`}>{children}</div>;
}

function CardHeader({ children, className }) {
  return <div className={`card-header ${className || ""}`}>{children}</div>;
}

function CardBody({ children, className }) {
  return <div className={`card-body ${className || ""}`}>{children}</div>;
}

function CardFooter({ children, className }) {
  return <div className={`card-footer ${className || ""}`}>{children}</div>;
}

function CardImage({ src, alt, className }) {
  return (
    <img className={`card-image ${className || ""}`} src={src} alt={alt} />
  );
}

Card.Header = CardHeader;
Card.Body = CardBody;
Card.Footer = CardFooter;
Card.Image = CardImage;

// Usage
<Card>
  <Card.Image src="/photo.jpg" alt="Product" />
  <Card.Header>Product Title</Card.Header>
  <Card.Body>
    <p>Product description goes here.</p>
    <p>Price: $99.99</p>
  </Card.Body>
  <Card.Footer>
    <button>Add to Cart</button>
  </Card.Footer>
</Card>;
```

### 4. Menu/Dropdown Menu

```jsx
const MenuContext = createContext(null);

function Menu({ children }) {
  const [isOpen, setIsOpen] = useState(false);
  const menuRef = useRef(null);

  useEffect(() => {
    const handleClickOutside = (e) => {
      if (menuRef.current && !menuRef.current.contains(e.target)) {
        setIsOpen(false);
      }
    };
    document.addEventListener("mousedown", handleClickOutside);
    return () => document.removeEventListener("mousedown", handleClickOutside);
  }, []);

  return (
    <MenuContext.Provider value={{ isOpen, setIsOpen }}>
      <div ref={menuRef} className="menu">
        {children}
      </div>
    </MenuContext.Provider>
  );
}

function MenuButton({ children }) {
  const { isOpen, setIsOpen } = useContext(MenuContext);

  return (
    <button
      className="menu-button"
      onClick={() => setIsOpen(!isOpen)}
      aria-expanded={isOpen}
    >
      {children}
    </button>
  );
}

function MenuList({ children }) {
  const { isOpen } = useContext(MenuContext);

  if (!isOpen) return null;

  return <ul className="menu-list">{children}</ul>;
}

function MenuItem({ onClick, children, disabled }) {
  const { setIsOpen } = useContext(MenuContext);

  const handleClick = () => {
    if (!disabled) {
      onClick?.();
      setIsOpen(false);
    }
  };

  return (
    <li
      className={`menu-item ${disabled ? "disabled" : ""}`}
      onClick={handleClick}
    >
      {children}
    </li>
  );
}

function MenuDivider() {
  return <li className="menu-divider" />;
}

Menu.Button = MenuButton;
Menu.List = MenuList;
Menu.Item = MenuItem;
Menu.Divider = MenuDivider;

// Usage
<Menu>
  <Menu.Button>Actions ▼</Menu.Button>
  <Menu.List>
    <Menu.Item onClick={() => console.log("Edit")}>Edit</Menu.Item>
    <Menu.Item onClick={() => console.log("Duplicate")}>Duplicate</Menu.Item>
    <Menu.Divider />
    <Menu.Item onClick={() => console.log("Delete")}>Delete</Menu.Item>
  </Menu.List>
</Menu>;
```

---

## 🧩 Advanced Patterns

### 1. Flexible Children Validation

```jsx
function Tabs({ children }) {
  const tabList = [];
  const tabPanels = [];

  React.Children.forEach(children, (child) => {
    if (child.type === TabList) {
      tabList.push(child);
    } else if (child.type === TabPanels) {
      tabPanels.push(child);
    }
  });

  if (tabList.length !== 1 || tabPanels.length !== 1) {
    console.warn("Tabs expects exactly one TabList and one TabPanels");
  }

  // ...
}
```

### 2. Controlled vs Uncontrolled

```jsx
function Accordion({ value, onChange, defaultValue, children }) {
  // Support both controlled and uncontrolled
  const [internalValue, setInternalValue] = useState(defaultValue);

  const isControlled = value !== undefined;
  const openItems = isControlled ? value : internalValue;

  const setOpenItems = (newValue) => {
    if (!isControlled) {
      setInternalValue(newValue);
    }
    onChange?.(newValue);
  };

  // ...
}
```

---

## ⚠️ Common Mistakes

### 1. Not Providing Context

```jsx
// ❌ Using sub-component without parent
<Accordion.Item id="1">...</Accordion.Item> // Error: Context is null

// ✅ Always use within parent
<Accordion>
  <Accordion.Item id="1">...</Accordion.Item>
</Accordion>
```

### 2. Forgetting to Forward Props

```jsx
// ❌ Props not forwarded
function MenuItem({ children }) {
  return <li>{children}</li>;
}

// ✅ Forward remaining props
function MenuItem({ children, className, ...props }) {
  return (
    <li className={className} {...props}>
      {children}
    </li>
  );
}
```

---

## 🔧 Best Practices

### 1. Helpful Error Messages

```jsx
function useAccordionContext() {
  const context = useContext(AccordionContext);
  if (!context) {
    throw new Error(
      "Accordion components must be used within an <Accordion> provider"
    );
  }
  return context;
}
```

### 2. TypeScript Support

```typescript
interface AccordionContextValue {
  openItems: string[];
  toggle: (id: string) => void;
  isOpen: (id: string) => boolean;
}

const AccordionContext = createContext<AccordionContextValue | null>(null);
```

---

## 🏋️ Practice Exercises

### Exercise 1: Tabs Component

Build a compound Tabs component with Tab and TabPanel sub-components.

### Exercise 2: Toast System

Create a Toast compound component with Toast.Container, Toast.Item.

### Exercise 3: Form Builder

Build a Form compound component with Form.Field, Form.Label, Form.Input.

---

## ❓ Interview Questions

**Q1: What is the Compound Components pattern?**

> Compound Components is a pattern where multiple components work together, sharing implicit state through Context. It provides a flexible API where the parent manages state and children access it implicitly.

**Q2: Context vs cloneElement for compound components?**

> Context is more flexible and works regardless of component nesting. cloneElement only works with direct children and breaks with wrapper components. Context is the preferred modern approach.

**Q3: Benefits of compound components?**

> Flexible composition, better separation of concerns, more readable JSX, allows customization of individual parts, and hides implementation complexity while exposing a clean API.

**Q4: How do you prevent misuse?**

> Throw helpful errors when sub-components are used outside their parent, validate children types, and use TypeScript for compile-time checks.

**Q5: When should you use compound components?**

> When building complex UI components with multiple parts that need to share state (dropdowns, modals, accordions, tabs), especially in component libraries.

---

## 🔑 Key Takeaways

> ### 💡 Summary
>
> - **Compound Components** share state implicitly via Context
> - **Flexible API** - consumers compose sub-components as needed
> - **Attach sub-components** to parent (`Parent.Child`)
> - **Use Context** over cloneElement for flexibility
> - **Provide error messages** when used incorrectly
> - **Support both controlled and uncontrolled** usage

---

**Next:** [16 - Performance Optimization](./16_Performance_Optimization.md) - Learn about optimizing React performance!
