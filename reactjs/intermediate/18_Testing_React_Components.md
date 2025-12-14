# 18 - Testing React Components

## 📖 Testing Overview

Testing React applications typically involves three types of tests:

1. **Unit Tests** - Test individual functions/components in isolation
2. **Integration Tests** - Test how components work together
3. **End-to-End (E2E) Tests** - Test complete user flows

```jsx
// The Testing Trophy (recommended distribution)
//
//        🏆 E2E Tests (few)
//       ████ Integration Tests (more)
//      ██████ Unit Tests (many)
//     ████████ Static Analysis (TypeScript, ESLint)
```

---

## 🎯 Testing Tools

### Common Stack

- **Jest** - Test runner, assertions, mocking
- **React Testing Library** - DOM-based component testing
- **Vitest** - Vite-native alternative to Jest
- **MSW (Mock Service Worker)** - API mocking
- **Playwright/Cypress** - E2E testing

---

## 📚 React Testing Library (RTL)

### Philosophy

> "The more your tests resemble the way your software is used, the more confidence they can give you."

RTL encourages testing **behavior, not implementation**.

```jsx
// ❌ Testing implementation details
expect(component.state.isOpen).toBe(true);
expect(wrapper.find("div.dropdown-menu").length).toBe(1);

// ✅ Testing user behavior
expect(screen.getByRole("menu")).toBeVisible();
expect(screen.getByText("Option 1")).toBeInTheDocument();
```

---

## 💻 Basic Testing

### 1. Rendering Components

```jsx
import { render, screen } from "@testing-library/react";
import Greeting from "./Greeting";

test("renders greeting message", () => {
  render(<Greeting name="Alice" />);

  expect(screen.getByText("Hello, Alice!")).toBeInTheDocument();
});
```

### 2. Finding Elements

```jsx
// By Role (preferred)
screen.getByRole("button", { name: /submit/i });
screen.getByRole("textbox", { name: /email/i });
screen.getByRole("heading", { level: 1 });

// By Label Text
screen.getByLabelText("Email");

// By Placeholder
screen.getByPlaceholderText("Enter your email");

// By Text
screen.getByText("Submit");
screen.getByText(/submit/i); // Case insensitive

// By Test ID (last resort)
screen.getByTestId("custom-element");

// Query variants
// getBy* - throws if not found (use for assertions)
// queryBy* - returns null if not found (use for absence checks)
// findBy* - returns promise, waits for element
```

### 3. User Interactions

```jsx
import { render, screen } from "@testing-library/react";
import userEvent from "@testing-library/user-event";
import Counter from "./Counter";

test("increments counter on click", async () => {
  const user = userEvent.setup();
  render(<Counter />);

  expect(screen.getByText("Count: 0")).toBeInTheDocument();

  await user.click(screen.getByRole("button", { name: /increment/i }));

  expect(screen.getByText("Count: 1")).toBeInTheDocument();
});
```

### 4. Form Testing

```jsx
import { render, screen } from "@testing-library/react";
import userEvent from "@testing-library/user-event";
import LoginForm from "./LoginForm";

test("submits form with user data", async () => {
  const user = userEvent.setup();
  const handleSubmit = jest.fn();

  render(<LoginForm onSubmit={handleSubmit} />);

  // Fill in form
  await user.type(screen.getByLabelText(/email/i), "test@example.com");
  await user.type(screen.getByLabelText(/password/i), "password123");

  // Submit
  await user.click(screen.getByRole("button", { name: /log in/i }));

  // Assert
  expect(handleSubmit).toHaveBeenCalledWith({
    email: "test@example.com",
    password: "password123",
  });
});

test("shows validation errors", async () => {
  const user = userEvent.setup();
  render(<LoginForm onSubmit={jest.fn()} />);

  // Submit empty form
  await user.click(screen.getByRole("button", { name: /log in/i }));

  // Check for error messages
  expect(screen.getByText(/email is required/i)).toBeInTheDocument();
  expect(screen.getByText(/password is required/i)).toBeInTheDocument();
});
```

---

## 💻 Practical Examples

### 1. Testing Component with State

```jsx
// Toggle.jsx
function Toggle({ onToggle }) {
  const [isOn, setIsOn] = useState(false);

  const handleClick = () => {
    setIsOn(!isOn);
    onToggle?.(!isOn);
  };

  return <button onClick={handleClick}>{isOn ? "ON" : "OFF"}</button>;
}

// Toggle.test.jsx
test("toggles state on click", async () => {
  const user = userEvent.setup();
  const handleToggle = jest.fn();

  render(<Toggle onToggle={handleToggle} />);

  // Initial state
  expect(screen.getByRole("button")).toHaveTextContent("OFF");

  // First click
  await user.click(screen.getByRole("button"));
  expect(screen.getByRole("button")).toHaveTextContent("ON");
  expect(handleToggle).toHaveBeenCalledWith(true);

  // Second click
  await user.click(screen.getByRole("button"));
  expect(screen.getByRole("button")).toHaveTextContent("OFF");
  expect(handleToggle).toHaveBeenCalledWith(false);
});
```

### 2. Testing Async Operations

```jsx
// UserProfile.jsx
function UserProfile({ userId }) {
  const [user, setUser] = useState(null);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);

  useEffect(() => {
    fetch(`/api/users/${userId}`)
      .then((res) => res.json())
      .then(setUser)
      .catch(setError)
      .finally(() => setLoading(false));
  }, [userId]);

  if (loading) return <div>Loading...</div>;
  if (error) return <div>Error: {error.message}</div>;
  return <div>Hello, {user.name}</div>;
}

// UserProfile.test.jsx
import { rest } from "msw";
import { setupServer } from "msw/node";

const server = setupServer(
  rest.get("/api/users/:id", (req, res, ctx) => {
    return res(ctx.json({ id: 1, name: "Alice" }));
  })
);

beforeAll(() => server.listen());
afterEach(() => server.resetHandlers());
afterAll(() => server.close());

test("renders user data after loading", async () => {
  render(<UserProfile userId={1} />);

  // Check loading state
  expect(screen.getByText("Loading...")).toBeInTheDocument();

  // Wait for data
  expect(await screen.findByText("Hello, Alice")).toBeInTheDocument();
});

test("renders error on fetch failure", async () => {
  server.use(
    rest.get("/api/users/:id", (req, res, ctx) => {
      return res(ctx.status(500), ctx.json({ message: "Server error" }));
    })
  );

  render(<UserProfile userId={1} />);

  expect(await screen.findByText(/error/i)).toBeInTheDocument();
});
```

### 3. Testing with Context

```jsx
// ThemeButton.test.jsx
import { ThemeProvider } from "./ThemeContext";

function renderWithTheme(ui, { theme = "light", ...options } = {}) {
  return render(
    <ThemeProvider initialTheme={theme}>{ui}</ThemeProvider>,
    options
  );
}

test("renders with dark theme", () => {
  renderWithTheme(<ThemeButton />, { theme: "dark" });

  expect(screen.getByRole("button")).toHaveClass("dark");
});
```

### 4. Testing Custom Hooks

```jsx
import { renderHook, act } from "@testing-library/react";
import useCounter from "./useCounter";

test("increments counter", () => {
  const { result } = renderHook(() => useCounter(0));

  expect(result.current.count).toBe(0);

  act(() => {
    result.current.increment();
  });

  expect(result.current.count).toBe(1);
});

test("decrements counter", () => {
  const { result } = renderHook(() => useCounter(10));

  act(() => {
    result.current.decrement();
  });

  expect(result.current.count).toBe(9);
});
```

### 5. Testing React Router

```jsx
import { MemoryRouter, Routes, Route } from "react-router-dom";

function renderWithRouter(ui, { route = "/" } = {}) {
  return render(
    <MemoryRouter initialEntries={[route]}>
      <Routes>
        <Route path="/" element={<Home />} />
        <Route path="/about" element={<About />} />
        <Route path="/users/:id" element={ui} />
      </Routes>
    </MemoryRouter>
  );
}

test("renders user page with route params", () => {
  renderWithRouter(<UserPage />, { route: "/users/123" });

  expect(screen.getByText("User ID: 123")).toBeInTheDocument();
});
```

### 6. Testing Modal/Portal

```jsx
test("renders modal content", async () => {
  const user = userEvent.setup();
  render(<ModalTrigger />);

  // Click to open modal
  await user.click(screen.getByRole("button", { name: /open modal/i }));

  // Modal content is visible
  expect(screen.getByRole("dialog")).toBeInTheDocument();
  expect(screen.getByText("Modal Title")).toBeInTheDocument();

  // Close modal
  await user.click(screen.getByRole("button", { name: /close/i }));

  // Modal is gone
  expect(screen.queryByRole("dialog")).not.toBeInTheDocument();
});
```

---

## 🧩 Testing Patterns

### 1. Test Utils and Custom Renders

```jsx
// test-utils.jsx
import { render } from "@testing-library/react";
import { QueryClient, QueryClientProvider } from "@tanstack/react-query";
import { ThemeProvider } from "./ThemeContext";
import { AuthProvider } from "./AuthContext";

function AllProviders({ children }) {
  const queryClient = new QueryClient({
    defaultOptions: {
      queries: { retry: false },
    },
  });

  return (
    <QueryClientProvider client={queryClient}>
      <AuthProvider>
        <ThemeProvider>{children}</ThemeProvider>
      </AuthProvider>
    </QueryClientProvider>
  );
}

export function renderWithProviders(ui, options) {
  return render(ui, { wrapper: AllProviders, ...options });
}

export * from "@testing-library/react";
```

### 2. Mocking Modules

```jsx
// Mock a module
jest.mock("./api", () => ({
  fetchUser: jest.fn(() => Promise.resolve({ id: 1, name: "Mock User" })),
}));

// Mock a hook
jest.mock("./useAuth", () => ({
  useAuth: () => ({
    user: { id: 1, name: "Test User" },
    isAuthenticated: true,
  }),
}));

// Mock React Router
jest.mock("react-router-dom", () => ({
  ...jest.requireActual("react-router-dom"),
  useNavigate: () => jest.fn(),
  useParams: () => ({ id: "123" }),
}));
```

### 3. Snapshot Testing

```jsx
test("matches snapshot", () => {
  const { container } = render(<Card title="Test" body="Content" />);

  expect(container).toMatchSnapshot();
});

// Use sparingly - can become brittle
```

---

## ⚠️ Common Mistakes

### 1. Testing Implementation Details

```jsx
// ❌ Testing internal state
expect(component.state.isLoading).toBe(true);

// ❌ Testing class names
expect(element).toHaveClass("btn-primary");

// ✅ Test what users see
expect(screen.getByRole("button")).toBeDisabled();
expect(screen.getByText("Loading...")).toBeInTheDocument();
```

### 2. Not Waiting for Async

```jsx
// ❌ Assertion runs before async completes
render(<AsyncComponent />);
expect(screen.getByText("Data")).toBeInTheDocument(); // Fails!

// ✅ Wait for element
expect(await screen.findByText("Data")).toBeInTheDocument();
```

### 3. Using getBy for Absence Checks

```jsx
// ❌ Throws error if not found
expect(screen.getByText("Error")).not.toBeInTheDocument();

// ✅ Use queryBy for absence checks
expect(screen.queryByText("Error")).not.toBeInTheDocument();
```

---

## 🔧 Best Practices

### 1. Test Behavior, Not Implementation

```jsx
// Test what users do and see
// - Click buttons
// - Fill forms
// - See content
// - Navigate pages
```

### 2. Use Descriptive Test Names

```jsx
// ❌ Vague
test("works correctly", () => {});

// ✅ Descriptive
test("disables submit button when form is invalid", () => {});
test("shows error message when API call fails", () => {});
```

### 3. Arrange-Act-Assert Pattern

```jsx
test("increments counter", async () => {
  // Arrange
  const user = userEvent.setup();
  render(<Counter initialCount={0} />);

  // Act
  await user.click(screen.getByRole("button", { name: /increment/i }));

  // Assert
  expect(screen.getByText("Count: 1")).toBeInTheDocument();
});
```

### 4. One Assertion Per Concept

```jsx
// ❌ Too many assertions in one test
test("form works", async () => {
  // 20 assertions covering everything
});

// ✅ Focused tests
test("shows validation error for empty email", () => {});
test("shows validation error for invalid email format", () => {});
test("enables submit when form is valid", () => {});
test("calls onSubmit with form data", () => {});
```

---

## 🏋️ Practice Exercises

### Exercise 1: Todo List

Write tests for a complete Todo list (add, complete, delete, filter).

### Exercise 2: Form Validation

Test a registration form with complex validation rules.

### Exercise 3: API Integration

Test a component that fetches and displays paginated data.

---

## ❓ Interview Questions

**Q1: Why use React Testing Library over Enzyme?**

> RTL encourages testing behavior (what users do and see) rather than implementation details. It uses actual DOM queries, making tests more realistic and resilient to refactoring.

**Q2: What's the difference between getBy, queryBy, and findBy?**

> getBy throws if not found (use for elements that should exist). queryBy returns null (use for absence checks). findBy returns a promise and waits (use for async elements).

**Q3: How do you test async operations?**

> Use findBy queries which wait for elements, or use waitFor for custom conditions. Mock API calls with MSW or Jest mocks.

**Q4: How do you test components with Context?**

> Create a custom render function that wraps components with necessary providers. Pass different context values to test various scenarios.

**Q5: What makes a good test?**

> Tests behavior not implementation, is isolated and deterministic, has clear descriptions, fails for the right reasons, and is maintainable.

---

## 🔑 Key Takeaways

> ### 💡 Summary
>
> - **Test behavior** - What users see and do
> - **Use RTL queries** - getByRole is preferred
> - **userEvent over fireEvent** - More realistic interactions
> - **findBy for async** - Wait for elements to appear
> - **queryBy for absence** - Check elements don't exist
> - **Mock APIs with MSW** - Realistic API testing
> - **Custom render** - Wrap with providers once

---

## 🎉 Congratulations!

You've completed the **ReactJS Intermediate Curriculum**!

### What's Next?

- Build real projects using these patterns
- Explore advanced topics (Server Components, React 19 features)
- Practice with coding challenges
- Contribute to open source React projects

**Keep learning and building! 🚀**
