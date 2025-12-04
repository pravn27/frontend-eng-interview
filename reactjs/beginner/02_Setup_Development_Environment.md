# 02. Setup Development Environment

> Setting up a modern React development environment with 2025 best practices.

---

## 🎯 What You'll Learn

- Install Node.js and npm
- Create a React project with Vite (2025 standard)
- Configure VS Code for React development
- Install and use React Developer Tools
- Understand the project structure

---

## 🤔 Why Proper Setup Matters

A well-configured development environment:

- ✅ Faster development with hot module replacement
- ✅ Better debugging with DevTools
- ✅ Consistent code with linting and formatting
- ✅ Improved productivity with IDE features

---

## 📚 Prerequisites

### 1. Node.js Installation

Node.js is required to run JavaScript outside the browser and use npm (Node Package Manager).

```bash
# Check if Node.js is installed
node --version  # Should be v18+ for React 19

# Check npm version
npm --version   # Should be v9+
```

**Download Node.js**: [https://nodejs.org/](https://nodejs.org/) (LTS version recommended)

### 2. Code Editor

**VS Code** is the recommended editor for React development.

**Download**: [https://code.visualstudio.com/](https://code.visualstudio.com/)

---

## 💻 Creating a React Project

### Option 1: Vite (Recommended - 2025 Standard)

Vite is the modern, fast build tool that has replaced Create React App (CRA).

```bash
# Create a new React project with Vite
npm create vite@latest my-react-app -- --template react

# For TypeScript (recommended for larger projects)
npm create vite@latest my-react-app -- --template react-ts

# Navigate to project
cd my-react-app

# Install dependencies
npm install

# Start development server
npm run dev
```

**Why Vite over Create React App (CRA)?**

| Feature           | Vite           | CRA                |
| ----------------- | -------------- | ------------------ |
| **Startup Time**  | ~300ms         | ~30s               |
| **Hot Reload**    | Instant        | 1-3s               |
| **Build Time**    | Fast (ESBuild) | Slower (Webpack)   |
| **Bundle Size**   | Smaller        | Larger             |
| **Maintenance**   | Active         | Deprecated         |
| **Status (2025)** | ✅ Recommended | ⚠️ Not recommended |

### Option 2: Next.js (Full-Stack Framework)

For production applications, Next.js is often preferred.

```bash
# Create a Next.js project
npx create-next-app@latest my-next-app

# With specific options
npx create-next-app@latest my-next-app --typescript --tailwind --app
```

### Option 3: React Router Framework

```bash
# Using React Router's framework mode
npx create-react-router@latest my-app
```

---

## 📁 Project Structure (Vite)

```
my-react-app/
├── node_modules/          # Dependencies
├── public/                # Static assets
│   └── vite.svg
├── src/                   # Source code
│   ├── assets/           # Images, fonts, etc.
│   │   └── react.svg
│   ├── App.css           # Component styles
│   ├── App.jsx           # Main App component
│   ├── index.css         # Global styles
│   └── main.jsx          # Entry point
├── .gitignore            # Git ignore rules
├── eslint.config.js      # ESLint configuration
├── index.html            # HTML template
├── package.json          # Project configuration
├── package-lock.json     # Dependency lock file
├── README.md             # Project documentation
└── vite.config.js        # Vite configuration
```

### Key Files Explained

**`main.jsx`** - Entry point

```jsx
import { StrictMode } from "react";
import { createRoot } from "react-dom/client";
import App from "./App.jsx";
import "./index.css";

createRoot(document.getElementById("root")).render(
  <StrictMode>
    <App />
  </StrictMode>
);
```

**`App.jsx`** - Main component

```jsx
import { useState } from "react";
import "./App.css";

function App() {
  const [count, setCount] = useState(0);

  return (
    <div className="App">
      <h1>Vite + React</h1>
      <button onClick={() => setCount(count + 1)}>count is {count}</button>
    </div>
  );
}

export default App;
```

**`index.html`** - HTML template

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <link rel="icon" type="image/svg+xml" href="/vite.svg" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Vite + React</title>
  </head>
  <body>
    <div id="root"></div>
    <script type="module" src="/src/main.jsx"></script>
  </body>
</html>
```

**`package.json`** - Project configuration

```json
{
  "name": "my-react-app",
  "private": true,
  "version": "0.0.0",
  "type": "module",
  "scripts": {
    "dev": "vite",
    "build": "vite build",
    "lint": "eslint .",
    "preview": "vite preview"
  },
  "dependencies": {
    "react": "^19.0.0",
    "react-dom": "^19.0.0"
  },
  "devDependencies": {
    "@vitejs/plugin-react": "^4.3.0",
    "eslint": "^9.0.0",
    "vite": "^6.0.0"
  }
}
```

---

## 🛠️ VS Code Setup

### Essential Extensions

Install these extensions for the best React development experience:

| Extension                                  | Purpose                     |
| ------------------------------------------ | --------------------------- |
| **ES7+ React/Redux/React-Native snippets** | Code snippets for React     |
| **Prettier - Code formatter**              | Auto-format code            |
| **ESLint**                                 | Identify code issues        |
| **Auto Rename Tag**                        | Rename paired HTML/JSX tags |
| **Bracket Pair Colorizer**                 | Color-matched brackets      |
| **Path Intellisense**                      | Autocomplete file paths     |
| **GitLens**                                | Git integration             |
| **Error Lens**                             | Inline error display        |

### VS Code Settings for React

Create/update `.vscode/settings.json` in your project:

```json
{
  "editor.formatOnSave": true,
  "editor.defaultFormatter": "esbenp.prettier-vscode",
  "editor.codeActionsOnSave": {
    "source.fixAll.eslint": "explicit"
  },
  "emmet.includeLanguages": {
    "javascript": "javascriptreact",
    "typescript": "typescriptreact"
  },
  "files.associations": {
    "*.jsx": "javascriptreact",
    "*.tsx": "typescriptreact"
  }
}
```

### Useful Snippets (ES7+ Extension)

| Snippet     | Output                                |
| ----------- | ------------------------------------- |
| `rafce`     | React Arrow Function Component Export |
| `rfc`       | React Functional Component            |
| `useState`  | useState Hook                         |
| `useEffect` | useEffect Hook                        |
| `imr`       | Import React                          |
| `imrs`      | Import React, { useState }            |

```jsx
// Type 'rafce' and press Tab to generate:
import React from "react";

const ComponentName = () => {
  return <div>ComponentName</div>;
};

export default ComponentName;
```

---

## 🔧 React Developer Tools

### Installation

1. **Chrome**: [React Developer Tools](https://chrome.google.com/webstore/detail/react-developer-tools/fmkadmapgofadopljbjfkapdkoienihi)
2. **Firefox**: [React Developer Tools](https://addons.mozilla.org/en-US/firefox/addon/react-devtools/)
3. **Edge**: [React Developer Tools](https://microsoftedge.microsoft.com/addons/detail/react-developer-tools/gpphkfbcpidddadnkolkpfckpihlkkil)

### Features

```
┌─────────────────────────────────────────────────────────────┐
│                  REACT DEVELOPER TOOLS                       │
├─────────────────────────────────────────────────────────────┤
│                                                              │
│  ⚛️ Components Tab                                          │
│  ├── View component hierarchy                               │
│  ├── Inspect props and state                                │
│  ├── Edit state in real-time                                │
│  └── Search for components                                  │
│                                                              │
│  📊 Profiler Tab                                            │
│  ├── Record rendering performance                           │
│  ├── Identify slow components                               │
│  ├── View commit information                                │
│  └── Flame graph visualization                              │
│                                                              │
└─────────────────────────────────────────────────────────────┘
```

### Using DevTools

```jsx
// Example: Inspecting this component in DevTools
function UserCard({ name, email }) {
  const [isFollowing, setIsFollowing] = useState(false);

  return (
    <div className="user-card">
      <h3>{name}</h3>
      <p>{email}</p>
      <button onClick={() => setIsFollowing(!isFollowing)}>
        {isFollowing ? "Unfollow" : "Follow"}
      </button>
    </div>
  );
}

// In DevTools Components tab, you can:
// - See props: { name: "John", email: "john@example.com" }
// - See state: { isFollowing: false }
// - Click to toggle state and see UI update
```

---

## 📜 NPM Scripts Explained

```json
{
  "scripts": {
    "dev": "vite", // Start development server
    "build": "vite build", // Create production build
    "lint": "eslint .", // Run linter
    "preview": "vite preview" // Preview production build locally
  }
}
```

```bash
# Start development server (http://localhost:5173)
npm run dev

# Create optimized production build
npm run build

# Preview production build locally
npm run preview

# Run ESLint to check for issues
npm run lint
```

---

## 🔄 Hot Module Replacement (HMR)

Vite includes HMR out of the box - your changes appear instantly without losing state!

```jsx
// Make a change to this component
function Counter() {
  const [count, setCount] = useState(5); // Current state: 5

  return (
    <div>
      <p>Count: {count}</p>
      <button onClick={() => setCount(count + 1)}>Add</button>
    </div>
  );
}

// Change the button text to "Increment"
// The page updates instantly, and count remains 5!
```

---

## ✅ Best Practices

1. **Use Vite for new projects** - CRA is deprecated
2. **Enable ESLint and Prettier** - Catch errors early, format consistently
3. **Use React DevTools** - Essential for debugging
4. **Organize files by feature** - Not by type (components/, hooks/, etc.)
5. **Use path aliases** - Avoid long relative imports

### Path Aliases Configuration (vite.config.js)

```javascript
import { defineConfig } from "vite";
import react from "@vitejs/plugin-react";
import path from "path";

export default defineConfig({
  plugins: [react()],
  resolve: {
    alias: {
      "@": path.resolve(__dirname, "./src"),
      "@components": path.resolve(__dirname, "./src/components"),
      "@hooks": path.resolve(__dirname, "./src/hooks"),
      "@utils": path.resolve(__dirname, "./src/utils"),
    },
  },
});
```

```jsx
// Before
import Button from "../../../components/Button";

// After
import Button from "@components/Button";
```

---

## ❌ Common Mistakes

1. **Using Create React App** - It's deprecated, use Vite instead
2. **Not installing DevTools** - Makes debugging much harder
3. **Ignoring ESLint warnings** - They often indicate real bugs
4. **Not using .gitignore** - Don't commit node_modules!
5. **Manual refreshing** - Trust HMR, it works!

---

## 🏋️ Practice Exercises

### Exercise 1: Project Setup

Create a new React project with Vite and verify it runs correctly.

```bash
npm create vite@latest practice-app -- --template react
cd practice-app
npm install
npm run dev
```

### Exercise 2: Explore DevTools

1. Install React Developer Tools
2. Open your app in Chrome
3. Open DevTools (F12) → Components tab
4. Find the App component
5. Try editing state values directly

### Exercise 3: Customize Your Setup

1. Install Prettier and ESLint extensions
2. Create a `.vscode/settings.json` file
3. Enable format on save
4. Create a test component using snippets

---

## 🎤 Interview Q&A

**Q1: What is Vite and why is it preferred over Create React App?**

> Vite is a modern build tool that offers faster startup times (milliseconds vs seconds), instant hot module replacement, and smaller bundle sizes. CRA is deprecated and uses the slower Webpack bundler.

**Q2: What is the purpose of `index.html` in a React project?**

> It's the single HTML file that serves as the entry point. It contains a root div where React mounts the application. The script tag loads the JavaScript that renders React components into this root element.

**Q3: What does `StrictMode` do?**

> StrictMode is a development tool that helps identify potential problems in an application. It intentionally double-invokes functions like component bodies and effects to detect side effects, warns about deprecated APIs, and doesn't render any visible UI.

**Q4: What is Hot Module Replacement (HMR)?**

> HMR allows you to see code changes instantly in the browser without a full page reload. It preserves application state, making development faster and more efficient.

**Q5: What are the essential VS Code extensions for React development?**

> Essential extensions include: ES7+ React snippets (code snippets), Prettier (formatting), ESLint (linting), Auto Rename Tag (paired tag editing), and the React Developer Tools browser extension for debugging.

---

## 📎 References

- [Vite Documentation](https://vitejs.dev/)
- [React Developer Tools](https://react.dev/learn/react-developer-tools)
- [VS Code for React](https://code.visualstudio.com/docs/nodejs/reactjs-tutorial)
- [ESLint for React](https://eslint.org/)
- [Prettier](https://prettier.io/)

---

**Previous Topic**: [← 01. Introduction to React](./01_Introduction_to_React.md)

**Next Topic**: [03. JSX Syntax →](./03_JSX_Syntax.md)
