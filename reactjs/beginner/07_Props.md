# 07. Props

> Passing data to components - the communication channel between parent and child.

---

## 🎯 What are Props?

**Props** (short for "properties") are the way you pass data from a parent component to a child component. Think of props like function arguments - they're how you customize and configure components.

```jsx
// Parent passes data via props
<UserCard name="Alice" age={25} />;

// Child receives and uses props
function UserCard({ name, age }) {
  return (
    <div>
      <h2>{name}</h2>
      <p>Age: {age}</p>
    </div>
  );
}
```

---

## 🤔 Why Do We Need Props?

### Without Props (The Problem)

```jsx
// Hardcoded components - not reusable
function AliceCard() {
  return (
    <div>
      <h2>Alice</h2>
      <p>Age: 25</p>
    </div>
  );
}

function BobCard() {
  return (
    <div>
      <h2>Bob</h2>
      <p>Age: 30</p>
    </div>
  );
}

function CharlieCard() {
  return (
    <div>
      <h2>Charlie</h2>
      <p>Age: 35</p>
    </div>
  );
}
```

### With Props (The Solution)

```jsx
// One reusable component
function UserCard({ name, age }) {
  return <div><h2>{name}</h2><p>Age: {age}</p></div>;
}

// Use it multiple times with different data
<UserCard name="Alice" age={25} />
<UserCard name="Bob" age={30} />
<UserCard name="Charlie" age={35} />
```

**Benefits:**

- ✅ **Reusability** - One component, infinite uses
- ✅ **Flexibility** - Customize behavior per instance
- ✅ **Data flow** - Clear parent-to-child communication
- ✅ **Maintainability** - Change component once, affects all uses

---

## 📚 Core Concepts

### 1. Passing Props

Props can be any JavaScript value:

```jsx
function PropsDemo() {
  const user = { name: 'Alice', email: 'alice@example.com' };
  const colors = ['red', 'green', 'blue'];

  return (
    <Component
      {/* Strings */}
      title="Hello World"
      subtitle={'Dynamic String'}

      {/* Numbers */}
      count={42}
      price={19.99}

      {/* Booleans */}
      isActive={true}
      disabled={false}
      loading  {/* shorthand for loading={true} */}

      {/* Objects */}
      user={user}
      config={{ theme: 'dark', size: 'large' }}

      {/* Arrays */}
      items={colors}
      numbers={[1, 2, 3, 4, 5]}

      {/* Functions */}
      onClick={() => console.log('clicked')}
      onSubmit={handleSubmit}

      {/* JSX/Elements */}
      icon={<Icon name="star" />}
      header={<h1>Custom Header</h1>}

      {/* Expressions */}
      total={price * quantity}
      greeting={`Hello, ${name}!`}
    />
  );
}
```

### 2. Receiving Props

Three ways to access props:

```jsx
// Method 1: props object (basic)
function Welcome(props) {
  return <h1>Hello, {props.name}!</h1>;
}

// Method 2: Destructuring in parameter (preferred)
function Welcome({ name }) {
  return <h1>Hello, {name}!</h1>;
}

// Method 3: Destructuring in body
function Welcome(props) {
  const { name, age, email } = props;
  return <h1>Hello, {name}!</h1>;
}
```

### 3. Default Props

Provide fallback values when props are not passed:

```jsx
// Method 1: Default parameters (recommended)
function Button({
  label = 'Click me',
  variant = 'primary',
  size = 'medium'
}) {
  return (
    <button className={`btn btn-${variant} btn-${size}`}>
      {label}
    </button>
  );
}

// Method 2: defaultProps (legacy, still works)
function Button({ label, variant, size }) {
  return (
    <button className={`btn btn-${variant} btn-${size}`}>
      {label}
    </button>
  );
}

Button.defaultProps = {
  label: 'Click me',
  variant: 'primary',
  size: 'medium'
};

// Usage
<Button />                           // All defaults
<Button label="Submit" />            // Custom label, other defaults
<Button variant="danger" size="lg"/> // Custom variant and size
```

### 4. Props are Read-Only

**Never modify props!** They're read-only.

```jsx
// ❌ WRONG - Never mutate props
function BadComponent({ user }) {
  user.name = "Modified"; // DON'T DO THIS!
  return <p>{user.name}</p>;
}

// ✅ CORRECT - Use state if you need to change values
function GoodComponent({ user }) {
  const [name, setName] = useState(user.name);

  return (
    <div>
      <p>{name}</p>
      <button onClick={() => setName("New Name")}>Change</button>
    </div>
  );
}
```

### 5. Spread Operator with Props

```jsx
// Passing all props from an object
const buttonProps = {
  label: 'Submit',
  variant: 'primary',
  disabled: false,
  onClick: handleClick
};

<Button {...buttonProps} />

// Equivalent to:
<Button
  label={buttonProps.label}
  variant={buttonProps.variant}
  disabled={buttonProps.disabled}
  onClick={buttonProps.onClick}
/>

// Forwarding props to child
function Container({ className, children, ...rest }) {
  return (
    <div className={`container ${className}`} {...rest}>
      {children}
    </div>
  );
}

// All extra props (id, data-*, aria-*, etc.) are passed to div
<Container className="main" id="app" data-testid="main">
  Content
</Container>
```

### 6. Children Prop

The special `children` prop contains content between opening and closing tags:

```jsx
// Component with children
function Card({ children, title }) {
  return (
    <div className="card">
      <h2>{title}</h2>
      <div className="card-body">
        {children}
      </div>
    </div>
  );
}

// Usage
<Card title="Welcome">
  <p>This is the card content.</p>
  <button>Click me</button>
</Card>

// children can be anything
<Card title="User Info">
  <UserProfile />
  <UserStats />
</Card>

<Card title="Actions">
  {['Edit', 'Delete', 'Share'].map(action => (
    <button key={action}>{action}</button>
  ))}
</Card>
```

### 7. Prop Types (Runtime Validation)

Use PropTypes for development-time type checking:

```jsx
import PropTypes from "prop-types";

function UserCard({ name, age, email, isAdmin, onSelect }) {
  return (
    <div onClick={onSelect}>
      <h2>{name}</h2>
      <p>Age: {age}</p>
      <p>Email: {email}</p>
      {isAdmin && <span>👑 Admin</span>}
    </div>
  );
}

UserCard.propTypes = {
  // Basic types
  name: PropTypes.string.isRequired,
  age: PropTypes.number,
  email: PropTypes.string,
  isAdmin: PropTypes.bool,

  // Function
  onSelect: PropTypes.func,

  // Array
  tags: PropTypes.arrayOf(PropTypes.string),

  // Object with shape
  user: PropTypes.shape({
    id: PropTypes.number.isRequired,
    name: PropTypes.string.isRequired,
    email: PropTypes.string,
  }),

  // One of specific values
  status: PropTypes.oneOf(["active", "inactive", "pending"]),

  // One of multiple types
  id: PropTypes.oneOfType([PropTypes.string, PropTypes.number]),

  // Any renderable content
  children: PropTypes.node,

  // A React element
  icon: PropTypes.element,

  // Instance of a class
  date: PropTypes.instanceOf(Date),
};

UserCard.defaultProps = {
  age: 0,
  isAdmin: false,
  onSelect: () => {},
};
```

### 8. TypeScript Props (Compile-time Validation)

For stronger type safety, use TypeScript:

```tsx
// TypeScript interface for props
interface UserCardProps {
  name: string;
  age?: number;          // Optional
  email: string;
  isAdmin?: boolean;
  onSelect?: () => void;
  children?: React.ReactNode;
}

function UserCard({
  name,
  age = 0,
  email,
  isAdmin = false,
  onSelect,
  children
}: UserCardProps) {
  return (
    <div onClick={onSelect}>
      <h2>{name}</h2>
      <p>Age: {age}</p>
      <p>Email: {email}</p>
      {isAdmin && <span>👑 Admin</span>}
      {children}
    </div>
  );
}

// Usage - TypeScript catches errors at compile time
<UserCard name="Alice" email="alice@example.com" />  // ✅ OK
<UserCard name={123} email="test" />                  // ❌ Error: name must be string
<UserCard email="test" />                             // ❌ Error: name is required
```

---

## 💻 Demo Code

### Complete Example: Product Card Component

```jsx
import { useState } from "react";
import PropTypes from "prop-types";

// ========================================
// REUSABLE UI COMPONENTS
// ========================================

function Badge({ children, variant = "default" }) {
  const variants = {
    default: "bg-gray-100 text-gray-800",
    success: "bg-green-100 text-green-800",
    warning: "bg-yellow-100 text-yellow-800",
    danger: "bg-red-100 text-red-800",
  };

  return (
    <span className={`px-2 py-1 rounded text-sm ${variants[variant]}`}>
      {children}
    </span>
  );
}

function Button({
  children,
  variant = "primary",
  size = "md",
  disabled = false,
  onClick,
}) {
  const baseStyles = "font-medium rounded transition-colors";
  const sizes = {
    sm: "px-2 py-1 text-sm",
    md: "px-4 py-2",
    lg: "px-6 py-3 text-lg",
  };
  const variants = {
    primary: "bg-blue-500 text-white hover:bg-blue-600",
    secondary: "bg-gray-200 text-gray-800 hover:bg-gray-300",
    outline: "border border-blue-500 text-blue-500 hover:bg-blue-50",
  };

  return (
    <button
      className={`${baseStyles} ${sizes[size]} ${variants[variant]}`}
      disabled={disabled}
      onClick={onClick}
    >
      {children}
    </button>
  );
}

// ========================================
// PRODUCT CARD COMPONENT
// ========================================

function ProductCard({
  // Required props
  name,
  price,
  image,

  // Optional props with defaults
  description = "",
  originalPrice = null,
  rating = 0,
  reviewCount = 0,
  inStock = true,
  badges = [],

  // Event handlers
  onAddToCart,
  onWishlist,
  onQuickView,
}) {
  const [isWishlisted, setIsWishlisted] = useState(false);
  const discount = originalPrice
    ? Math.round((1 - price / originalPrice) * 100)
    : 0;

  const handleWishlist = () => {
    setIsWishlisted(!isWishlisted);
    onWishlist?.(!isWishlisted);
  };

  return (
    <div className="border rounded-lg overflow-hidden shadow-sm hover:shadow-md transition-shadow">
      {/* Image Section */}
      <div className="relative">
        <img src={image} alt={name} className="w-full h-48 object-cover" />

        {/* Badges */}
        {badges.length > 0 && (
          <div className="absolute top-2 left-2 flex gap-1">
            {badges.map((badge, index) => (
              <Badge key={index} variant={badge.variant}>
                {badge.text}
              </Badge>
            ))}
          </div>
        )}

        {/* Discount Badge */}
        {discount > 0 && (
          <Badge variant="danger" className="absolute top-2 right-2">
            -{discount}%
          </Badge>
        )}

        {/* Quick View Button */}
        {onQuickView && (
          <button
            onClick={onQuickView}
            className="absolute bottom-2 right-2 bg-white p-2 rounded-full shadow"
          >
            👁️
          </button>
        )}
      </div>

      {/* Content Section */}
      <div className="p-4">
        <h3 className="font-semibold text-lg">{name}</h3>

        {description && (
          <p className="text-gray-600 text-sm mt-1 line-clamp-2">
            {description}
          </p>
        )}

        {/* Rating */}
        {rating > 0 && (
          <div className="flex items-center gap-1 mt-2">
            <span className="text-yellow-400">
              {"★".repeat(Math.floor(rating))}
              {"☆".repeat(5 - Math.floor(rating))}
            </span>
            <span className="text-sm text-gray-500">
              ({reviewCount} reviews)
            </span>
          </div>
        )}

        {/* Price */}
        <div className="mt-2 flex items-center gap-2">
          <span className="text-xl font-bold">${price.toFixed(2)}</span>
          {originalPrice && (
            <span className="text-gray-400 line-through">
              ${originalPrice.toFixed(2)}
            </span>
          )}
        </div>

        {/* Stock Status */}
        <p
          className={`text-sm mt-1 ${
            inStock ? "text-green-600" : "text-red-600"
          }`}
        >
          {inStock ? "✓ In Stock" : "✗ Out of Stock"}
        </p>
      </div>

      {/* Actions Section */}
      <div className="p-4 pt-0 flex gap-2">
        <Button
          variant={inStock ? "primary" : "secondary"}
          disabled={!inStock}
          onClick={() => onAddToCart?.({ name, price })}
        >
          {inStock ? "Add to Cart" : "Sold Out"}
        </Button>

        <Button variant="outline" onClick={handleWishlist}>
          {isWishlisted ? "❤️" : "🤍"}
        </Button>
      </div>
    </div>
  );
}

// PropTypes validation
ProductCard.propTypes = {
  name: PropTypes.string.isRequired,
  price: PropTypes.number.isRequired,
  image: PropTypes.string.isRequired,
  description: PropTypes.string,
  originalPrice: PropTypes.number,
  rating: PropTypes.number,
  reviewCount: PropTypes.number,
  inStock: PropTypes.bool,
  badges: PropTypes.arrayOf(
    PropTypes.shape({
      text: PropTypes.string.isRequired,
      variant: PropTypes.oneOf(["default", "success", "warning", "danger"]),
    })
  ),
  onAddToCart: PropTypes.func,
  onWishlist: PropTypes.func,
  onQuickView: PropTypes.func,
};

// ========================================
// USAGE EXAMPLE
// ========================================

function ProductGrid() {
  const products = [
    {
      id: 1,
      name: "Wireless Headphones",
      description: "Premium noise-cancelling Bluetooth headphones",
      price: 199.99,
      originalPrice: 249.99,
      image: "/headphones.jpg",
      rating: 4.5,
      reviewCount: 128,
      inStock: true,
      badges: [
        { text: "Best Seller", variant: "success" },
        { text: "New", variant: "warning" },
      ],
    },
    {
      id: 2,
      name: "Smart Watch",
      description: "Fitness tracking with heart rate monitor",
      price: 299.99,
      image: "/watch.jpg",
      rating: 4.2,
      reviewCount: 89,
      inStock: true,
      badges: [],
    },
    {
      id: 3,
      name: "Vintage Camera",
      description: "Classic film camera for enthusiasts",
      price: 449.99,
      image: "/camera.jpg",
      rating: 4.8,
      reviewCount: 42,
      inStock: false,
      badges: [{ text: "Limited Edition", variant: "danger" }],
    },
  ];

  const handleAddToCart = (product) => {
    console.log("Added to cart:", product);
  };

  const handleWishlist = (productId, isWishlisted) => {
    console.log(`Product ${productId} wishlisted:`, isWishlisted);
  };

  return (
    <div className="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-6 p-6">
      {products.map((product) => (
        <ProductCard
          key={product.id}
          name={product.name}
          description={product.description}
          price={product.price}
          originalPrice={product.originalPrice}
          image={product.image}
          rating={product.rating}
          reviewCount={product.reviewCount}
          inStock={product.inStock}
          badges={product.badges}
          onAddToCart={handleAddToCart}
          onWishlist={(isWishlisted) =>
            handleWishlist(product.id, isWishlisted)
          }
        />
      ))}
    </div>
  );
}

export default ProductGrid;
```

---

## ✅ Best Practices

### 1. Destructure Props

```jsx
// ❌ Verbose
function UserCard(props) {
  return (
    <div>
      {props.user.name} - {props.user.email}
    </div>
  );
}

// ✅ Clean
function UserCard({ user: { name, email } }) {
  return (
    <div>
      {name} - {email}
    </div>
  );
}
```

### 2. Use Default Values

```jsx
// ✅ Provide sensible defaults
function Avatar({ src, alt = "User avatar", size = 48 }) {
  return <img src={src} alt={alt} width={size} height={size} />;
}
```

### 3. Keep Props Minimal

```jsx
// ❌ Too many props
<UserCard
  userId={user.id}
  userName={user.name}
  userEmail={user.email}
  userAvatar={user.avatar}
  userRole={user.role}
/>

// ✅ Pass the object
<UserCard user={user} />
```

### 4. Document with PropTypes/TypeScript

```jsx
// Always document expected props
Button.propTypes = {
  label: PropTypes.string.isRequired,
  onClick: PropTypes.func.isRequired,
  variant: PropTypes.oneOf(["primary", "secondary", "danger"]),
};
```

---

## ❌ Common Mistakes

### 1. Mutating Props

```jsx
// ❌ NEVER mutate props
function BadComponent({ items }) {
  items.push(newItem); // WRONG!
  items.sort(); // WRONG!
}

// ✅ Create new arrays/objects
function GoodComponent({ items }) {
  const sortedItems = [...items].sort();
  const withNewItem = [...items, newItem];
}
```

### 2. Forgetting to Pass Required Props

```jsx
// ❌ Missing required prop
<UserCard />  // Error if name is required

// ✅ Always pass required props
<UserCard name="Alice" />
```

### 3. Passing Wrong Types

```jsx
// ❌ Wrong type
<Counter count="5" />      // String instead of number
<Button onClick="click" /> // String instead of function

// ✅ Correct types
<Counter count={5} />
<Button onClick={() => {}} />
```

### 4. Over-spreading Props

```jsx
// ❌ Spreads everything (including invalid HTML attributes)
function Input(props) {
  return <input {...props} />;
}
<Input customProp="value" />; // customProp goes to DOM!

// ✅ Extract known props, spread rest
function Input({ customProp, ...rest }) {
  // customProp used internally
  return <input {...rest} />;
}
```

---

## 🏋️ Practice Exercises

### Exercise 1: Create a Button Component

Create a reusable `Button` component with these props:

- `label` (string, required)
- `variant` ('primary' | 'secondary' | 'danger')
- `size` ('sm' | 'md' | 'lg')
- `disabled` (boolean)
- `onClick` (function)

### Exercise 2: Build a Card with Children

Create a `Card` component that uses the `children` prop:

```jsx
<Card title="My Card" footer={<button>Action</button>}>
  <p>Card content goes here</p>
</Card>
```

### Exercise 3: Props Drilling Challenge

Create a component tree where data is passed through multiple levels:

- `App` → `Dashboard` → `UserSection` → `UserCard`

Then discuss how this becomes problematic and what solutions exist.

---

## 🎤 Interview Q&A

**Q1: What are props in React?**

> Props are inputs to components - they're how data flows from parent to child. They're read-only and make components reusable by allowing different configurations.

**Q2: What's the difference between props and state?**

> Props are passed from parent and are read-only (owned by parent). State is managed within the component and can be changed (owned by component). Props are external, state is internal.

**Q3: Why are props immutable?**

> Props are immutable to maintain unidirectional data flow and predictable component behavior. If props could be changed, it would be unclear which component owns the data, leading to bugs and hard-to-trace issues.

**Q4: What is the `children` prop?**

> The `children` prop contains whatever is passed between the opening and closing tags of a component. It enables composition patterns and makes components flexible containers.

**Q5: How do you validate props in React?**

> You can use PropTypes (runtime validation) or TypeScript (compile-time validation). PropTypes warn in development when props don't match expected types. TypeScript catches type errors during development and build.

**Q6: What happens if you don't pass a required prop?**

> With PropTypes, you'll see a console warning in development. With TypeScript, you'll get a compile error. The component may render incorrectly or throw an error depending on how it handles missing data.

---

## 📎 References

- [React Docs - Passing Props to a Component](https://react.dev/learn/passing-props-to-a-component)
- [React Docs - Component Props](https://react.dev/learn/your-first-component#passing-props-to-a-component)
- [PropTypes Documentation](https://www.npmjs.com/package/prop-types)
- [TypeScript with React](https://react.dev/learn/typescript)

---

**Previous Topic**: [← 06. Keeping Components Pure](./06_Keeping_Components_Pure.md)

**Next Topic**: [08. State and useState →](./08_State_and_useState.md)
