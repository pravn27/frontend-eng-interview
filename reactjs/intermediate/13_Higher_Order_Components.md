# 13 - Higher Order Components (HOCs)

## 📖 What is a Higher Order Component?

A Higher Order Component is a function that takes a component and returns a new component with additional functionality. HOCs are a pattern for **reusing component logic**.

```jsx
// HOC definition
function withLogger(WrappedComponent) {
  return function WithLogger(props) {
    console.log("Props:", props);
    return <WrappedComponent {...props} />;
  };
}

// Usage
const EnhancedComponent = withLogger(MyComponent);
```

---

## 🎯 Why HOCs?

### The Problem: Duplicated Logic

```jsx
// ❌ Same logic repeated in multiple components
function UserList() {
  const [loading, setLoading] = useState(true);
  const [data, setData] = useState(null);

  useEffect(() => {
    fetchUsers()
      .then(setData)
      .finally(() => setLoading(false));
  }, []);

  if (loading) return <Loading />;
  return <ul>{data.map(/*...*/)}</ul>;
}

function ProductList() {
  const [loading, setLoading] = useState(true);
  const [data, setData] = useState(null);

  useEffect(() => {
    fetchProducts()
      .then(setData)
      .finally(() => setLoading(false));
  }, []);

  if (loading) return <Loading />;
  return <ul>{data.map(/*...*/)}</ul>;
}
```

### The Solution: HOC

```jsx
// ✅ Extract common logic to HOC
function withDataFetching(WrappedComponent, fetchFn) {
  return function WithDataFetching(props) {
    const [loading, setLoading] = useState(true);
    const [data, setData] = useState(null);

    useEffect(() => {
      fetchFn()
        .then(setData)
        .finally(() => setLoading(false));
    }, []);

    if (loading) return <Loading />;
    return <WrappedComponent data={data} {...props} />;
  };
}

// Usage
const UserListWithData = withDataFetching(UserList, fetchUsers);
const ProductListWithData = withDataFetching(ProductList, fetchProducts);
```

---

## 📚 Core Concepts

### 1. Basic HOC Structure

```jsx
function withEnhancement(WrappedComponent) {
  // Return a new component
  return function EnhancedComponent(props) {
    // Add functionality
    const enhancedProps = {
      /* ... */
    };

    // Render wrapped component with enhanced props
    return <WrappedComponent {...props} {...enhancedProps} />;
  };
}
```

### 2. Naming Convention

```jsx
// Convention: "with" prefix
function withAuth(Component) {
  function WithAuth(props) {
    // ...
  }

  // Set display name for DevTools
  WithAuth.displayName = `WithAuth(${
    Component.displayName || Component.name || "Component"
  })`;

  return WithAuth;
}
```

### 3. Passing Props Through

```jsx
// ✅ Always spread props to wrapped component
function withLogger(WrappedComponent) {
  return function WithLogger(props) {
    console.log("Rendered with:", props);
    return <WrappedComponent {...props} />;
  };
}
```

---

## 💻 Practical Examples

### 1. withAuth - Authentication HOC

```jsx
function withAuth(WrappedComponent) {
  return function WithAuth(props) {
    const { user, loading } = useAuth();

    if (loading) {
      return <LoadingSpinner />;
    }

    if (!user) {
      return <Navigate to="/login" />;
    }

    return <WrappedComponent {...props} user={user} />;
  };
}

// Usage
const ProtectedDashboard = withAuth(Dashboard);
const ProtectedSettings = withAuth(Settings);

function App() {
  return (
    <Routes>
      <Route path="/dashboard" element={<ProtectedDashboard />} />
      <Route path="/settings" element={<ProtectedSettings />} />
    </Routes>
  );
}
```

### 2. withTheme - Theme Injection

```jsx
const ThemeContext = createContext({ theme: "light" });

function withTheme(WrappedComponent) {
  return function WithTheme(props) {
    const { theme } = useContext(ThemeContext);

    return <WrappedComponent {...props} theme={theme} />;
  };
}

// Usage
const ThemedButton = withTheme(Button);
const ThemedCard = withTheme(Card);

function Button({ theme, children, ...props }) {
  return (
    <button className={`btn btn-${theme}`} {...props}>
      {children}
    </button>
  );
}
```

### 3. withLoading - Loading State HOC

```jsx
function withLoading(WrappedComponent, loadingProp = "isLoading") {
  return function WithLoading(props) {
    if (props[loadingProp]) {
      return (
        <div className="loading-container">
          <Spinner />
        </div>
      );
    }

    // Remove loading prop before passing to wrapped component
    const { [loadingProp]: _, ...otherProps } = props;
    return <WrappedComponent {...otherProps} />;
  };
}

// Usage
const UserListWithLoading = withLoading(UserList);

function App() {
  const [loading, setLoading] = useState(true);
  const [users, setUsers] = useState([]);

  return <UserListWithLoading isLoading={loading} users={users} />;
}
```

### 4. withErrorBoundary - Error Handling HOC

```jsx
function withErrorBoundary(WrappedComponent, FallbackComponent) {
  return class WithErrorBoundary extends React.Component {
    state = { hasError: false, error: null };

    static getDerivedStateFromError(error) {
      return { hasError: true, error };
    }

    componentDidCatch(error, errorInfo) {
      console.error("Error:", error, errorInfo);
    }

    render() {
      if (this.state.hasError) {
        return FallbackComponent ? (
          <FallbackComponent error={this.state.error} />
        ) : (
          <div>Something went wrong</div>
        );
      }

      return <WrappedComponent {...this.props} />;
    }
  };
}

// Usage
const SafeWidget = withErrorBoundary(Widget, WidgetErrorFallback);
```

### 5. withLogging - Analytics HOC

```jsx
function withLogging(WrappedComponent, componentName) {
  return function WithLogging(props) {
    useEffect(() => {
      console.log(`${componentName} mounted`);
      analytics.track(`${componentName}_view`);

      return () => {
        console.log(`${componentName} unmounted`);
      };
    }, []);

    useEffect(() => {
      console.log(`${componentName} props changed:`, props);
    }, [props]);

    return <WrappedComponent {...props} />;
  };
}

// Usage
const LoggedDashboard = withLogging(Dashboard, "Dashboard");
```

### 6. Composing Multiple HOCs

```jsx
// Multiple HOCs
const EnhancedComponent = withAuth(
  withTheme(withLogging(MyComponent, "MyComponent"))
);

// Using compose utility
function compose(...fns) {
  return (Component) => fns.reduceRight((acc, fn) => fn(acc), Component);
}

const enhance = compose(withAuth, withTheme, (Component) =>
  withLogging(Component, "MyComponent")
);

const EnhancedComponent = enhance(MyComponent);
```

---

## 🧩 Advanced Patterns

### 1. HOC with Configuration

```jsx
function withFetching(config) {
  return function (WrappedComponent) {
    return function WithFetching(props) {
      const [data, setData] = useState(null);
      const [loading, setLoading] = useState(true);
      const [error, setError] = useState(null);

      useEffect(() => {
        const url = typeof config.url === "function"
          ? config.url(props)
          : config.url;

        fetch(url)
          .then((res) => res.json())
          .then(setData)
          .catch(setError)
          .finally(() => setLoading(false));
      }, [config.dependencies?.map((dep) => props[dep])]);

      if (loading) return config.LoadingComponent || <Loading />;
      if (error) return config.ErrorComponent || <Error />;

      return (
        <WrappedComponent
          {...props}
          [config.dataProp || "data"]: data
        />
      );
    };
  };
}

// Usage
const UserProfile = withFetching({
  url: (props) => `/api/users/${props.userId}`,
  dependencies: ["userId"],
  dataProp: "user",
  LoadingComponent: <ProfileSkeleton />,
})(ProfileDisplay);
```

### 2. Forwarding Refs

```jsx
function withMeasure(WrappedComponent) {
  function WithMeasure(props, ref) {
    const [dimensions, setDimensions] = useState({ width: 0, height: 0 });
    const measureRef = useRef(null);

    useEffect(() => {
      if (measureRef.current) {
        const { width, height } = measureRef.current.getBoundingClientRect();
        setDimensions({ width, height });
      }
    }, []);

    return (
      <div ref={measureRef}>
        <WrappedComponent ref={ref} {...props} dimensions={dimensions} />
      </div>
    );
  }

  return forwardRef(WithMeasure);
}
```

### 3. Copying Static Methods

```jsx
import hoistNonReactStatics from "hoist-non-react-statics";

function withEnhancement(WrappedComponent) {
  function WithEnhancement(props) {
    return <WrappedComponent {...props} enhanced />;
  }

  // Copy static methods from WrappedComponent
  hoistNonReactStatics(WithEnhancement, WrappedComponent);

  return WithEnhancement;
}
```

---

## ⚠️ Common Mistakes

### 1. Creating HOCs Inside Components

```jsx
// ❌ Creates new component on every render
function Parent() {
  const Enhanced = withLogger(Child); // New component each render!
  return <Enhanced />;
}

// ✅ Create HOC outside component
const Enhanced = withLogger(Child);

function Parent() {
  return <Enhanced />;
}
```

### 2. Not Passing All Props

```jsx
// ❌ Props are lost
function withWrapper(Component) {
  return function Wrapper() {
    return <Component />; // No props passed!
  };
}

// ✅ Spread all props
function withWrapper(Component) {
  return function Wrapper(props) {
    return <Component {...props} />;
  };
}
```

### 3. Mutating the Original Component

```jsx
// ❌ Mutating original component
function withLogger(Component) {
  Component.prototype.componentDidMount = function () {
    console.log("Mounted!");
  };
  return Component;
}

// ✅ Return new component
function withLogger(Component) {
  return function WithLogger(props) {
    useEffect(() => console.log("Mounted!"), []);
    return <Component {...props} />;
  };
}
```

---

## 🔧 HOCs vs Hooks

### When to Use HOCs

- Cross-cutting concerns (auth, logging)
- Legacy class components
- When you need to wrap JSX

### When to Use Hooks

- Modern React (preferred approach)
- More flexible and composable
- Better TypeScript support

```jsx
// HOC approach
const EnhancedComponent = withAuth(withTheme(withLogging(Component)));

// Hook approach (preferred)
function Component() {
  const user = useAuth();
  const theme = useTheme();
  useLogging("Component");

  // ...
}
```

---

## 🏋️ Practice Exercises

### Exercise 1: withPagination

Create an HOC that adds pagination controls to any list component.

### Exercise 2: withPermissions

Build an HOC that checks user permissions before rendering.

### Exercise 3: withCache

Create an HOC that caches API responses.

---

## ❓ Interview Questions

**Q1: What is a Higher Order Component?**

> An HOC is a function that takes a component and returns a new enhanced component. It's a pattern for reusing component logic without modifying the original component.

**Q2: What are common use cases for HOCs?**

> Authentication/authorization, data fetching, logging/analytics, error handling, theming, and connecting to stores (like Redux's connect).

**Q3: What's the difference between HOCs and Hooks?**

> HOCs wrap components and pass props; Hooks are functions called inside components. Hooks are more flexible, composable, and are the modern preferred approach in React.

**Q4: What are HOC best practices?**

> Don't mutate the wrapped component, pass through all props, forward refs, copy static methods, set displayName, and don't create HOCs inside render.

**Q5: Why are Hooks preferred over HOCs now?**

> Hooks avoid "wrapper hell" (deeply nested components), are easier to compose, have better TypeScript support, and don't create extra components in the tree.

---

## 🔑 Key Takeaways

> ### 💡 Summary
>
> - **HOCs** are functions that enhance components
> - **Use "with" prefix** naming convention
> - **Pass through all props** to wrapped component
> - **Create outside render** to avoid re-mounting
> - **Set displayName** for DevTools
> - **Forward refs** when needed
> - **Prefer Hooks** for new code

---

**Next:** [14 - Render Props Pattern](./14_Render_Props_Pattern.md) - Learn about sharing behavior via render functions!
