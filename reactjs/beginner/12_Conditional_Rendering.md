# 12. Conditional Rendering

> Showing different UI based on conditions - making dynamic interfaces.

---

## 🎯 What is Conditional Rendering?

Conditional rendering lets you display different content based on certain conditions - just like using `if` statements in JavaScript, but for your UI.

```jsx
function Greeting({ isLoggedIn }) {
  if (isLoggedIn) {
    return <h1>Welcome back!</h1>;
  }
  return <h1>Please sign in.</h1>;
}
```

---

## 🤔 Why Conditional Rendering?

Real applications need to:

- ✅ Show/hide elements based on state
- ✅ Display different content for different users
- ✅ Handle loading, error, and success states
- ✅ Respond to user interactions
- ✅ Create dynamic, responsive UIs

---

## 📚 Core Concepts

### 1. If/Else Statements

The most straightforward approach - use regular JavaScript if/else:

```jsx
function UserGreeting({ user }) {
  if (!user) {
    return <p>Loading...</p>;
  }

  if (user.isAdmin) {
    return (
      <div>
        <h1>Welcome, Admin {user.name}!</h1>
        <AdminDashboard />
      </div>
    );
  }

  return (
    <div>
      <h1>Welcome, {user.name}!</h1>
      <UserDashboard />
    </div>
  );
}
```

### 2. Ternary Operator

Best for simple either/or conditions, especially inline:

```jsx
function StatusBadge({ isOnline }) {
  return (
    <span className={isOnline ? "online" : "offline"}>
      {isOnline ? "🟢 Online" : "🔴 Offline"}
    </span>
  );
}

// Nested ternary (use sparingly)
function PriorityLabel({ level }) {
  return (
    <span
      className={
        level === "high"
          ? "text-red"
          : level === "medium"
          ? "text-yellow"
          : "text-green"
      }
    >
      {level}
    </span>
  );
}
```

### 3. Logical && Operator

Show something or nothing:

```jsx
function Notifications({ messages }) {
  return (
    <div>
      <h1>Dashboard</h1>

      {/* Show only if messages exist */}
      {messages.length > 0 && (
        <div className="notification-badge">{messages.length} new messages</div>
      )}

      {/* Multiple conditions */}
      {messages.length > 0 && messages.some((m) => m.urgent) && (
        <div className="urgent-alert">You have urgent messages!</div>
      )}
    </div>
  );
}
```

**⚠️ Gotcha with &&:**

```jsx
// ❌ Bug: Shows "0" when count is 0
{
  count && <span>{count} items</span>;
}

// ✅ Fixed: Explicit boolean check
{
  count > 0 && <span>{count} items</span>;
}

// ✅ Alternative: Convert to boolean
{
  !!count && <span>{count} items</span>;
}

// ✅ Better: Use ternary for clarity
{
  count ? <span>{count} items</span> : null;
}
```

### 4. Logical || Operator (Fallback)

Show fallback content:

```jsx
function UserAvatar({ user }) {
  return (
    <div>
      {/* Show avatar or placeholder */}
      {user.avatar || <DefaultAvatar />}

      {/* Show name or "Anonymous" */}
      <span>{user.name || "Anonymous"}</span>
    </div>
  );
}
```

### 5. Nullish Coalescing (??)

For null/undefined specifically (not falsy values):

```jsx
function Settings({ settings }) {
  // ?? only checks for null/undefined
  const volume = settings.volume ?? 50; // 0 is valid!
  const notifications = settings.notifications ?? true;

  return (
    <div>
      <p>Volume: {volume}%</p>
      <p>Notifications: {notifications ? "On" : "Off"}</p>
    </div>
  );
}
```

### 6. Switch Statements

Best for multiple discrete options:

```jsx
function StatusMessage({ status }) {
  switch (status) {
    case "loading":
      return <Spinner />;
    case "success":
      return <SuccessMessage />;
    case "error":
      return <ErrorMessage />;
    case "empty":
      return <EmptyState />;
    default:
      return null;
  }
}

// Alternative: Object lookup
function StatusMessage({ status }) {
  const statusComponents = {
    loading: <Spinner />,
    success: <SuccessMessage />,
    error: <ErrorMessage />,
    empty: <EmptyState />,
  };

  return statusComponents[status] || null;
}
```

### 7. Returning null (Render Nothing)

```jsx
function WarningBanner({ warning }) {
  if (!warning) {
    return null; // Render nothing
  }

  return <div className="warning">{warning}</div>;
}
```

### 8. Conditional Classes and Styles

```jsx
function Button({ isActive, isDisabled, size }) {
  // Method 1: Template literals
  const className = `
    btn 
    ${isActive ? "btn-active" : ""} 
    ${isDisabled ? "btn-disabled" : ""}
    btn-${size}
  `.trim();

  // Method 2: Array join
  const classNames = [
    "btn",
    isActive && "btn-active",
    isDisabled && "btn-disabled",
    `btn-${size}`,
  ]
    .filter(Boolean)
    .join(" ");

  // Method 3: Conditional styles
  const style = {
    backgroundColor: isActive ? "blue" : "gray",
    opacity: isDisabled ? 0.5 : 1,
    cursor: isDisabled ? "not-allowed" : "pointer",
  };

  return (
    <button className={className} style={style} disabled={isDisabled}>
      Click me
    </button>
  );
}
```

### 9. Rendering Lists Conditionally

```jsx
function ItemList({ items, isLoading, error }) {
  if (isLoading) {
    return <LoadingSpinner />;
  }

  if (error) {
    return <ErrorMessage error={error} />;
  }

  if (items.length === 0) {
    return <EmptyState message="No items found" />;
  }

  return (
    <ul>
      {items.map((item) => (
        <li key={item.id}>
          {item.name}
          {item.isNew && <span className="badge">New</span>}
          {item.discount > 0 && (
            <span className="discount">{item.discount}% off</span>
          )}
        </li>
      ))}
    </ul>
  );
}
```

---

## 💻 Demo Code

### Example 1: Authentication State

```jsx
import { useState } from "react";

function App() {
  const [user, setUser] = useState(null);
  const [isLoading, setIsLoading] = useState(false);

  const handleLogin = () => {
    setIsLoading(true);
    // Simulate API call
    setTimeout(() => {
      setUser({ name: "Alice", role: "admin" });
      setIsLoading(false);
    }, 1000);
  };

  const handleLogout = () => {
    setUser(null);
  };

  // Loading state
  if (isLoading) {
    return (
      <div className="loading">
        <Spinner />
        <p>Logging in...</p>
      </div>
    );
  }

  // Not logged in
  if (!user) {
    return (
      <div className="login-page">
        <h1>Welcome</h1>
        <p>Please log in to continue</p>
        <button onClick={handleLogin}>Log In</button>
      </div>
    );
  }

  // Logged in
  return (
    <div className="dashboard">
      <header>
        <h1>Welcome, {user.name}!</h1>
        {user.role === "admin" && <span className="admin-badge">👑 Admin</span>}
        <button onClick={handleLogout}>Log Out</button>
      </header>

      <main>
        {user.role === "admin" ? <AdminDashboard /> : <UserDashboard />}
      </main>
    </div>
  );
}

function Spinner() {
  return <div className="spinner">Loading...</div>;
}

function AdminDashboard() {
  return <div>Admin Dashboard Content</div>;
}

function UserDashboard() {
  return <div>User Dashboard Content</div>;
}
```

### Example 2: Fetch States Pattern

```jsx
import { useState, useEffect } from "react";

function DataDisplay({ endpoint }) {
  const [data, setData] = useState(null);
  const [isLoading, setIsLoading] = useState(true);
  const [error, setError] = useState(null);

  useEffect(() => {
    setIsLoading(true);
    setError(null);

    fetch(endpoint)
      .then((res) => {
        if (!res.ok) throw new Error("Failed to fetch");
        return res.json();
      })
      .then((data) => {
        setData(data);
        setIsLoading(false);
      })
      .catch((err) => {
        setError(err.message);
        setIsLoading(false);
      });
  }, [endpoint]);

  // Render based on state
  if (isLoading) {
    return (
      <div className="loading-state">
        <div className="skeleton" />
        <div className="skeleton" />
        <div className="skeleton" />
      </div>
    );
  }

  if (error) {
    return (
      <div className="error-state">
        <span>⚠️</span>
        <h3>Something went wrong</h3>
        <p>{error}</p>
        <button onClick={() => window.location.reload()}>Try Again</button>
      </div>
    );
  }

  if (!data || data.length === 0) {
    return (
      <div className="empty-state">
        <span>📭</span>
        <h3>No data found</h3>
        <p>There's nothing to display yet.</p>
      </div>
    );
  }

  return (
    <div className="data-list">
      {data.map((item) => (
        <DataCard key={item.id} item={item} />
      ))}
    </div>
  );
}

function DataCard({ item }) {
  return (
    <div className="card">
      <h4>{item.title}</h4>
      {item.description && <p>{item.description}</p>}
      {item.tags?.length > 0 && (
        <div className="tags">
          {item.tags.map((tag) => (
            <span key={tag} className="tag">
              {tag}
            </span>
          ))}
        </div>
      )}
    </div>
  );
}
```

### Example 3: Multi-Step Form

```jsx
import { useState } from "react";

function MultiStepForm() {
  const [step, setStep] = useState(1);
  const [formData, setFormData] = useState({
    name: "",
    email: "",
    plan: "",
    cardNumber: "",
  });

  const updateField = (field, value) => {
    setFormData((prev) => ({ ...prev, [field]: value }));
  };

  const nextStep = () => setStep((s) => Math.min(s + 1, 4));
  const prevStep = () => setStep((s) => Math.max(s - 1, 1));

  // Step indicator
  const StepIndicator = () => (
    <div className="steps">
      {[1, 2, 3, 4].map((s) => (
        <div
          key={s}
          className={`step ${s === step ? "active" : ""} ${
            s < step ? "completed" : ""
          }`}
        >
          {s < step ? "✓" : s}
        </div>
      ))}
    </div>
  );

  // Render current step content
  const renderStep = () => {
    switch (step) {
      case 1:
        return (
          <div className="step-content">
            <h2>Personal Info</h2>
            <input
              placeholder="Name"
              value={formData.name}
              onChange={(e) => updateField("name", e.target.value)}
            />
            <input
              placeholder="Email"
              type="email"
              value={formData.email}
              onChange={(e) => updateField("email", e.target.value)}
            />
          </div>
        );

      case 2:
        return (
          <div className="step-content">
            <h2>Choose Plan</h2>
            {["basic", "pro", "enterprise"].map((plan) => (
              <label key={plan} className="plan-option">
                <input
                  type="radio"
                  name="plan"
                  value={plan}
                  checked={formData.plan === plan}
                  onChange={(e) => updateField("plan", e.target.value)}
                />
                {plan.charAt(0).toUpperCase() + plan.slice(1)}
              </label>
            ))}
          </div>
        );

      case 3:
        return (
          <div className="step-content">
            <h2>Payment</h2>
            <input
              placeholder="Card Number"
              value={formData.cardNumber}
              onChange={(e) => updateField("cardNumber", e.target.value)}
            />
          </div>
        );

      case 4:
        return (
          <div className="step-content">
            <h2>Confirmation</h2>
            <div className="summary">
              <p>
                <strong>Name:</strong> {formData.name}
              </p>
              <p>
                <strong>Email:</strong> {formData.email}
              </p>
              <p>
                <strong>Plan:</strong> {formData.plan}
              </p>
              <p>
                <strong>Card:</strong> **** {formData.cardNumber.slice(-4)}
              </p>
            </div>
          </div>
        );

      default:
        return null;
    }
  };

  return (
    <div className="multi-step-form">
      <StepIndicator />

      {renderStep()}

      <div className="buttons">
        {step > 1 && <button onClick={prevStep}>Previous</button>}
        {step < 4 ? (
          <button onClick={nextStep}>Next</button>
        ) : (
          <button onClick={() => alert("Submitted!")}>Submit</button>
        )}
      </div>
    </div>
  );
}
```

### Example 4: Permission-Based UI

```jsx
function Dashboard({ user }) {
  const { role, permissions } = user;

  const canEdit = permissions.includes("edit");
  const canDelete = permissions.includes("delete");
  const canPublish = permissions.includes("publish");
  const isAdmin = role === "admin";

  return (
    <div className="dashboard">
      <header>
        <h1>Dashboard</h1>
        {isAdmin && <AdminBanner />}
      </header>

      <main>
        <ContentList />

        {/* Action bar - only show if user has any action permissions */}
        {(canEdit || canDelete || canPublish) && (
          <div className="action-bar">
            {canEdit && <button className="btn-edit">Edit</button>}
            {canPublish && <button className="btn-publish">Publish</button>}
            {canDelete && <button className="btn-delete">Delete</button>}
          </div>
        )}
      </main>

      {/* Admin-only sidebar */}
      {isAdmin && (
        <aside className="admin-sidebar">
          <h3>Admin Tools</h3>
          <ul>
            <li>User Management</li>
            <li>Analytics</li>
            <li>Settings</li>
          </ul>
        </aside>
      )}
    </div>
  );
}
```

---

## ✅ Best Practices

### 1. Early Returns for Cleaner Code

```jsx
// ✅ Good - early returns
function UserProfile({ user, isLoading, error }) {
  if (isLoading) return <Spinner />;
  if (error) return <ErrorMessage error={error} />;
  if (!user) return <NotFound />;

  return <ProfileContent user={user} />;
}

// ❌ Avoid - deeply nested
function UserProfile({ user, isLoading, error }) {
  return (
    <div>
      {isLoading ? (
        <Spinner />
      ) : error ? (
        <ErrorMessage error={error} />
      ) : !user ? (
        <NotFound />
      ) : (
        <ProfileContent user={user} />
      )}
    </div>
  );
}
```

### 2. Extract Complex Conditions

```jsx
// ✅ Good - clear intent
const canShowDashboard = user && !isLoading && !error;
const hasPermission =
  user?.role === "admin" || user?.permissions?.includes("view");

return canShowDashboard && hasPermission ? <Dashboard /> : <AccessDenied />;

// ❌ Avoid - hard to read
return user &&
  !isLoading &&
  !error &&
  (user.role === "admin" || user.permissions?.includes("view")) ? (
  <Dashboard />
) : (
  <AccessDenied />
);
```

### 3. Use Components for Complex Branches

```jsx
// ✅ Good - separate components
function App() {
  if (isLoggedIn) return <AuthenticatedApp />;
  return <UnauthenticatedApp />;
}

// Each branch is its own component with its own logic
function AuthenticatedApp() { ... }
function UnauthenticatedApp() { ... }
```

---

## ❌ Common Mistakes

### 1. Falsy Value Pitfall with &&

```jsx
// ❌ Bug: Shows "0" when count is 0
{
  count && <span>{count}</span>;
}

// ✅ Fix: Explicit comparison
{
  count > 0 && <span>{count}</span>;
}
```

### 2. Missing Keys in Conditional Lists

```jsx
// ❌ Missing keys
{
  items.map((item) => item.active && <Item item={item} />);
}

// ✅ With keys
{
  items.map((item) => item.active && <Item key={item.id} item={item} />);
}
```

### 3. Overusing Ternary

```jsx
// ❌ Hard to read
{
  a ? b ? <A /> : <B /> : c ? <C /> : <D />;
}

// ✅ Use if/else or switch
const getComponent = () => {
  if (a && b) return <A />;
  if (a) return <B />;
  if (c) return <C />;
  return <D />;
};
return getComponent();
```

---

## 🏋️ Practice Exercises

### Exercise 1: Login/Signup Toggle

Create a component that toggles between login and signup forms.

### Exercise 2: Theme Switcher

Build a theme selector with light/dark/system options.

### Exercise 3: Notification System

Create notifications that show different styles for info/warning/error.

---

## 🎤 Interview Q&A

**Q1: What are the different ways to conditionally render in React?**

> if/else statements, ternary operator (`? :`), logical AND (`&&`), logical OR (`||`), switch statements, and returning null. Choose based on complexity and readability.

**Q2: What's the gotcha with using && for conditional rendering?**

> If the left side is a falsy value like 0 or '', it will render that value instead of nothing. Use explicit boolean conditions like `count > 0 &&` or ternary operators.

**Q3: When should you use early returns vs ternary operators?**

> Use early returns for multiple exclusive conditions (loading, error, empty states). Use ternary for simple inline either/or decisions. Prioritize readability.

**Q4: How do you conditionally apply CSS classes?**

> Template literals: `` `btn ${isActive ? 'active' : ''}` ``, array join: `['btn', isActive && 'active'].filter(Boolean).join(' ')`, or libraries like classnames/clsx.

**Q5: What happens when a component returns null?**

> The component renders nothing to the DOM, but it still goes through the render cycle. This is useful for conditionally hiding components.

---

## 📎 References

- [React Docs - Conditional Rendering](https://react.dev/learn/conditional-rendering)
- [React Docs - Rendering Lists](https://react.dev/learn/rendering-lists)
- [JavaScript Truthy/Falsy](https://developer.mozilla.org/en-US/docs/Glossary/Truthy)

---

**Previous Topic**: [← 11. Event Handling](./11_Event_Handling.md)

**Next Topic**: [13. Lists and Keys →](./13_Lists_and_Keys.md)
