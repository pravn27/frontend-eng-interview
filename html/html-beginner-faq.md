# HTML Interview FAQ - Beginner Level ⭐

> **Target Audience**: Frontend Engineers preparing for HTML fundamentals
> **Focus**: Core concepts, semantic markup, forms, basic accessibility
> **Last Updated**: December 2025

---

## Table of Contents

1. [What is HTML and its purpose?](#1-what-is-html-and-its-purpose)
2. [Difference between HTML and HTML5](#2-difference-between-html-and-html5)
3. [What are semantic HTML elements?](#3-what-are-semantic-html-elements)
4. [Difference between div and span](#4-difference-between-div-and-span)
5. [What is the DOCTYPE declaration?](#5-what-is-the-doctype-declaration)
6. [Block vs Inline elements](#6-block-vs-inline-elements)
7. [HTML attributes - id vs class](#7-html-attributes-id-vs-class)
8. [What are data attributes?](#8-what-are-data-attributes)
9. [HTML Forms - Basic concepts](#9-html-forms-basic-concepts)
10. [Form input types in HTML5](#10-form-input-types-in-html5)
11. [What is the alt attribute?](#11-what-is-the-alt-attribute)
12. [Meta tags and their purpose](#12-meta-tags-and-their-purpose)
13. [Difference between <script>, <script async>, and <script defer>](#13-difference-between-script-script-async-and-script-defer)
14. [What are HTML entities?](#14-what-are-html-entities)
15. [Accessibility basics - ARIA](#15-accessibility-basics-aria)

---

## 1. What is HTML and its purpose? [⭐]

### 🎯 Concept & Purpose

HTML (HyperText Markup Language) is the standard markup language for creating web pages. It provides the **structure and semantic meaning** to web content, allowing browsers to understand and render information correctly. HTML is not a programming language; it's a markup language that defines the structure of content.

**Why it exists:**

- To create structured documents with headings, paragraphs, lists, links, etc.
- To provide semantic meaning to content for browsers, search engines, and assistive technologies
- To serve as the foundation layer of web development (HTML for structure, CSS for presentation, JavaScript for behavior)

### 📝 Answer

HTML is the backbone of web pages. It uses **tags** (enclosed in angle brackets) to mark up content and tell browsers how to display it.

**Basic HTML Structure:**

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Page Title</title>
  </head>
  <body>
    <h1>Main Heading</h1>
    <p>This is a paragraph.</p>
  </body>
</html>
```

**Key Components:**

- `<!DOCTYPE html>` - Declares HTML5 document type
- `<html>` - Root element
- `<head>` - Contains metadata
- `<body>` - Contains visible content

### 💡 Key Takeaways

- HTML provides structure, not styling (that's CSS's job)
- Proper HTML structure improves SEO and accessibility
- Always use semantic HTML for better code maintainability
- HTML5 is the current standard (since 2014)

### 🔗 Resources

- [MDN: Introduction to HTML](https://developer.mozilla.org/en-US/docs/Learn/HTML/Introduction_to_HTML)
- [W3C HTML Specification](https://html.spec.whatwg.org/)
- [HTML Living Standard](https://html.spec.whatwg.org/multipage/)
- [web.dev: Learn HTML](https://web.dev/learn/html/)

### 🏢 Real-World Application

Every website you visit uses HTML as its foundation. From simple blogs to complex web applications like Gmail, Facebook, or LinkedIn - all start with HTML structure.

---

## 2. Difference between HTML and HTML5 [⭐]

### 🎯 Concept & Purpose

HTML5 is the latest evolution of HTML, introducing new semantic elements, APIs, and features that make web development more powerful and accessible. It was designed to:

- Reduce dependency on plugins (Flash, Silverlight)
- Improve semantic structure
- Add native multimedia support
- Provide better mobile support
- Introduce powerful JavaScript APIs

### 📝 Answer

| Feature               | HTML (4.01)                                                                                           | HTML5                                                                       |
| --------------------- | ----------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------- |
| **DOCTYPE**           | Complex: `<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01//EN" "http://www.w3.org/TR/html4/strict.dtd">` | Simple: `<!DOCTYPE html>`                                                   |
| **Multimedia**        | Required plugins (Flash)                                                                              | Native `<audio>`, `<video>`                                                 |
| **Graphics**          | Required plugins or images                                                                            | Native `<canvas>`, `<svg>`                                                  |
| **Semantic Elements** | Limited (`<div>`, `<span>`)                                                                           | Rich (`<header>`, `<footer>`, `<article>`, `<section>`, `<nav>`, `<aside>`) |
| **Form Controls**     | Basic inputs                                                                                          | New types: `email`, `date`, `range`, `color`, etc.                          |
| **Storage**           | Cookies only                                                                                          | localStorage, sessionStorage, IndexedDB                                     |
| **APIs**              | Limited                                                                                               | Geolocation, Web Workers, WebSockets, Drag & Drop                           |
| **Mobile Support**    | Poor                                                                                                  | Excellent with responsive features                                          |
| **Error Handling**    | Strict                                                                                                | More forgiving parsing                                                      |

**HTML5 New Semantic Elements:**

```html
<!-- HTML5 Semantic Structure -->
<header>
  <nav>
    <ul>
      <li><a href="#home">Home</a></li>
      <li><a href="#about">About</a></li>
    </ul>
  </nav>
</header>

<main>
  <article>
    <h1>Article Title</h1>
    <p>Article content...</p>
  </article>

  <aside>
    <h2>Related Links</h2>
  </aside>
</main>

<footer>
  <p>&copy; 2025 Company Name</p>
</footer>
```

**HTML5 Multimedia:**

```html
<!-- Native video support -->
<video controls width="640" height="360">
  <source src="video.mp4" type="video/mp4" />
  <source src="video.webm" type="video/webm" />
  Your browser doesn't support video.
</video>

<!-- Native audio support -->
<audio controls>
  <source src="audio.mp3" type="audio/mpeg" />
  <source src="audio.ogg" type="audio/ogg" />
  Your browser doesn't support audio.
</audio>
```

### 💡 Key Takeaways

- HTML5 is backward compatible with older HTML versions
- No need for plugins for common multimedia tasks
- Better semantic structure improves SEO and accessibility
- Mobile-first approach is built into HTML5
- All modern browsers support HTML5

### 🔗 Resources

- [MDN: HTML5](https://developer.mozilla.org/en-US/docs/Glossary/HTML5)
- [Can I Use: HTML5 Features](https://caniuse.com/?search=html5)
- [HTML5 Doctor](http://html5doctor.com/)
- [Dive Into HTML5](https://diveintohtml5.info/)

### 🏢 Real-World Application

Netflix, YouTube, and Spotify use HTML5 for video and audio playback. E-commerce sites use HTML5 form validation. News websites use semantic HTML5 for better SEO and content structure.

---

## 3. What are semantic HTML elements? [⭐]

### 🎯 Concept & Purpose

Semantic HTML uses elements that clearly describe their meaning to both the browser and the developer. Instead of using generic `<div>` tags everywhere, semantic elements like `<article>`, `<nav>`, and `<footer>` convey the **purpose** of the content.

**Why semantics matter:**

- **Accessibility**: Screen readers can navigate content better
- **SEO**: Search engines understand content structure
- **Maintainability**: Developers can understand code faster
- **Future-proof**: Browsers can provide better default styling and behavior

### 📝 Answer

**Semantic vs Non-Semantic:**

```html
<!-- ❌ Non-Semantic (Poor Practice) -->
<div id="header">
  <div id="nav">
    <div class="menu-item">Home</div>
    <div class="menu-item">About</div>
  </div>
</div>
<div id="main">
  <div class="post">
    <div class="title">Blog Post</div>
    <div class="content">Content here...</div>
  </div>
</div>
<div id="footer">
  <div>Copyright 2025</div>
</div>

<!-- ✅ Semantic (Best Practice) -->
<header>
  <nav>
    <ul>
      <li><a href="/">Home</a></li>
      <li><a href="/about">About</a></li>
    </ul>
  </nav>
</header>
<main>
  <article>
    <h1>Blog Post</h1>
    <p>Content here...</p>
  </article>
</main>
<footer>
  <p>&copy; 2025</p>
</footer>
```

**Common Semantic Elements:**

| Element                     | Purpose                            | When to Use                           |
| --------------------------- | ---------------------------------- | ------------------------------------- |
| `<header>`                  | Introductory content or navigation | Top of page or section                |
| `<nav>`                     | Navigation links                   | Main menu, breadcrumbs, pagination    |
| `<main>`                    | Main content of document           | Primary content (only one per page)   |
| `<article>`                 | Self-contained content             | Blog posts, news articles, comments   |
| `<section>`                 | Thematic grouping                  | Chapters, tabs, themed content blocks |
| `<aside>`                   | Tangentially related content       | Sidebars, pull quotes, ads            |
| `<footer>`                  | Footer information                 | Bottom of page or section             |
| `<figure>` & `<figcaption>` | Images with captions               | Diagrams, photos, code listings       |
| `<time>`                    | Date/time                          | Publish dates, event times            |
| `<mark>`                    | Highlighted text                   | Search results, important text        |

**Practical Example:**

```html
<article>
    <header>
        <h1>Understanding Semantic HTML</h1>
        <p>Published on <time datetime="2025-12-03">December 3, 2025</time></p>
        <p>By <address>John Doe</address></p>
    </header>

    <section>
        <h2>Introduction</h2>
        <p>Semantic HTML is crucial for modern web development...</p>
    </section>

    <section>
        <h2>Benefits</h2>
        <ul>
            <li>Better accessibility</li>
            <li>Improved SEO</li>
        </ul>
    </section>

    <figure>
        <img src="semantic-example.png" alt="Semantic HTML structure diagram">
        <figcaption>Figure 1: Semantic HTML structure</figcaption>
    </figure>

    <footer>
        <p>Tags: <mark>HTML</mark>, <mark>Web Development</mark></p>
    </footer>
</article>
```

### 💡 Key Takeaways

- Use semantic elements instead of `<div>` when meaning is clear
- Semantic HTML improves accessibility for screen reader users
- Search engines rank semantic content better
- Code becomes self-documenting and easier to maintain
- **Interview Tip**: Be able to explain when to use `<section>` vs `<article>` vs `<div>`

### 🔗 Resources

- [MDN: HTML Elements Reference](https://developer.mozilla.org/en-US/docs/Web/HTML/Element)
- [HTML5 Semantic Elements](https://www.w3schools.com/html/html5_semantic_elements.asp)
- [A Look Into Proper HTML5 Semantics](https://www.smashingmagazine.com/2020/01/html5-article-section/)
- [HTML5 Doctor: Element Index](http://html5doctor.com/element-index/)

### 🏢 Real-World Application

Major news sites (BBC, The Guardian) use semantic HTML for articles. E-commerce sites use `<article>` for product listings. Corporate websites use semantic structure for better SEO rankings.

---

## 4. Difference between div and span [⭐]

### 🎯 Concept & Purpose

`<div>` and `<span>` are generic containers used when no semantic element is appropriate. The key difference is their **display behavior**:

- `<div>` is a **block-level** container
- `<span>` is an **inline** container

Understanding this distinction is fundamental to HTML layout and styling.

### 📝 Answer

**Visual Difference:**

```html
<!-- Block-level: <div> -->
<div style="background: lightblue;">First div</div>
<div style="background: lightgreen;">Second div</div>
<!-- Each div takes full width and starts on a new line -->

<!-- Inline: <span> -->
<span style="background: lightblue;">First span</span>
<span style="background: lightgreen;">Second span</span>
<!-- Spans sit side-by-side on the same line -->
```

**Comparison:**

| Feature            | `<div>`                     | `<span>`                           |
| ------------------ | --------------------------- | ---------------------------------- |
| **Display**        | Block                       | Inline                             |
| **Width**          | Takes full width available  | Only as wide as content            |
| **Line Break**     | Starts on new line          | Stays on same line                 |
| **Height/Width**   | Can set dimensions          | Cannot set dimensions (by default) |
| **Use Case**       | Layout containers, sections | Styling parts of text              |
| **Padding/Margin** | All directions work         | Vertical may not work as expected  |

**Practical Examples:**

```html
<!-- Using <div> for layout -->
<div class="container">
  <div class="header">Header Content</div>
  <div class="content">
    <div class="sidebar">Sidebar</div>
    <div class="main">Main Content</div>
  </div>
  <div class="footer">Footer</div>
</div>

<!-- Using <span> for inline styling -->
<p>
  This is a paragraph with
  <span style="color: red; font-weight: bold;">highlighted text</span>
  in the middle.
</p>

<p>Price: <span class="currency">$</span><span class="amount">99.99</span></p>

<!-- Common pattern: Icon with text -->
<button>
  <span class="icon">🔍</span>
  <span class="label">Search</span>
</button>
```

**When to Use:**

```html
<!-- ✅ Good: Use <div> for layout blocks -->
<div class="card">
    <div class="card-header">Title</div>
    <div class="card-body">Content</div>
</div>

<!-- ✅ Good: Use <span> for inline styling -->
<p>Contact us at <span class="phone">555-1234</span></p>

<!-- ❌ Bad: Using <span> for layout -->
<span style="display: block; width: 100%;">This defeats the purpose</span>

<!-- ❌ Bad: Using <div> inside inline content -->
<p>This is <div>wrong</div> markup</p>
```

### 💡 Key Takeaways

- `<div>` = block-level container (layout)
- `<span>` = inline container (text styling)
- Both are non-semantic; prefer semantic elements when possible
- Don't use `<div>` when a semantic element exists
- **Interview Gotcha**: `<span>` can't contain block-level elements

### 🔗 Resources

- [MDN: div element](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/div)
- [MDN: span element](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/span)
- [Block vs Inline Elements](https://www.w3schools.com/html/html_blocks.asp)

### 🏢 Real-World Application

CSS frameworks (Bootstrap, Tailwind) use `<div>` extensively for grid systems. Text editors use `<span>` for syntax highlighting. E-commerce sites use `<span>` for price formatting and `<div>` for product cards.

---

## 5. What is the DOCTYPE declaration? [⭐]

### 🎯 Concept & Purpose

The DOCTYPE (Document Type Declaration) tells the browser which version of HTML the page is written in and how to render it. It's the **first line** of every HTML document and is crucial for:

- **Standards mode**: Ensures consistent rendering across browsers
- **Avoiding quirks mode**: Prevents legacy rendering bugs
- **Validation**: Allows validators to check markup correctness

### 📝 Answer

**HTML5 DOCTYPE (Current Standard):**

```html
<!DOCTYPE html>
```

**Why it's so simple:**

- HTML5 simplified the declaration
- No need to reference DTD (Document Type Definition)
- Triggers standards mode in all modern browsers

**Older DOCTYPE Examples (for reference):**

```html
<!-- HTML 4.01 Strict -->
<!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01//EN" "http://www.w3.org/TR/html4/strict.dtd">

<!-- XHTML 1.0 Strict -->
<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Strict//EN" "http://www.w3.org/TR/xhtml1/DTD/xhtml1-strict.dtd">

<!-- HTML5 (much simpler!) -->
<!DOCTYPE html>
```

**What happens without DOCTYPE:**

```html
<!-- ❌ Missing DOCTYPE -->
<html>
  <head>
    <title>Page</title>
  </head>
  <body>
    <p>Content</p>
  </body>
</html>
```

**Result**: Browser enters **quirks mode**, causing:

- Inconsistent box model behavior
- Different CSS rendering
- Unpredictable JavaScript behavior
- Layout issues across browsers

**Complete HTML5 Document:**

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Document</title>
  </head>
  <body>
    <h1>Hello World</h1>
  </body>
</html>
```

### 💡 Key Takeaways

- Always include `<!DOCTYPE html>` as the first line
- Case-insensitive, but lowercase is convention
- Not an HTML tag; it's an instruction to the browser
- Missing DOCTYPE triggers quirks mode (bad!)
- HTML5 DOCTYPE works for all modern HTML
- **Interview Tip**: Know the difference between standards mode and quirks mode

### 🔗 Resources

- [MDN: DOCTYPE](https://developer.mozilla.org/en-US/docs/Glossary/Doctype)
- [Quirks Mode and Standards Mode](https://developer.mozilla.org/en-US/docs/Web/HTML/Quirks_Mode_and_Standards_Mode)
- [W3C: Recommended Doctypes](https://www.w3.org/QA/2002/04/valid-dtd-list.html)

### 🏢 Real-World Application

Every professional website includes DOCTYPE. Missing it can cause subtle CSS bugs that are hard to debug. Build tools and linters check for DOCTYPE presence.

---

## 6. Block vs Inline elements [⭐]

### 🎯 Concept & Purpose

Understanding block and inline elements is fundamental to HTML layout. This concept determines:

- How elements flow on the page
- What elements can be nested inside others
- How CSS properties apply
- Layout behavior before CSS modifications

### 📝 Answer

**Block-Level Elements:**

- Start on a new line
- Take up full width available
- Can contain other block and inline elements
- Respect width, height, margin, padding (all directions)

**Common Block Elements:**

```html
<div>, <p>, <h1>-<h6>, <ul>, <ol>, <li>,
<section>, <article>, <header>, <footer>,
<nav>, <main>, <aside>, <form>, <table>,
<blockquote>, <pre>, <canvas>
```

**Inline Elements:**

- Stay on the same line
- Only take up as much width as needed
- Cannot contain block-level elements
- Width/height don't apply (by default)
- Vertical margin/padding may not work as expected

**Common Inline Elements:**

```html
<span>, <a>, <strong>, <em>, <img>, <br>,
<code>, <small>, <abbr>, <cite>, <label>,
<input>, <button>, <textarea>, <select>
```

**Visual Demonstration:**

```html
<!DOCTYPE html>
<html>
  <head>
    <style>
      /* Visual styling for demonstration */
      .block {
        background: lightblue;
        margin: 5px 0;
      }
      .inline {
        background: lightgreen;
        margin: 5px;
      }
    </style>
  </head>
  <body>
    <!-- Block elements -->
    <div class="block">Block Element 1</div>
    <div class="block">Block Element 2</div>
    <p class="block">Block paragraph</p>

    <!-- Inline elements -->
    <span class="inline">Inline 1</span>
    <span class="inline">Inline 2</span>
    <span class="inline">Inline 3</span>

    <!-- Mixed -->
    <p>
      This is a paragraph (block) with
      <strong>bold text (inline)</strong> and
      <a href="#">a link (inline)</a> inside it.
    </p>
  </body>
</html>
```

**Inline-Block (Hybrid):**

```html
<style>
  .inline-block {
    display: inline-block;
    width: 100px;
    height: 100px;
    background: coral;
    margin: 10px;
  }
</style>

<!-- Acts inline but accepts width/height -->
<div class="inline-block">Box 1</div>
<div class="inline-block">Box 2</div>
<div class="inline-block">Box 3</div>
```

**Nesting Rules:**

```html
<!-- ✅ Correct: Block can contain inline -->
<div>
  <span>Inline inside block</span>
</div>

<!-- ✅ Correct: Block can contain block -->
<div>
  <p>Paragraph inside div</p>
</div>

<!-- ❌ Wrong: Inline should not contain block -->
<span>
  <div>This is invalid HTML!</div>
</span>

<!-- ✅ Exception: <a> can contain block in HTML5 -->
<a href="/page">
  <div>Clickable card</div>
</a>
```

**Comparison Table:**

| Property         | Block | Inline  | Inline-Block |
| ---------------- | ----- | ------- | ------------ |
| New line         | Yes   | No      | No           |
| Full width       | Yes   | No      | No           |
| Width/Height     | Yes   | No      | Yes          |
| Vertical margin  | Yes   | No      | Yes          |
| Vertical padding | Yes   | Partial | Yes          |
| Contains blocks  | Yes   | No\*    | Yes          |

\*Exception: `<a>` in HTML5

### 💡 Key Takeaways

- Block elements stack vertically, inline elements flow horizontally
- You can change display behavior with CSS (`display: block/inline/inline-block`)
- Most layout issues stem from misunderstanding block vs inline
- `<img>` is inline but accepts width/height (inline-replaced element)
- **Interview Gotcha**: `<a>` can wrap block elements in HTML5 (for card patterns)

### 🔗 Resources

- [MDN: Block-level elements](https://developer.mozilla.org/en-US/docs/Web/HTML/Block-level_elements)
- [MDN: Inline elements](https://developer.mozilla.org/en-US/docs/Web/HTML/Inline_elements)
- [CSS Display Property](https://developer.mozilla.org/en-US/docs/Web/CSS/display)

### 🏢 Real-World Application

Navigation menus use inline/inline-block for horizontal layout. Card layouts use block elements. Text formatting uses inline elements. Understanding this is crucial for debugging layout issues.

---

## 7. HTML attributes - id vs class [⭐]

### 🎯 Concept & Purpose

`id` and `class` are the two most common attributes for identifying and styling HTML elements. Understanding when to use each is crucial for:

- **CSS specificity**: How styles are applied
- **JavaScript selection**: Efficient DOM manipulation
- **Accessibility**: ARIA labels and form associations
- **Code maintainability**: Reusable vs unique identifiers

### 📝 Answer

**Key Differences:**

| Feature              | `id`                                  | `class`                      |
| -------------------- | ------------------------------------- | ---------------------------- |
| **Uniqueness**       | Must be unique on page                | Can be reused multiple times |
| **Multiple values**  | One per element                       | Multiple classes per element |
| **CSS Specificity**  | Higher (0,1,0,0)                      | Lower (0,0,1,0)              |
| **JavaScript**       | `getElementById()`                    | `getElementsByClassName()`   |
| **URL Fragment**     | Can be used as anchor                 | Cannot                       |
| **Form association** | `<label for="id">`                    | Not used                     |
| **ARIA**             | `aria-labelledby`, `aria-describedby` | Not typically used           |

**Syntax Examples:**

```html
<!-- id: Single, unique identifier -->
<div id="header">Header</div>
<div id="main-content">Content</div>
<button id="submit-btn">Submit</button>

<!-- class: Reusable, multiple allowed -->
<div class="card">Card 1</div>
<div class="card featured">Card 2</div>
<div class="card featured premium">Card 3</div>

<!-- Combining both -->
<article id="post-123" class="blog-post featured">
  <h2 class="post-title">Article Title</h2>
  <p class="post-excerpt">Excerpt text...</p>
</article>
```

**CSS Targeting:**

```css
/* ID selector - # prefix */
#header {
  background: blue;
  height: 60px;
}

#submit-btn {
  background: green;
  color: white;
}

/* Class selector - . prefix */
.card {
  border: 1px solid #ccc;
  padding: 20px;
  margin: 10px;
}

.featured {
  border-color: gold;
  border-width: 3px;
}

.premium {
  background: linear-gradient(to right, #667eea, #764ba2);
}

/* Multiple classes work together */
.card.featured.premium {
  box-shadow: 0 10px 30px rgba(0, 0, 0, 0.3);
}
```

**JavaScript Selection:**

```javascript
// ID - Returns single element or null
const header = document.getElementById("header");
const btn = document.getElementById("submit-btn");

// Class - Returns HTMLCollection (array-like)
const cards = document.getElementsByClassName("card");
const featured = document.getElementsByClassName("featured");

// Modern: querySelector (works with both)
const headerModern = document.querySelector("#header");
const firstCard = document.querySelector(".card");
const allCards = document.querySelectorAll(".card");
```

**Form Association:**

```html
<!-- id connects label to input -->
<label for="email">Email Address:</label>
<input type="email" id="email" name="email" class="form-input" />

<label for="password">Password:</label>
<input type="password" id="password" name="password" class="form-input" />

<!-- class for styling -->
<style>
  .form-input {
    padding: 8px;
    border: 1px solid #ddd;
    border-radius: 4px;
  }
</style>
```

**URL Fragment (Anchor Links):**

```html
<!-- Navigation -->
<nav>
  <a href="#about">About</a>
  <a href="#services">Services</a>
  <a href="#contact">Contact</a>
</nav>

<!-- Sections with id -->
<section id="about">
  <h2>About Us</h2>
</section>

<section id="services">
  <h2>Our Services</h2>
</section>

<section id="contact">
  <h2>Contact</h2>
</section>
```

**Best Practices:**

```html
<!-- ✅ Good: Unique ids, reusable classes -->
<div id="user-profile" class="container">
  <div class="profile-header">
    <img
      id="profile-avatar"
      class="avatar avatar-large"
      src="user.jpg"
      alt="User"
    />
    <h1 id="profile-name" class="heading heading-primary">John Doe</h1>
  </div>
</div>

<!-- ❌ Bad: Duplicate ids -->
<div id="card">Card 1</div>
<div id="card">Card 2</div>
<!-- Invalid! -->

<!-- ❌ Bad: Using id for styling repeated elements -->
<div id="product-1" style="border: 1px solid #ccc;">Product 1</div>
<div id="product-2" style="border: 1px solid #ccc;">Product 2</div>
<!-- Should use class instead -->

<!-- ✅ Better: -->
<div id="product-1" class="product-card">Product 1</div>
<div id="product-2" class="product-card">Product 2</div>
```

**Naming Conventions:**

```html
<!-- Use kebab-case for both -->
<div id="main-navigation" class="nav-container">
  <ul class="nav-list">
    <li class="nav-item active">
      <a href="#" class="nav-link">Home</a>
    </li>
  </ul>
</div>

<!-- Avoid: -->
<!-- camelCase: mainNavigation -->
<!-- snake_case: main_navigation -->
<!-- PascalCase: MainNavigation -->
```

### 💡 Key Takeaways

- Use `id` for unique elements (one per page)
- Use `class` for styling groups of elements
- `id` has higher CSS specificity (can make overriding harder)
- Multiple classes can be applied to one element
- `id` is required for form labels and ARIA associations
- **Interview Tip**: Explain specificity - inline styles > id > class > element
- **Common Mistake**: Using same `id` multiple times (invalid HTML)

### 🔗 Resources

- [MDN: id attribute](https://developer.mozilla.org/en-US/docs/Web/HTML/Global_attributes/id)
- [MDN: class attribute](https://developer.mozilla.org/en-US/docs/Web/HTML/Global_attributes/class)
- [CSS Specificity Calculator](https://specificity.keegan.st/)
- [W3C: Classes vs IDs](https://www.w3.org/wiki/CSS/Training/Selectors#Class_and_ID_selectors)

### 🏢 Real-World Application

Single-page applications use `id` for unique page sections. Component libraries use `class` for reusable styling. Forms use `id` for accessibility. CSS frameworks (Bootstrap, Tailwind) rely heavily on classes.

---

## 8. What are data attributes? [⭐]

### 🎯 Concept & Purpose

Data attributes (`data-*`) allow you to store custom data directly on HTML elements without using non-standard attributes or extra DOM properties. They provide:

- **Clean separation**: Keep data in HTML, logic in JavaScript
- **Valid HTML**: Custom attributes without breaking validation
- **Accessibility**: Data that doesn't interfere with assistive technologies
- **Flexibility**: Store any custom information needed

**Why they exist:**
Before data attributes, developers used invalid attributes or classes to store data, breaking HTML validation and causing maintenance issues.

### 📝 Answer

**Syntax:**

```html
<!-- Format: data-{name}="{value}" -->
<div data-user-id="12345">User Profile</div>
<button data-action="delete" data-confirm="true">Delete</button>
<article data-category="technology" data-published="2025-12-03">
  Article
</article>
```

**Naming Rules:**

- Must start with `data-`
- Can contain letters, numbers, hyphens, dots, colons, underscores
- Case-insensitive in HTML, but converted to camelCase in JavaScript
- No uppercase letters in HTML (use hyphens instead)

**Accessing with JavaScript:**

```html
<div
  id="product"
  data-product-id="12345"
  data-product-name="Laptop"
  data-price="999.99"
  data-in-stock="true"
>
  Product Details
</div>

<script>
  // Method 1: dataset API (recommended)
  const product = document.getElementById("product");

  console.log(product.dataset.productId); // "12345" (camelCase!)
  console.log(product.dataset.productName); // "Laptop"
  console.log(product.dataset.price); // "999.99"
  console.log(product.dataset.inStock); // "true" (string!)

  // Setting values
  product.dataset.discount = "10";
  product.dataset.featured = "true";

  // Method 2: getAttribute (works too)
  console.log(product.getAttribute("data-product-id")); // "12345"
  product.setAttribute("data-discount", "10");

  // Method 3: Direct attribute access
  console.log(product.attributes["data-product-id"].value);
</script>
```

**Practical Examples:**

**1. Toggle Functionality:**

```html
<button data-toggle="modal" data-target="#myModal">Open Modal</button>

<div id="myModal" data-visible="false">
  <p>Modal Content</p>
  <button data-action="close">Close</button>
</div>

<script>
  document.querySelectorAll('[data-toggle="modal"]').forEach((btn) => {
    btn.addEventListener("click", function () {
      const targetId = this.dataset.target;
      const modal = document.querySelector(targetId);
      modal.dataset.visible = "true";
      modal.style.display = "block";
    });
  });

  document.querySelectorAll('[data-action="close"]').forEach((btn) => {
    btn.addEventListener("click", function () {
      const modal = this.closest("[data-visible]");
      modal.dataset.visible = "false";
      modal.style.display = "none";
    });
  });
</script>
```

**2. Sorting and Filtering:**

```html
<div class="products">
  <div
    class="product"
    data-category="electronics"
    data-price="999"
    data-rating="4.5"
  >
    <h3>Laptop</h3>
  </div>
  <div class="product" data-category="books" data-price="29" data-rating="4.8">
    <h3>JavaScript Guide</h3>
  </div>
  <div
    class="product"
    data-category="electronics"
    data-price="599"
    data-rating="4.2"
  >
    <h3>Tablet</h3>
  </div>
</div>

<script>
  // Filter by category
  function filterByCategory(category) {
    const products = document.querySelectorAll(".product");
    products.forEach((product) => {
      if (product.dataset.category === category) {
        product.style.display = "block";
      } else {
        product.style.display = "none";
      }
    });
  }

  // Sort by price
  function sortByPrice() {
    const container = document.querySelector(".products");
    const products = Array.from(document.querySelectorAll(".product"));

    products.sort((a, b) => {
      return parseFloat(a.dataset.price) - parseFloat(b.dataset.price);
    });

    products.forEach((product) => container.appendChild(product));
  }

  filterByCategory("electronics");
  sortByPrice();
</script>
```

**3. Form Validation:**

```html
<form>
  <input
    type="text"
    id="username"
    data-min-length="3"
    data-max-length="20"
    data-required="true"
    data-pattern="^[a-zA-Z0-9]+$"
  />

  <input type="email" id="email" data-required="true" data-validation="email" />

  <button type="submit">Submit</button>
</form>

<script>
  function validateInput(input) {
    const value = input.value;
    const { required, minLength, maxLength, pattern } = input.dataset;

    if (required === "true" && !value) {
      return "This field is required";
    }

    if (minLength && value.length < parseInt(minLength)) {
      return `Minimum length is ${minLength}`;
    }

    if (maxLength && value.length > parseInt(maxLength)) {
      return `Maximum length is ${maxLength}`;
    }

    if (pattern && !new RegExp(pattern).test(value)) {
      return "Invalid format";
    }

    return null; // Valid
  }
</script>
```

**4. CSS Styling with Data Attributes:**

```html
<style>
  /* Style based on data attributes */
  [data-status="active"] {
    color: green;
    font-weight: bold;
  }

  [data-status="inactive"] {
    color: gray;
    opacity: 0.6;
  }

  [data-priority="high"] {
    border-left: 4px solid red;
  }

  [data-priority="medium"] {
    border-left: 4px solid orange;
  }

  [data-priority="low"] {
    border-left: 4px solid blue;
  }

  /* Attribute value selectors */
  [data-size="small"] {
    font-size: 12px;
  }
  [data-size="medium"] {
    font-size: 16px;
  }
  [data-size="large"] {
    font-size: 20px;
  }
</style>

<div data-status="active" data-priority="high">High Priority Active Task</div>
<div data-status="inactive" data-priority="low">Low Priority Inactive Task</div>
```

**5. Analytics Tracking:**

```html
<button
  data-track="click"
  data-event="purchase"
  data-category="product"
  data-label="laptop-pro-15"
  data-value="999"
>
  Buy Now
</button>

<script>
  document.querySelectorAll('[data-track="click"]').forEach((element) => {
    element.addEventListener("click", function () {
      const { event, category, label, value } = this.dataset;

      // Send to analytics
      analytics.track(event, {
        category: category,
        label: label,
        value: parseFloat(value),
      });
    });
  });
</script>
```

**Type Considerations:**

```javascript
// ⚠️ Data attributes are always strings!
const element = document.querySelector('[data-count="5"]');

console.log(element.dataset.count); // "5" (string)
console.log(typeof element.dataset.count); // "string"

// Convert to numbers
const count = parseInt(element.dataset.count);
const price = parseFloat(element.dataset.price);

// Convert to boolean
const isActive = element.dataset.active === "true";

// Parse JSON
const config = JSON.parse(element.dataset.config);
```

### 💡 Key Takeaways

- Use `data-*` for custom data storage on elements
- Always strings in HTML, need type conversion in JavaScript
- Accessed via `dataset` API (camelCase) or `getAttribute` (kebab-case)
- Valid HTML5 and doesn't affect accessibility
- Can be styled with CSS attribute selectors
- Perfect for configuration, state management, and tracking
- **Interview Tip**: Explain the camelCase conversion (data-user-id → dataset.userId)
- **Performance**: Don't store large amounts of data; use for small metadata only

### 🔗 Resources

- [MDN: Using data attributes](https://developer.mozilla.org/en-US/docs/Learn/HTML/Howto/Use_data_attributes)
- [MDN: HTMLElement.dataset](https://developer.mozilla.org/en-US/docs/Web/API/HTMLElement/dataset)
- [HTML5 Doctor: Data Attributes](http://html5doctor.com/html5-custom-data-attributes/)
- [Can I Use: dataset](https://caniuse.com/dataset)

### 🏢 Real-World Application

E-commerce sites use data attributes for product IDs and prices. Analytics platforms (Google Analytics, Mixpanel) use them for tracking. Component libraries (Bootstrap, Material-UI) use them for configuration. SPAs use them for state management.

---

_[Continue to next questions...]_

## 9. HTML Forms - Basic concepts [⭐]

### 🎯 Concept & Purpose

HTML forms are the primary way users interact with web applications by submitting data. Understanding forms is crucial for:

- **User input**: Collecting information from users
- **Data submission**: Sending data to servers
- **Validation**: Ensuring data quality before submission
- **Accessibility**: Making forms usable for everyone

### 📝 Answer

**Basic Form Structure:**

```html
<form action="/submit" method="POST">
  <!-- Form controls go here -->
  <button type="submit">Submit</button>
</form>
```

**Key Form Attributes:**

| Attribute      | Purpose                     | Values                                                                   |
| -------------- | --------------------------- | ------------------------------------------------------------------------ |
| `action`       | URL where form data is sent | URL or empty (same page)                                                 |
| `method`       | HTTP method for submission  | `GET`, `POST`                                                            |
| `enctype`      | How form data is encoded    | `application/x-www-form-urlencoded`, `multipart/form-data`, `text/plain` |
| `name`         | Form identifier             | String                                                                   |
| `autocomplete` | Browser autofill behavior   | `on`, `off`                                                              |
| `novalidate`   | Disable HTML5 validation    | Boolean                                                                  |
| `target`       | Where to display response   | `_self`, `_blank`, `_parent`, `_top`                                     |

**Complete Form Example:**

```html
<form action="/register" method="POST" autocomplete="on">
  <!-- Text Input -->
  <label for="username">Username:</label>
  <input
    type="text"
    id="username"
    name="username"
    required
    minlength="3"
    maxlength="20"
    placeholder="Enter username"
  />

  <!-- Email Input -->
  <label for="email">Email:</label>
  <input
    type="email"
    id="email"
    name="email"
    required
    placeholder="user@example.com"
  />

  <!-- Password Input -->
  <label for="password">Password:</label>
  <input type="password" id="password" name="password" required minlength="8" />

  <!-- Radio Buttons -->
  <fieldset>
    <legend>Gender:</legend>
    <label>
      <input type="radio" name="gender" value="male" checked />
      Male
    </label>
    <label>
      <input type="radio" name="gender" value="female" />
      Female
    </label>
    <label>
      <input type="radio" name="gender" value="other" />
      Other
    </label>
  </fieldset>

  <!-- Checkboxes -->
  <label>
    <input type="checkbox" name="terms" required />
    I agree to terms and conditions
  </label>

  <label>
    <input type="checkbox" name="newsletter" value="yes" />
    Subscribe to newsletter
  </label>

  <!-- Select Dropdown -->
  <label for="country">Country:</label>
  <select id="country" name="country" required>
    <option value="">Select a country</option>
    <option value="us">United States</option>
    <option value="uk">United Kingdom</option>
    <option value="ca">Canada</option>
  </select>

  <!-- Textarea -->
  <label for="bio">Bio:</label>
  <textarea
    id="bio"
    name="bio"
    rows="4"
    cols="50"
    maxlength="500"
    placeholder="Tell us about yourself"
  ></textarea>

  <!-- Buttons -->
  <button type="submit">Submit</button>
  <button type="reset">Reset</button>
  <button type="button">Cancel</button>
</form>
```

**Form Methods - GET vs POST:**

```html
<!-- GET: Data in URL, visible, cacheable -->
<form action="/search" method="GET">
  <input type="text" name="q" placeholder="Search..." />
  <button type="submit">Search</button>
</form>
<!-- Submits to: /search?q=keyword -->

<!-- POST: Data in request body, not visible, not cacheable -->
<form action="/login" method="POST">
  <input type="email" name="email" />
  <input type="password" name="password" />
  <button type="submit">Login</button>
</form>
```

| Feature          | GET                   | POST                             |
| ---------------- | --------------------- | -------------------------------- |
| **Visibility**   | Data in URL           | Data in body                     |
| **Security**     | Less secure (visible) | More secure                      |
| **Caching**      | Can be cached         | Not cached                       |
| **Bookmarkable** | Yes                   | No                               |
| **Data length**  | Limited (~2048 chars) | Unlimited                        |
| **Use case**     | Search, filters       | Login, registration, file upload |

**File Upload:**

```html
<form action="/upload" method="POST" enctype="multipart/form-data">
  <label for="avatar">Profile Picture:</label>
  <input type="file" id="avatar" name="avatar" accept="image/*" required />

  <label for="documents">Documents:</label>
  <input
    type="file"
    id="documents"
    name="documents"
    multiple
    accept=".pdf,.doc,.docx"
  />

  <button type="submit">Upload</button>
</form>
```

**Form Validation:**

```html
<form>
  <!-- Required field -->
  <input type="text" name="name" required />

  <!-- Min/Max length -->
  <input type="text" name="username" minlength="3" maxlength="20" />

  <!-- Pattern (regex) -->
  <input
    type="text"
    name="phone"
    pattern="[0-9]{3}-[0-9]{3}-[0-9]{4}"
    placeholder="123-456-7890"
  />

  <!-- Min/Max value (numbers) -->
  <input type="number" name="age" min="18" max="100" />

  <!-- Email validation -->
  <input type="email" name="email" />

  <!-- URL validation -->
  <input type="url" name="website" />

  <button type="submit">Submit</button>
</form>
```

**JavaScript Form Handling:**

```html
<form id="myForm">
  <input type="text" name="username" required />
  <input type="email" name="email" required />
  <button type="submit">Submit</button>
</form>

<script>
  const form = document.getElementById("myForm");

  form.addEventListener("submit", function (event) {
    event.preventDefault(); // Prevent default submission

    // Get form data
    const formData = new FormData(form);

    // Method 1: Access individual fields
    const username = formData.get("username");
    const email = formData.get("email");

    // Method 2: Convert to object
    const data = Object.fromEntries(formData);
    console.log(data); // { username: "...", email: "..." }

    // Method 3: Iterate over entries
    for (let [key, value] of formData.entries()) {
      console.log(key, value);
    }

    // Send via fetch
    fetch("/api/submit", {
      method: "POST",
      body: formData, // or JSON.stringify(data)
    })
      .then((response) => response.json())
      .then((data) => console.log("Success:", data))
      .catch((error) => console.error("Error:", error));
  });
</script>
```

### 💡 Key Takeaways

- Always use `<label>` for accessibility (connects to input via `for` attribute)
- Use `name` attribute for server-side data (not `id`)
- GET for searches/filters, POST for sensitive data
- Use `required` for client-side validation
- `enctype="multipart/form-data"` required for file uploads
- Prevent default form submission when using JavaScript
- **Interview Tip**: Explain difference between `name` and `id` attributes
- **Security**: Never trust client-side validation alone; always validate server-side

### 🔗 Resources

- [MDN: HTML Forms](https://developer.mozilla.org/en-US/docs/Learn/Forms)
- [MDN: Form element](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/form)
- [MDN: FormData API](https://developer.mozilla.org/en-US/docs/Web/API/FormData)
- [Web.dev: Learn Forms](https://web.dev/learn/forms/)

### 🏢 Real-World Application

Every login, registration, checkout, and search feature uses forms. E-commerce sites use complex multi-step forms. SaaS applications rely heavily on form validation and submission.

---

## 10. Form input types in HTML5 [⭐]

### 🎯 Concept & Purpose

HTML5 introduced new input types that provide:

- **Built-in validation**: Browser validates data format
- **Better UX**: Mobile keyboards adapt to input type
- **Semantic meaning**: Clear purpose of each field
- **Accessibility**: Screen readers announce input purpose

### 📝 Answer

**HTML5 Input Types:**

```html
<form>
  <!-- Text (default) -->
  <input type="text" name="name" placeholder="Full Name" />

  <!-- Email - validates email format -->
  <input type="email" name="email" placeholder="user@example.com" />

  <!-- Password - hides characters -->
  <input type="password" name="password" />

  <!-- Number - numeric input with spinners -->
  <input type="number" name="age" min="0" max="120" step="1" />

  <!-- Tel - telephone number -->
  <input type="tel" name="phone" pattern="[0-9]{3}-[0-9]{3}-[0-9]{4}" />

  <!-- URL - validates URL format -->
  <input type="url" name="website" placeholder="https://example.com" />

  <!-- Search - search box with clear button -->
  <input type="search" name="q" placeholder="Search..." />

  <!-- Date - date picker -->
  <input type="date" name="birthday" min="1900-01-01" max="2025-12-31" />

  <!-- Time - time picker -->
  <input type="time" name="appointment" />

  <!-- Datetime-local - date and time -->
  <input type="datetime-local" name="meeting" />

  <!-- Month - month picker -->
  <input type="month" name="start-month" />

  <!-- Week - week picker -->
  <input type="week" name="week" />

  <!-- Color - color picker -->
  <input type="color" name="theme-color" value="#ff0000" />

  <!-- Range - slider -->
  <input type="range" name="volume" min="0" max="100" value="50" />

  <!-- File - file upload -->
  <input type="file" name="avatar" accept="image/*" />

  <!-- Checkbox - boolean option -->
  <input type="checkbox" name="terms" value="agreed" />

  <!-- Radio - single choice from group -->
  <input type="radio" name="gender" value="male" />

  <!-- Hidden - invisible field -->
  <input type="hidden" name="user-id" value="12345" />

  <!-- Submit - form submission button -->
  <input type="submit" value="Submit Form" />

  <!-- Reset - reset form button -->
  <input type="reset" value="Clear Form" />

  <!-- Button - generic button -->
  <input type="button" value="Click Me" />
</form>
```

**Detailed Examples with Validation:**

**1. Email Input:**

```html
<label for="email">Email:</label>
<input
  type="email"
  id="email"
  name="email"
  required
  placeholder="user@example.com"
  autocomplete="email"
/>
<!-- Browser validates: must contain @ and domain -->
```

**2. Number Input:**

```html
<label for="quantity">Quantity (1-10):</label>
<input
  type="number"
  id="quantity"
  name="quantity"
  min="1"
  max="10"
  step="1"
  value="1"
/>
<!-- Spinners appear, validates range -->

<label for="price">Price:</label>
<input
  type="number"
  id="price"
  name="price"
  min="0"
  step="0.01"
  placeholder="0.00"
/>
<!-- Allows decimals with step="0.01" -->
```

**3. Date/Time Inputs:**

```html
<!-- Date -->
<label for="birthday">Birthday:</label>
<input
  type="date"
  id="birthday"
  name="birthday"
  min="1900-01-01"
  max="2025-12-31"
/>

<!-- Time -->
<label for="appointment">Appointment Time:</label>
<input
  type="time"
  id="appointment"
  name="appointment"
  min="09:00"
  max="17:00"
/>

<!-- Datetime-local -->
<label for="event">Event Date & Time:</label>
<input type="datetime-local" id="event" name="event" />

<!-- Month -->
<label for="credit-expiry">Card Expiry:</label>
<input type="month" id="credit-expiry" name="expiry" min="2025-01" />
```

**4. Range Slider:**

```html
<label for="volume">Volume: <span id="volume-value">50</span></label>
<input
  type="range"
  id="volume"
  name="volume"
  min="0"
  max="100"
  value="50"
  step="5"
/>

<script>
  const slider = document.getElementById("volume");
  const output = document.getElementById("volume-value");

  slider.addEventListener("input", function () {
    output.textContent = this.value;
  });
</script>
```

**5. Color Picker:**

```html
<label for="bg-color">Background Color:</label>
<input type="color" id="bg-color" name="bg-color" value="#ffffff" />

<script>
  document.getElementById("bg-color").addEventListener("change", function () {
    document.body.style.backgroundColor = this.value;
  });
</script>
```

**6. File Upload:**

```html
<!-- Single file, images only -->
<label for="avatar">Profile Picture:</label>
<input type="file" id="avatar" name="avatar" accept="image/*" />

<!-- Multiple files, specific types -->
<label for="documents">Upload Documents:</label>
<input
  type="file"
  id="documents"
  name="documents"
  multiple
  accept=".pdf,.doc,.docx,.txt"
/>

<!-- Capture from camera (mobile) -->
<label for="photo">Take Photo:</label>
<input
  type="file"
  id="photo"
  name="photo"
  accept="image/*"
  capture="environment"
/>
```

**7. Search Input:**

```html
<label for="search">Search:</label>
<input
  type="search"
  id="search"
  name="q"
  placeholder="Search products..."
  autocomplete="off"
/>
<!-- Shows clear (X) button when text is entered -->
```

**8. Tel Input:**

```html
<label for="phone">Phone Number:</label>
<input
  type="tel"
  id="phone"
  name="phone"
  pattern="[0-9]{3}-[0-9]{3}-[0-9]{4}"
  placeholder="123-456-7890"
/>
<!-- Mobile devices show numeric keyboard -->
```

**Mobile Keyboard Optimization:**

| Input Type | Mobile Keyboard             |
| ---------- | --------------------------- |
| `text`     | Standard QWERTY             |
| `email`    | QWERTY with @ and .         |
| `tel`      | Numeric keypad              |
| `number`   | Numeric keypad with +/-     |
| `url`      | QWERTY with .com, /         |
| `search`   | QWERTY with "Search" button |

**Browser Support & Fallbacks:**

```html
<!-- Modern browsers: date picker -->
<!-- Older browsers: text input -->
<input type="date" name="birthday" />

<!-- Check support with JavaScript -->
<script>
  function supportsInputType(type) {
    const input = document.createElement("input");
    input.setAttribute("type", type);
    return input.type === type;
  }

  if (!supportsInputType("date")) {
    // Load polyfill or use library (e.g., Flatpickr)
    console.log("Date input not supported, loading fallback...");
  }
</script>
```

**Validation Attributes:**

```html
<input type="text" required <!-- Must be filled -- /> minlength="3"
<!-- Minimum length -->
maxlength="20"
<!-- Maximum length -->
pattern="[A-Za-z]+"
<!-- Regex pattern -->
placeholder="Username">

<input type="number" min="0" <!-- Minimum value -- />
max="100"
<!-- Maximum value -->
step="5">
<!-- Increment step -->

<input type="email" multiple />
<!-- Allow multiple emails -->

<input type="file" accept="image/*" <!-- File type filter -- />
multiple>
<!-- Multiple files -->
```

### 💡 Key Takeaways

- HTML5 input types provide built-in validation
- Mobile keyboards adapt to input type (better UX)
- Always provide fallbacks for older browsers
- Use appropriate type for better accessibility
- `pattern` attribute for custom validation (regex)
- **Interview Tip**: Explain mobile keyboard differences
- **Gotcha**: `type="number"` doesn't accept letters, but `type="tel"` does
- **Security**: Client-side validation is UX, not security—always validate server-side

### 🔗 Resources

- [MDN: Input element](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/input)
- [MDN: Input types](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/input#input_types)
- [Can I Use: Input types](https://caniuse.com/?search=input%20type)
- [HTML5 Input Types Demo](https://www.html5tutorial.info/html5-contact.php)

### 🏢 Real-World Application

E-commerce sites use date pickers for delivery dates, number inputs for quantities, and file uploads for product reviews. Banking apps use tel inputs for phone verification. Booking platforms use datetime inputs for reservations.

---

_[Continuing with remaining beginner questions...]_

Due to length constraints, I'll create the file with the first 10 questions completed. Would you like me to continue with questions 11-15 and then proceed to create the intermediate and advanced FAQ files?
