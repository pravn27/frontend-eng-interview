# 17 - Data Fetching Patterns

## 📖 Data Fetching in React

React doesn't have built-in data fetching. You choose from several patterns: useEffect with fetch, custom hooks, or libraries like React Query/SWR.

```jsx
// Basic pattern
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

  if (loading) return <Spinner />;
  if (error) return <Error message={error.message} />;
  return <div>{user.name}</div>;
}
```

---

## 🎯 Why Data Fetching Matters

### Common Requirements

- ✅ Loading states
- ✅ Error handling
- ✅ Caching
- ✅ Deduplication
- ✅ Refetching (on focus, interval)
- ✅ Optimistic updates
- ✅ Pagination/Infinite scroll

---

## 📚 Fetching Patterns

### 1. useEffect + fetch (Basic)

```jsx
function useFetch(url) {
  const [data, setData] = useState(null);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);

  useEffect(() => {
    const controller = new AbortController();

    const fetchData = async () => {
      setLoading(true);
      setError(null);

      try {
        const response = await fetch(url, { signal: controller.signal });
        if (!response.ok) {
          throw new Error(`HTTP error! status: ${response.status}`);
        }
        const json = await response.json();
        setData(json);
      } catch (err) {
        if (err.name !== "AbortError") {
          setError(err);
        }
      } finally {
        setLoading(false);
      }
    };

    fetchData();

    return () => controller.abort();
  }, [url]);

  return { data, loading, error };
}

// Usage
function UserList() {
  const { data: users, loading, error } = useFetch("/api/users");

  if (loading) return <Spinner />;
  if (error) return <Error message={error.message} />;

  return (
    <ul>
      {users.map((user) => (
        <li key={user.id}>{user.name}</li>
      ))}
    </ul>
  );
}
```

### 2. React Query (Recommended)

```jsx
import { useQuery, useMutation, useQueryClient } from "@tanstack/react-query";

// Fetch data
function UserProfile({ userId }) {
  const {
    data: user,
    isLoading,
    error,
  } = useQuery({
    queryKey: ["user", userId],
    queryFn: () => fetch(`/api/users/${userId}`).then((res) => res.json()),
    staleTime: 5 * 60 * 1000, // 5 minutes
    cacheTime: 30 * 60 * 1000, // 30 minutes
  });

  if (isLoading) return <Spinner />;
  if (error) return <Error message={error.message} />;

  return <div>{user.name}</div>;
}

// Mutation (create, update, delete)
function CreateUser() {
  const queryClient = useQueryClient();

  const mutation = useMutation({
    mutationFn: (newUser) =>
      fetch("/api/users", {
        method: "POST",
        body: JSON.stringify(newUser),
      }).then((res) => res.json()),
    onSuccess: () => {
      // Invalidate and refetch
      queryClient.invalidateQueries({ queryKey: ["users"] });
    },
  });

  return (
    <button
      onClick={() => mutation.mutate({ name: "New User" })}
      disabled={mutation.isPending}
    >
      {mutation.isPending ? "Creating..." : "Create User"}
    </button>
  );
}
```

### 3. SWR (Stale-While-Revalidate)

```jsx
import useSWR from "swr";

const fetcher = (url) => fetch(url).then((res) => res.json());

function UserProfile({ userId }) {
  const { data, error, isLoading, mutate } = useSWR(
    `/api/users/${userId}`,
    fetcher,
    {
      revalidateOnFocus: true,
      revalidateOnReconnect: true,
      refreshInterval: 30000, // Poll every 30s
    }
  );

  if (isLoading) return <Spinner />;
  if (error) return <Error />;

  return (
    <div>
      <h1>{data.name}</h1>
      <button onClick={() => mutate()}>Refresh</button>
    </div>
  );
}
```

---

## 💻 Practical Examples

### 1. Pagination

```jsx
import { useQuery, keepPreviousData } from "@tanstack/react-query";

function PaginatedList() {
  const [page, setPage] = useState(1);

  const { data, isLoading, isFetching, isPlaceholderData } = useQuery({
    queryKey: ["posts", page],
    queryFn: () => fetchPosts(page),
    placeholderData: keepPreviousData,
  });

  return (
    <div>
      <ul style={{ opacity: isFetching ? 0.5 : 1 }}>
        {data?.posts.map((post) => (
          <li key={post.id}>{post.title}</li>
        ))}
      </ul>

      <div className="pagination">
        <button
          onClick={() => setPage((p) => Math.max(1, p - 1))}
          disabled={page === 1}
        >
          Previous
        </button>

        <span>Page {page}</span>

        <button
          onClick={() => setPage((p) => p + 1)}
          disabled={isPlaceholderData || !data?.hasMore}
        >
          Next
        </button>
      </div>
    </div>
  );
}
```

### 2. Infinite Scroll

```jsx
import { useInfiniteQuery } from "@tanstack/react-query";
import { useInView } from "react-intersection-observer";

function InfiniteList() {
  const { ref, inView } = useInView();

  const {
    data,
    fetchNextPage,
    hasNextPage,
    isFetchingNextPage,
    isLoading,
    error,
  } = useInfiniteQuery({
    queryKey: ["posts"],
    queryFn: ({ pageParam = 0 }) => fetchPosts(pageParam),
    getNextPageParam: (lastPage) => lastPage.nextCursor,
  });

  useEffect(() => {
    if (inView && hasNextPage) {
      fetchNextPage();
    }
  }, [inView, hasNextPage, fetchNextPage]);

  if (isLoading) return <Spinner />;
  if (error) return <Error />;

  return (
    <div>
      {data.pages.map((page, i) => (
        <Fragment key={i}>
          {page.posts.map((post) => (
            <PostCard key={post.id} post={post} />
          ))}
        </Fragment>
      ))}

      <div ref={ref}>
        {isFetchingNextPage ? (
          <Spinner />
        ) : hasNextPage ? (
          "Load more"
        ) : (
          "No more posts"
        )}
      </div>
    </div>
  );
}
```

### 3. Optimistic Updates

```jsx
import { useMutation, useQueryClient } from "@tanstack/react-query";

function TodoItem({ todo }) {
  const queryClient = useQueryClient();

  const toggleMutation = useMutation({
    mutationFn: (completed) =>
      fetch(`/api/todos/${todo.id}`, {
        method: "PATCH",
        body: JSON.stringify({ completed }),
      }),
    onMutate: async (completed) => {
      // Cancel outgoing refetches
      await queryClient.cancelQueries({ queryKey: ["todos"] });

      // Snapshot previous value
      const previousTodos = queryClient.getQueryData(["todos"]);

      // Optimistically update
      queryClient.setQueryData(["todos"], (old) =>
        old.map((t) => (t.id === todo.id ? { ...t, completed } : t))
      );

      return { previousTodos };
    },
    onError: (err, variables, context) => {
      // Rollback on error
      queryClient.setQueryData(["todos"], context.previousTodos);
    },
    onSettled: () => {
      // Refetch after error or success
      queryClient.invalidateQueries({ queryKey: ["todos"] });
    },
  });

  return (
    <li>
      <input
        type="checkbox"
        checked={todo.completed}
        onChange={(e) => toggleMutation.mutate(e.target.checked)}
      />
      {todo.text}
    </li>
  );
}
```

### 4. Dependent Queries

```jsx
function UserPosts({ userId }) {
  // First query
  const { data: user } = useQuery({
    queryKey: ["user", userId],
    queryFn: () => fetchUser(userId),
  });

  // Second query depends on first
  const { data: posts } = useQuery({
    queryKey: ["posts", user?.id],
    queryFn: () => fetchPostsByUser(user.id),
    enabled: !!user, // Only run when user exists
  });

  return (
    <div>
      <h1>{user?.name}</h1>
      <ul>
        {posts?.map((post) => (
          <li key={post.id}>{post.title}</li>
        ))}
      </ul>
    </div>
  );
}
```

### 5. Parallel Queries

```jsx
import { useQueries } from "@tanstack/react-query";

function Dashboard() {
  const results = useQueries({
    queries: [
      { queryKey: ["users"], queryFn: fetchUsers },
      { queryKey: ["posts"], queryFn: fetchPosts },
      { queryKey: ["comments"], queryFn: fetchComments },
    ],
  });

  const isLoading = results.some((r) => r.isLoading);
  const [users, posts, comments] = results.map((r) => r.data);

  if (isLoading) return <Spinner />;

  return (
    <div>
      <UsersWidget data={users} />
      <PostsWidget data={posts} />
      <CommentsWidget data={comments} />
    </div>
  );
}
```

### 6. Custom useFetch with Caching

```jsx
const cache = new Map();

function useFetch(url, options = {}) {
  const [state, setState] = useState({
    data: cache.get(url) || null,
    loading: !cache.has(url),
    error: null,
  });

  useEffect(() => {
    const controller = new AbortController();

    const fetchData = async () => {
      // Return cached data immediately
      if (cache.has(url) && !options.refetch) {
        return;
      }

      setState((prev) => ({ ...prev, loading: true }));

      try {
        const response = await fetch(url, { signal: controller.signal });
        const data = await response.json();

        cache.set(url, data);
        setState({ data, loading: false, error: null });
      } catch (error) {
        if (error.name !== "AbortError") {
          setState((prev) => ({ ...prev, loading: false, error }));
        }
      }
    };

    fetchData();

    return () => controller.abort();
  }, [url, options.refetch]);

  const refetch = () => {
    cache.delete(url);
    setState((prev) => ({ ...prev, loading: true }));
  };

  return { ...state, refetch };
}
```

---

## 🧩 Error Handling Patterns

### 1. Error Boundary + Query Error

```jsx
import { QueryErrorResetBoundary } from "@tanstack/react-query";
import { ErrorBoundary } from "react-error-boundary";

function App() {
  return (
    <QueryErrorResetBoundary>
      {({ reset }) => (
        <ErrorBoundary
          onReset={reset}
          fallbackRender={({ error, resetErrorBoundary }) => (
            <div>
              <p>Error: {error.message}</p>
              <button onClick={resetErrorBoundary}>Retry</button>
            </div>
          )}
        >
          <Suspense fallback={<Loading />}>
            <DataComponent />
          </Suspense>
        </ErrorBoundary>
      )}
    </QueryErrorResetBoundary>
  );
}
```

### 2. Global Error Handler

```jsx
const queryClient = new QueryClient({
  defaultOptions: {
    queries: {
      retry: 3,
      retryDelay: (attemptIndex) => Math.min(1000 * 2 ** attemptIndex, 30000),
      onError: (error) => {
        console.error("Query error:", error);
        toast.error("Failed to fetch data");
      },
    },
    mutations: {
      onError: (error) => {
        console.error("Mutation error:", error);
        toast.error("Failed to save data");
      },
    },
  },
});
```

---

## ⚠️ Common Mistakes

### 1. Fetching in useEffect Without Cleanup

```jsx
// ❌ Race condition
useEffect(() => {
  fetch(url).then(setData);
}, [url]);

// ✅ With cleanup
useEffect(() => {
  let cancelled = false;
  fetch(url).then((data) => {
    if (!cancelled) setData(data);
  });
  return () => {
    cancelled = true;
  };
}, [url]);
```

### 2. Missing Loading/Error States

```jsx
// ❌ No loading state
function Component() {
  const { data } = useFetch(url);
  return <div>{data.name}</div>; // Crash if data is null!
}

// ✅ Handle all states
function Component() {
  const { data, loading, error } = useFetch(url);
  if (loading) return <Spinner />;
  if (error) return <Error />;
  return <div>{data.name}</div>;
}
```

---

## 🔧 Best Practices

### 1. Use a Data Fetching Library

```jsx
// React Query or SWR for production apps
// They handle caching, deduplication, retries, etc.
```

### 2. Centralize API Logic

```jsx
// api/users.js
export const usersApi = {
  getAll: () => fetch("/api/users").then((r) => r.json()),
  getById: (id) => fetch(`/api/users/${id}`).then((r) => r.json()),
  create: (data) =>
    fetch("/api/users", {
      method: "POST",
      body: JSON.stringify(data),
    }).then((r) => r.json()),
};

// Usage
const { data } = useQuery({
  queryKey: ["users"],
  queryFn: usersApi.getAll,
});
```

### 3. Type Your API Responses

```typescript
interface User {
  id: number;
  name: string;
  email: string;
}

const { data } = useQuery<User[], Error>({
  queryKey: ["users"],
  queryFn: fetchUsers,
});
```

---

## 🏋️ Practice Exercises

### Exercise 1: Search with Debounce

Build a search component with debounced API calls and caching.

### Exercise 2: CRUD Operations

Implement a full CRUD interface with optimistic updates.

### Exercise 3: Real-time Data

Create a component that polls for updates and handles stale data.

---

## ❓ Interview Questions

**Q1: How do you handle data fetching in React?**

> Use useEffect for basic cases, but prefer libraries like React Query or SWR for production. They handle caching, deduplication, retries, and provide better DX with less code.

**Q2: What is SWR (stale-while-revalidate)?**

> A caching strategy where stale data is returned immediately while fresh data is fetched in the background. It provides instant UI while ensuring data freshness.

**Q3: How do you handle race conditions in data fetching?**

> Use AbortController to cancel pending requests, or use a cancelled flag in useEffect cleanup. Libraries like React Query handle this automatically.

**Q4: What are optimistic updates?**

> Updating the UI immediately before the server confirms the change, then rolling back if the request fails. This makes the app feel faster and more responsive.

**Q5: When would you use infinite queries vs pagination?**

> Infinite scroll for feeds/timelines where users scroll continuously. Pagination for data tables where users need to jump to specific pages.

---

## 🔑 Key Takeaways

> ### 💡 Summary
>
> - **Use a library** (React Query/SWR) for production
> - **Handle all states** - loading, error, success
> - **Cancel requests** on unmount or dependency change
> - **Cache data** to avoid redundant fetches
> - **Optimistic updates** for better UX
> - **Centralize API logic** for maintainability
> - **Type your responses** with TypeScript

---

**Next:** [18 - Testing React Components](./18_Testing_React_Components.md) - Learn about testing!
