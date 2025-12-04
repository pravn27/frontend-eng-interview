# 03. JSX Syntax

> Understanding JSX - the syntax extension that makes React intuitive.

---

## 🎯 What is JSX?

**JSX (JavaScript XML)** is a syntax extension for JavaScript that looks like HTML but is actually JavaScript. It allows you to write UI code in a familiar, readable format.

```jsx
// This is JSX
const element = <h1>Hello, World!</h1>;

// JSX is transformed into this JavaScript
const element = React.createElement("h1", null, "Hello, World!");
```

---

## 🤔 Why Do We Need JSX?

### Without JSX (Pure JavaScript)

```javascript
// Creating UI without JSX - verbose and hard to read
const element = React.createElement(
  "div",
  { className: "container" },
  React.createElement("h1", null, "Welcome"),
  React.createElement("p", null, "This is a paragraph"),
  React.createElement("button", { onClick: handleClick }, "Click me")
);
```

### With JSX (Readable and Intuitive)

```jsx
// Same UI with JSX - clean and intuitive
const element = (
  <div className="container">
    <h1>Welcome</h1>
    <p>This is a paragraph</p>
    <button onClick={handleClick}>Click me</button>
  </div>
);
```

**Benefits of JSX:**

- ✅ Familiar HTML-like syntax
- ✅ Easier to visualize the UI structure
- ✅ Catches errors at compile time
- ✅ Prevents injection attacks (auto-escapes)
- ✅ Co-locates markup with logic

---

## 📚 Core Concepts

### 1. JSX is an Expression

JSX compiles to regular JavaScript, so you can:

- Assign it to variables
- Return it from functions
- Use it in conditions and loops

```jsx
// Assign to variable
const greeting = <h1>Hello!</h1>;

// Return from function
function getGreeting(user) {
  if (user) {
    return <h1>Hello, {user.name}!</h1>;
  }
  return <h1>Hello, Stranger!</h1>;
}

// Use in array
const items = [
  <li key="1">First</li>,
  <li key="2">Second</li>,
  <li key="3">Third</li>,
];
```

### 2. JSX vs HTML Differences

| HTML                 | JSX                        | Reason                            |
| -------------------- | -------------------------- | --------------------------------- |
| `class`              | `className`                | `class` is reserved in JavaScript |
| `for`                | `htmlFor`                  | `for` is reserved in JavaScript   |
| `onclick`            | `onClick`                  | camelCase convention              |
| `tabindex`           | `tabIndex`                 | camelCase convention              |
| `style="color: red"` | `style={{ color: 'red' }}` | Object, not string                |
| `<!-- comment -->`   | `{/* comment */}`          | JavaScript comment syntax         |

```jsx
// HTML
<div class="container" onclick="handleClick()" style="color: red;">
  <label for="name">Name:</label>
  <input type="text" tabindex="1" />
</div>

// JSX
<div className="container" onClick={handleClick} style={{ color: 'red' }}>
  <label htmlFor="name">Name:</label>
  <input type="text" tabIndex={1} />
</div>
```

### 3. Embedding Expressions with Curly Braces

Use `{}` to embed any JavaScript expression in JSX.

```jsx
const name = "John";
const age = 25;
const isLoggedIn = true;

function UserProfile() {
  return (
    <div>
      {/* Variables */}
      <h1>Hello, {name}!</h1>

      {/* Expressions */}
      <p>
        Age: {age} (Born in {2025 - age})
      </p>

      {/* Function calls */}
      <p>Name length: {name.length}</p>
      <p>Uppercase: {name.toUpperCase()}</p>

      {/* Ternary expressions */}
      <p>Status: {isLoggedIn ? "Logged In" : "Logged Out"}</p>

      {/* Logical operators */}
      {isLoggedIn && <button>Logout</button>}

      {/* Math */}
      <p>Random: {Math.random().toFixed(2)}</p>
    </div>
  );
}
```

### 4. JSX Attributes

```jsx
function AttributeExamples() {
  const imageUrl = "https://example.com/image.jpg";
  const inputId = "user-email";
  const customData = { userId: 123 };

  return (
    <div>
      {/* String literals */}
      <img src="static-image.jpg" alt="Static" />
      {/* JavaScript expressions */}
      <img src={imageUrl} alt="Dynamic" />
      {/* Template literals */}
      <img src={`${imageUrl}?size=large`} alt="Template" />
      {/* Dynamic attributes */}
      <label htmlFor={inputId}>Email:</label>
      <input id={inputId} type="email" />
      {/* Boolean attributes */}
      <input type="text" disabled /> {/* disabled={true} */}
      <input type="text" disabled={false} /> {/* not disabled */}
      {/* Spread attributes */}
      <input {...customData} />
      {/* data-* and aria-* (keep kebab-case) */}
      <div data-testid="user-card" aria-label="User information">
        Content
      </div>
    </div>
  );
}
```

### 5. JSX Children

```jsx
function ChildrenExamples() {
  const items = ["Apple", "Banana", "Cherry"];

  return (
    <div>
      {/* Text children */}
      <p>Simple text content</p>

      {/* Element children */}
      <div>
        <h1>Title</h1>
        <p>Paragraph</p>
      </div>

      {/* Mixed children */}
      <p>
        Hello, <strong>World</strong>!
      </p>

      {/* Expression children */}
      <ul>
        {items.map((item, index) => (
          <li key={index}>{item}</li>
        ))}
      </ul>

      {/* Component children */}
      <Container>
        <Header />
        <Content />
        <Footer />
      </Container>

      {/* Function as children (Render Props) */}
      <DataFetcher>{(data) => <div>{data}</div>}</DataFetcher>
    </div>
  );
}
```

### 6. Inline Styles

```jsx
function StyleExamples() {
  // Style object
  const cardStyle = {
    backgroundColor: "#f0f0f0",
    borderRadius: "8px",
    padding: "20px",
    boxShadow: "0 2px 4px rgba(0,0,0,0.1)",
  };

  // Dynamic styles
  const isActive = true;
  const dynamicStyle = {
    color: isActive ? "green" : "gray",
    fontWeight: isActive ? "bold" : "normal",
  };

  return (
    <div>
      {/* Inline style object */}
      <div style={{ color: "blue", fontSize: "16px" }}>Inline styled text</div>

      {/* Style variable */}
      <div style={cardStyle}>Card content</div>

      {/* Dynamic styles */}
      <span style={dynamicStyle}>{isActive ? "Active" : "Inactive"}</span>

      {/* Merged styles */}
      <div style={{ ...cardStyle, ...dynamicStyle }}>Merged styles</div>
    </div>
  );
}
```

**Note**: CSS property names use camelCase in JSX:

- `background-color` → `backgroundColor`
- `font-size` → `fontSize`
- `margin-top` → `marginTop`

### 7. Fragments

When you need to return multiple elements without a wrapper div:

```jsx
import { Fragment } from "react";

function FragmentExamples() {
  return (
    <>
      {/* Short syntax (preferred) */}
      <>
        <h1>Title</h1>
        <p>Paragraph</p>
      </>

      {/* Long syntax (needed for keys) */}
      <Fragment>
        <h1>Title</h1>
        <p>Paragraph</p>
      </Fragment>

      {/* With key (must use long syntax) */}
      {items.map((item) => (
        <Fragment key={item.id}>
          <dt>{item.term}</dt>
          <dd>{item.description}</dd>
        </Fragment>
      ))}
    </>
  );
}
```

### 8. Comments in JSX

```jsx
function CommentExamples() {
  return (
    <div>
      {/* This is a JSX comment */}
      <h1>Title</h1>

      {/* 
        Multi-line
        JSX comment 
      */}
      <p>Content</p>

      <input
        type="text"
        // This comment style works in multi-line JSX
        placeholder="Enter text"
      />
    </div>
  );
}
```

---

## 💻 Demo Code

### Complete JSX Example

```jsx
import { useState } from "react";

function ProductCard({ product }) {
  const [quantity, setQuantity] = useState(1);
  const [isFavorite, setIsFavorite] = useState(false);

  // Calculated values
  const totalPrice = product.price * quantity;
  const isOutOfStock = product.stock === 0;
  const discountedPrice = product.discount
    ? product.price * (1 - product.discount / 100)
    : null;

  // Styles
  const priceStyle = {
    color: discountedPrice ? "red" : "inherit",
    textDecoration: discountedPrice ? "line-through" : "none",
  };

  return (
    <article className="product-card">
      {/* Image with fallback */}
      <img
        src={product.image || "/placeholder.jpg"}
        alt={product.name}
        className="product-image"
      />

      {/* Badge - conditional rendering */}
      {product.discount && (
        <span className="discount-badge">{product.discount}% OFF</span>
      )}

      {/* Product info */}
      <div className="product-info">
        <h2>{product.name}</h2>
        <p className="description">{product.description}</p>

        {/* Price display */}
        <div className="price-container">
          <span style={priceStyle}>${product.price.toFixed(2)}</span>
          {discountedPrice && (
            <span className="discounted-price">
              ${discountedPrice.toFixed(2)}
            </span>
          )}
        </div>

        {/* Stock status */}
        <p className={`stock ${isOutOfStock ? "out-of-stock" : "in-stock"}`}>
          {isOutOfStock ? "Out of Stock" : `${product.stock} in stock`}
        </p>
      </div>

      {/* Actions */}
      <div className="product-actions">
        {/* Quantity selector */}
        <div className="quantity-selector">
          <button
            onClick={() => setQuantity((q) => Math.max(1, q - 1))}
            disabled={isOutOfStock}
          >
            -
          </button>
          <span>{quantity}</span>
          <button
            onClick={() => setQuantity((q) => Math.min(product.stock, q + 1))}
            disabled={isOutOfStock || quantity >= product.stock}
          >
            +
          </button>
        </div>

        {/* Add to cart */}
        <button
          className="add-to-cart"
          disabled={isOutOfStock}
          onClick={() => console.log(`Added ${quantity} of ${product.name}`)}
        >
          {isOutOfStock
            ? "Sold Out"
            : `Add to Cart - $${totalPrice.toFixed(2)}`}
        </button>

        {/* Favorite button */}
        <button
          className={`favorite-btn ${isFavorite ? "active" : ""}`}
          onClick={() => setIsFavorite(!isFavorite)}
          aria-label={isFavorite ? "Remove from favorites" : "Add to favorites"}
        >
          {isFavorite ? "❤️" : "🤍"}
        </button>
      </div>
    </article>
  );
}

// Usage
function App() {
  const sampleProduct = {
    id: 1,
    name: "Wireless Headphones",
    description: "High-quality Bluetooth headphones with noise cancellation",
    price: 199.99,
    discount: 20,
    stock: 5,
    image: "/headphones.jpg",
  };

  return <ProductCard product={sampleProduct} />;
}
```

### JSX Transformation Example

```jsx
// What you write (JSX)
function Greeting({ name }) {
  return (
    <div className="greeting">
      <h1>Hello, {name}!</h1>
      <p>Welcome to React</p>
    </div>
  );
}

// What Babel transforms it to (JavaScript)
function Greeting({ name }) {
  return React.createElement(
    'div',
    { className: 'greeting' },
    React.createElement('h1', null, 'Hello, ', name, '!'),
    React.createElement('p', null, 'Welcome to React')
  );
}

// The createElement returns an object like this:
{
  type: 'div',
  props: {
    className: 'greeting',
    children: [
      {
        type: 'h1',
        props: {
          children: ['Hello, ', 'John', '!']
        }
      },
      {
        type: 'p',
        props: {
          children: 'Welcome to React'
        }
      }
    ]
  }
}
```

---

## ✅ Best Practices

1. **Use parentheses for multi-line JSX**

   ```jsx
   // Good
   return (
     <div>
       <h1>Title</h1>
     </div>
   );

   // Avoid
   return (
     <div>
       <h1>Title</h1>
     </div>
   );
   ```

2. **Self-close empty elements**

   ```jsx
   // Good
   <img src="image.jpg" alt="description" />
   <input type="text" />
   <br />

   // Avoid
   <img src="image.jpg" alt="description"></img>
   ```

3. **Use Fragments to avoid unnecessary divs**

   ```jsx
   // Good
   return (
     <>
       <Header />
       <Main />
     </>
   );

   // Avoid (unless you need the wrapper)
   return (
     <div>
       <Header />
       <Main />
     </div>
   );
   ```

4. **Keep expressions simple**

   ```jsx
   // Good - extract complex logic
   const displayName = user.firstName + " " + user.lastName;
   return <h1>{displayName}</h1>;

   // Avoid - complex expressions in JSX
   return <h1>{user.firstName + " " + user.lastName.toUpperCase()}</h1>;
   ```

5. **Use meaningful variable names for styles**

   ```jsx
   // Good
   const errorMessageStyle = { color: "red", fontWeight: "bold" };
   return <p style={errorMessageStyle}>{error}</p>;

   // Avoid
   return <p style={{ color: "red", fontWeight: "bold" }}>{error}</p>;
   ```

---

## ❌ Common Mistakes

### 1. Using `class` instead of `className`

```jsx
// ❌ Wrong
<div class="container">

// ✅ Correct
<div className="container">
```

### 2. Forgetting curly braces for expressions

```jsx
// ❌ Wrong - displays literal text "name"
<h1>Hello, name!</h1>

// ✅ Correct - displays the value of name variable
<h1>Hello, {name}!</h1>
```

### 3. Returning multiple elements without wrapper

```jsx
// ❌ Wrong - syntax error
return (
  <h1>Title</h1>
  <p>Content</p>
);

// ✅ Correct - use Fragment
return (
  <>
    <h1>Title</h1>
    <p>Content</p>
  </>
);
```

### 4. Using statements instead of expressions

```jsx
// ❌ Wrong - if statement not allowed in JSX
<div>
  {if (isLoggedIn) { return <User /> }}
</div>

// ✅ Correct - use ternary or logical operators
<div>
  {isLoggedIn ? <User /> : <Guest />}
</div>
```

### 5. Incorrect style syntax

```jsx
// ❌ Wrong - string style like HTML
<div style="color: red; font-size: 16px">

// ✅ Correct - object with camelCase
<div style={{ color: 'red', fontSize: '16px' }}>
```

---

## 🏋️ Practice Exercises

### Exercise 1: Convert HTML to JSX

Convert this HTML to valid JSX:

```html
<div class="user-card">
  <img src="avatar.jpg" alt="User avatar" />
  <label for="username">Username:</label>
  <input type="text" id="username" tabindex="1" autofocus />
  <p style="color: blue; font-weight: bold;">Welcome!</p>
  <!-- This is a comment -->
</div>
```

### Exercise 2: Dynamic JSX

Create a component that displays a shopping cart with:

- Product name and price using variables
- Total calculated using expression
- Conditional "Empty Cart" message

### Exercise 3: List Rendering

Create a component that renders a list of users from an array:

```javascript
const users = [
  { id: 1, name: "Alice", role: "Admin" },
  { id: 2, name: "Bob", role: "User" },
  { id: 3, name: "Charlie", role: "User" },
];
```

---

## 🎤 Interview Q&A

**Q1: What is JSX and why is it used in React?**

> JSX is a syntax extension that looks like HTML but is JavaScript. It's used because it provides a familiar, readable way to describe UI structure, catches errors at compile time, and co-locates markup with logic for easier maintenance.

**Q2: Is JSX mandatory for React?**

> No, JSX is not mandatory. React can be used with pure JavaScript using React.createElement(), but JSX is strongly recommended because it's more readable and intuitive.

**Q3: Why do we use `className` instead of `class` in JSX?**

> Because `class` is a reserved keyword in JavaScript (used for class declarations). JSX is JavaScript, so it uses `className` to avoid conflicts with the JavaScript `class` keyword.

**Q4: What are the rules for JSX?**

> Key rules: (1) Must return a single root element, (2) All tags must be closed, (3) Use camelCase for attributes (className, onClick), (4) Use curly braces for JavaScript expressions, (5) Use double curly braces for style objects.

**Q5: How do you add comments in JSX?**

> Use `{/* comment */}` for comments inside JSX. Regular JavaScript comments (`//`) only work in the JavaScript parts of your code, not inside JSX markup.

**Q6: What is a Fragment and when would you use it?**

> A Fragment (`<>...</>` or `<Fragment>...</Fragment>`) lets you group multiple elements without adding an extra DOM node. Use it when you need to return multiple elements but don't want a wrapper div affecting your HTML structure or styling.

---

## 📎 References

- [React Docs - Writing Markup with JSX](https://react.dev/learn/writing-markup-with-jsx)
- [React Docs - JavaScript in JSX with Curly Braces](https://react.dev/learn/javascript-in-jsx-with-curly-braces)
- [Babel REPL - See JSX transformation](https://babeljs.io/repl)
- [JSX In Depth](https://react.dev/learn/writing-markup-with-jsx)

---

**Previous Topic**: [← 02. Setup Development Environment](./02_Setup_Development_Environment.md)

**Next Topic**: [04. Element vs Component vs Instance →](./04_Element_vs_Component_vs_Instance.md)
