# 15. React Router Basics

> Client-side navigation - building multi-page experiences in SPAs.

---

## 🎯 What is React Router?

**React Router** is the standard routing library for React. It enables navigation between different views/pages in your application without full page reloads.

```jsx
import { BrowserRouter, Routes, Route, Link } from "react-router-dom";

function App() {
  return (
    <BrowserRouter>
      <nav>
        <Link to="/">Home</Link>
        <Link to="/about">About</Link>
      </nav>
      <Routes>
        <Route path="/" element={<Home />} />
        <Route path="/about" element={<About />} />
      </Routes>
    </BrowserRouter>
  );
}
```

---

## 🤔 Why Client-Side Routing?

Benefits of React Router:

- ✅ **Fast navigation** - No full page reloads
- ✅ **Maintains state** - App state persists across navigation
- ✅ **Better UX** - Instant transitions, loading states
- ✅ **Deep linking** - Shareable URLs for any page
- ✅ **Browser integration** - Back/forward buttons work

---

## 📚 Core Concepts

### 1. Installation and Setup

```bash
# Install React Router
npm install react-router-dom
```

```jsx
// main.jsx
import { BrowserRouter } from "react-router-dom";

createRoot(document.getElementById("root")).render(
  <BrowserRouter>
    <App />
  </BrowserRouter>
);
```

### 2. Basic Route Configuration

```jsx
import { Routes, Route } from "react-router-dom";

function App() {
  return (
    <Routes>
      {/* Exact path match */}
      <Route path="/" element={<Home />} />
      <Route path="/about" element={<About />} />
      <Route path="/contact" element={<Contact />} />

      {/* 404 - No match */}
      <Route path="*" element={<NotFound />} />
    </Routes>
  );
}
```

### 3. Navigation Components

```jsx
import { Link, NavLink, useNavigate } from "react-router-dom";

function Navigation() {
  const navigate = useNavigate();

  return (
    <nav>
      {/* Basic Link */}
      <Link to="/">Home</Link>

      {/* Link with state */}
      <Link to="/product/123" state={{ from: "navigation" }}>
        Product
      </Link>

      {/* NavLink - adds active class automatically */}
      <NavLink
        to="/about"
        className={({ isActive }) => (isActive ? "active" : "")}
      >
        About
      </NavLink>

      {/* NavLink with style */}
      <NavLink
        to="/contact"
        style={({ isActive }) => ({
          fontWeight: isActive ? "bold" : "normal",
        })}
      >
        Contact
      </NavLink>

      {/* Programmatic navigation */}
      <button onClick={() => navigate("/dashboard")}>Go to Dashboard</button>

      {/* Go back */}
      <button onClick={() => navigate(-1)}>Go Back</button>
    </nav>
  );
}
```

### 4. Route Parameters

```jsx
import { Routes, Route, useParams } from "react-router-dom";

function App() {
  return (
    <Routes>
      {/* Dynamic segment with : */}
      <Route path="/users/:userId" element={<UserProfile />} />
      <Route path="/products/:category/:productId" element={<Product />} />

      {/* Optional parameter */}
      <Route path="/posts/:postId?" element={<PostList />} />
    </Routes>
  );
}

function UserProfile() {
  // Access URL parameters
  const { userId } = useParams();

  return <h1>User ID: {userId}</h1>;
}

function Product() {
  const { category, productId } = useParams();

  return (
    <div>
      <p>Category: {category}</p>
      <p>Product ID: {productId}</p>
    </div>
  );
}
```

### 5. Query Parameters

```jsx
import { useSearchParams } from "react-router-dom";

function SearchResults() {
  const [searchParams, setSearchParams] = useSearchParams();

  // Read query params: /search?q=react&page=1
  const query = searchParams.get("q");
  const page = searchParams.get("page") || "1";

  // Update query params
  const handlePageChange = (newPage) => {
    setSearchParams({ q: query, page: newPage });
  };

  // Update single param
  const handleSortChange = (sort) => {
    searchParams.set("sort", sort);
    setSearchParams(searchParams);
  };

  return (
    <div>
      <p>Searching for: {query}</p>
      <p>Page: {page}</p>
      <button onClick={() => handlePageChange(Number(page) + 1)}>
        Next Page
      </button>
    </div>
  );
}
```

### 6. Nested Routes

```jsx
import { Routes, Route, Outlet, Link } from "react-router-dom";

function App() {
  return (
    <Routes>
      <Route path="/" element={<Layout />}>
        {/* index route - matches / exactly */}
        <Route index element={<Home />} />
        <Route path="about" element={<About />} />

        {/* Nested routes under /dashboard */}
        <Route path="dashboard" element={<Dashboard />}>
          <Route index element={<DashboardHome />} />
          <Route path="profile" element={<Profile />} />
          <Route path="settings" element={<Settings />} />
        </Route>
      </Route>
    </Routes>
  );
}

function Layout() {
  return (
    <div>
      <header>
        <nav>
          <Link to="/">Home</Link>
          <Link to="/about">About</Link>
          <Link to="/dashboard">Dashboard</Link>
        </nav>
      </header>
      <main>
        {/* Child routes render here */}
        <Outlet />
      </main>
      <footer>© 2025</footer>
    </div>
  );
}

function Dashboard() {
  return (
    <div className="dashboard">
      <aside>
        <nav>
          <Link to="/dashboard">Overview</Link>
          <Link to="/dashboard/profile">Profile</Link>
          <Link to="/dashboard/settings">Settings</Link>
        </nav>
      </aside>
      <section>
        {/* Nested routes render here */}
        <Outlet />
      </section>
    </div>
  );
}
```

### 7. Protected Routes

```jsx
import { Navigate, useLocation } from "react-router-dom";

// Auth context (simplified)
function useAuth() {
  // In real app, this would check actual auth state
  return { user: null, isLoading: false };
}

function ProtectedRoute({ children }) {
  const { user, isLoading } = useAuth();
  const location = useLocation();

  if (isLoading) {
    return <div>Loading...</div>;
  }

  if (!user) {
    // Redirect to login, preserving intended destination
    return <Navigate to="/login" state={{ from: location }} replace />;
  }

  return children;
}

function App() {
  return (
    <Routes>
      <Route path="/" element={<Home />} />
      <Route path="/login" element={<Login />} />

      {/* Protected routes */}
      <Route
        path="/dashboard"
        element={
          <ProtectedRoute>
            <Dashboard />
          </ProtectedRoute>
        }
      />

      {/* Or wrap multiple routes */}
      <Route
        element={
          <ProtectedRoute>
            <Outlet />
          </ProtectedRoute>
        }
      >
        <Route path="/profile" element={<Profile />} />
        <Route path="/settings" element={<Settings />} />
      </Route>
    </Routes>
  );
}

function Login() {
  const location = useLocation();
  const navigate = useNavigate();

  const handleLogin = () => {
    // After login, redirect to original destination
    const from = location.state?.from?.pathname || "/dashboard";
    navigate(from, { replace: true });
  };

  return (
    <div>
      <h1>Login</h1>
      <button onClick={handleLogin}>Log In</button>
    </div>
  );
}
```

### 8. Programmatic Navigation

```jsx
import { useNavigate, useLocation } from "react-router-dom";

function ProductPage() {
  const navigate = useNavigate();
  const location = useLocation();

  // Navigate to path
  const goToCart = () => {
    navigate("/cart");
  };

  // Navigate with state
  const goToCheckout = () => {
    navigate("/checkout", {
      state: { from: "product", productId: 123 },
    });
  };

  // Replace history (can't go back)
  const goToConfirmation = () => {
    navigate("/confirmation", { replace: true });
  };

  // Go back
  const goBack = () => {
    navigate(-1);
  };

  // Go forward
  const goForward = () => {
    navigate(1);
  };

  // Access current location
  console.log(location.pathname); // '/product/123'
  console.log(location.search); // '?color=blue'
  console.log(location.state); // Any state passed

  return (
    <div>
      <button onClick={goBack}>← Back</button>
      <button onClick={goToCart}>Add to Cart</button>
      <button onClick={goToCheckout}>Checkout</button>
    </div>
  );
}
```

### 9. Loading States and Error Handling

```jsx
import {
  useLoaderData,
  useNavigation,
  useRouteError,
  isRouteErrorResponse,
} from "react-router-dom";

// Route with loader (data fetching)
const router = createBrowserRouter([
  {
    path: "/users/:userId",
    element: <UserProfile />,
    loader: async ({ params }) => {
      const response = await fetch(`/api/users/${params.userId}`);
      if (!response.ok) throw new Response("Not Found", { status: 404 });
      return response.json();
    },
    errorElement: <ErrorBoundary />,
  },
]);

function UserProfile() {
  const user = useLoaderData(); // Data from loader
  const navigation = useNavigation();

  if (navigation.state === "loading") {
    return <LoadingSpinner />;
  }

  return (
    <div>
      <h1>{user.name}</h1>
      <p>{user.email}</p>
    </div>
  );
}

function ErrorBoundary() {
  const error = useRouteError();

  if (isRouteErrorResponse(error)) {
    if (error.status === 404) {
      return <h1>User not found!</h1>;
    }
    return <h1>Error: {error.status}</h1>;
  }

  return <h1>Something went wrong!</h1>;
}
```

---

## 💻 Demo Code

### Complete Router Setup

```jsx
import {
  BrowserRouter,
  Routes,
  Route,
  Link,
  NavLink,
  Outlet,
  useParams,
  useSearchParams,
  useNavigate,
  useLocation,
  Navigate,
} from "react-router-dom";
import { useState, createContext, useContext } from "react";

// ========================================
// AUTH CONTEXT
// ========================================

const AuthContext = createContext(null);

function AuthProvider({ children }) {
  const [user, setUser] = useState(null);

  const login = (userData) => setUser(userData);
  const logout = () => setUser(null);

  return (
    <AuthContext.Provider value={{ user, login, logout }}>
      {children}
    </AuthContext.Provider>
  );
}

function useAuth() {
  return useContext(AuthContext);
}

// ========================================
// PROTECTED ROUTE
// ========================================

function ProtectedRoute({ children }) {
  const { user } = useAuth();
  const location = useLocation();

  if (!user) {
    return <Navigate to="/login" state={{ from: location }} replace />;
  }

  return children;
}

// ========================================
// LAYOUT COMPONENTS
// ========================================

function RootLayout() {
  const { user, logout } = useAuth();

  return (
    <div className="app">
      <header>
        <nav>
          <NavLink to="/" end>
            Home
          </NavLink>
          <NavLink to="/products">Products</NavLink>
          <NavLink to="/about">About</NavLink>

          {user ? (
            <>
              <NavLink to="/dashboard">Dashboard</NavLink>
              <button onClick={logout}>Logout ({user.name})</button>
            </>
          ) : (
            <NavLink to="/login">Login</NavLink>
          )}
        </nav>
      </header>

      <main>
        <Outlet />
      </main>

      <footer>
        <p>© 2025 My App</p>
      </footer>
    </div>
  );
}

function DashboardLayout() {
  return (
    <div className="dashboard-layout">
      <aside>
        <nav>
          <NavLink to="/dashboard" end>
            Overview
          </NavLink>
          <NavLink to="/dashboard/profile">Profile</NavLink>
          <NavLink to="/dashboard/settings">Settings</NavLink>
          <NavLink to="/dashboard/orders">Orders</NavLink>
        </nav>
      </aside>
      <section>
        <Outlet />
      </section>
    </div>
  );
}

// ========================================
// PAGE COMPONENTS
// ========================================

function Home() {
  return (
    <div>
      <h1>Welcome Home</h1>
      <p>This is the home page.</p>
    </div>
  );
}

function About() {
  return (
    <div>
      <h1>About Us</h1>
      <p>Learn more about our company.</p>
    </div>
  );
}

function Login() {
  const { login } = useAuth();
  const navigate = useNavigate();
  const location = useLocation();

  const from = location.state?.from?.pathname || "/dashboard";

  const handleLogin = () => {
    login({ id: 1, name: "John", email: "john@example.com" });
    navigate(from, { replace: true });
  };

  return (
    <div>
      <h1>Login</h1>
      <p>You must log in to view the dashboard.</p>
      <button onClick={handleLogin}>Log In</button>
    </div>
  );
}

// Products with search/filter
function Products() {
  const [searchParams, setSearchParams] = useSearchParams();
  const category = searchParams.get("category") || "all";
  const sort = searchParams.get("sort") || "name";

  const products = [
    { id: 1, name: "Laptop", category: "electronics", price: 999 },
    { id: 2, name: "T-Shirt", category: "clothing", price: 29 },
    { id: 3, name: "Headphones", category: "electronics", price: 199 },
    { id: 4, name: "Jeans", category: "clothing", price: 59 },
  ];

  const filteredProducts = products
    .filter((p) => category === "all" || p.category === category)
    .sort((a, b) =>
      sort === "price" ? a.price - b.price : a.name.localeCompare(b.name)
    );

  return (
    <div>
      <h1>Products</h1>

      <div className="filters">
        <select
          value={category}
          onChange={(e) => {
            searchParams.set("category", e.target.value);
            setSearchParams(searchParams);
          }}
        >
          <option value="all">All Categories</option>
          <option value="electronics">Electronics</option>
          <option value="clothing">Clothing</option>
        </select>

        <select
          value={sort}
          onChange={(e) => {
            searchParams.set("sort", e.target.value);
            setSearchParams(searchParams);
          }}
        >
          <option value="name">Sort by Name</option>
          <option value="price">Sort by Price</option>
        </select>
      </div>

      <div className="product-list">
        {filteredProducts.map((product) => (
          <Link
            key={product.id}
            to={`/products/${product.id}`}
            className="product-card"
          >
            <h3>{product.name}</h3>
            <p>${product.price}</p>
          </Link>
        ))}
      </div>
    </div>
  );
}

function ProductDetail() {
  const { productId } = useParams();
  const navigate = useNavigate();

  // In real app, fetch product by ID
  const product = {
    id: productId,
    name: `Product ${productId}`,
    description: "A great product",
    price: 99.99,
  };

  return (
    <div>
      <button onClick={() => navigate(-1)}>← Back</button>
      <h1>{product.name}</h1>
      <p>{product.description}</p>
      <p>${product.price}</p>
      <button onClick={() => navigate("/cart")}>Add to Cart</button>
    </div>
  );
}

function DashboardHome() {
  return <h2>Dashboard Overview</h2>;
}

function Profile() {
  const { user } = useAuth();
  return (
    <div>
      <h2>Profile</h2>
      <p>Name: {user?.name}</p>
      <p>Email: {user?.email}</p>
    </div>
  );
}

function Settings() {
  return <h2>Settings</h2>;
}

function Orders() {
  return <h2>Your Orders</h2>;
}

function NotFound() {
  return (
    <div>
      <h1>404 - Page Not Found</h1>
      <Link to="/">Go Home</Link>
    </div>
  );
}

// ========================================
// APP WITH ROUTES
// ========================================

function App() {
  return (
    <AuthProvider>
      <BrowserRouter>
        <Routes>
          <Route path="/" element={<RootLayout />}>
            {/* Public routes */}
            <Route index element={<Home />} />
            <Route path="about" element={<About />} />
            <Route path="login" element={<Login />} />
            <Route path="products" element={<Products />} />
            <Route path="products/:productId" element={<ProductDetail />} />

            {/* Protected routes */}
            <Route
              path="dashboard"
              element={
                <ProtectedRoute>
                  <DashboardLayout />
                </ProtectedRoute>
              }
            >
              <Route index element={<DashboardHome />} />
              <Route path="profile" element={<Profile />} />
              <Route path="settings" element={<Settings />} />
              <Route path="orders" element={<Orders />} />
            </Route>

            {/* 404 */}
            <Route path="*" element={<NotFound />} />
          </Route>
        </Routes>
      </BrowserRouter>
    </AuthProvider>
  );
}

export default App;
```

---

## ✅ Best Practices

### 1. Use NavLink for Navigation Menus

```jsx
<NavLink
  to="/dashboard"
  className={({ isActive }) => (isActive ? "nav-active" : "")}
>
  Dashboard
</NavLink>
```

### 2. Preserve State on Redirect

```jsx
<Navigate to="/login" state={{ from: location }} replace />
```

### 3. Use `replace` for Login Redirects

```jsx
navigate("/dashboard", { replace: true }); // Can't go back to login
```

### 4. Handle 404s

```jsx
<Route path="*" element={<NotFound />} />
```

---

## ❌ Common Mistakes

### 1. Forgetting BrowserRouter

```jsx
// ❌ Error: useNavigate() may only be used in context of Router
<App />

// ✅ Wrap with BrowserRouter
<BrowserRouter>
  <App />
</BrowserRouter>
```

### 2. Using `<a>` Instead of `<Link>`

```jsx
// ❌ Causes full page reload
<a href="/about">About</a>

// ✅ Client-side navigation
<Link to="/about">About</Link>
```

### 3. Wrong Path Matching

```jsx
// ❌ /dashboard matches /dashboard/profile too
<Route path="/dashboard">

// ✅ Use nested routes or exact matching
<Route path="/dashboard" element={...}>
  <Route index element={...} />
  <Route path="profile" element={...} />
</Route>
```

---

## 🎤 Interview Q&A

**Q1: What is React Router and why use it?**

> React Router enables client-side routing in SPAs. It allows navigation between views without page reloads, maintaining app state and providing a better user experience.

**Q2: Difference between Link and NavLink?**

> Both prevent full page reloads. NavLink adds active styling capabilities - it can apply classes or styles when the link matches the current URL.

**Q3: How do you protect routes?**

> Create a ProtectedRoute component that checks authentication state. If not authenticated, redirect to login using Navigate, preserving the original destination in state.

**Q4: How do you access URL parameters?**

> Use the `useParams` hook: `const { id } = useParams()`. For query strings, use `useSearchParams`.

**Q5: What's the difference between `navigate()` and `<Navigate>`?**

> `navigate()` is for programmatic navigation in event handlers. `<Navigate>` is a component for declarative redirects in JSX, typically used for conditional redirects.

---

## 📎 References

- [React Router Documentation](https://reactrouter.com/)
- [React Router Tutorial](https://reactrouter.com/en/main/start/tutorial)
- [React Router Examples](https://reactrouter.com/en/main/start/examples)

---

**Previous Topic**: [← 14. Forms in React](./14_Forms_in_React.md)

**Next Topic**: [16. Styling in React →](./16_Styling_in_React.md)
