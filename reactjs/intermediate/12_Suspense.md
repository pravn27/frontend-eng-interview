# 12 - Suspense

## 📖 What is Suspense?

Suspense is a React component that lets you "wait" for something before rendering. It shows a fallback UI while components are loading or fetching data.

```jsx
import { Suspense } from "react";

function App() {
  return (
    <Suspense fallback={<Loading />}>
      <DataComponent />
    </Suspense>
  );
}
```

---

## 🎯 Suspense Use Cases

### Current (React 18)

- ✅ **Lazy loading** - `React.lazy()` components
- ✅ **Data fetching** - With compatible libraries (React Query, Relay)
- ✅ **Streaming SSR** - Server-side rendering

### Coming (React 19+)

- 🔜 **use() hook** - Native data fetching
- 🔜 **Server Components** - Integrated with RSC

---

## 📚 Core Concepts

### 1. Basic Suspense

```jsx
import { Suspense, lazy } from "react";

const LazyComponent = lazy(() => import("./LazyComponent"));

function App() {
  return (
    <Suspense fallback={<div>Loading...</div>}>
      <LazyComponent />
    </Suspense>
  );
}
```

### 2. Nested Suspense

```jsx
function App() {
  return (
    <Suspense fallback={<PageSkeleton />}>
      <Header />
      <Suspense fallback={<ContentSkeleton />}>
        <MainContent />
      </Suspense>
      <Suspense fallback={<SidebarSkeleton />}>
        <Sidebar />
      </Suspense>
    </Suspense>
  );
}
```

### 3. Suspense with Data Fetching (React Query)

```jsx
import { Suspense } from "react";
import { useQuery } from "@tanstack/react-query";

function UserProfile({ userId }) {
  const { data: user } = useQuery({
    queryKey: ["user", userId],
    queryFn: () => fetchUser(userId),
    suspense: true, // Enable Suspense mode
  });

  return <div>{user.name}</div>;
}

function App() {
  return (
    <Suspense fallback={<ProfileSkeleton />}>
      <UserProfile userId={1} />
    </Suspense>
  );
}
```

---

## 💻 Practical Examples

### 1. Page with Multiple Data Sources

```jsx
import { Suspense } from "react";
import { useQuery } from "@tanstack/react-query";

function Dashboard() {
  return (
    <div className="dashboard">
      <h1>Dashboard</h1>

      {/* Each section loads independently */}
      <div className="grid">
        <Suspense fallback={<StatsSkeleton />}>
          <StatsSection />
        </Suspense>

        <Suspense fallback={<ChartSkeleton />}>
          <ChartSection />
        </Suspense>

        <Suspense fallback={<ActivitySkeleton />}>
          <RecentActivity />
        </Suspense>

        <Suspense fallback={<TableSkeleton />}>
          <DataTable />
        </Suspense>
      </div>
    </div>
  );
}

function StatsSection() {
  const { data } = useQuery({
    queryKey: ["stats"],
    queryFn: fetchStats,
    suspense: true,
  });

  return (
    <div className="stats">
      <StatCard title="Users" value={data.users} />
      <StatCard title="Revenue" value={data.revenue} />
      <StatCard title="Orders" value={data.orders} />
    </div>
  );
}
```

### 2. SuspenseList for Coordinated Loading

```jsx
import { Suspense, SuspenseList } from "react";

function Feed() {
  return (
    <SuspenseList revealOrder="forwards" tail="collapsed">
      <Suspense fallback={<PostSkeleton />}>
        <Post id={1} />
      </Suspense>
      <Suspense fallback={<PostSkeleton />}>
        <Post id={2} />
      </Suspense>
      <Suspense fallback={<PostSkeleton />}>
        <Post id={3} />
      </Suspense>
    </SuspenseList>
  );
}

// revealOrder options:
// "forwards" - reveal in order, top to bottom
// "backwards" - reveal in reverse order
// "together" - reveal all at once when ready

// tail options:
// "collapsed" - show only next fallback
// "hidden" - don't show fallbacks for unrevealed
```

### 3. Streaming Content

```jsx
function ArticlePage({ articleId }) {
  return (
    <article>
      {/* Critical content loads first */}
      <Suspense fallback={<HeaderSkeleton />}>
        <ArticleHeader articleId={articleId} />
      </Suspense>

      {/* Main content with own boundary */}
      <Suspense fallback={<ContentSkeleton />}>
        <ArticleContent articleId={articleId} />
      </Suspense>

      {/* Non-critical, can load later */}
      <Suspense fallback={<CommentsSkeleton />}>
        <Comments articleId={articleId} />
      </Suspense>

      {/* Lowest priority */}
      <Suspense fallback={<RelatedSkeleton />}>
        <RelatedArticles articleId={articleId} />
      </Suspense>
    </article>
  );
}
```

### 4. Transition with Suspense

```jsx
import { useState, useTransition, Suspense } from "react";

function TabContainer() {
  const [tab, setTab] = useState("home");
  const [isPending, startTransition] = useTransition();

  function selectTab(nextTab) {
    startTransition(() => {
      setTab(nextTab);
    });
  }

  return (
    <div>
      <nav>
        <button
          onClick={() => selectTab("home")}
          className={tab === "home" ? "active" : ""}
        >
          Home
        </button>
        <button
          onClick={() => selectTab("posts")}
          className={tab === "posts" ? "active" : ""}
        >
          Posts
        </button>
        <button
          onClick={() => selectTab("about")}
          className={tab === "about" ? "active" : ""}
        >
          About
        </button>
      </nav>

      <div style={{ opacity: isPending ? 0.7 : 1 }}>
        <Suspense fallback={<TabSkeleton />}>
          {tab === "home" && <Home />}
          {tab === "posts" && <Posts />}
          {tab === "about" && <About />}
        </Suspense>
      </div>
    </div>
  );
}
```

### 5. Error Handling with Suspense

```jsx
import { Suspense } from "react";
import { ErrorBoundary } from "react-error-boundary";

function App() {
  return (
    <ErrorBoundary FallbackComponent={ErrorFallback}>
      <Suspense fallback={<Loading />}>
        <DataComponent />
      </Suspense>
    </ErrorBoundary>
  );
}

function ErrorFallback({ error, resetErrorBoundary }) {
  return (
    <div className="error">
      <h2>Something went wrong</h2>
      <pre>{error.message}</pre>
      <button onClick={resetErrorBoundary}>Try again</button>
    </div>
  );
}
```

---

## 🧩 Skeleton Patterns

### 1. Content-Matching Skeletons

```jsx
function CardSkeleton() {
  return (
    <div className="card skeleton">
      <div className="skeleton-image" />
      <div className="skeleton-line title" />
      <div className="skeleton-line" />
      <div className="skeleton-line short" />
    </div>
  );
}

function ListSkeleton({ count = 5 }) {
  return (
    <div className="list">
      {Array(count)
        .fill(null)
        .map((_, i) => (
          <div key={i} className="list-item skeleton">
            <div className="skeleton-avatar" />
            <div className="skeleton-content">
              <div className="skeleton-line title" />
              <div className="skeleton-line" />
            </div>
          </div>
        ))}
    </div>
  );
}
```

### 2. Animated Skeletons

```css
.skeleton {
  background: linear-gradient(90deg, #f0f0f0 25%, #e0e0e0 50%, #f0f0f0 75%);
  background-size: 200% 100%;
  animation: shimmer 1.5s infinite;
}

@keyframes shimmer {
  0% {
    background-position: -200% 0;
  }
  100% {
    background-position: 200% 0;
  }
}
```

---

## ⚠️ Common Mistakes

### 1. Suspense Without Boundary

```jsx
// ❌ No Suspense boundary
const LazyComponent = lazy(() => import("./Component"));
<LazyComponent /> // Error!

// ✅ Wrap with Suspense
<Suspense fallback={<Loading />}>
  <LazyComponent />
</Suspense>
```

### 2. Fetching in Render

```jsx
// ❌ This doesn't work with Suspense
function Component() {
  const [data, setData] = useState(null);
  useEffect(() => {
    fetch("/api/data").then(setData);
  }, []);
  // ...
}

// ✅ Use Suspense-compatible library
function Component() {
  const { data } = useQuery({
    queryKey: ["data"],
    queryFn: fetchData,
    suspense: true,
  });
  // ...
}
```

### 3. Too Many Suspense Boundaries

```jsx
// ❌ Too granular - jarring UI
<Suspense fallback={<Skeleton />}>
  <Title />
</Suspense>
<Suspense fallback={<Skeleton />}>
  <Subtitle />
</Suspense>
<Suspense fallback={<Skeleton />}>
  <Body />
</Suspense>

// ✅ Group related content
<Suspense fallback={<ContentSkeleton />}>
  <Title />
  <Subtitle />
  <Body />
</Suspense>
```

---

## 🔧 Best Practices

### 1. Strategic Suspense Placement

```jsx
// ✅ Suspense at logical boundaries
<Layout>
  <Header /> {/* Always visible */}
  <Suspense fallback={<MainSkeleton />}>
    <MainContent /> {/* Primary content */}
  </Suspense>
  <Suspense fallback={<SidebarSkeleton />}>
    <Sidebar /> {/* Secondary content */}
  </Suspense>
</Layout>
```

### 2. Meaningful Fallbacks

```jsx
// ❌ Generic spinner
<Suspense fallback={<Spinner />}>

// ✅ Skeleton matching content
<Suspense fallback={<ArticleSkeleton />}>
  <Article />
</Suspense>
```

### 3. Combine with Error Boundaries

```jsx
function DataSection() {
  return (
    <ErrorBoundary FallbackComponent={SectionError}>
      <Suspense fallback={<SectionSkeleton />}>
        <DataComponent />
      </Suspense>
    </ErrorBoundary>
  );
}
```

---

## 🏋️ Practice Exercises

### Exercise 1: Dashboard with Suspense

Build a dashboard where each widget loads independently with skeletons.

### Exercise 2: Infinite Scroll with Suspense

Create an infinite scroll list using Suspense for loading states.

### Exercise 3: Streaming Search Results

Build a search with results that stream in as they load.

---

## ❓ Interview Questions

**Q1: What is Suspense and how does it work?**

> Suspense lets you declaratively handle loading states. Components can "suspend" while loading, and Suspense shows a fallback until they're ready. It works with React.lazy and Suspense-compatible libraries.

**Q2: What can trigger Suspense?**

> React.lazy components, Suspense-enabled data fetching libraries (React Query, Relay, SWR), and future React features like the use() hook.

**Q3: How is Suspense different from loading states?**

> Traditional loading uses imperative state (isLoading). Suspense is declarative - components "suspend" and React handles the loading UI. It enables better composition and coordination.

**Q4: What is SuspenseList?**

> SuspenseList coordinates the reveal order of multiple Suspense boundaries. It can reveal children in order, together, or in reverse, providing smoother loading experiences.

**Q5: How does Suspense interact with useTransition?**

> useTransition lets you mark state updates as non-urgent. When navigating to suspended content, the old content stays visible (with pending state) instead of showing the fallback immediately.

---

## 🔑 Key Takeaways

> ### 💡 Summary
>
> - **Suspense** declaratively handles loading states
> - **Works with** lazy(), React Query, Relay, and more
> - **Nested Suspense** enables granular loading UI
> - **Skeletons** provide better UX than spinners
> - **SuspenseList** coordinates reveal order
> - **useTransition** keeps old content during navigation
> - **Combine with Error Boundaries** for complete handling

---

**Next:** [13 - Higher Order Components](./13_Higher_Order_Components.md) - Learn about the HOC pattern!
