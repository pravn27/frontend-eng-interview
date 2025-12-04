# 16. Styling in React

> Making your components beautiful - CSS approaches for React applications.

---

## 🎯 Styling Options Overview

React doesn't mandate a styling approach. You have multiple options:

| Approach              | Scope     | Dynamic | Setup               |
| --------------------- | --------- | ------- | ------------------- |
| **Regular CSS**       | Global    | Limited | None                |
| **CSS Modules**       | Component | Limited | Built-in (Vite/CRA) |
| **Inline Styles**     | Element   | Full    | None                |
| **Tailwind CSS**      | Utility   | Full    | Config needed       |
| **Styled Components** | Component | Full    | npm install         |
| **CSS-in-JS**         | Component | Full    | Library dependent   |

---

## 📚 Core Concepts

### 1. Regular CSS Files

The simplest approach - import CSS files directly:

```css
/* Button.css */
.button {
  padding: 10px 20px;
  border-radius: 4px;
  font-size: 16px;
  cursor: pointer;
}

.button-primary {
  background-color: #007bff;
  color: white;
  border: none;
}

.button-primary:hover {
  background-color: #0056b3;
}

.button-secondary {
  background-color: white;
  color: #333;
  border: 1px solid #ccc;
}
```

```jsx
// Button.jsx
import "./Button.css";

function Button({ variant = "primary", children, onClick }) {
  return (
    <button className={`button button-${variant}`} onClick={onClick}>
      {children}
    </button>
  );
}
```

**Pros:**

- ✅ Familiar syntax
- ✅ No setup required
- ✅ Full CSS features (media queries, pseudo-elements)

**Cons:**

- ❌ Global scope - naming conflicts possible
- ❌ No dynamic styling based on props/state

---

### 2. CSS Modules

CSS Modules automatically scope class names to the component:

```css
/* Button.module.css */
.button {
  padding: 10px 20px;
  border-radius: 4px;
}

.primary {
  background-color: #007bff;
  color: white;
}

.secondary {
  background-color: white;
  color: #333;
}

.large {
  font-size: 18px;
  padding: 15px 30px;
}
```

```jsx
// Button.jsx
import styles from "./Button.module.css";

function Button({ variant = "primary", size, children }) {
  // styles.button becomes something like "Button_button_x7yz"
  const className = `${styles.button} ${styles[variant]} ${
    size ? styles[size] : ""
  }`;

  return <button className={className}>{children}</button>;
}

// Or use template literals
function Button({ variant, size, children }) {
  return (
    <button
      className={[styles.button, styles[variant], size && styles[size]]
        .filter(Boolean)
        .join(" ")}
    >
      {children}
    </button>
  );
}
```

**Pros:**

- ✅ Scoped by default - no naming conflicts
- ✅ Full CSS features
- ✅ Built into Vite/CRA

**Cons:**

- ❌ Dynamic styling requires multiple classes
- ❌ Slightly more verbose

---

### 3. Inline Styles

JavaScript objects as styles - fully dynamic:

```jsx
function Card({ isHighlighted, padding = 16 }) {
  // Style object
  const cardStyle = {
    backgroundColor: isHighlighted ? "#fffde7" : "#ffffff",
    padding: `${padding}px`,
    borderRadius: "8px",
    boxShadow: "0 2px 4px rgba(0,0,0,0.1)",
    border: isHighlighted ? "2px solid #ffc107" : "1px solid #e0e0e0",
    transition: "all 0.2s ease",
  };

  // Conditional styles
  const titleStyle = {
    margin: 0,
    fontSize: "1.25rem",
    color: isHighlighted ? "#f57f17" : "#333",
  };

  return (
    <div style={cardStyle}>
      <h2 style={titleStyle}>Card Title</h2>
      <p style={{ color: "#666", lineHeight: 1.5 }}>Card content goes here.</p>
    </div>
  );
}
```

**Note:** CSS property names use camelCase:

- `background-color` → `backgroundColor`
- `font-size` → `fontSize`
- `border-radius` → `borderRadius`

**Pros:**

- ✅ Fully dynamic based on props/state
- ✅ No external files
- ✅ Type-safe with TypeScript

**Cons:**

- ❌ No pseudo-classes (:hover, :focus)
- ❌ No media queries
- ❌ No keyframes/animations
- ❌ Can be verbose

---

### 4. Tailwind CSS

Utility-first CSS framework - extremely popular in 2025:

```bash
# Installation
npm install -D tailwindcss postcss autoprefixer
npx tailwindcss init -p
```

```js
// tailwind.config.js
export default {
  content: ["./index.html", "./src/**/*.{js,ts,jsx,tsx}"],
  theme: {
    extend: {},
  },
  plugins: [],
};
```

```jsx
// Component with Tailwind
function Card({ isHighlighted }) {
  return (
    <div
      className={`
      p-4 rounded-lg shadow-md
      ${
        isHighlighted
          ? "bg-yellow-50 border-2 border-yellow-400"
          : "bg-white border border-gray-200"
      }
      transition-all duration-200 hover:shadow-lg
    `}
    >
      <h2
        className={`
        text-xl font-semibold mb-2
        ${isHighlighted ? "text-yellow-700" : "text-gray-800"}
      `}
      >
        Card Title
      </h2>
      <p className="text-gray-600 leading-relaxed">Card content goes here.</p>
    </div>
  );
}

// Button component with variants
function Button({ variant = "primary", size = "md", children, ...props }) {
  const baseClasses =
    "font-medium rounded transition-colors focus:outline-none focus:ring-2";

  const variants = {
    primary: "bg-blue-500 text-white hover:bg-blue-600 focus:ring-blue-300",
    secondary:
      "bg-gray-200 text-gray-800 hover:bg-gray-300 focus:ring-gray-300",
    danger: "bg-red-500 text-white hover:bg-red-600 focus:ring-red-300",
    outline: "border-2 border-blue-500 text-blue-500 hover:bg-blue-50",
  };

  const sizes = {
    sm: "px-3 py-1.5 text-sm",
    md: "px-4 py-2",
    lg: "px-6 py-3 text-lg",
  };

  return (
    <button
      className={`${baseClasses} ${variants[variant]} ${sizes[size]}`}
      {...props}
    >
      {children}
    </button>
  );
}
```

**With clsx/classnames library:**

```jsx
import clsx from "clsx";

function Button({ variant, size, disabled, className, children }) {
  return (
    <button
      className={clsx(
        "font-medium rounded transition-colors",
        {
          "bg-blue-500 text-white": variant === "primary",
          "bg-gray-200 text-gray-800": variant === "secondary",
          "px-3 py-1.5 text-sm": size === "sm",
          "px-4 py-2": size === "md",
          "opacity-50 cursor-not-allowed": disabled,
        },
        className // Allow additional classes
      )}
    >
      {children}
    </button>
  );
}
```

**Pros:**

- ✅ Rapid development
- ✅ Consistent design system
- ✅ Small production bundle (unused classes removed)
- ✅ Responsive design utilities
- ✅ No naming conflicts

**Cons:**

- ❌ Learning curve for utility names
- ❌ Long class strings in JSX
- ❌ Requires build setup

---

### 5. Styled Components (CSS-in-JS)

Write actual CSS in JavaScript:

```bash
npm install styled-components
```

```jsx
import styled from "styled-components";

// Basic styled component
const Button = styled.button`
  padding: 10px 20px;
  border-radius: 4px;
  font-size: 16px;
  cursor: pointer;
  transition: all 0.2s ease;

  &:hover {
    transform: translateY(-1px);
  }

  &:active {
    transform: translateY(0);
  }
`;

// With props
const StyledButton = styled.button`
  padding: ${(props) => (props.size === "large" ? "15px 30px" : "10px 20px")};
  font-size: ${(props) => (props.size === "large" ? "18px" : "16px")};
  background-color: ${(props) =>
    props.variant === "primary" ? "#007bff" : "#f8f9fa"};
  color: ${(props) => (props.variant === "primary" ? "white" : "#333")};
  border: ${(props) =>
    props.variant === "primary" ? "none" : "1px solid #dee2e6"};
  border-radius: 4px;
  cursor: pointer;

  &:hover {
    background-color: ${(props) =>
      props.variant === "primary" ? "#0056b3" : "#e9ecef"};
  }

  &:disabled {
    opacity: 0.5;
    cursor: not-allowed;
  }
`;

// Extending styles
const PrimaryButton = styled(Button)`
  background-color: #007bff;
  color: white;
  border: none;
`;

const SecondaryButton = styled(Button)`
  background-color: transparent;
  color: #007bff;
  border: 2px solid #007bff;
`;

// With theme
const Card = styled.div`
  background: ${(props) => props.theme.colors.background};
  padding: ${(props) => props.theme.spacing.md};
  border-radius: ${(props) => props.theme.borderRadius.md};
  box-shadow: ${(props) => props.theme.shadows.sm};
`;

// Usage
function App() {
  return (
    <div>
      <StyledButton variant="primary" size="large">
        Primary Large
      </StyledButton>
      <StyledButton variant="secondary">Secondary</StyledButton>
      <PrimaryButton>Primary</PrimaryButton>
      <SecondaryButton>Secondary</SecondaryButton>
    </div>
  );
}
```

**Theming with Styled Components:**

```jsx
import { ThemeProvider } from "styled-components";

const theme = {
  colors: {
    primary: "#007bff",
    secondary: "#6c757d",
    success: "#28a745",
    danger: "#dc3545",
    background: "#ffffff",
    text: "#333333",
  },
  spacing: {
    xs: "4px",
    sm: "8px",
    md: "16px",
    lg: "24px",
    xl: "32px",
  },
  borderRadius: {
    sm: "4px",
    md: "8px",
    lg: "16px",
    full: "9999px",
  },
};

function App() {
  return (
    <ThemeProvider theme={theme}>
      <MyApp />
    </ThemeProvider>
  );
}
```

**Pros:**

- ✅ Full CSS features (pseudo-classes, media queries)
- ✅ Dynamic styling with props
- ✅ Scoped by default
- ✅ Theming support
- ✅ TypeScript support

**Cons:**

- ❌ Runtime overhead
- ❌ Additional dependency
- ❌ Different syntax to learn

---

### 6. CSS Variables for Theming

Works with any CSS approach:

```css
/* index.css */
:root {
  --color-primary: #007bff;
  --color-secondary: #6c757d;
  --color-background: #ffffff;
  --color-text: #333333;
  --spacing-unit: 8px;
  --border-radius: 4px;
  --shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
}

[data-theme="dark"] {
  --color-primary: #4dabf7;
  --color-background: #1a1a2e;
  --color-text: #ffffff;
}

.button {
  background-color: var(--color-primary);
  padding: calc(var(--spacing-unit) * 2);
  border-radius: var(--border-radius);
}
```

```jsx
function ThemeToggle() {
  const [isDark, setIsDark] = useState(false);

  const toggleTheme = () => {
    setIsDark(!isDark);
    document.documentElement.setAttribute(
      "data-theme",
      isDark ? "light" : "dark"
    );
  };

  return (
    <button onClick={toggleTheme}>{isDark ? "☀️ Light" : "🌙 Dark"}</button>
  );
}
```

---

## 💻 Demo Code

### Complete Component Library Example

```jsx
// styles/theme.js
export const theme = {
  colors: {
    primary: '#3b82f6',
    primaryHover: '#2563eb',
    secondary: '#64748b',
    success: '#22c55e',
    danger: '#ef4444',
    warning: '#f59e0b',
    background: '#ffffff',
    surface: '#f8fafc',
    text: '#1e293b',
    textMuted: '#64748b',
    border: '#e2e8f0'
  },
  spacing: {
    xs: '0.25rem',
    sm: '0.5rem',
    md: '1rem',
    lg: '1.5rem',
    xl: '2rem'
  },
  borderRadius: {
    sm: '0.25rem',
    md: '0.375rem',
    lg: '0.5rem',
    full: '9999px'
  },
  fontSize: {
    xs: '0.75rem',
    sm: '0.875rem',
    base: '1rem',
    lg: '1.125rem',
    xl: '1.25rem'
  }
};

// components/Button.jsx
import styles from './Button.module.css';
import clsx from 'clsx';

export function Button({
  variant = 'primary',
  size = 'md',
  fullWidth = false,
  disabled = false,
  loading = false,
  leftIcon,
  rightIcon,
  children,
  className,
  ...props
}) {
  return (
    <button
      className={clsx(
        styles.button,
        styles[variant],
        styles[size],
        fullWidth && styles.fullWidth,
        loading && styles.loading,
        className
      )}
      disabled={disabled || loading}
      {...props}
    >
      {loading && <span className={styles.spinner} />}
      {!loading && leftIcon && <span className={styles.icon}>{leftIcon}</span>}
      <span>{children}</span>
      {!loading && rightIcon && <span className={styles.icon}>{rightIcon}</span>}
    </button>
  );
}

// components/Button.module.css
.button {
  display: inline-flex;
  align-items: center;
  justify-content: center;
  gap: 0.5rem;
  font-weight: 500;
  border-radius: 0.375rem;
  transition: all 0.15s ease;
  cursor: pointer;
  border: none;
}

.button:focus {
  outline: none;
  box-shadow: 0 0 0 3px rgba(59, 130, 246, 0.3);
}

.button:disabled {
  opacity: 0.5;
  cursor: not-allowed;
}

/* Variants */
.primary {
  background-color: #3b82f6;
  color: white;
}

.primary:hover:not(:disabled) {
  background-color: #2563eb;
}

.secondary {
  background-color: #f1f5f9;
  color: #475569;
}

.secondary:hover:not(:disabled) {
  background-color: #e2e8f0;
}

.outline {
  background-color: transparent;
  border: 2px solid #3b82f6;
  color: #3b82f6;
}

.outline:hover:not(:disabled) {
  background-color: #eff6ff;
}

.danger {
  background-color: #ef4444;
  color: white;
}

.danger:hover:not(:disabled) {
  background-color: #dc2626;
}

/* Sizes */
.sm {
  padding: 0.375rem 0.75rem;
  font-size: 0.875rem;
}

.md {
  padding: 0.5rem 1rem;
  font-size: 1rem;
}

.lg {
  padding: 0.75rem 1.5rem;
  font-size: 1.125rem;
}

.fullWidth {
  width: 100%;
}

/* Loading */
.loading {
  position: relative;
  color: transparent;
}

.spinner {
  position: absolute;
  width: 1em;
  height: 1em;
  border: 2px solid currentColor;
  border-right-color: transparent;
  border-radius: 50%;
  animation: spin 0.6s linear infinite;
}

@keyframes spin {
  to { transform: rotate(360deg); }
}

// components/Card.jsx
import styles from './Card.module.css';
import clsx from 'clsx';

export function Card({
  variant = 'elevated',
  padding = 'md',
  children,
  className,
  ...props
}) {
  return (
    <div
      className={clsx(
        styles.card,
        styles[variant],
        styles[`padding-${padding}`],
        className
      )}
      {...props}
    >
      {children}
    </div>
  );
}

export function CardHeader({ children, className }) {
  return (
    <div className={clsx(styles.header, className)}>
      {children}
    </div>
  );
}

export function CardBody({ children, className }) {
  return (
    <div className={clsx(styles.body, className)}>
      {children}
    </div>
  );
}

export function CardFooter({ children, className }) {
  return (
    <div className={clsx(styles.footer, className)}>
      {children}
    </div>
  );
}

// Usage example
function App() {
  return (
    <div className="p-8 space-y-4">
      <h1>Component Library Demo</h1>

      {/* Buttons */}
      <div className="flex gap-4 flex-wrap">
        <Button variant="primary">Primary</Button>
        <Button variant="secondary">Secondary</Button>
        <Button variant="outline">Outline</Button>
        <Button variant="danger">Danger</Button>
        <Button variant="primary" loading>Loading</Button>
        <Button variant="primary" disabled>Disabled</Button>
      </div>

      {/* Sizes */}
      <div className="flex gap-4 items-center">
        <Button size="sm">Small</Button>
        <Button size="md">Medium</Button>
        <Button size="lg">Large</Button>
      </div>

      {/* Cards */}
      <Card variant="elevated">
        <CardHeader>
          <h2>Card Title</h2>
        </CardHeader>
        <CardBody>
          <p>Card content goes here.</p>
        </CardBody>
        <CardFooter>
          <Button variant="primary" size="sm">Action</Button>
        </CardFooter>
      </Card>
    </div>
  );
}
```

---

## ✅ Best Practices

### 1. Be Consistent

Pick one primary approach and stick with it across your project.

### 2. Use CSS Variables for Theming

```css
:root {
  --primary: #007bff;
}

.button {
  background: var(--primary);
}
```

### 3. Component-Level Styles

Keep styles close to components:

```
components/
  Button/
    Button.jsx
    Button.module.css
```

### 4. Use a Utility Library

For conditional classes:

```jsx
import clsx from 'clsx';

className={clsx('base', { active: isActive })}
```

---

## ❌ Common Mistakes

### 1. Global CSS Conflicts

```css
/* ❌ Too generic - may conflict */
.button {
}
.card {
}

/* ✅ Use CSS Modules or prefixes */
.MyComponent_button {
}
```

### 2. Inline Style Limitations

```jsx
// ❌ Can't do hover states inline
<button style={{ ':hover': { bg: 'blue' } }}>  // Won't work!

// ✅ Use CSS or styled-components for pseudo-classes
```

### 3. Not Using Design Tokens

```jsx
// ❌ Magic numbers everywhere
<div style={{ padding: '17px', color: '#3a7bc8' }}>

// ✅ Use consistent tokens
<div style={{ padding: theme.spacing.md, color: theme.colors.primary }}>
```

---

## 🎤 Interview Q&A

**Q1: What styling approaches are available in React?**

> Regular CSS, CSS Modules, inline styles, CSS-in-JS (styled-components, Emotion), and utility frameworks (Tailwind). Each has tradeoffs between scope, dynamic capability, and setup complexity.

**Q2: What are CSS Modules and why use them?**

> CSS Modules automatically scope class names to the component, preventing naming conflicts. They compile to unique class names like `Button_primary_x7yz`.

**Q3: When would you use inline styles?**

> For highly dynamic styles that depend on props or state. But remember: no pseudo-classes or media queries with inline styles.

**Q4: What's the benefit of Tailwind CSS?**

> Rapid development with utility classes, consistent design system, and small production bundle (unused classes are purged). Trade-off is longer class strings in JSX.

**Q5: How do you implement theming in React?**

> CSS Variables for simple theming, Context API with styled-components/Emotion for full JavaScript theming, or Tailwind's dark mode classes.

---

## 📎 References

- [React Docs - Styling](https://react.dev/learn#adding-styles)
- [CSS Modules](https://github.com/css-modules/css-modules)
- [Tailwind CSS](https://tailwindcss.com/)
- [Styled Components](https://styled-components.com/)
- [Emotion](https://emotion.sh/)

---

**Previous Topic**: [← 15. React Router Basics](./15_React_Router_Basics.md)

---

## 🎉 Congratulations!

You've completed the **ReactJS Beginner Curriculum**!

### What's Next?

1. **Build Projects** - Apply what you learned
2. **Intermediate Topics** - useEffect, Custom Hooks, Context API
3. **Advanced Topics** - Performance optimization, Testing, State Management

### Suggested Projects

- **Todo App** - Forms, Lists, State
- **Weather App** - API calls, Conditional rendering
- **E-commerce** - Router, Cart state, Components
- **Blog** - CRUD operations, Routing, Forms

**Keep building! 🚀**
