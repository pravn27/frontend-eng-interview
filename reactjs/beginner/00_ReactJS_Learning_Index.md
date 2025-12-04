# 📚 ReactJS Beginner Learning Curriculum

> A comprehensive guide to mastering React fundamentals with 2025 industry standards.

---

## 🎯 Learning Objectives

By completing this curriculum, you will:

- ✅ Understand React's core philosophy and mental model
- ✅ Build components using modern functional patterns with Hooks
- ✅ Master state management and data flow
- ✅ Handle user interactions and events effectively
- ✅ Create dynamic, responsive user interfaces
- ✅ Implement routing and styling best practices

---

## 📖 Curriculum Overview

### 🏗️ FUNDAMENTALS

| #   | Topic                                                                        | Description                                       | Status |
| --- | ---------------------------------------------------------------------------- | ------------------------------------------------- | ------ |
| 01  | [Introduction to React](./01_Introduction_to_React.md)                       | What is React, Why React, Virtual DOM, SPA vs MPA | ⬜     |
| 02  | [Setup Development Environment](./02_Setup_Development_Environment.md)       | Vite, Node.js, VS Code, React DevTools            | ⬜     |
| 03  | [JSX Syntax](./03_JSX_Syntax.md)                                             | JSX fundamentals, expressions, attributes         | ⬜     |
| 04  | [Element vs Component vs Instance](./04_Element_vs_Component_vs_Instance.md) | Core mental model of React                        | ⬜     |
| 05  | [Components](./05_Components.md)                                             | Functional vs Class, Composition, Naming          | ⬜     |
| 06  | [Keeping Components Pure](./06_Keeping_Components_Pure.md)                   | Purity rules, side effects, StrictMode            | ⬜     |

### 📊 DATA FLOW

| #   | Topic                                                                          | Description                             | Status |
| --- | ------------------------------------------------------------------------------ | --------------------------------------- | ------ |
| 07  | [Props](./07_Props.md)                                                         | Passing data, destructuring, PropTypes  | ⬜     |
| 08  | [State and useState](./08_State_and_useState.md)                               | Managing internal state with Hooks      | ⬜     |
| 09  | [State as Snapshot](./09_State_as_Snapshot.md)                                 | Understanding state behavior in renders | ⬜     |
| 10  | [Updating Objects & Arrays in State](./10_Updating_Objects_Arrays_in_State.md) | Immutable updates, spread operator      | ⬜     |

### ⚡ INTERACTIVITY

| #   | Topic                                                  | Description                        | Status |
| --- | ------------------------------------------------------ | ---------------------------------- | ------ |
| 11  | [Event Handling](./11_Event_Handling.md)               | Events, handlers, synthetic events | ⬜     |
| 12  | [Conditional Rendering](./12_Conditional_Rendering.md) | if/else, ternary, && operator      | ⬜     |
| 13  | [Lists and Keys](./13_Lists_and_Keys.md)               | map(), key importance, filtering   | ⬜     |
| 14  | [Forms in React](./14_Forms_in_React.md)               | Controlled components, validation  | ⬜     |

### 🎨 NAVIGATION & STYLING

| #   | Topic                                              | Description                              | Status |
| --- | -------------------------------------------------- | ---------------------------------------- | ------ |
| 15  | [React Router Basics](./15_React_Router_Basics.md) | Routes, navigation, parameters           | ⬜     |
| 16  | [Styling in React](./16_Styling_in_React.md)       | CSS Modules, Tailwind, Styled Components | ⬜     |

---

## 🛠️ Prerequisites

Before starting, ensure you have:

- ✅ **HTML/CSS** - Solid understanding of markup and styling
- ✅ **JavaScript ES6+** - Arrow functions, destructuring, spread, modules, promises
- ✅ **Node.js** - v18+ installed ([download](https://nodejs.org/))
- ✅ **Code Editor** - VS Code recommended ([download](https://code.visualstudio.com/))

---

## 📂 Each Topic Includes

```
📖 What is it?          - Clear definition
🤔 Why do we need it?   - Purpose and motivation
📚 Core Concepts        - Key principles explained
💻 Demo Code            - 2025 industry standard examples
✅ Best Practices       - Do's
❌ Common Mistakes      - Don'ts
🏋️ Practice Exercises  - Hands-on challenges
🎤 Interview Q&A       - Key interview questions
📎 References          - Official docs & resources
```

---

## 🚀 Quick Start

### Option 1: Create a new React project (Recommended)

```bash
# Using Vite (2025 standard - faster than CRA)
npm create vite@latest my-react-app -- --template react

# Navigate to project
cd my-react-app

# Install dependencies
npm install

# Start development server
npm run dev
```

### Option 2: Try React online

- [React Playground](https://react.dev/learn)
- [CodeSandbox](https://codesandbox.io/)
- [StackBlitz](https://stackblitz.com/)

---

## 📈 Learning Path Visualization

```
┌─────────────────────────────────────────────────────────────────┐
│                         FUNDAMENTALS                             │
│  ┌──────────┐  ┌───────┐  ┌─────┐  ┌────────────┐  ┌──────────┐│
│  │  Intro   │→ │ Setup │→ │ JSX │→ │ Element vs │→ │Components││
│  │          │  │       │  │     │  │ Component  │  │          ││
│  └──────────┘  └───────┘  └─────┘  └────────────┘  └──────────┘│
└─────────────────────────────────────────────────────────────────┘
                              ↓
┌─────────────────────────────────────────────────────────────────┐
│                          DATA FLOW                               │
│  ┌───────┐  ┌─────────────┐  ┌──────────┐  ┌─────────────────┐ │
│  │ Props │→ │ State/Hook  │→ │ Snapshot │→ │ Update Objects  │ │
│  │       │  │             │  │          │  │ & Arrays        │ │
│  └───────┘  └─────────────┘  └──────────┘  └─────────────────┘ │
└─────────────────────────────────────────────────────────────────┘
                              ↓
┌─────────────────────────────────────────────────────────────────┐
│                        INTERACTIVITY                             │
│  ┌────────┐  ┌─────────────┐  ┌───────────┐  ┌───────┐         │
│  │ Events │→ │ Conditional │→ │ Lists/Keys│→ │ Forms │         │
│  │        │  │ Rendering   │  │           │  │       │         │
│  └────────┘  └─────────────┘  └───────────┘  └───────┘         │
└─────────────────────────────────────────────────────────────────┘
                              ↓
┌─────────────────────────────────────────────────────────────────┐
│                     NAVIGATION & STYLING                         │
│  ┌──────────────┐  ┌─────────────────┐                          │
│  │ React Router │→ │ Styling Options │                          │
│  │              │  │                 │                          │
│  └──────────────┘  └─────────────────┘                          │
└─────────────────────────────────────────────────────────────────┘
```

---

## 📚 Official References

- [React Official Documentation](https://react.dev/)
- [React GitHub Repository](https://github.com/facebook/react)
- [Vite Documentation](https://vitejs.dev/)
- [React Router Documentation](https://reactrouter.com/)

---

## 🎯 How to Use This Curriculum

1. **Sequential Learning**: Follow topics in order (01 → 16)
2. **Practice Each Topic**: Complete exercises before moving on
3. **Build Projects**: Apply concepts in real mini-projects
4. **Review Interview Q&A**: Prepare for technical interviews
5. **Mark Progress**: Update status (⬜ → ✅) as you complete

---

> **Note**: This curriculum follows **React 19** and **2025 industry standards**. We focus on **functional components with Hooks** as the primary pattern.

---

**Happy Learning! 🚀**
