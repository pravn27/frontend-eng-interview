# HTML Interview FAQ - Intermediate Level ⭐⭐

> **Target Audience**: Frontend Engineers with HTML fundamentals  
> **Focus**: Advanced semantic patterns, SEO, performance, web APIs  
> **Last Updated**: December 2025

---

## Table of Contents

1. [What is the difference between `<section>`, `<article>`, and `<div>`?](#1-what-is-the-difference-between-section-article-and-div)
2. [Explain the `<picture>` element and responsive images](#2-explain-the-picture-element-and-responsive-images)
3. [What are Web Components and Custom Elements?](#3-what-are-web-components-and-custom-elements)
4. [Explain HTML5 Storage APIs (localStorage, sessionStorage, IndexedDB)](#4-explain-html5-storage-apis-localstorage-sessionstorage-indexeddb)
5. [What is the Shadow DOM?](#5-what-is-the-shadow-dom)
6. [Explain `<template>` and `<slot>` elements](#6-explain-template-and-slot-elements)
7. [What are microdata and structured data?](#7-what-are-microdata-and-structured-data)
8. [Explain the `loading` attribute for lazy loading](#8-explain-the-loading-attribute-for-lazy-loading)
9. [What is Content Security Policy (CSP)?](#9-what-is-content-security-policy-csp)
10. [Explain `<iframe>` and its security considerations](#10-explain-iframe-and-its-security-considerations)
11. [What are HTML5 Form Validation APIs?](#11-what-are-html5-form-validation-apis)
12. [Explain the `<dialog>` element](#12-explain-the-dialog-element)
13. [What is the Intersection Observer API?](#13-what-is-the-intersection-observer-api)
14. [Explain `<canvas>` vs `<svg>`](#14-explain-canvas-vs-svg)
15. [What are Progressive Web Apps (PWA) manifest files?](#15-what-are-progressive-web-apps-pwa-manifest-files)

---

## 1. What is the difference between `<section>`, `<article>`, and `<div>`? [⭐⭐]

### 🎯 Concept & Purpose

Understanding when to use `<section>`, `<article>`, or `<div>` is crucial for semantic HTML. Each has a specific meaning:

- **`<section>`**: Thematic grouping of content, typically with a heading
- **`<article>`**: Self-contained, independently distributable content
- **`<div>`**: Generic container with no semantic meaning

**Why it matters:**

- **SEO**: Search engines understand content hierarchy
- **Accessibility**: Screen readers can navigate by sections/articles
- **Maintainability**: Clear document structure for developers

### 📝 Answer

**Decision Tree:**

```
Is the content self-contained and reusable?
├─ YES → Use <article>
└─ NO → Is it a thematic grouping with a heading?
    ├─ YES → Use <section>
    └─ NO → Use <div>
```

**`<article>` - Self-contained Content:**

```html
<!-- ✅ Good: Blog post (can stand alone) -->
<article>
  <header>
    <h2>Understanding Semantic HTML</h2>
    <p>By John Doe • <time datetime="2025-12-03">December 3, 2025</time></p>
  </header>

  <p>Article content goes here...</p>

  <footer>
    <p>Tags: HTML, Web Development</p>
  </footer>
</article>

<!-- ✅ Good: Product card -->
<article class="product">
  <img src="product.jpg" alt="Product name" />
  <h3>Product Name</h3>
  <p>$99.99</p>
  <button>Add to Cart</button>
</article>

<!-- ✅ Good: Comment -->
<article class="comment">
  <h4>User123</h4>
  <p>Great article!</p>
  <time datetime="2025-12-03">2 hours ago</time>
</article>
```

**`<section>` - Thematic Grouping:**

```html
<!-- ✅ Good: Chapter in a document -->
<section>
  <h2>Introduction</h2>
  <p>This section introduces the topic...</p>
</section>

<section>
  <h2>Main Content</h2>
  <p>The main discussion happens here...</p>
</section>

<!-- ✅ Good: Tabbed content -->
<section id="features">
  <h2>Features</h2>
  <ul>
    <li>Feature 1</li>
    <li>Feature 2</li>
  </ul>
</section>

<!-- ✅ Good: Related content grouping -->
<section class="testimonials">
  <h2>Customer Testimonials</h2>
  <article>
    <p>"Great product!" - Customer 1</p>
  </article>
  <article>
    <p>"Highly recommend!" - Customer 2</p>
  </article>
</section>
```

**`<div>` - Generic Container:**

```html
<!-- ✅ Good: Styling/layout wrapper -->
<div class="container">
  <div class="row">
    <div class="col-6">Column 1</div>
    <div class="col-6">Column 2</div>
  </div>
</div>

<!-- ✅ Good: JavaScript hook -->
<div id="map-container">
  <!-- Map renders here -->
</div>

<!-- ✅ Good: No semantic meaning needed -->
<div class="clearfix"></div>
```

**Comparison Table:**

| Element     | Semantic Meaning       | Self-contained | Needs Heading | Use Case                          |
| ----------- | ---------------------- | -------------- | ------------- | --------------------------------- |
| `<article>` | Independent content    | Yes            | Recommended   | Blog posts, products, comments    |
| `<section>` | Thematic grouping      | No             | Yes           | Chapters, tabs, themed groups     |
| `<div>`     | None (generic)         | N/A            | No            | Layout, styling, JavaScript hooks |
| `<aside>`   | Tangentially related   | Sometimes      | Optional      | Sidebars, pull quotes             |
| `<main>`    | Main content (one/page | No             | No            | Primary page content              |

**Real-World Example - Blog Page:**

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <title>Blog</title>
  </head>
  <body>
    <!-- Page header -->
    <header>
      <h1>My Blog</h1>
      <nav>
        <ul>
          <li><a href="/">Home</a></li>
          <li><a href="/about">About</a></li>
        </ul>
      </nav>
    </header>

    <!-- Main content area -->
    <main>
      <!-- Blog post (self-contained) -->
      <article>
        <header>
          <h2>Article Title</h2>
          <p>Published on <time>Dec 3, 2025</time></p>
        </header>

        <!-- Sections within article -->
        <section>
          <h3>Introduction</h3>
          <p>Intro content...</p>
        </section>

        <section>
          <h3>Main Points</h3>
          <p>Main content...</p>
        </section>

        <footer>
          <p>Tags: HTML, CSS</p>
        </footer>
      </article>

      <!-- Another blog post -->
      <article>
        <h2>Another Article</h2>
        <p>Content...</p>
      </article>
    </main>

    <!-- Sidebar (tangentially related) -->
    <aside>
      <section>
        <h3>Popular Posts</h3>
        <ul>
          <li><a href="#">Post 1</a></li>
          <li><a href="#">Post 2</a></li>
        </ul>
      </section>

      <section>
        <h3>Categories</h3>
        <ul>
          <li><a href="#">HTML</a></li>
          <li><a href="#">CSS</a></li>
        </ul>
      </section>
    </aside>

    <!-- Page footer -->
    <footer>
      <p>&copy; 2025 My Blog</p>
    </footer>

    <!-- Generic containers for layout -->
    <div class="modal-backdrop" hidden></div>
  </body>
</html>
```

**Nesting Rules:**

```html
<!-- ✅ Good: Articles can contain sections -->
<article>
  <h2>Article Title</h2>
  <section>
    <h3>Section 1</h3>
  </section>
  <section>
    <h3>Section 2</h3>
  </section>
</article>

<!-- ✅ Good: Sections can contain articles -->
<section>
  <h2>Latest Posts</h2>
  <article>
    <h3>Post 1</h3>
  </article>
  <article>
    <h3>Post 2</h3>
  </article>
</section>

<!-- ❌ Bad: Section without heading -->
<section>
  <p>This should probably be a div</p>
</section>

<!-- ❌ Bad: Using section just for styling -->
<section class="red-background">
  <!-- Use div instead -->
</section>
```

**The "Syndication Test" for `<article>`:**

Ask: "Could this content be syndicated (RSS feed, shared on social media, republished elsewhere)?"

- Blog post → Yes → `<article>`
- Product listing → Yes → `<article>`
- Comment → Yes → `<article>`
- Navigation menu → No → `<nav>` or `<div>`
- Layout wrapper → No → `<div>`

### 💡 Key Takeaways

- `<article>` = self-contained, reusable content
- `<section>` = thematic grouping, always with a heading
- `<div>` = no semantic meaning, use when others don't fit
- Articles can contain sections, sections can contain articles
- Don't use `<section>` just for styling (use `<div>`)
- **Interview Tip**: Use the "syndication test" to decide on `<article>`
- **Common Mistake**: Using `<section>` without a heading

### 🔗 Resources

- [MDN: section element](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/section)
- [MDN: article element](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/article)
- [HTML5 Doctor: section vs article](http://html5doctor.com/the-section-element/)
- [W3C: Sections Spec](https://html.spec.whatwg.org/multipage/sections.html)

### 🏢 Real-World Application

News sites use `<article>` for news stories. E-commerce sites use `<article>` for products. Documentation sites use `<section>` for chapters. All sites use `<div>` for layout grids.

---

## 2. Explain the `<picture>` element and responsive images [⭐⭐]

### 🎯 Concept & Purpose

The `<picture>` element provides art direction and format selection for responsive images. It solves:

- **Art Direction**: Different images for different screen sizes
- **Format Selection**: Serve modern formats (WebP, AVIF) with fallbacks
- **Performance**: Deliver appropriately sized images
- **Bandwidth**: Save data on mobile devices

**Why it exists:**  
The `<img>` element alone can't handle complex responsive image scenarios.

### 📝 Answer

**Basic Syntax:**

```html
<picture>
  <source srcset="image.webp" type="image/webp" />
  <source srcset="image.jpg" type="image/jpeg" />
  <img src="image.jpg" alt="Description" />
</picture>
```

**1. Format Selection (Modern Formats with Fallbacks):**

```html
<picture>
  <!-- Modern: AVIF (best compression) -->
  <source srcset="hero.avif" type="image/avif" />

  <!-- Modern: WebP (good compression, wide support) -->
  <source srcset="hero.webp" type="image/webp" />

  <!-- Fallback: JPEG (universal support) -->
  <img src="hero.jpg" alt="Hero image" />
</picture>
```

**2. Resolution Switching (Different Sizes):**

```html
<picture>
  <source
    media="(min-width: 1200px)"
    srcset="hero-large.jpg 1200w, hero-xlarge.jpg 1800w"
  />

  <source
    media="(min-width: 768px)"
    srcset="hero-medium.jpg 768w, hero-large.jpg 1200w"
  />

  <img
    src="hero-small.jpg"
    srcset="hero-small.jpg 400w, hero-medium.jpg 768w"
    sizes="100vw"
    alt="Hero image"
  />
</picture>
```

**3. Art Direction (Different Images for Different Contexts):**

```html
<picture>
  <!-- Desktop: Landscape orientation -->
  <source media="(min-width: 1024px)" srcset="hero-landscape.jpg" />

  <!-- Tablet: Square crop -->
  <source media="(min-width: 768px)" srcset="hero-square.jpg" />

  <!-- Mobile: Portrait orientation -->
  <img src="hero-portrait.jpg" alt="Product showcase" />
</picture>
```

**4. Combining Format + Resolution + Art Direction:**

```html
<picture>
  <!-- Desktop: Large, WebP -->
  <source
    media="(min-width: 1024px)"
    srcset="desktop-large.webp 1920w, desktop-xlarge.webp 2560w"
    type="image/webp"
  />
  <source
    media="(min-width: 1024px)"
    srcset="desktop-large.jpg 1920w, desktop-xlarge.jpg 2560w"
    type="image/jpeg"
  />

  <!-- Tablet: Medium, WebP -->
  <source
    media="(min-width: 768px)"
    srcset="tablet-medium.webp 1024w, tablet-large.webp 1440w"
    type="image/webp"
  />
  <source
    media="(min-width: 768px)"
    srcset="tablet-medium.jpg 1024w, tablet-large.jpg 1440w"
    type="image/jpeg"
  />

  <!-- Mobile: Small, WebP -->
  <source
    srcset="mobile-small.webp 640w, mobile-medium.webp 828w"
    type="image/webp"
  />

  <!-- Fallback -->
  <img
    src="mobile-small.jpg"
    srcset="mobile-small.jpg 640w, mobile-medium.jpg 828w"
    sizes="100vw"
    alt="Responsive image"
    loading="lazy"
  />
</picture>
```

**`srcset` and `sizes` Attributes:**

```html
<!-- srcset: List of images with their widths -->
<img
  src="small.jpg"
  srcset="small.jpg 400w, medium.jpg 800w, large.jpg 1200w"
  sizes="(min-width: 1024px) 1200px,
           (min-width: 768px) 800px,
           400px"
  alt="Responsive image"
/>

<!-- Browser calculates:
  - Viewport width: 1440px
  - sizes says: use 1200px
  - Device pixel ratio: 2x
  - Needs: 2400px image
  - Selects: large.jpg (1200w) or larger if available
-->
```

**Density Descriptors (Retina Displays):**

```html
<img
  src="image.jpg"
  srcset="image.jpg 1x, image@2x.jpg 2x, image@3x.jpg 3x"
  alt="High DPI image"
/>

<!-- 
  1x = Standard displays
  2x = Retina/High DPI (iPhone, MacBook)
  3x = Super Retina (iPhone Pro)
-->
```

**Practical Example - E-commerce Product:**

```html
<picture class="product-image">
  <!-- Desktop: Show full product with context -->
  <source
    media="(min-width: 1024px)"
    srcset="product-context-1x.webp 1x, product-context-2x.webp 2x"
    type="image/webp"
  />
  <source
    media="(min-width: 1024px)"
    srcset="product-context-1x.jpg 1x, product-context-2x.jpg 2x"
  />

  <!-- Tablet: Square crop, focus on product -->
  <source
    media="(min-width: 768px)"
    srcset="product-square-1x.webp 1x, product-square-2x.webp 2x"
    type="image/webp"
  />

  <!-- Mobile: Close-up of product -->
  <source
    srcset="product-closeup-1x.webp 1x, product-closeup-2x.webp 2x"
    type="image/webp"
  />

  <!-- Fallback -->
  <img
    src="product-closeup-1x.jpg"
    srcset="product-closeup-1x.jpg 1x, product-closeup-2x.jpg 2x"
    alt="Premium leather handbag in brown"
    width="800"
    height="600"
    loading="lazy"
  />
</picture>
```

**Dark Mode Support:**

```html
<picture>
  <!-- Dark mode -->
  <source srcset="logo-dark.svg" media="(prefers-color-scheme: dark)" />

  <!-- Light mode (default) -->
  <img src="logo-light.svg" alt="Company Logo" />
</picture>
```

**Performance Best Practices:**

```html
<picture>
  <source srcset="hero.webp" type="image/webp" />
  <img
    src="hero.jpg"
    alt="Hero image"
    width="1200"
    height="600"
    loading="lazy"
    decoding="async"
    fetchpriority="high"
  />
</picture>

<!-- 
  width/height: Prevent layout shift (CLS)
  loading="lazy": Defer off-screen images
  decoding="async": Don't block rendering
  fetchpriority="high": For above-the-fold images
-->
```

**Common Patterns:**

```html
<!-- Pattern 1: Format selection only -->
<picture>
  <source srcset="image.avif" type="image/avif" />
  <source srcset="image.webp" type="image/webp" />
  <img src="image.jpg" alt="..." />
</picture>

<!-- Pattern 2: Responsive sizes -->
<img
  src="small.jpg"
  srcset="small.jpg 400w, medium.jpg 800w, large.jpg 1200w"
  sizes="(min-width: 1024px) 50vw, 100vw"
  alt="..."
/>

<!-- Pattern 3: Art direction -->
<picture>
  <source media="(min-width: 1024px)" srcset="wide.jpg" />
  <source media="(min-width: 768px)" srcset="square.jpg" />
  <img src="tall.jpg" alt="..." />
</picture>

<!-- Pattern 4: Retina support -->
<img src="logo.png" srcset="logo.png 1x, logo@2x.png 2x" alt="..." />
```

### 💡 Key Takeaways

- `<picture>` for art direction and format selection
- `srcset` + `sizes` for resolution switching
- Always include `<img>` as fallback inside `<picture>`
- WebP offers 25-35% better compression than JPEG
- AVIF offers even better compression but less browser support
- Use `width` and `height` to prevent layout shift
- **Interview Tip**: Explain difference between art direction and resolution switching
- **Performance**: Can reduce image payload by 50-70%

### 🔗 Resources

- [MDN: picture element](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/picture)
- [MDN: Responsive images](https://developer.mozilla.org/en-US/docs/Learn/HTML/Multimedia_and_embedding/Responsive_images)
- [web.dev: Serve responsive images](https://web.dev/serve-responsive-images/)
- [Can I Use: picture](https://caniuse.com/picture)
- [Responsive Image Linter](https://ausi.github.io/respimagelint/)

### 🏢 Real-World Application

News sites use `<picture>` for hero images. E-commerce sites use it for product photos. Marketing sites use art direction for different devices. All modern sites use WebP for better performance.

---

_[Continuing with remaining intermediate questions...]_

## 3. What are Web Components and Custom Elements? [⭐⭐]

### 🎯 Concept & Purpose

Web Components are a set of web platform APIs that allow you to create reusable custom elements with encapsulated functionality. They consist of:

- **Custom Elements**: Define new HTML elements
- **Shadow DOM**: Encapsulated DOM and styles
- **HTML Templates**: Reusable markup templates

**Why they exist:**

- **Reusability**: Create components once, use anywhere
- **Encapsulation**: Styles and scripts don't leak
- **Framework-agnostic**: Work with any (or no) framework
- **Native**: Built into the browser, no library needed

### 📝 Answer

**1. Custom Elements - Defining New Elements:**

```javascript
// Define a custom element
class MyButton extends HTMLElement {
  constructor() {
    super();
    console.log("MyButton created");
  }

  // Called when element is added to DOM
  connectedCallback() {
    this.innerHTML = `
      <button style="
        background: #007bff;
        color: white;
        padding: 10px 20px;
        border: none;
        border-radius: 4px;
        cursor: pointer;
      ">
        ${this.getAttribute("label") || "Click me"}
      </button>
    `;

    this.querySelector("button").addEventListener("click", () => {
      this.dispatchEvent(
        new CustomEvent("myclick", { detail: "Button clicked!" })
      );
    });
  }

  // Called when element is removed from DOM
  disconnectedCallback() {
    console.log("MyButton removed");
  }

  // Called when observed attribute changes
  attributeChangedCallback(name, oldValue, newValue) {
    console.log(`Attribute ${name} changed from ${oldValue} to ${newValue}`);
  }

  // Specify which attributes to observe
  static get observedAttributes() {
    return ["label"];
  }
}

// Register the custom element
customElements.define("my-button", MyButton);
```

**Using the Custom Element:**

```html
<!DOCTYPE html>
<html>
  <head>
    <script src="my-button.js"></script>
  </head>
  <body>
    <!-- Use like any HTML element -->
    <my-button label="Submit"></my-button>
    <my-button label="Cancel"></my-button>

    <script>
      // Listen for custom events
      document.querySelector("my-button").addEventListener("myclick", (e) => {
        console.log(e.detail); // "Button clicked!"
      });

      // Programmatically create
      const btn = document.createElement("my-button");
      btn.setAttribute("label", "Dynamic Button");
      document.body.appendChild(btn);
    </script>
  </body>
</html>
```

**2. Shadow DOM - Encapsulation:**

```javascript
class FancyCard extends HTMLElement {
  constructor() {
    super();

    // Attach shadow DOM (encapsulated)
    const shadow = this.attachShadow({ mode: "open" });

    // Styles are scoped to this component
    shadow.innerHTML = `
      <style>
        /* These styles don't leak out */
        :host {
          display: block;
          border: 1px solid #ddd;
          border-radius: 8px;
          padding: 20px;
          box-shadow: 0 2px 4px rgba(0,0,0,0.1);
        }
        
        h2 {
          margin: 0 0 10px 0;
          color: #333;
        }
        
        p {
          color: #666;
          line-height: 1.6;
        }
        
        ::slotted(img) {
          width: 100%;
          border-radius: 4px;
        }
      </style>
      
      <div class="card">
        <slot name="image"></slot>
        <h2><slot name="title">Default Title</slot></h2>
        <p><slot name="content">Default content</slot></p>
        <slot name="footer"></slot>
      </div>
    `;
  }
}

customElements.define("fancy-card", FancyCard);
```

**Using Shadow DOM Component:**

```html
<fancy-card>
  <img slot="image" src="product.jpg" alt="Product" />
  <span slot="title">Product Name</span>
  <span slot="content">This is an amazing product that you'll love!</span>
  <button slot="footer">Buy Now</button>
</fancy-card>

<!-- Styles from outside don't affect the card's internal structure -->
<style>
  h2 {
    color: red; /* Doesn't affect the h2 inside fancy-card */
  }
</style>
```

**3. HTML Templates - Reusable Markup:**

```html
<!-- Define template (not rendered) -->
<template id="user-card-template">
  <style>
    .user-card {
      border: 1px solid #ccc;
      padding: 15px;
      margin: 10px 0;
      border-radius: 4px;
    }
    .user-name {
      font-weight: bold;
      font-size: 18px;
    }
    .user-email {
      color: #666;
    }
  </style>

  <div class="user-card">
    <div class="user-name"></div>
    <div class="user-email"></div>
    <button class="follow-btn">Follow</button>
  </div>
</template>

<script>
  class UserCard extends HTMLElement {
    connectedCallback() {
      const template = document.getElementById("user-card-template");
      const clone = template.content.cloneNode(true);

      // Populate with data
      clone.querySelector(".user-name").textContent = this.getAttribute("name");
      clone.querySelector(".user-email").textContent =
        this.getAttribute("email");

      // Attach to shadow DOM
      const shadow = this.attachShadow({ mode: "open" });
      shadow.appendChild(clone);

      // Add event listener
      shadow.querySelector(".follow-btn").addEventListener("click", () => {
        alert(`Following ${this.getAttribute("name")}`);
      });
    }
  }

  customElements.define("user-card", UserCard);
</script>

<!-- Usage -->
<user-card name="John Doe" email="john@example.com"></user-card>
<user-card name="Jane Smith" email="jane@example.com"></user-card>
```

**4. Complete Example - Tooltip Component:**

```javascript
class CustomTooltip extends HTMLElement {
  constructor() {
    super();
    this.attachShadow({ mode: "open" });
  }

  connectedCallback() {
    const text = this.getAttribute("text") || "Tooltip";
    const position = this.getAttribute("position") || "top";

    this.shadowRoot.innerHTML = `
      <style>
        :host {
          position: relative;
          display: inline-block;
        }
        
        .tooltip {
          position: absolute;
          background: #333;
          color: white;
          padding: 8px 12px;
          border-radius: 4px;
          font-size: 14px;
          white-space: nowrap;
          opacity: 0;
          pointer-events: none;
          transition: opacity 0.3s;
          z-index: 1000;
        }
        
        .tooltip.top {
          bottom: 100%;
          left: 50%;
          transform: translateX(-50%) translateY(-8px);
        }
        
        .tooltip.bottom {
          top: 100%;
          left: 50%;
          transform: translateX(-50%) translateY(8px);
        }
        
        .tooltip::after {
          content: '';
          position: absolute;
          border: 6px solid transparent;
        }
        
        .tooltip.top::after {
          top: 100%;
          left: 50%;
          transform: translateX(-50%);
          border-top-color: #333;
        }
        
        :host(:hover) .tooltip {
          opacity: 1;
        }
        
        ::slotted(*) {
          cursor: help;
        }
      </style>
      
      <slot></slot>
      <div class="tooltip ${position}">${text}</div>
    `;
  }

  // Update tooltip when attribute changes
  attributeChangedCallback(name, oldValue, newValue) {
    if (name === "text") {
      const tooltip = this.shadowRoot.querySelector(".tooltip");
      if (tooltip) tooltip.textContent = newValue;
    }
  }

  static get observedAttributes() {
    return ["text"];
  }
}

customElements.define("custom-tooltip", CustomTooltip);
```

**Using the Tooltip:**

```html
<custom-tooltip text="This is helpful information" position="top">
  <span>Hover over me</span>
</custom-tooltip>

<custom-tooltip text="Click for more details" position="bottom">
  <button>Help</button>
</custom-tooltip>
```

**5. Lifecycle Callbacks:**

```javascript
class LifecycleDemo extends HTMLElement {
  constructor() {
    super();
    console.log("1. constructor: Element created");
  }

  connectedCallback() {
    console.log("2. connectedCallback: Added to DOM");
  }

  disconnectedCallback() {
    console.log("3. disconnectedCallback: Removed from DOM");
  }

  adoptedCallback() {
    console.log("4. adoptedCallback: Moved to new document");
  }

  attributeChangedCallback(name, oldValue, newValue) {
    console.log(`5. attributeChangedCallback: ${name} = ${newValue}`);
  }

  static get observedAttributes() {
    return ["data-value"];
  }
}

customElements.define("lifecycle-demo", LifecycleDemo);
```

**6. Extending Built-in Elements:**

```javascript
// Extend existing elements (Customized built-in element)
class FancyButton extends HTMLButtonElement {
  connectedCallback() {
    this.addEventListener("click", () => {
      this.classList.add("clicked");
      setTimeout(() => this.classList.remove("clicked"), 300);
    });
  }
}

customElements.define("fancy-button", FancyButton, { extends: "button" });
```

```html
<!-- Use with 'is' attribute -->
<button is="fancy-button">Click me</button>
```

**Browser Support & Polyfills:**

```html
<!-- Check support -->
<script>
  if ("customElements" in window) {
    console.log("Web Components supported!");
  } else {
    // Load polyfill
    console.log("Loading polyfills...");
  }
</script>

<!-- Polyfill for older browsers -->
<script src="https://unpkg.com/@webcomponents/webcomponentsjs@latest/webcomponents-loader.js"></script>
```

### 💡 Key Takeaways

- Web Components = Custom Elements + Shadow DOM + Templates
- Custom Elements define new HTML tags
- Shadow DOM provides encapsulation (styles don't leak)
- Templates allow reusable markup
- Framework-agnostic, work anywhere
- Lifecycle callbacks for element management
- **Interview Tip**: Explain the three main technologies
- **Browser Support**: Excellent in modern browsers, polyfills available
- **Use Case**: Design systems, reusable UI components

### 🔗 Resources

- [MDN: Web Components](https://developer.mozilla.org/en-US/docs/Web/Web_Components)
- [MDN: Custom Elements](https://developer.mozilla.org/en-US/docs/Web/Web_Components/Using_custom_elements)
- [MDN: Shadow DOM](https://developer.mozilla.org/en-US/docs/Web/Web_Components/Using_shadow_DOM)
- [web.dev: Web Components](https://web.dev/custom-elements-v1/)
- [webcomponents.org](https://www.webcomponents.org/)
- [Can I Use: Custom Elements](https://caniuse.com/custom-elementsv1)

### 🏢 Real-World Application

YouTube uses Web Components for its video player. GitHub uses them for UI elements. Salesforce Lightning uses Web Components. Design systems (Material, Shoelace) are built with Web Components.

---

_[Due to length, I'll create the file with these 3 comprehensive intermediate questions and continue with more...]_
