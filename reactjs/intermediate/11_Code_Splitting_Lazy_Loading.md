# 11 - Code Splitting & Lazy Loading

## 📖 What is Code Splitting?

Code splitting is a technique that splits your bundle into smaller chunks that can be loaded **on demand**. React provides `lazy()` and `Suspense` for component-level code splitting.

```jsx
import { lazy, Suspense } from "react";

// Instead of static import
// import Dashboard from './Dashboard';

// Dynamic import with lazy
const Dashboard = lazy(() => import("./Dashboard"));

function App() {
  return (
    <Suspense fallback={<Loading />}>
      <Dashboard />
    </Suspense>
  );
}
```

---

## 🎯 Why Code Splitting?

### The Problem: Large Bundle Size

```
Without code splitting:
bundle.js - 2.5MB (users wait for entire bundle)

With code splitting:
main.js - 200KB (loads immediately)
dashboard.chunk.js - 500KB (loads when needed)
admin.chunk.js - 800KB (loads when needed)
```

### Benefits

- ✅ **Faster initial load** - Smaller initial bundle
- ✅ **Better performance** - Load code when needed
- ✅ **Reduced memory** - Don't load unused code
- ✅ **Better caching** - Changed chunks update independently

---

## 📚 Core Concepts

### 1. React.lazy()

```jsx
// Basic usage
const MyComponent = lazy(() => import("./MyComponent"));

// With named exports (requires wrapper)
const MyComponent = lazy(() =>
  import("./MyComponents").then((module) => ({
    default: module.MyComponent,
  }))
);

// With artificial delay for testing
const MyComponent = lazy(
  () =>
    new Promise((resolve) => {
      setTimeout(() => {
        resolve(import("./MyComponent"));
      }, 1000);
    })
);
```

### 2. Suspense

```jsx
import { Suspense } from "react";

// Single lazy component
<Suspense fallback={<Loading />}>
  <LazyComponent />
</Suspense>

// Multiple lazy components
<Suspense fallback={<Loading />}>
  <LazyHeader />
  <LazyContent />
  <LazyFooter />
</Suspense>

// Nested Suspense for granular loading
<Suspense fallback={<PageLoader />}>
  <Header />
  <Suspense fallback={<ContentLoader />}>
    <MainContent />
  </Suspense>
</Suspense>
```

---

## 💻 Practical Examples

### 1. Route-Based Code Splitting

```jsx
import { lazy, Suspense } from "react";
import { BrowserRouter, Routes, Route } from "react-router-dom";

// Lazy load routes
const Home = lazy(() => import("./pages/Home"));
const Dashboard = lazy(() => import("./pages/Dashboard"));
const Settings = lazy(() => import("./pages/Settings"));
const Admin = lazy(() => import("./pages/Admin"));

function App() {
  return (
    <BrowserRouter>
      <Suspense fallback={<PageLoader />}>
        <Routes>
          <Route path="/" element={<Home />} />
          <Route path="/dashboard" element={<Dashboard />} />
          <Route path="/settings" element={<Settings />} />
          <Route path="/admin" element={<Admin />} />
        </Routes>
      </Suspense>
    </BrowserRouter>
  );
}

function PageLoader() {
  return (
    <div className="page-loader">
      <div className="spinner" />
      <p>Loading...</p>
    </div>
  );
}
```

### 2. Component-Based Code Splitting

```jsx
import { lazy, Suspense, useState } from "react";

// Heavy components loaded on demand
const ChartComponent = lazy(() => import("./ChartComponent"));
const DataTable = lazy(() => import("./DataTable"));
const RichTextEditor = lazy(() => import("./RichTextEditor"));

function Dashboard() {
  const [showChart, setShowChart] = useState(false);
  const [showEditor, setShowEditor] = useState(false);

  return (
    <div>
      <h1>Dashboard</h1>

      <button onClick={() => setShowChart(true)}>Show Analytics</button>
      {showChart && (
        <Suspense fallback={<ChartPlaceholder />}>
          <ChartComponent />
        </Suspense>
      )}

      <button onClick={() => setShowEditor(true)}>Open Editor</button>
      {showEditor && (
        <Suspense fallback={<EditorPlaceholder />}>
          <RichTextEditor />
        </Suspense>
      )}

      <Suspense fallback={<TablePlaceholder />}>
        <DataTable />
      </Suspense>
    </div>
  );
}
```

### 3. Modal Code Splitting

```jsx
import { lazy, Suspense, useState } from "react";

// Heavy modal components
const ImageEditor = lazy(() => import("./modals/ImageEditor"));
const PdfViewer = lazy(() => import("./modals/PdfViewer"));
const VideoPlayer = lazy(() => import("./modals/VideoPlayer"));

function FilePreview({ file }) {
  const [isOpen, setIsOpen] = useState(false);

  const getViewer = () => {
    switch (file.type) {
      case "image":
        return <ImageEditor file={file} onClose={() => setIsOpen(false)} />;
      case "pdf":
        return <PdfViewer file={file} onClose={() => setIsOpen(false)} />;
      case "video":
        return <VideoPlayer file={file} onClose={() => setIsOpen(false)} />;
      default:
        return null;
    }
  };

  return (
    <>
      <button onClick={() => setIsOpen(true)}>Preview {file.name}</button>
      {isOpen && <Suspense fallback={<ModalLoader />}>{getViewer()}</Suspense>}
    </>
  );
}
```

### 4. Conditional Feature Loading

```jsx
import { lazy, Suspense } from "react";

// Admin-only features
const AdminPanel = lazy(() => import("./AdminPanel"));
const Analytics = lazy(() => import("./Analytics"));

// Premium features
const AdvancedFilters = lazy(() => import("./AdvancedFilters"));
const ExportTools = lazy(() => import("./ExportTools"));

function Dashboard({ user }) {
  return (
    <div>
      <MainContent />

      {user.isAdmin && (
        <Suspense fallback={<AdminLoader />}>
          <AdminPanel />
          <Analytics />
        </Suspense>
      )}

      {user.isPremium && (
        <Suspense fallback={<PremiumLoader />}>
          <AdvancedFilters />
          <ExportTools />
        </Suspense>
      )}
    </div>
  );
}
```

### 5. Tab-Based Loading

```jsx
import { lazy, Suspense, useState } from "react";

const tabs = {
  overview: lazy(() => import("./tabs/Overview")),
  analytics: lazy(() => import("./tabs/Analytics")),
  reports: lazy(() => import("./tabs/Reports")),
  settings: lazy(() => import("./tabs/Settings")),
};

function TabPanel() {
  const [activeTab, setActiveTab] = useState("overview");

  const TabContent = tabs[activeTab];

  return (
    <div>
      <nav className="tabs">
        {Object.keys(tabs).map((tab) => (
          <button
            key={tab}
            onClick={() => setActiveTab(tab)}
            className={activeTab === tab ? "active" : ""}
          >
            {tab}
          </button>
        ))}
      </nav>

      <Suspense fallback={<TabLoader />}>
        <TabContent />
      </Suspense>
    </div>
  );
}
```

### 6. Preloading Components

```jsx
import { lazy, Suspense } from "react";

// Create lazy component
const HeavyComponent = lazy(() => import("./HeavyComponent"));

// Preload function
const preloadHeavyComponent = () => {
  import("./HeavyComponent");
};

function App() {
  return (
    <div>
      {/* Preload on hover */}
      <button
        onMouseEnter={preloadHeavyComponent}
        onClick={() => setShowHeavy(true)}
      >
        Show Heavy Component
      </button>

      {/* Preload on focus */}
      <input onFocus={preloadHeavyComponent} placeholder="Search..." />
    </div>
  );
}

// Preload on route link hover
function NavLink({ to, children, preload }) {
  return (
    <Link to={to} onMouseEnter={preload}>
      {children}
    </Link>
  );
}

// Usage
<NavLink to="/dashboard" preload={() => import("./Dashboard")}>
  Dashboard
</NavLink>;
```

---

## 🧩 Advanced Patterns

### 1. Error Boundary with Lazy

```jsx
import { lazy, Suspense } from "react";
import { ErrorBoundary } from "react-error-boundary";

const LazyComponent = lazy(() => import("./LazyComponent"));

function App() {
  return (
    <ErrorBoundary
      FallbackComponent={ErrorFallback}
      onReset={() => window.location.reload()}
    >
      <Suspense fallback={<Loading />}>
        <LazyComponent />
      </Suspense>
    </ErrorBoundary>
  );
}

function ErrorFallback({ error, resetErrorBoundary }) {
  return (
    <div>
      <p>Failed to load component: {error.message}</p>
      <button onClick={resetErrorBoundary}>Retry</button>
    </div>
  );
}
```

### 2. Loading Progress

```jsx
import { lazy, Suspense } from "react";
import { useNProgress } from "@tanem/react-nprogress";

// Wrapper with progress bar
function LazyWithProgress({ children }) {
  return <Suspense fallback={<ProgressBar />}>{children}</Suspense>;
}

function ProgressBar() {
  const { animationDuration, isFinished, progress } = useNProgress({
    isAnimating: true,
  });

  return (
    <div
      style={{
        opacity: isFinished ? 0 : 1,
        transition: `opacity ${animationDuration}ms linear`,
      }}
    >
      <div
        style={{
          background: "#29d",
          height: 2,
          left: 0,
          position: "fixed",
          top: 0,
          width: `${progress * 100}%`,
          transition: `width ${animationDuration}ms linear`,
        }}
      />
    </div>
  );
}
```

### 3. Retry on Failure

```jsx
function lazyWithRetry(componentImport, retries = 3) {
  return lazy(async () => {
    let lastError;

    for (let i = 0; i < retries; i++) {
      try {
        return await componentImport();
      } catch (error) {
        lastError = error;
        // Wait before retry
        await new Promise((resolve) => setTimeout(resolve, 1000 * (i + 1)));
      }
    }

    throw lastError;
  });
}

// Usage
const Dashboard = lazyWithRetry(() => import("./Dashboard"));
```

### 4. Named Exports

```jsx
// utils/lazyNamed.js
export function lazyNamed(importFn, exportName) {
  return lazy(() =>
    importFn().then((module) => ({
      default: module[exportName],
    }))
  );
}

// Usage
const Button = lazyNamed(() => import("./components"), "Button");

const Modal = lazyNamed(() => import("./components"), "Modal");
```

---

## ⚠️ Common Mistakes

### 1. Lazy Inside Component

```jsx
// ❌ Creates new lazy component every render
function App() {
  const LazyComponent = lazy(() => import("./Component"));
  return <LazyComponent />;
}

// ✅ Define outside component
const LazyComponent = lazy(() => import("./Component"));

function App() {
  return <LazyComponent />;
}
```

### 2. Missing Suspense

```jsx
// ❌ Error: Lazy component without Suspense
const Lazy = lazy(() => import("./Component"));

function App() {
  return <Lazy />; // Error!
}

// ✅ Wrap with Suspense
function App() {
  return (
    <Suspense fallback={<Loading />}>
      <Lazy />
    </Suspense>
  );
}
```

### 3. Poor Fallback UI

```jsx
// ❌ Jarring experience
<Suspense fallback={null}>

// ❌ Generic loader for everything
<Suspense fallback={<Spinner />}>

// ✅ Skeleton that matches content
<Suspense fallback={<DashboardSkeleton />}>
  <Dashboard />
</Suspense>
```

---

## 🔧 Best Practices

### 1. Split at Route Level

```jsx
// Most impactful - each route is a chunk
const routes = [
  { path: "/", component: lazy(() => import("./Home")) },
  { path: "/products", component: lazy(() => import("./Products")) },
  { path: "/checkout", component: lazy(() => import("./Checkout")) },
];
```

### 2. Use Magic Comments for Chunk Names

```jsx
// Webpack magic comments for better debugging
const Dashboard = lazy(() =>
  import(/* webpackChunkName: "dashboard" */ "./Dashboard")
);

const Admin = lazy(() => import(/* webpackChunkName: "admin" */ "./Admin"));

// Creates dashboard.chunk.js and admin.chunk.js
```

### 3. Analyze Bundle

```bash
# Vite
npx vite-bundle-visualizer

# Webpack
npm install --save-dev webpack-bundle-analyzer
```

### 4. Prefetch Important Routes

```jsx
// Prefetch after initial load
useEffect(() => {
  // Prefetch likely next routes
  import("./Dashboard");
  import("./Profile");
}, []);
```

---

## 🏋️ Practice Exercises

### Exercise 1: Route Splitting

Split a multi-page app by routes with proper loading states.

### Exercise 2: Modal Lazy Loading

Create modals that only load when opened.

### Exercise 3: Feature Flags

Implement feature-flagged lazy loading for A/B testing.

---

## ❓ Interview Questions

**Q1: What is code splitting and why use it?**

> Code splitting divides your bundle into smaller chunks loaded on demand. It improves initial load time by not loading code until needed, reducing the initial bundle size.

**Q2: How does React.lazy work?**

> React.lazy takes a function that returns a dynamic import() promise. It creates a component that loads the module only when first rendered, and must be wrapped in Suspense.

**Q3: What is Suspense?**

> Suspense lets you declaratively specify loading UI while waiting for lazy components. It shows a fallback until all nested lazy components have loaded.

**Q4: When should you split code?**

> Split at route level (most impactful), for heavy components (charts, editors), for features not all users need (admin panels), and for modals/dialogs.

**Q5: How do you handle errors with lazy loading?**

> Wrap lazy components with an Error Boundary. The boundary catches import failures and can show retry UI or error messages.

---

## 🔑 Key Takeaways

> ### 💡 Summary
>
> - **React.lazy** enables component-level code splitting
> - **Suspense** provides loading UI while chunks load
> - **Split at route level** for maximum impact
> - **Preload** likely-needed components on hover/focus
> - **Use Error Boundaries** for failed imports
> - **Analyze bundle** to find split opportunities
> - **Skeleton loaders** provide better UX than spinners

---

**Next:** [12 - Suspense](./12_Suspense.md) - Learn more about Suspense for data fetching!
