# 05. Components

> Building blocks of React applications - reusable, composable UI pieces.

---

## 🎯 What is a Component?

A **Component** is an independent, reusable piece of UI. Think of components as custom HTML elements that you define. They let you split the UI into independent, reusable pieces and think about each piece in isolation.

```jsx
// A simple component
function Greeting() {
  return <h1>Hello, World!</h1>;
}

// Using the component
function App() {
  return (
    <div>
      <Greeting />
      <Greeting />
      <Greeting />
    </div>
  );
}
```

---

## 🤔 Why Components?

### Without Components (The Problem)

```jsx
// Everything in one place - hard to maintain
function App() {
  return (
    <div>
      <header>
        <nav>
          <a href="/">Home</a>
          <a href="/about">About</a>
          <a href="/contact">Contact</a>
        </nav>
        <h1>My Website</h1>
      </header>
      <main>
        <article>
          <h2>Article 1</h2>
          <p>Content...</p>
          <button>Read More</button>
        </article>
        <article>
          <h2>Article 2</h2>
          <p>Content...</p>
          <button>Read More</button>
        </article>
      </main>
      <footer>
        <p>© 2025</p>
      </footer>
    </div>
  );
}
```

### With Components (The Solution)

```jsx
// Clean, organized, reusable
function App() {
  return (
    <div>
      <Header />
      <main>
        <ArticleCard title="Article 1" content="Content..." />
        <ArticleCard title="Article 2" content="Content..." />
      </main>
      <Footer />
    </div>
  );
}
```

**Benefits:**

- ✅ **Reusability** - Write once, use everywhere
- ✅ **Maintainability** - Easy to update and fix
- ✅ **Testability** - Test components in isolation
- ✅ **Collaboration** - Team members work on different components
- ✅ **Readability** - Clear structure and intent

---

## 📚 Core Concepts

### 1. Functional Components (Modern Standard)

Function components are the **recommended approach in 2025**. They're simpler, cleaner, and fully featured with Hooks.

```jsx
// Basic function component
function Welcome(props) {
  return <h1>Hello, {props.name}</h1>;
}

// Arrow function syntax (also common)
const Welcome = (props) => {
  return <h1>Hello, {props.name}</h1>;
};

// Arrow function with implicit return
const Welcome = (props) => <h1>Hello, {props.name}</h1>;

// With destructured props (preferred)
const Welcome = ({ name }) => <h1>Hello, {name}</h1>;

// With multiple props
function UserCard({ name, email, avatar }) {
  return (
    <div className="user-card">
      <img src={avatar} alt={name} />
      <h2>{name}</h2>
      <p>{email}</p>
    </div>
  );
}
```

### 2. Class Components (Legacy)

Class components are the older way of writing React components. You'll encounter them in legacy codebases.

```jsx
import React, { Component } from "react";

class Welcome extends Component {
  render() {
    return <h1>Hello, {this.props.name}</h1>;
  }
}

// With state
class Counter extends Component {
  constructor(props) {
    super(props);
    this.state = { count: 0 };
  }

  increment = () => {
    this.setState({ count: this.state.count + 1 });
  };

  render() {
    return (
      <div>
        <p>Count: {this.state.count}</p>
        <button onClick={this.increment}>Add</button>
      </div>
    );
  }
}
```

### Functional vs Class Components Comparison

| Feature          | Functional Component | Class Component   |
| ---------------- | -------------------- | ----------------- |
| **Syntax**       | Function             | ES6 Class         |
| **State**        | useState Hook        | this.state        |
| **Lifecycle**    | useEffect Hook       | Lifecycle methods |
| **this keyword** | Not needed           | Required          |
| **Code length**  | Shorter              | Longer            |
| **Performance**  | Slightly better      | Slightly slower   |
| **2025 Status**  | ✅ Recommended       | ⚠️ Legacy         |

```jsx
// Same component - Functional vs Class

// Functional (Modern - Preferred)
function Counter() {
  const [count, setCount] = useState(0);

  return <button onClick={() => setCount(count + 1)}>Count: {count}</button>;
}

// Class (Legacy)
class Counter extends Component {
  state = { count: 0 };

  render() {
    return (
      <button onClick={() => this.setState({ count: this.state.count + 1 })}>
        Count: {this.state.count}
      </button>
    );
  }
}
```

### 3. Component Composition

Building complex UIs by combining simpler components.

```jsx
// Small, focused components
function Avatar({ src, alt }) {
  return <img className="avatar" src={src} alt={alt} />;
}

function UserName({ name }) {
  return <span className="username">{name}</span>;
}

function FollowButton({ userId, isFollowing }) {
  return (
    <button className={isFollowing ? "following" : ""}>
      {isFollowing ? "Following" : "Follow"}
    </button>
  );
}

// Composed together
function UserCard({ user }) {
  return (
    <div className="user-card">
      <Avatar src={user.avatar} alt={user.name} />
      <div className="user-info">
        <UserName name={user.name} />
        <FollowButton userId={user.id} isFollowing={user.isFollowing} />
      </div>
    </div>
  );
}

// Higher level composition
function UserList({ users }) {
  return (
    <div className="user-list">
      {users.map((user) => (
        <UserCard key={user.id} user={user} />
      ))}
    </div>
  );
}
```

```
Component Composition Tree:

                    App
                     │
        ┌────────────┼────────────┐
        │            │            │
     Header       UserList     Footer
        │            │
       Nav       UserCard[]
                     │
            ┌────────┼────────┐
            │        │        │
         Avatar  UserName  FollowButton
```

### 4. Component Naming Conventions

```jsx
// ✅ PascalCase for component names
function UserProfile() {}
function NavigationBar() {}
function ShoppingCartItem() {}

// ❌ Don't use camelCase or lowercase
function userProfile() {} // Wrong
function navigationbar() {} // Wrong

// ✅ File names match component names
// UserProfile.jsx contains UserProfile component
// NavigationBar.jsx contains NavigationBar component

// ✅ Descriptive names that explain purpose
function ProductCard() {}
function SearchInput() {}
function ModalOverlay() {}

// ❌ Avoid generic names
function Item() {} // Too generic
function Component() {} // Meaningless
function Stuff() {} // Unclear
```

### 5. Component Organization

```
src/
├── components/
│   ├── common/              # Reusable across features
│   │   ├── Button.jsx
│   │   ├── Input.jsx
│   │   ├── Modal.jsx
│   │   └── index.js
│   │
│   ├── layout/              # Layout components
│   │   ├── Header.jsx
│   │   ├── Footer.jsx
│   │   ├── Sidebar.jsx
│   │   └── index.js
│   │
│   └── features/            # Feature-specific
│       ├── auth/
│       │   ├── LoginForm.jsx
│       │   ├── SignupForm.jsx
│       │   └── index.js
│       │
│       └── products/
│           ├── ProductCard.jsx
│           ├── ProductList.jsx
│           └── index.js
│
├── pages/                   # Page components
│   ├── HomePage.jsx
│   ├── ProductPage.jsx
│   └── ProfilePage.jsx
│
└── App.jsx
```

---

## 💻 Demo Code

### Complete Component Example

```jsx
import { useState } from "react";

// ========================================
// REUSABLE UI COMPONENTS
// ========================================

function Button({ children, variant = "primary", onClick, disabled }) {
  const baseStyles = "px-4 py-2 rounded font-medium transition-colors";
  const variants = {
    primary: "bg-blue-500 text-white hover:bg-blue-600",
    secondary: "bg-gray-200 text-gray-800 hover:bg-gray-300",
    danger: "bg-red-500 text-white hover:bg-red-600",
  };

  return (
    <button
      className={`${baseStyles} ${variants[variant]}`}
      onClick={onClick}
      disabled={disabled}
    >
      {children}
    </button>
  );
}

function Card({ children, title }) {
  return (
    <div className="bg-white rounded-lg shadow-md p-6">
      {title && <h3 className="text-xl font-bold mb-4">{title}</h3>}
      {children}
    </div>
  );
}

function Badge({ children, color = "gray" }) {
  const colors = {
    gray: "bg-gray-100 text-gray-800",
    green: "bg-green-100 text-green-800",
    red: "bg-red-100 text-red-800",
    blue: "bg-blue-100 text-blue-800",
  };

  return (
    <span className={`px-2 py-1 rounded-full text-sm ${colors[color]}`}>
      {children}
    </span>
  );
}

// ========================================
// FEATURE COMPONENTS
// ========================================

function ProductImage({ src, alt }) {
  return (
    <img
      src={src}
      alt={alt}
      className="w-full h-48 object-cover rounded-t-lg"
    />
  );
}

function ProductInfo({ name, description, price, originalPrice }) {
  const hasDiscount = originalPrice && originalPrice > price;

  return (
    <div className="p-4">
      <h2 className="text-lg font-semibold">{name}</h2>
      <p className="text-gray-600 text-sm mt-1">{description}</p>
      <div className="mt-2 flex items-center gap-2">
        <span className="text-xl font-bold">${price}</span>
        {hasDiscount && (
          <span className="text-gray-400 line-through">${originalPrice}</span>
        )}
      </div>
    </div>
  );
}

function ProductActions({ onAddToCart, onWishlist, inStock }) {
  return (
    <div className="p-4 pt-0 flex gap-2">
      <Button
        onClick={onAddToCart}
        disabled={!inStock}
        variant={inStock ? "primary" : "secondary"}
      >
        {inStock ? "Add to Cart" : "Out of Stock"}
      </Button>
      <Button variant="secondary" onClick={onWishlist}>
        ♡
      </Button>
    </div>
  );
}

// ========================================
// COMPOSED COMPONENT
// ========================================

function ProductCard({ product }) {
  const [isWishlisted, setIsWishlisted] = useState(false);

  const handleAddToCart = () => {
    console.log(`Added ${product.name} to cart`);
  };

  const handleWishlist = () => {
    setIsWishlisted(!isWishlisted);
  };

  return (
    <div className="bg-white rounded-lg shadow-md overflow-hidden relative">
      {/* Badges */}
      <div className="absolute top-2 left-2 flex gap-1">
        {product.isNew && <Badge color="blue">New</Badge>}
        {product.discount && <Badge color="red">{product.discount}% OFF</Badge>}
      </div>

      <ProductImage src={product.image} alt={product.name} />
      <ProductInfo
        name={product.name}
        description={product.description}
        price={product.price}
        originalPrice={product.originalPrice}
      />
      <ProductActions
        onAddToCart={handleAddToCart}
        onWishlist={handleWishlist}
        inStock={product.inStock}
      />
    </div>
  );
}

// ========================================
// PAGE COMPONENT
// ========================================

function ProductGrid({ products }) {
  return (
    <div className="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-6">
      {products.map((product) => (
        <ProductCard key={product.id} product={product} />
      ))}
    </div>
  );
}

// Usage
function App() {
  const products = [
    {
      id: 1,
      name: "Wireless Headphones",
      description: "Premium noise-cancelling headphones",
      price: 199,
      originalPrice: 249,
      discount: 20,
      image: "/headphones.jpg",
      inStock: true,
      isNew: true,
    },
    {
      id: 2,
      name: "Smart Watch",
      description: "Fitness tracking smartwatch",
      price: 299,
      image: "/watch.jpg",
      inStock: true,
      isNew: false,
    },
    {
      id: 3,
      name: "Bluetooth Speaker",
      description: "Portable waterproof speaker",
      price: 79,
      image: "/speaker.jpg",
      inStock: false,
      isNew: false,
    },
  ];

  return (
    <div className="container mx-auto p-6">
      <h1 className="text-3xl font-bold mb-6">Our Products</h1>
      <ProductGrid products={products} />
    </div>
  );
}

export default App;
```

---

## ✅ Best Practices

### 1. Single Responsibility Principle

```jsx
// ❌ Bad - Component does too much
function UserDashboard({ userId }) {
  // Fetches user, displays profile, shows orders, handles settings...
  // 500+ lines of code
}

// ✅ Good - Split into focused components
function UserDashboard({ userId }) {
  return (
    <div>
      <UserProfile userId={userId} />
      <UserOrders userId={userId} />
      <UserSettings userId={userId} />
    </div>
  );
}
```

### 2. Props Destructuring

```jsx
// ❌ Verbose
function Welcome(props) {
  return (
    <h1>
      Hello, {props.name}! You are {props.age} years old.
    </h1>
  );
}

// ✅ Clean
function Welcome({ name, age }) {
  return (
    <h1>
      Hello, {name}! You are {age} years old.
    </h1>
  );
}

// ✅ With defaults
function Welcome({ name = "Guest", age = 0 }) {
  return (
    <h1>
      Hello, {name}! You are {age} years old.
    </h1>
  );
}
```

### 3. Keep Components Pure

```jsx
// ❌ Bad - Side effect in render
function BadComponent({ userId }) {
  document.title = `User ${userId}`; // Side effect!

  return <div>User: {userId}</div>;
}

// ✅ Good - Use useEffect for side effects
function GoodComponent({ userId }) {
  useEffect(() => {
    document.title = `User ${userId}`;
  }, [userId]);

  return <div>User: {userId}</div>;
}
```

### 4. Extract Complex Logic

```jsx
// ❌ Logic mixed with JSX
function OrderSummary({ items }) {
  return (
    <div>
      <p>
        Subtotal: ${items.reduce((sum, item) => sum + item.price * item.qty, 0)}
      </p>
      <p>
        Tax: $
        {items.reduce((sum, item) => sum + item.price * item.qty, 0) * 0.1}
      </p>
      <p>
        Total: $
        {items.reduce((sum, item) => sum + item.price * item.qty, 0) * 1.1}
      </p>
    </div>
  );
}

// ✅ Good - Extract calculations
function OrderSummary({ items }) {
  const subtotal = items.reduce((sum, item) => sum + item.price * item.qty, 0);
  const tax = subtotal * 0.1;
  const total = subtotal + tax;

  return (
    <div>
      <p>Subtotal: ${subtotal.toFixed(2)}</p>
      <p>Tax: ${tax.toFixed(2)}</p>
      <p>Total: ${total.toFixed(2)}</p>
    </div>
  );
}
```

---

## ❌ Common Mistakes

### 1. Defining Components Inside Components

```jsx
// ❌ Bad - Creates new component every render
function Parent() {
  function Child() {
    // Don't do this!
    return <div>Child</div>;
  }

  return <Child />;
}

// ✅ Good - Define outside
function Child() {
  return <div>Child</div>;
}

function Parent() {
  return <Child />;
}
```

### 2. Not Using Keys in Lists

```jsx
// ❌ Bad - Missing key
function List({ items }) {
  return items.map((item) => <li>{item.name}</li>);
}

// ✅ Good - Unique key
function List({ items }) {
  return items.map((item) => <li key={item.id}>{item.name}</li>);
}
```

### 3. Modifying Props

```jsx
// ❌ Bad - Props are read-only
function BadComponent(props) {
  props.name = "Modified"; // Never do this!
  return <div>{props.name}</div>;
}

// ✅ Good - Use state for modifications
function GoodComponent({ name: initialName }) {
  const [name, setName] = useState(initialName);
  return <div>{name}</div>;
}
```

---

## 🏋️ Practice Exercises

### Exercise 1: Create a Card Component

Create a reusable `Card` component that accepts:

- `title` - Card header
- `children` - Card content
- `footer` - Optional footer content

### Exercise 2: Build a Comment Thread

Build these components:

- `Comment` - Single comment with author, text, timestamp
- `CommentList` - List of comments
- `CommentForm` - Form to add new comment

### Exercise 3: Refactor This Code

Break this monolithic component into smaller, reusable pieces:

```jsx
function Dashboard() {
  return (
    <div>
      <header>
        <img src="logo.png" alt="Logo" />
        <nav>
          <a href="/">Home</a>
          <a href="/profile">Profile</a>
        </nav>
        <button>Logout</button>
      </header>
      <main>
        <h1>Welcome back, John!</h1>
        <div>
          <h2>Recent Activity</h2>
          <ul>
            <li>Logged in - 2 hours ago</li>
            <li>Updated profile - 1 day ago</li>
          </ul>
        </div>
        <div>
          <h2>Quick Stats</h2>
          <p>Posts: 42</p>
          <p>Followers: 1,234</p>
        </div>
      </main>
      <footer>
        <p>© 2025 MyApp</p>
      </footer>
    </div>
  );
}
```

---

## 🎤 Interview Q&A

**Q1: What is a component in React?**

> A component is an independent, reusable piece of UI that encapsulates structure (JSX), logic (JavaScript), and optionally styling. Components can accept inputs (props) and manage their own state.

**Q2: What's the difference between functional and class components?**

> Functional components are JavaScript functions that return JSX, while class components are ES6 classes that extend React.Component. Functional components use Hooks for state and lifecycle, while class components use this.state and lifecycle methods. Functional components are the modern standard as of 2025.

**Q3: Why is component composition important?**

> Component composition lets you build complex UIs from simple pieces. It promotes reusability, maintainability, and separation of concerns. Each component has a single responsibility, making code easier to understand, test, and modify.

**Q4: What are the naming conventions for components?**

> Components should use PascalCase (UserProfile, not userProfile). This distinguishes them from regular HTML elements and is required for JSX to recognize custom components. File names should match component names.

**Q5: When should you create a new component?**

> Create a new component when: (1) UI is repeated multiple times, (2) A section of UI has its own distinct responsibility, (3) The current component is getting too large or complex, (4) You want to isolate a piece for testing, (5) Different team members need to work on different parts.

---

## 📎 References

- [React Docs - Your First Component](https://react.dev/learn/your-first-component)
- [React Docs - Importing and Exporting Components](https://react.dev/learn/importing-and-exporting-components)
- [React Docs - Thinking in React](https://react.dev/learn/thinking-in-react)
- [React Docs - Passing Props to a Component](https://react.dev/learn/passing-props-to-a-component)

---

**Previous Topic**: [← 04. Element vs Component vs Instance](./04_Element_vs_Component_vs_Instance.md)

**Next Topic**: [06. Keeping Components Pure →](./06_Keeping_Components_Pure.md)
