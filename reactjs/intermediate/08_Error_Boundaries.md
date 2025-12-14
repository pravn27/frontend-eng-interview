# 08 - Error Boundaries

## 📖 What are Error Boundaries?

Error Boundaries are React components that **catch JavaScript errors** in their child component tree, log those errors, and display a fallback UI instead of crashing the entire app.

```jsx
class ErrorBoundary extends React.Component {
  state = { hasError: false };

  static getDerivedStateFromError(error) {
    return { hasError: true };
  }

  componentDidCatch(error, errorInfo) {
    console.error("Error:", error);
    console.error("Error Info:", errorInfo);
  }

  render() {
    if (this.state.hasError) {
      return <h1>Something went wrong.</h1>;
    }
    return this.props.children;
  }
}

// Usage
<ErrorBoundary>
  <MyComponent />
</ErrorBoundary>;
```

---

## 🎯 Why Error Boundaries?

### Without Error Boundaries

```jsx
// A single error crashes the entire app
function BuggyComponent() {
  throw new Error("Oops!"); // 💥 Entire app crashes
  return <div>Never renders</div>;
}

function App() {
  return (
    <div>
      <Header />
      <BuggyComponent /> {/* Error here crashes everything */}
      <Footer />
    </div>
  );
}
```

### With Error Boundaries

```jsx
// Error is contained, rest of app works
function App() {
  return (
    <div>
      <Header />
      <ErrorBoundary>
        <BuggyComponent /> {/* Error caught, shows fallback */}
      </ErrorBoundary>
      <Footer /> {/* Still works! */}
    </div>
  );
}
```

---

## 📚 Core Concepts

### 1. Class Component Requirement

Error Boundaries **must be class components** (as of React 18). There's no hook equivalent yet.

```jsx
// ✅ Error Boundary - must be class component
class ErrorBoundary extends React.Component {
  state = { hasError: false, error: null };

  static getDerivedStateFromError(error) {
    // Update state to render fallback UI
    return { hasError: true, error };
  }

  componentDidCatch(error, errorInfo) {
    // Log error to monitoring service
    logErrorToService(error, errorInfo);
  }

  render() {
    if (this.state.hasError) {
      return this.props.fallback || <h1>Something went wrong.</h1>;
    }
    return this.props.children;
  }
}
```

### 2. What Errors Are Caught?

| Caught ✅              | Not Caught ❌                     |
| ---------------------- | --------------------------------- |
| Render errors          | Event handlers                    |
| Lifecycle methods      | Async code (setTimeout, promises) |
| Constructor errors     | Server-side rendering             |
| Child component errors | Errors in error boundary itself   |

```jsx
function Component() {
  // ✅ Caught - error during render
  if (someCondition) {
    throw new Error("Render error");
  }

  const handleClick = () => {
    // ❌ NOT caught - error in event handler
    throw new Error("Event handler error");
  };

  useEffect(() => {
    // ❌ NOT caught - async error
    setTimeout(() => {
      throw new Error("Async error");
    }, 1000);
  }, []);

  return <button onClick={handleClick}>Click</button>;
}
```

### 3. Handling Event Handler Errors

```jsx
function Component() {
  const [error, setError] = useState(null);

  const handleClick = () => {
    try {
      riskyOperation();
    } catch (err) {
      setError(err);
    }
  };

  if (error) {
    return <ErrorDisplay error={error} />;
  }

  return <button onClick={handleClick}>Click</button>;
}
```

---

## 💻 Practical Examples

### 1. Reusable Error Boundary

```jsx
import { Component } from "react";

class ErrorBoundary extends Component {
  constructor(props) {
    super(props);
    this.state = { hasError: false, error: null, errorInfo: null };
  }

  static getDerivedStateFromError(error) {
    return { hasError: true, error };
  }

  componentDidCatch(error, errorInfo) {
    this.setState({ errorInfo });

    // Log to error monitoring service
    if (this.props.onError) {
      this.props.onError(error, errorInfo);
    }
  }

  handleReset = () => {
    this.setState({ hasError: false, error: null, errorInfo: null });
  };

  render() {
    if (this.state.hasError) {
      // Custom fallback or default
      if (this.props.fallback) {
        return typeof this.props.fallback === "function"
          ? this.props.fallback({
              error: this.state.error,
              errorInfo: this.state.errorInfo,
              reset: this.handleReset,
            })
          : this.props.fallback;
      }

      return (
        <div className="error-boundary">
          <h2>Something went wrong</h2>
          <details>
            <summary>Error Details</summary>
            <pre>{this.state.error?.toString()}</pre>
            <pre>{this.state.errorInfo?.componentStack}</pre>
          </details>
          <button onClick={this.handleReset}>Try Again</button>
        </div>
      );
    }

    return this.props.children;
  }
}

// Usage with custom fallback
function App() {
  return (
    <ErrorBoundary
      fallback={({ error, reset }) => (
        <div>
          <h1>Oops! {error.message}</h1>
          <button onClick={reset}>Retry</button>
        </div>
      )}
      onError={(error, info) => {
        // Send to Sentry, LogRocket, etc.
        logToService(error, info);
      }}
    >
      <MyApp />
    </ErrorBoundary>
  );
}
```

### 2. Granular Error Boundaries

```jsx
function App() {
  return (
    <div className="app">
      {/* Critical - whole app error boundary */}
      <ErrorBoundary fallback={<AppCrashScreen />}>
        <Header />

        <main>
          {/* Non-critical widget */}
          <ErrorBoundary fallback={<WidgetError />}>
            <RecommendationsWidget />
          </ErrorBoundary>

          {/* Important content */}
          <ErrorBoundary fallback={<ContentError />}>
            <MainContent />
          </ErrorBoundary>

          {/* Non-critical sidebar */}
          <ErrorBoundary fallback={<SidebarError />}>
            <Sidebar />
          </ErrorBoundary>
        </main>

        <Footer />
      </ErrorBoundary>
    </div>
  );
}

function WidgetError() {
  return (
    <div className="widget-error">
      <p>Unable to load recommendations</p>
    </div>
  );
}
```

### 3. Error Boundary with Retry

```jsx
class RetryErrorBoundary extends Component {
  state = { hasError: false, retryCount: 0 };

  static getDerivedStateFromError() {
    return { hasError: true };
  }

  componentDidCatch(error, errorInfo) {
    console.error("Error:", error, errorInfo);
  }

  handleRetry = () => {
    this.setState((state) => ({
      hasError: false,
      retryCount: state.retryCount + 1,
    }));
  };

  render() {
    if (this.state.hasError) {
      return (
        <div className="error-fallback">
          <h2>Something went wrong</h2>
          <p>Attempt {this.state.retryCount + 1}</p>
          <button onClick={this.handleRetry}>Try Again</button>
        </div>
      );
    }

    // Key forces remount of children on retry
    return <div key={this.state.retryCount}>{this.props.children}</div>;
  }
}
```

### 4. Error Boundary with Error Reporting

```jsx
class ReportingErrorBoundary extends Component {
  state = { hasError: false, eventId: null };

  static getDerivedStateFromError() {
    return { hasError: true };
  }

  componentDidCatch(error, errorInfo) {
    // Report to Sentry
    Sentry.withScope((scope) => {
      scope.setExtras(errorInfo);
      const eventId = Sentry.captureException(error);
      this.setState({ eventId });
    });
  }

  render() {
    if (this.state.hasError) {
      return (
        <div className="error-page">
          <h1>We're sorry — something's gone wrong.</h1>
          <p>Our team has been notified.</p>
          <button
            onClick={() =>
              Sentry.showReportDialog({ eventId: this.state.eventId })
            }
          >
            Report feedback
          </button>
        </div>
      );
    }

    return this.props.children;
  }
}
```

### 5. Using react-error-boundary Library

```jsx
import { ErrorBoundary, useErrorBoundary } from "react-error-boundary";

// Fallback component
function ErrorFallback({ error, resetErrorBoundary }) {
  return (
    <div role="alert">
      <p>Something went wrong:</p>
      <pre style={{ color: "red" }}>{error.message}</pre>
      <button onClick={resetErrorBoundary}>Try again</button>
    </div>
  );
}

// Usage
function App() {
  return (
    <ErrorBoundary
      FallbackComponent={ErrorFallback}
      onReset={() => {
        // Reset app state
      }}
      resetKeys={[someKey]}
    >
      <MyComponent />
    </ErrorBoundary>
  );
}

// Using hook to trigger error boundary
function MyComponent() {
  const { showBoundary } = useErrorBoundary();

  const handleClick = async () => {
    try {
      await riskyAsyncOperation();
    } catch (error) {
      showBoundary(error); // Triggers nearest error boundary
    }
  };

  return <button onClick={handleClick}>Do risky thing</button>;
}
```

---

## 🧩 Patterns

### 1. Multiple Layers of Error Boundaries

```jsx
function App() {
  return (
    // App-level: catches catastrophic errors
    <ErrorBoundary fallback={<CriticalErrorPage />}>
      <Router>
        {/* Route-level: catches route errors */}
        <ErrorBoundary fallback={<RouteError />}>
          <Routes>
            <Route
              path="/dashboard"
              element={
                // Feature-level: catches widget errors
                <ErrorBoundary fallback={<DashboardError />}>
                  <Dashboard />
                </ErrorBoundary>
              }
            />
          </Routes>
        </ErrorBoundary>
      </Router>
    </ErrorBoundary>
  );
}
```

### 2. Error Boundary HOC

```jsx
function withErrorBoundary(Component, fallback) {
  return function WrappedComponent(props) {
    return (
      <ErrorBoundary fallback={fallback}>
        <Component {...props} />
      </ErrorBoundary>
    );
  };
}

// Usage
const SafeWidget = withErrorBoundary(Widget, <WidgetError />);
```

### 3. Resetting on Route Change

```jsx
function App() {
  const location = useLocation();

  return (
    <ErrorBoundary key={location.pathname}>
      <Routes>
        <Route path="/" element={<Home />} />
        <Route path="/about" element={<About />} />
      </Routes>
    </ErrorBoundary>
  );
}
```

---

## ⚠️ Common Mistakes

### 1. Expecting Event Handler Errors to Be Caught

```jsx
// ❌ This error is NOT caught by Error Boundary
function Button() {
  const handleClick = () => {
    throw new Error("Click error"); // Not caught!
  };
  return <button onClick={handleClick}>Click</button>;
}

// ✅ Handle event errors with try/catch or state
function Button() {
  const [error, setError] = useState(null);

  const handleClick = () => {
    try {
      riskyOperation();
    } catch (err) {
      setError(err);
    }
  };

  if (error) throw error; // Now it's caught during render

  return <button onClick={handleClick}>Click</button>;
}
```

### 2. Forgetting Async Error Handling

```jsx
// ❌ Async errors NOT caught
function DataFetcher() {
  useEffect(() => {
    fetch("/api/data")
      .then((res) => res.json())
      .catch((err) => {
        throw err; // Not caught by Error Boundary!
      });
  }, []);
}

// ✅ Use state to trigger error during render
function DataFetcher() {
  const [error, setError] = useState(null);

  useEffect(() => {
    fetch("/api/data")
      .then((res) => res.json())
      .catch(setError);
  }, []);

  if (error) throw error; // Caught during render
}
```

### 3. Too Few Error Boundaries

```jsx
// ❌ One error boundary for entire app - one error breaks everything
<ErrorBoundary>
  <EntireApp />
</ErrorBoundary>

// ✅ Strategic placement
<ErrorBoundary>
  <Header />
  <ErrorBoundary>
    <MainContent />
  </ErrorBoundary>
  <ErrorBoundary>
    <Sidebar />
  </ErrorBoundary>
  <Footer />
</ErrorBoundary>
```

---

## 🔧 Best Practices

### 1. Log Errors to Monitoring Service

```jsx
componentDidCatch(error, errorInfo) {
  // Send to error tracking service
  Sentry.captureException(error, { extra: errorInfo });
  LogRocket.captureException(error);

  // Or custom logging
  fetch('/api/log-error', {
    method: 'POST',
    body: JSON.stringify({
      error: error.toString(),
      stack: error.stack,
      componentStack: errorInfo.componentStack,
    }),
  });
}
```

### 2. Provide Helpful Fallbacks

```jsx
function UserFriendlyError({ error, reset }) {
  return (
    <div className="error-container">
      <h2>Oops! Something went wrong</h2>
      <p>We've been notified and are working on a fix.</p>

      <div className="error-actions">
        <button onClick={reset}>Try Again</button>
        <button onClick={() => window.location.reload()}>Refresh Page</button>
        <a href="/">Go Home</a>
      </div>

      {process.env.NODE_ENV === "development" && (
        <details>
          <summary>Technical Details</summary>
          <pre>{error.stack}</pre>
        </details>
      )}
    </div>
  );
}
```

### 3. Test Error Boundaries

```jsx
// Component that throws on demand
function BuggyComponent({ shouldThrow }) {
  if (shouldThrow) {
    throw new Error("Test error");
  }
  return <div>Working</div>;
}

// Test
test("renders fallback when child throws", () => {
  render(
    <ErrorBoundary fallback={<div>Error occurred</div>}>
      <BuggyComponent shouldThrow={true} />
    </ErrorBoundary>
  );

  expect(screen.getByText("Error occurred")).toBeInTheDocument();
});
```

---

## 🏋️ Practice Exercises

### Exercise 1: Widget Error Boundary

Create an error boundary that shows a "Try Again" button and tracks retry attempts.

### Exercise 2: Form Error Recovery

Build a form with error boundary that preserves form data when an error occurs.

### Exercise 3: Error Logging Dashboard

Create an error boundary that sends errors to a mock API and displays an error log.

---

## ❓ Interview Questions

**Q1: What are Error Boundaries and why do we need them?**

> Error Boundaries are React components that catch JavaScript errors in their child tree and display a fallback UI instead of crashing the entire app. They prevent a single component failure from breaking the whole application.

**Q2: What errors do Error Boundaries NOT catch?**

> Event handlers, async code (setTimeout, promises), server-side rendering errors, and errors thrown in the error boundary itself. These need try/catch or state-based error handling.

**Q3: Why must Error Boundaries be class components?**

> Error Boundaries use the lifecycle methods getDerivedStateFromError and componentDidCatch, which don't have hook equivalents. React team hasn't added hook support yet.

**Q4: How do you handle async errors with Error Boundaries?**

> Catch async errors, store them in state, then throw during render: `if (error) throw error;`. This converts async errors to render errors that Error Boundaries can catch.

**Q5: Where should you place Error Boundaries?**

> At multiple levels: app-level for catastrophic errors, route-level for page errors, and component-level for non-critical features. This ensures graceful degradation.

---

## 🔑 Key Takeaways

> ### 💡 Summary
>
> - **Error Boundaries** catch render errors and show fallbacks
> - **Must be class components** (no hooks available)
> - **Don't catch** event handlers or async errors
> - **Use multiple boundaries** for graceful degradation
> - **Log errors** to monitoring services
> - **Provide helpful fallbacks** with recovery options
> - **Use react-error-boundary** for simpler API

---

**Next:** [09 - Refs & ForwardRef](./09_Refs_and_ForwardRef.md) - Learn about advanced ref patterns!
