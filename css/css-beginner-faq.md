# CSS Interview FAQ - Beginner Level ⭐

> **Target Audience**: Frontend Engineers preparing for CSS fundamentals  
> **Focus**: Selectors, box model, positioning, layouts, responsive basics  
> **Last Updated**: December 2025

---

## Table of Contents

1. [What is CSS and its purpose?](#1-what-is-css-and-its-purpose)
2. [CSS Selectors and Specificity](#2-css-selectors-and-specificity)
3. [The Box Model](#3-the-box-model)
4. [Display property (block, inline, inline-block)](#4-display-property-block-inline-inline-block)
5. [Position property (static, relative, absolute, fixed, sticky)](#5-position-property-static-relative-absolute-fixed-sticky)
6. [Flexbox basics](#6-flexbox-basics)
7. [CSS Grid basics](#7-css-grid-basics)
8. [Units in CSS (px, em, rem, %, vw, vh)](#8-units-in-css-px-em-rem-vw-vh)
9. [Pseudo-classes and Pseudo-elements](#9-pseudo-classes-and-pseudo-elements)
10. [CSS Cascade and Inheritance](#10-css-cascade-and-inheritance)
11. [Responsive Design basics (Media Queries)](#11-responsive-design-basics-media-queries)
12. [CSS Colors and Backgrounds](#12-css-colors-and-backgrounds)
13. [Typography in CSS](#13-typography-in-css)
14. [CSS Transitions](#14-css-transitions)
15. [CSS Variables (Custom Properties)](#15-css-variables-custom-properties)

---

## 1. What is CSS and its purpose? [⭐]

### 🎯 Concept & Purpose

CSS (Cascading Style Sheets) is the language used to describe the presentation of HTML documents. It controls:

- **Visual appearance**: Colors, fonts, spacing
- **Layout**: Positioning and arrangement of elements
- **Responsive design**: Adaptation to different screen sizes
- **Animations**: Visual effects and transitions

**Why it exists:**  
Separates content (HTML) from presentation (CSS), making websites easier to maintain and style consistently.

### 📝 Answer

**CSS Syntax:**

```css
selector {
  property: value;
}
```

**Three Ways to Add CSS:**

**1. Inline CSS (Highest Specificity):**

```html
<p style="color: red; font-size: 16px;">This is red text</p>
```

**2. Internal CSS (In `<style>` tag):**

```html
<!DOCTYPE html>
<html>
  <head>
    <style>
      p {
        color: blue;
        font-size: 16px;
      }
    </style>
  </head>
  <body>
    <p>This is blue text</p>
  </body>
</html>
```

**3. External CSS (Best Practice):**

```html
<!-- index.html -->
<!DOCTYPE html>
<html>
  <head>
    <link rel="stylesheet" href="styles.css" />
  </head>
  <body>
    <p>This is styled text</p>
  </body>
</html>
```

```css
/* styles.css */
p {
  color: green;
  font-size: 16px;
}
```

**Basic CSS Example:**

```css
/* Style all paragraphs */
p {
  color: #333;
  font-size: 16px;
  line-height: 1.6;
}

/* Style element with class */
.highlight {
  background-color: yellow;
  font-weight: bold;
}

/* Style element with ID */
#header {
  background-color: #007bff;
  color: white;
  padding: 20px;
}

/* Style links */
a {
  color: #007bff;
  text-decoration: none;
}

a:hover {
  text-decoration: underline;
}
```

**CSS Layers (Separation of Concerns):**

```
HTML (Structure)    →  <div class="card">
CSS (Presentation)  →  .card { border: 1px solid #ccc; }
JavaScript (Behavior) →  card.addEventListener('click', ...)
```

### 💡 Key Takeaways

- CSS controls visual presentation of HTML
- Separates content from styling
- Three ways to add CSS: inline, internal, external
- External CSS is best for maintainability
- Cascading means styles can override each other
- **Interview Tip**: Explain separation of concerns (HTML/CSS/JS)

### 🔗 Resources

- [MDN: CSS Basics](https://developer.mozilla.org/en-US/docs/Learn/Getting_started_with_the_web/CSS_basics)
- [MDN: CSS Reference](https://developer.mozilla.org/en-US/docs/Web/CSS/Reference)
- [web.dev: Learn CSS](https://web.dev/learn/css/)
- [CSS-Tricks](https://css-tricks.com/)

### 🏢 Real-World Application

Every website uses CSS. Design systems (Material Design, Bootstrap) are built with CSS. Responsive websites adapt using CSS media queries.

---

## 2. CSS Selectors and Specificity [⭐]

### 🎯 Concept & Purpose

CSS selectors target HTML elements for styling. Specificity determines which styles apply when multiple rules target the same element. Understanding this is crucial for:

- **Precise targeting**: Style exactly what you want
- **Avoiding conflicts**: Prevent unexpected style overrides
- **Maintainable code**: Write predictable CSS
- **Debugging**: Understand why styles aren't applying

### 📝 Answer

**Basic Selectors:**

```css
/* Element selector */
p {
  color: blue;
}

/* Class selector */
.highlight {
  background: yellow;
}

/* ID selector */
#header {
  font-size: 24px;
}

/* Universal selector */
* {
  margin: 0;
  padding: 0;
}

/* Attribute selector */
input[type="text"] {
  border: 1px solid #ccc;
}
```

**Combinators:**

```css
/* Descendant selector (space) - all p inside div */
div p {
  color: red;
}

/* Child selector (>) - direct children only */
div > p {
  color: blue;
}

/* Adjacent sibling (+) - immediately following */
h1 + p {
  font-weight: bold;
}

/* General sibling (~) - all following siblings */
h1 ~ p {
  color: gray;
}
```

**Attribute Selectors:**

```css
/* Has attribute */
a[target] {
  color: red;
}

/* Exact value */
a[href="https://example.com"]
{
  color: green;
}

/* Contains value */
a[href*="example"] {
  color: blue;
}

/* Starts with */
a[href^="https"] {
  color: purple;
}

/* Ends with */
a[href$=".pdf"] {
  color: orange;
}

/* Contains word */
div[class~="active"] {
  border: 2px solid red;
}
```

**Pseudo-classes:**

```css
/* Link states */
a:link {
  color: blue;
}
a:visited {
  color: purple;
}
a:hover {
  color: red;
}
a:active {
  color: orange;
}

/* Form states */
input:focus {
  border-color: blue;
}
input:disabled {
  opacity: 0.5;
}
input:checked {
  background: green;
}

/* Structural */
li:first-child {
  font-weight: bold;
}
li:last-child {
  border-bottom: none;
}
li:nth-child(odd) {
  background: #f0f0f0;
}
li:nth-child(3n) {
  color: red;
}

/* Negation */
p:not(.special) {
  color: gray;
}
```

**Pseudo-elements:**

```css
/* First letter */
p::first-letter {
  font-size: 2em;
  font-weight: bold;
}

/* First line */
p::first-line {
  color: blue;
}

/* Before/After */
.icon::before {
  content: "→ ";
  color: blue;
}

.icon::after {
  content: " ✓";
  color: green;
}

/* Selection */
::selection {
  background: yellow;
  color: black;
}
```

**Specificity Calculation:**

```
Specificity is calculated as: (inline, IDs, classes, elements)

Style                           Specificity
----------------------------------------------
*                               (0, 0, 0, 0)
p                               (0, 0, 0, 1)
.class                          (0, 0, 1, 0)
#id                             (0, 1, 0, 0)
style=""                        (1, 0, 0, 0)
!important                      Overrides everything

p                               (0, 0, 0, 1)
div p                           (0, 0, 0, 2)
.class p                        (0, 0, 1, 1)
div.class p                     (0, 0, 1, 2)
#id p                           (0, 1, 0, 1)
div#id .class p                 (0, 1, 1, 2)
```

**Specificity Examples:**

```css
/* Specificity: (0, 0, 0, 1) */
p {
  color: black;
}

/* Specificity: (0, 0, 1, 0) - WINS */
.text {
  color: blue;
}

/* Specificity: (0, 1, 0, 0) - WINS */
#main {
  color: red;
}

/* Specificity: (0, 1, 1, 1) - WINS */
#main .text p {
  color: green;
}
```

```html
<div id="main">
  <p class="text">What color is this?</p>
  <!-- Answer: green (highest specificity) -->
</div>
```

**The `!important` Rule:**

```css
/* Normal specificity */
p {
  color: blue;
}

/* Higher specificity */
#main p {
  color: red;
}

/* !important overrides everything (use sparingly!) */
p {
  color: green !important; /* This wins */
}
```

**Specificity Best Practices:**

```css
/* ❌ Bad: Too specific, hard to override */
div#header nav.main-nav ul.menu li.item a.link {
  color: blue;
}

/* ✅ Good: Low specificity, easy to override */
.nav-link {
  color: blue;
}

/* ❌ Bad: Overusing !important */
.button {
  background: blue !important;
  color: white !important;
}

/* ✅ Good: Use specificity properly */
.button {
  background: blue;
  color: white;
}

.button-primary {
  background: green;
}
```

**Grouping Selectors:**

```css
/* Group multiple selectors */
h1,
h2,
h3 {
  font-family: Arial, sans-serif;
  color: #333;
}

/* Multiple classes */
.button,
.link,
.card {
  border-radius: 4px;
}
```

**Complex Selectors:**

```css
/* Style all links inside nav that are not active */
nav a:not(.active) {
  color: gray;
}

/* Style every 3rd item starting from 2nd */
li:nth-child(3n + 2) {
  background: lightblue;
}

/* Style inputs that are required and invalid */
input:required:invalid {
  border-color: red;
}

/* Style div with both classes */
div.card.featured {
  border: 2px solid gold;
}
```

### 💡 Key Takeaways

- Selectors target elements for styling
- Specificity determines which styles win
- Order: inline > ID > class > element
- `!important` overrides everything (use sparingly)
- Keep specificity low for maintainability
- **Interview Tip**: Calculate specificity (inline, IDs, classes, elements)
- **Common Mistake**: Overusing IDs and `!important`

### 🔗 Resources

- [MDN: CSS Selectors](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_Selectors)
- [MDN: Specificity](https://developer.mozilla.org/en-US/docs/Web/CSS/Specificity)
- [Specificity Calculator](https://specificity.keegan.st/)
- [CSS Diner (Selector Game)](https://flukeout.github.io/)

### 🏢 Real-World Application

Complex sites use advanced selectors for precise styling. CSS frameworks use class-based selectors. Form validation uses pseudo-classes. Design systems maintain low specificity.

---

## 3. The Box Model [⭐]

### 🎯 Concept & Purpose

The CSS Box Model describes how elements are rendered as rectangular boxes with:

- **Content**: The actual content (text, images)
- **Padding**: Space between content and border
- **Border**: Line around padding
- **Margin**: Space outside border

Understanding the box model is fundamental for:

- **Layout control**: Precise spacing and sizing
- **Debugging**: Understanding why elements don't fit
- **Responsive design**: Calculating element dimensions

### 📝 Answer

**Box Model Diagram:**

```
┌─────────────────────────────────────┐
│           Margin (transparent)       │
│  ┌───────────────────────────────┐  │
│  │      Border                    │  │
│  │  ┌─────────────────────────┐  │  │
│  │  │    Padding              │  │  │
│  │  │  ┌───────────────────┐  │  │  │
│  │  │  │    Content        │  │  │  │
│  │  │  │   (text, images)  │  │  │  │
│  │  │  └───────────────────┘  │  │  │
│  │  └─────────────────────────┘  │  │
│  └───────────────────────────────┘  │
└─────────────────────────────────────┘
```

**Box Model Properties:**

```css
.box {
  /* Content */
  width: 200px;
  height: 100px;

  /* Padding (inside border) */
  padding: 20px; /* All sides */
  /* OR */
  padding-top: 10px;
  padding-right: 15px;
  padding-bottom: 10px;
  padding-left: 15px;
  /* OR */
  padding: 10px 15px; /* vertical horizontal */
  padding: 10px 15px 20px; /* top horizontal bottom */
  padding: 10px 15px 20px 25px; /* top right bottom left (clockwise) */

  /* Border */
  border: 2px solid #333;
  /* OR */
  border-width: 2px;
  border-style: solid; /* solid, dashed, dotted, double, none */
  border-color: #333;
  /* OR individual sides */
  border-top: 1px solid red;
  border-right: 2px dashed blue;

  /* Margin (outside border) */
  margin: 20px;
  /* Same syntax as padding */
}

/* Total width = width + padding-left + padding-right + border-left + border-right
   Total width = 200px + 20px + 20px + 2px + 2px = 244px */
```

**Content-Box vs Border-Box:**

```css
/* Default: content-box */
.box1 {
  box-sizing: content-box; /* Default */
  width: 200px;
  padding: 20px;
  border: 2px solid black;
  /* Total width: 200 + 40 + 4 = 244px */
}

/* Better: border-box */
.box2 {
  box-sizing: border-box;
  width: 200px;
  padding: 20px;
  border: 2px solid black;
  /* Total width: 200px (padding and border included) */
}

/* Best practice: Apply to all elements */
*,
*::before,
*::after {
  box-sizing: border-box;
}
```

**Practical Example:**

```html
<div class="card">
  <h2>Card Title</h2>
  <p>Card content goes here.</p>
</div>
```

```css
.card {
  /* Content */
  width: 300px;

  /* Padding: space inside */
  padding: 20px;

  /* Border */
  border: 1px solid #ddd;
  border-radius: 8px;

  /* Margin: space outside */
  margin: 20px;

  /* Use border-box for easier calculations */
  box-sizing: border-box;

  /* Total width: 300px (with border-box) */
  /* Without border-box: 300 + 40 + 2 = 342px */
}
```

**Margin Collapsing:**

```css
/* Vertical margins collapse */
.box1 {
  margin-bottom: 30px;
}

.box2 {
  margin-top: 20px;
}

/* Actual space between: 30px (not 50px!)
   The larger margin wins */
```

```css
/* Horizontal margins don't collapse */
.box1 {
  margin-right: 30px;
}

.box2 {
  margin-left: 20px;
}

/* Actual space between: 50px (30 + 20) */
```

**Negative Margins:**

```css
.overlap {
  margin-top: -20px; /* Moves element up */
  margin-left: -10px; /* Moves element left */
}
```

**Auto Margins (Centering):**

```css
.center {
  width: 600px;
  margin: 0 auto; /* Centers horizontally */
}

/* Doesn't work for vertical centering with margin */
```

**Padding vs Margin - When to Use:**

```css
/* Use PADDING when:
   - You want space inside the element
   - Background color should extend to the space
   - Border should be outside the space
*/
.button {
  padding: 10px 20px;
  background: blue;
  border: 2px solid darkblue;
  /* Padding creates clickable area */
}

/* Use MARGIN when:
   - You want space between elements
   - Creating gaps in layouts
   - Centering elements
*/
.card {
  margin: 20px; /* Space around card */
  padding: 20px; /* Space inside card */
}
```

**Box Model Debugging:**

```css
/* Visualize box model */
* {
  outline: 1px solid red; /* Shows boundaries without affecting layout */
}

/* Or use browser DevTools:
   - Chrome: Right-click → Inspect → Computed tab
   - Shows visual box model diagram
*/
```

**Responsive Box Model:**

```css
.responsive-box {
  width: 100%; /* Full width */
  max-width: 600px; /* But not too wide */
  padding: 5%; /* Responsive padding */
  margin: 0 auto; /* Center */
  box-sizing: border-box;
}

@media (max-width: 768px) {
  .responsive-box {
    padding: 20px; /* Fixed padding on mobile */
  }
}
```

### 💡 Key Takeaways

- Box model: content → padding → border → margin
- `box-sizing: border-box` makes width calculations easier
- Vertical margins collapse, horizontal don't
- Padding is inside border, margin is outside
- Use margin for spacing between elements
- Use padding for spacing inside elements
- **Interview Tip**: Draw the box model diagram
- **Best Practice**: Always use `box-sizing: border-box`

### 🔗 Resources

- [MDN: Box Model](https://developer.mozilla.org/en-US/docs/Learn/CSS/Building_blocks/The_box_model)
- [MDN: box-sizing](https://developer.mozilla.org/en-US/docs/Web/CSS/box-sizing)
- [CSS-Tricks: Box Sizing](https://css-tricks.com/box-sizing/)
- [Interactive Box Model](https://codepen.io/carolineartz/full/ogVXZj)

### 🏢 Real-World Application

Every CSS layout uses the box model. Buttons use padding for clickable area. Cards use padding and margin for spacing. Responsive designs adjust padding/margin for different screens.

---

_[Continuing with more beginner CSS questions...]_
