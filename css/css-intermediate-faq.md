# CSS Interview FAQ - Intermediate Level ⭐⭐

> **Target Audience**: Frontend Engineers with CSS fundamentals  
> **Focus**: Advanced layouts, animations, preprocessors, methodologies  
> **Last Updated**: December 2025

---

## Table of Contents

1. [Advanced Flexbox patterns](#1-advanced-flexbox-patterns)
2. [Advanced CSS Grid patterns](#2-advanced-css-grid-patterns)
3. [CSS Animations vs Transitions](#3-css-animations-vs-transitions)
4. [CSS Transform and 3D transforms](#4-css-transform-and-3d-transforms)
5. [CSS Methodologies (BEM, SMACSS, OOCSS)](#5-css-methodologies-bem-smacss-oocss)
6. [CSS Preprocessors (Sass, Less)](#6-css-preprocessors-sass-less)
7. [CSS Architecture and Scalability](#7-css-architecture-and-scalability)
8. [Advanced Selectors (:has, :is, :where)](#8-advanced-selectors-has-is-where)
9. [CSS Clipping and Masking](#9-css-clipping-and-masking)
10. [CSS Filters and Blend Modes](#10-css-filters-and-blend-modes)
11. [Responsive Typography (clamp, fluid)](#11-responsive-typography-clamp-fluid)
12. [CSS Logical Properties](#12-css-logical-properties)
13. [CSS Aspect Ratio](#13-css-aspect-ratio)
14. [CSS Scroll Snap](#14-css-scroll-snap)
15. [CSS Container Queries](#15-css-container-queries)

---

## 1. Advanced Flexbox patterns [⭐⭐]

### 🎯 Concept & Purpose

Flexbox is a one-dimensional layout system for arranging items in rows or columns. Advanced patterns solve common layout challenges:

- **Equal height columns**: Automatic height matching
- **Sticky footer**: Footer at bottom regardless of content
- **Holy Grail layout**: Header, footer, sidebar, main content
- **Card layouts**: Responsive, equal-height cards

### 📝 Answer

**1. Equal Height Cards:**

```html
<div class="card-container">
  <div class="card">
    <h3>Card 1</h3>
    <p>Short content</p>
    <button>Action</button>
  </div>
  <div class="card">
    <h3>Card 2</h3>
    <p>
      Much longer content that spans multiple lines and makes this card taller
    </p>
    <button>Action</button>
  </div>
  <div class="card">
    <h3>Card 3</h3>
    <p>Medium content</p>
    <button>Action</button>
  </div>
</div>
```

```css
.card-container {
  display: flex;
  gap: 20px;
  flex-wrap: wrap;
}

.card {
  flex: 1 1 300px; /* grow shrink basis */
  display: flex;
  flex-direction: column;
  border: 1px solid #ddd;
  border-radius: 8px;
  padding: 20px;
}

.card h3 {
  margin-top: 0;
}

.card p {
  flex: 1; /* Takes up available space */
}

.card button {
  margin-top: auto; /* Push to bottom */
  align-self: flex-start;
}
```

**2. Sticky Footer (Flexbox):**

```html
<body>
  <header>Header</header>
  <main>Main Content</main>
  <footer>Footer</footer>
</body>
```

```css
body {
  min-height: 100vh;
  display: flex;
  flex-direction: column;
  margin: 0;
}

header {
  background: #333;
  color: white;
  padding: 20px;
}

main {
  flex: 1; /* Takes remaining space */
  padding: 20px;
}

footer {
  background: #333;
  color: white;
  padding: 20px;
  margin-top: auto;
}
```

**3. Holy Grail Layout:**

```html
<div class="holy-grail">
  <header>Header</header>
  <div class="content">
    <nav>Navigation</nav>
    <main>Main Content</main>
    <aside>Sidebar</aside>
  </div>
  <footer>Footer</footer>
</div>
```

```css
.holy-grail {
  min-height: 100vh;
  display: flex;
  flex-direction: column;
}

header,
footer {
  background: #333;
  color: white;
  padding: 20px;
}

.content {
  flex: 1;
  display: flex;
  gap: 20px;
  padding: 20px;
}

nav {
  flex: 0 0 200px; /* Fixed width sidebar */
  background: #f0f0f0;
  padding: 20px;
}

main {
  flex: 1; /* Takes remaining space */
  background: white;
  padding: 20px;
}

aside {
  flex: 0 0 250px;
  background: #f0f0f0;
  padding: 20px;
}

/* Responsive */
@media (max-width: 768px) {
  .content {
    flex-direction: column;
  }

  nav,
  aside {
    flex: 0 0 auto;
  }
}
```

**4. Centering (Multiple Methods):**

```css
/* Method 1: Flexbox centering */
.center-flex {
  display: flex;
  justify-content: center; /* Horizontal */
  align-items: center; /* Vertical */
  min-height: 100vh;
}

/* Method 2: Margin auto */
.center-margin {
  display: flex;
  min-height: 100vh;
}

.center-margin > * {
  margin: auto;
}

/* Method 3: Absolute positioning + flex */
.center-absolute {
  position: relative;
  min-height: 100vh;
}

.center-absolute > * {
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
}
```

**5. Responsive Navigation:**

```html
<nav class="navbar">
  <div class="logo">Logo</div>
  <ul class="nav-links">
    <li><a href="#">Home</a></li>
    <li><a href="#">About</a></li>
    <li><a href="#">Services</a></li>
    <li><a href="#">Contact</a></li>
  </ul>
  <button class="menu-toggle">☰</button>
</nav>
```

```css
.navbar {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 1rem 2rem;
  background: #333;
  color: white;
}

.nav-links {
  display: flex;
  gap: 2rem;
  list-style: none;
  margin: 0;
  padding: 0;
}

.nav-links a {
  color: white;
  text-decoration: none;
}

.menu-toggle {
  display: none;
  background: none;
  border: none;
  color: white;
  font-size: 1.5rem;
  cursor: pointer;
}

@media (max-width: 768px) {
  .navbar {
    flex-wrap: wrap;
  }

  .nav-links {
    flex-direction: column;
    width: 100%;
    display: none;
  }

  .nav-links.active {
    display: flex;
  }

  .menu-toggle {
    display: block;
  }
}
```

**6. Auto-fit Grid with Flexbox Fallback:**

```css
.grid-container {
  display: flex;
  flex-wrap: wrap;
  gap: 20px;
}

.grid-item {
  flex: 1 1 calc(33.333% - 20px); /* 3 columns */
  min-width: 250px; /* Minimum before wrapping */
}

@media (max-width: 768px) {
  .grid-item {
    flex: 1 1 calc(50% - 20px); /* 2 columns */
  }
}

@media (max-width: 480px) {
  .grid-item {
    flex: 1 1 100%; /* 1 column */
  }
}
```

### 💡 Key Takeaways

- Flexbox excels at one-dimensional layouts
- `flex: 1` makes items grow to fill space
- `margin: auto` works for centering in flex containers
- Sticky footer: `body { display: flex; flex-direction: column; min-height: 100vh; }`
- `gap` property simplifies spacing
- **Interview Tip**: Explain `flex: 1 1 300px` (grow, shrink, basis)

### 🔗 Resources

- [MDN: Flexbox](https://developer.mozilla.org/en-US/docs/Learn/CSS/CSS_layout/Flexbox)
- [CSS-Tricks: Flexbox Guide](https://css-tricks.com/snippets/css/a-guide-to-flexbox/)
- [Flexbox Froggy (Game)](https://flexboxfroggy.com/)
- [Flexbox Patterns](https://www.flexboxpatterns.com/)

### 🏢 Real-World Application

Navigation bars, card layouts, dashboards, and most modern UI components use Flexbox. Bootstrap and Tailwind use Flexbox utilities extensively.

---

## 2. Advanced CSS Grid patterns [⭐⭐]

### 🎯 Concept & Purpose

CSS Grid is a two-dimensional layout system perfect for complex layouts:

- **Magazine layouts**: Overlapping, asymmetric designs
- **Dashboard layouts**: Multiple panels and widgets
- **Responsive grids**: Auto-fit, auto-fill columns
- **Named grid areas**: Semantic layout definition

### 📝 Answer

**1. Responsive Auto-fit Grid:**

```css
.grid-auto {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(250px, 1fr));
  gap: 20px;
}

/* auto-fit: Collapses empty tracks
   auto-fill: Keeps empty tracks
   minmax(250px, 1fr): Min 250px, max 1fr
*/
```

**2. Named Grid Areas (Magazine Layout):**

```html
<div class="magazine">
  <header class="header">Header</header>
  <nav class="nav">Navigation</nav>
  <main class="main">Main Content</main>
  <aside class="sidebar">Sidebar</aside>
  <footer class="footer">Footer</footer>
</div>
```

```css
.magazine {
  display: grid;
  grid-template-areas:
    "header header header"
    "nav main sidebar"
    "footer footer footer";
  grid-template-columns: 200px 1fr 250px;
  grid-template-rows: auto 1fr auto;
  gap: 20px;
  min-height: 100vh;
}

.header {
  grid-area: header;
}
.nav {
  grid-area: nav;
}
.main {
  grid-area: main;
}
.sidebar {
  grid-area: sidebar;
}
.footer {
  grid-area: footer;
}

@media (max-width: 768px) {
  .magazine {
    grid-template-areas:
      "header"
      "nav"
      "main"
      "sidebar"
      "footer";
    grid-template-columns: 1fr;
  }
}
```

**3. Overlapping Grid Items:**

```css
.grid-overlap {
  display: grid;
  grid-template-columns: repeat(4, 1fr);
  grid-template-rows: repeat(3, 200px);
  gap: 10px;
}

.item-1 {
  grid-column: 1 / 3; /* Spans 2 columns */
  grid-row: 1 / 3; /* Spans 2 rows */
  z-index: 1;
}

.item-2 {
  grid-column: 2 / 4;
  grid-row: 2 / 4;
  z-index: 2; /* Overlaps item-1 */
}
```

**4. Dashboard Layout:**

```css
.dashboard {
  display: grid;
  grid-template-columns: repeat(12, 1fr);
  grid-auto-rows: minmax(100px, auto);
  gap: 20px;
  padding: 20px;
}

.widget-large {
  grid-column: span 8;
  grid-row: span 2;
}

.widget-medium {
  grid-column: span 4;
  grid-row: span 2;
}

.widget-small {
  grid-column: span 4;
  grid-row: span 1;
}

@media (max-width: 768px) {
  .widget-large,
  .widget-medium,
  .widget-small {
    grid-column: span 12;
  }
}
```

**5. Masonry Layout (CSS Grid):**

```css
.masonry {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(250px, 1fr));
  grid-auto-rows: 20px;
  gap: 10px;
}

.masonry-item {
  /* JavaScript calculates span based on content height */
  grid-row: span var(--row-span);
}
```

```javascript
// Calculate row span for masonry
const items = document.querySelectorAll(".masonry-item");
items.forEach((item) => {
  const height = item.offsetHeight;
  const rowSpan = Math.ceil(height / 20);
  item.style.setProperty("--row-span", rowSpan);
});
```

### 💡 Key Takeaways

- Grid is best for two-dimensional layouts
- `auto-fit` vs `auto-fill`: fit collapses empty tracks
- Named grid areas make layouts semantic
- `minmax()` creates responsive columns without media queries
- Grid items can overlap using `z-index`
- **Interview Tip**: Explain when to use Grid vs Flexbox

### 🔗 Resources

- [MDN: CSS Grid](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_Grid_Layout)
- [CSS-Tricks: Grid Guide](https://css-tricks.com/snippets/css/complete-guide-grid/)
- [Grid Garden (Game)](https://cssgridgarden.com/)
- [Grid by Example](https://gridbyexample.com/)

### 🏢 Real-World Application

Pinterest uses Grid for masonry layouts. Dashboards use Grid for widget placement. News sites use Grid for complex article layouts.

---

## 3. CSS Animations vs Transitions [⭐⭐]

### 🎯 Concept & Purpose

**Transitions**: Smooth change between two states (hover, focus)  
**Animations**: Complex, multi-step animations with keyframes

**When to use each:**

- Transitions: Simple state changes (hover effects, toggles)
- Animations: Complex sequences, looping, multiple steps

### 📝 Answer

**Transitions:**

```css
.button {
  background: blue;
  color: white;
  padding: 10px 20px;
  border: none;
  cursor: pointer;

  /* Transition syntax: property duration timing-function delay */
  transition: background 0.3s ease, transform 0.2s ease;
}

.button:hover {
  background: darkblue;
  transform: scale(1.05);
}

/* Transition all properties */
.box {
  transition: all 0.3s ease;
}

/* Multiple transitions */
.card {
  transition: transform 0.3s ease, box-shadow 0.3s ease, opacity 0.2s linear;
}

.card:hover {
  transform: translateY(-5px);
  box-shadow: 0 10px 20px rgba(0, 0, 0, 0.2);
}
```

**Timing Functions:**

```css
.ease {
  transition-timing-function: ease;
} /* Default: slow start, fast middle, slow end */
.linear {
  transition-timing-function: linear;
} /* Constant speed */
.ease-in {
  transition-timing-function: ease-in;
} /* Slow start */
.ease-out {
  transition-timing-function: ease-out;
} /* Slow end */
.ease-in-out {
  transition-timing-function: ease-in-out;
} /* Slow start and end */

/* Custom cubic-bezier */
.custom {
  transition-timing-function: cubic-bezier(
    0.68,
    -0.55,
    0.265,
    1.55
  ); /* Bounce effect */
}

/* Steps (for sprite animations) */
.steps {
  transition-timing-function: steps(4, end);
}
```

**Animations (Keyframes):**

```css
/* Define keyframes */
@keyframes fadeIn {
  from {
    opacity: 0;
  }
  to {
    opacity: 1;
  }
}

/* Or with percentages */
@keyframes slideIn {
  0% {
    transform: translateX(-100%);
    opacity: 0;
  }
  50% {
    opacity: 0.5;
  }
  100% {
    transform: translateX(0);
    opacity: 1;
  }
}

/* Apply animation */
.fade-in {
  animation: fadeIn 1s ease-in-out;
}

.slide-in {
  animation: slideIn 0.5s ease-out;
}

/* Animation shorthand: name duration timing-function delay iteration-count direction fill-mode */
.complex-animation {
  animation: slideIn 2s ease-in-out 0.5s infinite alternate both;
}
```

**Animation Properties:**

```css
.animated {
  animation-name: bounce;
  animation-duration: 2s;
  animation-timing-function: ease-in-out;
  animation-delay: 0.5s;
  animation-iteration-count: infinite; /* or number */
  animation-direction: alternate; /* normal, reverse, alternate, alternate-reverse */
  animation-fill-mode: both; /* none, forwards, backwards, both */
  animation-play-state: running; /* running, paused */
}

/* Pause on hover */
.animated:hover {
  animation-play-state: paused;
}
```

**Practical Examples:**

**1. Loading Spinner:**

```css
@keyframes spin {
  from {
    transform: rotate(0deg);
  }
  to {
    transform: rotate(360deg);
  }
}

.spinner {
  width: 50px;
  height: 50px;
  border: 5px solid #f3f3f3;
  border-top: 5px solid #3498db;
  border-radius: 50%;
  animation: spin 1s linear infinite;
}
```

**2. Pulse Effect:**

```css
@keyframes pulse {
  0%,
  100% {
    transform: scale(1);
    opacity: 1;
  }
  50% {
    transform: scale(1.1);
    opacity: 0.7;
  }
}

.pulse {
  animation: pulse 2s ease-in-out infinite;
}
```

**3. Slide-in on Scroll:**

```css
@keyframes slideInFromLeft {
  from {
    transform: translateX(-100%);
    opacity: 0;
  }
  to {
    transform: translateX(0);
    opacity: 1;
  }
}

.slide-in {
  opacity: 0;
}

.slide-in.visible {
  animation: slideInFromLeft 0.6s ease-out forwards;
}
```

```javascript
// Trigger animation on scroll
const observer = new IntersectionObserver((entries) => {
  entries.forEach((entry) => {
    if (entry.isIntersecting) {
      entry.target.classList.add("visible");
    }
  });
});

document.querySelectorAll(".slide-in").forEach((el) => observer.observe(el));
```

**4. Typewriter Effect:**

```css
@keyframes typing {
  from {
    width: 0;
  }
  to {
    width: 100%;
  }
}

@keyframes blink {
  50% {
    border-color: transparent;
  }
}

.typewriter {
  overflow: hidden;
  border-right: 2px solid;
  white-space: nowrap;
  animation: typing 3.5s steps(40, end), blink 0.75s step-end infinite;
}
```

**Performance Considerations:**

```css
/* ✅ Good: Animate transform and opacity (GPU accelerated) */
.performant {
  transition: transform 0.3s, opacity 0.3s;
}

.performant:hover {
  transform: translateX(10px);
  opacity: 0.8;
}

/* ❌ Bad: Animate layout properties (causes reflow) */
.slow {
  transition: width 0.3s, height 0.3s, margin 0.3s;
}

/* ✅ Use will-change for complex animations */
.optimized {
  will-change: transform, opacity;
  animation: complexAnimation 2s;
}

/* Remove will-change after animation */
.optimized.done {
  will-change: auto;
}
```

### 💡 Key Takeaways

- **Transitions**: Two states, triggered by state change
- **Animations**: Multi-step, can loop, more control
- Animate `transform` and `opacity` for best performance
- Use `will-change` for complex animations
- `animation-fill-mode: forwards` keeps final state
- **Interview Tip**: Explain GPU-accelerated properties
- **Performance**: Avoid animating width, height, margin

### 🔗 Resources

- [MDN: CSS Transitions](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_Transitions)
- [MDN: CSS Animations](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_Animations)
- [Animista (Animation Library)](https://animista.net/)
- [Cubic-bezier.com](https://cubic-bezier.com/)

### 🏢 Real-World Application

Loading spinners, hover effects, page transitions, scroll animations, skeleton screens, and microinteractions all use CSS animations.

---

_[Continuing with more intermediate CSS topics...]_
