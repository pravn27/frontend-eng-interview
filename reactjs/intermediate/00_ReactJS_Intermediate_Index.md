# 🚀 ReactJS Intermediate Learning Curriculum

## Overview

This intermediate curriculum builds upon the beginner fundamentals to explore advanced hooks, patterns, and optimization techniques used in production React applications.

**Prerequisites:**

- ✅ Completed [Beginner Curriculum](../beginner/00_ReactJS_Learning_Index.md)
- ✅ Comfortable with useState, props, and component composition
- ✅ Familiarity with ES6+ JavaScript features

---

## 📚 Learning Path

### 🎣 ADVANCED HOOKS

| #   | Topic                                                | Description                         | Status |
| --- | ---------------------------------------------------- | ----------------------------------- | ------ |
| 01  | [useEffect Hook](./01_useEffect_Hook.md)             | Side effects, cleanup, dependencies | ⬜     |
| 02  | [useRef Hook](./02_useRef_Hook.md)                   | DOM access, mutable values, timers  | ⬜     |
| 03  | [useContext Hook](./03_useContext_Hook.md)           | Context API, avoiding prop drilling | ⬜     |
| 04  | [Custom Hooks](./04_Custom_Hooks.md)                 | Creating reusable hook logic        | ⬜     |
| 05  | [useReducer Hook](./05_useReducer_Hook.md)           | Complex state, dispatch actions     | ⬜     |
| 06  | [useMemo & useCallback](./06_useMemo_useCallback.md) | Memoization for performance         | ⬜     |

### ⚡ PERFORMANCE & OPTIMIZATION

| #   | Topic                                                        | Description                          | Status |
| --- | ------------------------------------------------------------ | ------------------------------------ | ------ |
| 07  | [React.memo](./07_React_Memo.md)                             | Preventing unnecessary re-renders    | ⬜     |
| 08  | [Error Boundaries](./08_Error_Boundaries.md)                 | Graceful error handling              | ⬜     |
| 09  | [Refs & ForwardRef](./09_Refs_and_ForwardRef.md)             | Advanced ref patterns                | ⬜     |
| 10  | [React Portals](./10_React_Portals.md)                       | Rendering outside DOM hierarchy      | ⬜     |
| 11  | [Code Splitting & Lazy](./11_Code_Splitting_Lazy_Loading.md) | Dynamic imports, bundle optimization | ⬜     |
| 12  | [Suspense](./12_Suspense.md)                                 | Loading states, concurrent features  | ⬜     |

### 🏗️ DESIGN PATTERNS

| #   | Topic                                                      | Description                           | Status |
| --- | ---------------------------------------------------------- | ------------------------------------- | ------ |
| 13  | [Higher Order Components](./13_Higher_Order_Components.md) | HOC pattern and use cases             | ⬜     |
| 14  | [Render Props Pattern](./14_Render_Props_Pattern.md)       | Sharing behavior via render functions | ⬜     |
| 15  | [Compound Components](./15_Compound_Components.md)         | Flexible, implicit component APIs     | ⬜     |

### 🔧 REAL-WORLD SKILLS

| #   | Topic                                                        | Description                            | Status |
| --- | ------------------------------------------------------------ | -------------------------------------- | ------ |
| 16  | [Performance Optimization](./16_Performance_Optimization.md) | Profiling, bottlenecks, best practices | ⬜     |
| 17  | [Data Fetching Patterns](./17_Data_Fetching_Patterns.md)     | API integration, caching, error states | ⬜     |
| 18  | [Testing React Components](./18_Testing_React_Components.md) | Jest, React Testing Library, mocking   | ⬜     |

---

## 📋 How to Use This Curriculum

### Learning Flow

```
┌─────────────────────────────────────────────────────────────────┐
│                    INTERMEDIATE REACT PATH                       │
├─────────────────────────────────────────────────────────────────┤
│                                                                  │
│  ┌──────────────┐    ┌──────────────┐    ┌──────────────┐       │
│  │  useEffect   │───▶│   useRef     │───▶│  useContext  │       │
│  │  (Effects)   │    │  (DOM/Refs)  │    │   (Context)  │       │
│  └──────────────┘    └──────────────┘    └──────────────┘       │
│         │                                       │                │
│         ▼                                       ▼                │
│  ┌──────────────┐    ┌──────────────┐    ┌──────────────┐       │
│  │ Custom Hooks │◀───│  useReducer  │───▶│ useMemo/     │       │
│  │  (Reuse)     │    │   (State)    │    │ useCallback  │       │
│  └──────────────┘    └──────────────┘    └──────────────┘       │
│         │                   │                   │                │
│         ▼                   ▼                   ▼                │
│  ┌─────────────────────────────────────────────────────────┐    │
│  │           PERFORMANCE & ERROR HANDLING                   │    │
│  │   React.memo │ Error Boundaries │ Portals │ Suspense    │    │
│  └─────────────────────────────────────────────────────────┘    │
│                              │                                   │
│                              ▼                                   │
│  ┌─────────────────────────────────────────────────────────┐    │
│  │              DESIGN PATTERNS                             │    │
│  │   HOCs │ Render Props │ Compound Components              │    │
│  └─────────────────────────────────────────────────────────┘    │
│                              │                                   │
│                              ▼                                   │
│  ┌─────────────────────────────────────────────────────────┐    │
│  │             REAL-WORLD APPLICATION                       │    │
│  │   Performance │ Data Fetching │ Testing                  │    │
│  └─────────────────────────────────────────────────────────┘    │
│                                                                  │
└─────────────────────────────────────────────────────────────────┘
```

### Each Topic Contains

1. **📖 Concept Explanation** - Why and when to use
2. **💻 Code Examples** - Practical implementations
3. **🏋️ Practice Exercises** - Hands-on challenges
4. **❓ Interview Questions** - Common interview topics
5. **⚠️ Common Mistakes** - Pitfalls to avoid

---

## 🎯 Learning Objectives

After completing this curriculum, you will be able to:

### Hooks Mastery

- [ ] Manage side effects with useEffect (data fetching, subscriptions, timers)
- [ ] Access DOM elements and persist values with useRef
- [ ] Share state globally with Context API
- [ ] Create reusable custom hooks
- [ ] Handle complex state logic with useReducer
- [ ] Optimize performance with useMemo and useCallback

### Performance Skills

- [ ] Prevent unnecessary re-renders with React.memo
- [ ] Handle errors gracefully with Error Boundaries
- [ ] Forward refs and use imperative handles
- [ ] Render content outside the DOM hierarchy with Portals
- [ ] Implement code splitting with React.lazy and Suspense

### Design Patterns

- [ ] Apply Higher Order Component pattern
- [ ] Implement Render Props for code sharing
- [ ] Create flexible Compound Component APIs

### Real-World Application

- [ ] Profile and optimize React application performance
- [ ] Implement robust data fetching with loading/error states
- [ ] Write comprehensive tests with Jest and React Testing Library

---

## 🗓️ Recommended Timeline

| Week | Topics                                  | Focus                        |
| ---- | --------------------------------------- | ---------------------------- |
| 1    | useEffect, useRef                       | Side effects & DOM           |
| 2    | useContext, Custom Hooks                | State sharing & reuse        |
| 3    | useReducer, useMemo/useCallback         | Complex state & optimization |
| 4    | React.memo, Error Boundaries, Refs      | Performance & error handling |
| 5    | Portals, Code Splitting, Suspense       | Advanced rendering           |
| 6    | HOCs, Render Props, Compound Components | Design patterns              |
| 7-8  | Performance, Data Fetching, Testing     | Real-world skills            |

---

## 🔗 Resources

### Official Documentation

- [React Docs - Escape Hatches](https://react.dev/learn/escape-hatches)
- [React Docs - Managing State](https://react.dev/learn/managing-state)
- [React Reference](https://react.dev/reference/react)

### Tools for Practice

- [React DevTools Profiler](https://react.dev/learn/react-developer-tools)
- [Vite](https://vitejs.dev/)
- [React Testing Library](https://testing-library.com/docs/react-testing-library/intro/)

---

## 🚦 Progress Tracker

Mark topics as complete by changing ⬜ to ✅ in the tables above.

**Current Progress: 0/18 topics completed**

---

> 💡 **Tip**: Each topic builds on the previous ones. While you can jump to specific topics, the recommended order ensures you have the necessary foundation for each concept.

**Ready to level up? Start with [01 - useEffect Hook](./01_useEffect_Hook.md)!**
