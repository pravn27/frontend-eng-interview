# Senior Frontend Engineer - HTML Concepts Guide

## Table of Contents

1. [Semantic HTML](#semantic-html)
2. [HTML5 APIs](#html5-apis)
3. [Accessibility (a11y)](#accessibility-a11y)
4. [Performance Optimization](#performance-optimization)
5. [SEO Best Practices](#seo-best-practices)
6. [Web Components](#web-components)
7. [Forms and Validation](#forms-and-validation)
8. [Meta Tags and Document Structure](#meta-tags-and-document-structure)
9. [Security Considerations](#security-considerations)
10. [Cross-Browser Compatibility](#cross-browser-compatibility)
11. [Advanced HTML Features](#advanced-html-features)

---

## Semantic HTML

### Why Semantic HTML Matters

- **Accessibility**: Screen readers and assistive technologies rely on semantic markup
- **SEO**: Search engines better understand content structure
- **Maintainability**: Code is more readable and self-documenting
- **Performance**: Browser optimizations for semantic elements

### Key Semantic Elements

#### Structural Elements

```html
<header>
  <!-- Page or section header -->
  <nav>
    <!-- Navigation links -->
    <main>
      <!-- Primary content (only one per page) -->
      <article>
        <!-- Self-contained content -->
        <section>
          <!-- Thematic grouping of content -->
          <aside>
            <!-- Tangentially related content -->
            <footer><!-- Footer information --></footer>
          </aside>
        </section>
      </article>
    </main>
  </nav>
</header>
```

#### Content Elements

```html
<figure>
  <!-- Self-contained content with optional caption -->
  <figcaption>
    <!-- Caption for figure -->
    <time>
      <!-- Datetime representation -->
      <mark>
        <!-- Highlighted/marked text -->
        <details>
          <!-- Disclosure widget -->
          <summary><!-- Summary for details element --></summary>
        </details></mark
      ></time
    >
  </figcaption>
</figure>
```

### Common Interview Questions

- **Q: Difference between `<article>` and `<section>`?**

  - `<article>`: Represents independent, self-contained content (blog post, news article)
  - `<section>`: Groups related content together, not necessarily independent

- **Q: When to use `<div>` vs semantic elements?**
  - Use semantic elements when the content has meaning
  - Use `<div>` only for styling/layout purposes with no semantic meaning

---

## HTML5 APIs

### 1. Canvas API

- 2D graphics drawing
- Game development
- Data visualization
- Image manipulation

```html
<canvas id="myCanvas" width="400" height="200"></canvas>
<script>
  const canvas = document.getElementById("myCanvas");
  const ctx = canvas.getContext("2d");
  ctx.fillStyle = "blue";
  ctx.fillRect(10, 10, 100, 100);
</script>
```

### 2. Geolocation API

```javascript
navigator.geolocation.getCurrentPosition(
  (position) => {
    console.log(position.coords.latitude, position.coords.longitude);
  },
  (error) => {
    console.error(error);
  }
);
```

### 3. Web Storage API

- **localStorage**: Persistent storage (no expiration)
- **sessionStorage**: Session-based storage (cleared on tab close)

```javascript
// localStorage
localStorage.setItem("key", "value");
localStorage.getItem("key");
localStorage.removeItem("key");

// sessionStorage
sessionStorage.setItem("key", "value");
```

### 4. Web Workers

- Background thread execution
- Prevents blocking main thread
- Perfect for heavy computations

```javascript
// main.js
const worker = new Worker("worker.js");
worker.postMessage({ data: "hello" });
worker.onmessage = (e) => console.log(e.data);

// worker.js
self.onmessage = (e) => {
  // Heavy computation
  self.postMessage({ result: "processed" });
};
```

### 5. Service Workers

- Offline functionality
- Push notifications
- Background sync
- Cache management

### 6. History API

```javascript
history.pushState(state, title, url);
history.replaceState(state, title, url);
window.addEventListener("popstate", (event) => {
  console.log(event.state);
});
```

### 7. Drag and Drop API

```html
<div draggable="true" ondragstart="handleDragStart(event)">Drag me</div>
<div ondrop="handleDrop(event)" ondragover="handleDragOver(event)">
  Drop here
</div>
```

### 8. Intersection Observer API

```javascript
const observer = new IntersectionObserver(
  (entries) => {
    entries.forEach((entry) => {
      if (entry.isIntersecting) {
        // Element is visible
      }
    });
  },
  { threshold: 0.5 }
);

observer.observe(element);
```

---

## Accessibility (a11y)

### ARIA (Accessible Rich Internet Applications)

#### ARIA Roles

```html
<div role="button" tabindex="0">Click me</div>
<nav role="navigation">...</nav>
<div role="alert">Important message</div>
<div role="dialog" aria-labelledby="dialogTitle">...</div>
```

#### ARIA Attributes

```html
<!-- aria-label: Provides accessible name -->
<button aria-label="Close dialog">×</button>

<!-- aria-labelledby: References element for label -->
<h2 id="dialogTitle">Confirm Action</h2>
<div role="dialog" aria-labelledby="dialogTitle">...</div>

<!-- aria-describedby: Additional description -->
<input aria-describedby="passwordHelp" />
<div id="passwordHelp">Password must be 8 characters</div>

<!-- aria-hidden: Hide from screen readers -->
<span aria-hidden="true">🎉</span>

<!-- aria-live: Announces dynamic content -->
<div aria-live="polite" aria-atomic="true">Status: Processing...</div>

<!-- aria-expanded: Collapsible content state -->
<button aria-expanded="false" aria-controls="menu">Menu</button>

<!-- aria-disabled: Disabled state -->
<button aria-disabled="true">Submit</button>
```

### Focus Management

```html
<!-- tabindex usage -->
<div tabindex="0">Focusable</div>
<!-- Natural tab order -->
<div tabindex="-1">Programmatically focusable</div>
<!-- Not in tab order -->
<div tabindex="1">First in tab order</div>
<!-- Avoid positive values -->
```

### Skip Links

```html
<a href="#main-content" class="skip-link">Skip to main content</a>
<main id="main-content">...</main>
```

### Image Accessibility

```html
<!-- Decorative images -->
<img src="decorative.png" alt="" role="presentation" />

<!-- Informative images -->
<img src="chart.png" alt="Bar chart showing 50% increase in sales" />

<!-- Complex images -->
<figure>
  <img src="complex.png" alt="Sales data visualization" />
  <figcaption>Detailed description of the data...</figcaption>
</figure>
```

### Common Accessibility Issues

- Missing alt text on images
- Low color contrast
- No keyboard navigation support
- Missing form labels
- Auto-playing audio/video
- Inaccessible modals/dialogs

---

## Performance Optimization

### 1. Resource Loading Strategies

#### Async vs Defer

```html
<!-- Async: Downloads in parallel, executes immediately when ready -->
<script src="analytics.js" async></script>

<!-- Defer: Downloads in parallel, executes after HTML parsing -->
<script src="app.js" defer></script>

<!-- Normal: Blocks HTML parsing -->
<script src="legacy.js"></script>
```

#### Preload, Prefetch, Preconnect

```html
<!-- Preload: High priority, needed for current page -->
<link rel="preload" href="critical.css" as="style" />
<link rel="preload" href="font.woff2" as="font" type="font/woff2" crossorigin />

<!-- Prefetch: Low priority, might be needed for future navigation -->
<link rel="prefetch" href="next-page.js" />

<!-- Preconnect: Early connection establishment -->
<link rel="preconnect" href="https://cdn.example.com" />

<!-- DNS-prefetch: DNS resolution only -->
<link rel="dns-prefetch" href="https://api.example.com" />
```

### 2. Image Optimization

#### Responsive Images

```html
<!-- srcset for different resolutions -->
<img
  src="image-400.jpg"
  srcset="image-400.jpg 400w, image-800.jpg 800w, image-1200.jpg 1200w"
  sizes="(max-width: 600px) 400px,
         (max-width: 1200px) 800px,
         1200px"
  alt="Description"
/>

<!-- picture element for art direction -->
<picture>
  <source media="(min-width: 800px)" srcset="large.jpg" />
  <source media="(min-width: 400px)" srcset="medium.jpg" />
  <img src="small.jpg" alt="Description" />
</picture>

<!-- Modern format support -->
<picture>
  <source srcset="image.avif" type="image/avif" />
  <source srcset="image.webp" type="image/webp" />
  <img src="image.jpg" alt="Fallback" />
</picture>
```

#### Lazy Loading

```html
<!-- Native lazy loading -->
<img src="image.jpg" loading="lazy" alt="Description" />
<iframe src="video.html" loading="lazy"></iframe>
```

### 3. Critical CSS

```html
<!-- Inline critical CSS -->
<style>
  /* Above-the-fold styles */
  .hero {
    /* ... */
  }
</style>

<!-- Defer non-critical CSS -->
<link
  rel="preload"
  href="styles.css"
  as="style"
  onload="this.rel='stylesheet'"
/>
<noscript><link rel="stylesheet" href="styles.css" /></noscript>
```

### 4. Content Visibility

```css
/* CSS property for performance */
.section {
  content-visibility: auto;
  contain-intrinsic-size: 0 500px;
}
```

---

## SEO Best Practices

### Meta Tags

```html
<head>
  <!-- Title (50-60 characters) -->
  <title>Page Title | Brand Name</title>

  <!-- Description (150-160 characters) -->
  <meta name="description" content="Compelling description">

  <!-- Keywords (less important now) -->
  <meta name="keywords" content="keyword1, keyword2">

  <!-- Robots -->
  <meta name="robots" content="index, follow">

  <!-- Canonical URL -->
  <link rel="canonical" href="https://example.com/page">

  <!-- Language -->
  <html lang="en">

  <!-- Viewport -->
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
</head>
```

### Open Graph (Social Media)

```html
<meta property="og:title" content="Page Title" />
<meta property="og:description" content="Description" />
<meta property="og:image" content="https://example.com/image.jpg" />
<meta property="og:url" content="https://example.com/page" />
<meta property="og:type" content="website" />
<meta property="og:site_name" content="Site Name" />

<!-- Twitter Card -->
<meta name="twitter:card" content="summary_large_image" />
<meta name="twitter:site" content="@username" />
<meta name="twitter:title" content="Page Title" />
<meta name="twitter:description" content="Description" />
<meta name="twitter:image" content="https://example.com/image.jpg" />
```

### Structured Data (Schema.org)

```html
<script type="application/ld+json">
  {
    "@context": "https://schema.org",
    "@type": "Article",
    "headline": "Article Title",
    "author": {
      "@type": "Person",
      "name": "Author Name"
    },
    "datePublished": "2025-01-01",
    "image": "https://example.com/image.jpg"
  }
</script>
```

### Heading Hierarchy

```html
<!-- Proper hierarchy -->
<h1>Main Page Title</h1>
<h2>Section 1</h2>
<h3>Subsection 1.1</h3>
<h2>Section 2</h2>
<h3>Subsection 2.1</h3>
```

---

## Web Components

### Custom Elements

```javascript
class MyButton extends HTMLElement {
  constructor() {
    super();
    this.attachShadow({ mode: "open" });
  }

  connectedCallback() {
    this.shadowRoot.innerHTML = `
      <style>
        button { 
          padding: 10px 20px; 
          background: blue; 
          color: white; 
        }
      </style>
      <button><slot></slot></button>
    `;
  }
}

customElements.define("my-button", MyButton);
```

```html
<!-- Usage -->
<my-button>Click Me</my-button>
```

### Shadow DOM

- Encapsulation of styles and markup
- Scoped CSS
- Protected from global styles

### Templates and Slots

```html
<template id="card-template">
  <style>
    .card {
      border: 1px solid #ccc;
      padding: 20px;
    }
  </style>
  <div class="card">
    <h2><slot name="title"></slot></h2>
    <p><slot name="content"></slot></p>
  </div>
</template>

<script>
  class CardElement extends HTMLElement {
    constructor() {
      super();
      const template = document.getElementById("card-template");
      const content = template.content.cloneNode(true);
      this.attachShadow({ mode: "open" }).appendChild(content);
    }
  }
  customElements.define("card-element", CardElement);
</script>

<!-- Usage -->
<card-element>
  <span slot="title">Card Title</span>
  <span slot="content">Card content goes here</span>
</card-element>
```

---

## Forms and Validation

### Input Types

```html
<!-- HTML5 Input Types -->
<input type="email" required />
<input type="url" required />
<input type="number" min="0" max="100" step="5" />
<input type="range" min="0" max="100" />
<input type="date" />
<input type="time" />
<input type="datetime-local" />
<input type="color" />
<input type="search" />
<input type="tel" pattern="[0-9]{3}-[0-9]{3}-[0-9]{4}" />
```

### Validation Attributes

```html
<!-- Required -->
<input type="text" required />

<!-- Pattern -->
<input type="text" pattern="[A-Za-z]{3,}" />

<!-- Min/Max Length -->
<input type="text" minlength="3" maxlength="20" />

<!-- Min/Max Value -->
<input type="number" min="18" max="99" />

<!-- Step -->
<input type="number" step="0.01" />

<!-- Multiple -->
<input type="email" multiple />
<select multiple>
  ...
</select>
```

### Custom Validation

```javascript
const input = document.querySelector("input");

input.addEventListener("input", (e) => {
  if (e.target.validity.valueMissing) {
    e.target.setCustomValidity("This field is required");
  } else if (e.target.validity.patternMismatch) {
    e.target.setCustomValidity("Please match the format requested");
  } else {
    e.target.setCustomValidity("");
  }
});

// Check validity
if (input.checkValidity()) {
  // Valid
} else {
  // Invalid
  input.reportValidity(); // Show validation message
}
```

### Form Attributes

```html
<!-- Autocomplete -->
<input type="text" autocomplete="name" />
<input type="email" autocomplete="email" />
<input type="tel" autocomplete="tel" />
<input type="text" autocomplete="address-line1" />

<!-- Autofocus -->
<input type="text" autofocus />

<!-- Placeholder -->
<input type="text" placeholder="Enter your name" />

<!-- Readonly and Disabled -->
<input type="text" readonly />
<input type="text" disabled />

<!-- Form association -->
<input type="text" form="myForm" />
<button type="submit" form="myForm">Submit</button>
```

### Fieldset and Legend

```html
<form>
  <fieldset>
    <legend>Personal Information</legend>
    <label for="name">Name:</label>
    <input type="text" id="name" name="name" />
  </fieldset>

  <fieldset disabled>
    <legend>Disabled Section</legend>
    <input type="text" />
  </fieldset>
</form>
```

---

## Meta Tags and Document Structure

### Essential Meta Tags

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <!-- Character Encoding -->
    <meta charset="UTF-8" />

    <!-- Viewport -->
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />

    <!-- IE Compatibility -->
    <meta http-equiv="X-UA-Compatible" content="IE=edge" />

    <!-- Theme Color (mobile browsers) -->
    <meta name="theme-color" content="#4285f4" />

    <!-- App Capable (PWA) -->
    <meta name="mobile-web-app-capable" content="yes" />
    <meta name="apple-mobile-web-app-capable" content="yes" />
    <meta name="apple-mobile-web-app-status-bar-style" content="black" />

    <!-- Referrer Policy -->
    <meta name="referrer" content="origin-when-cross-origin" />

    <!-- Content Security Policy -->
    <meta
      http-equiv="Content-Security-Policy"
      content="default-src 'self'; script-src 'self' 'unsafe-inline';"
    />
  </head>
</html>
```

### Favicon

```html
<!-- Modern approach -->
<link rel="icon" type="image/svg+xml" href="/favicon.svg" />
<link rel="icon" type="image/png" href="/favicon.png" />

<!-- Apple Touch Icon -->
<link rel="apple-touch-icon" sizes="180x180" href="/apple-touch-icon.png" />

<!-- Manifest -->
<link rel="manifest" href="/site.webmanifest" />
```

### Base URL

```html
<!-- Sets base URL for relative links -->
<base href="https://example.com/" />
<base target="_blank" />
```

---

## Security Considerations

### 1. Content Security Policy (CSP)

```html
<!-- Via meta tag -->
<meta
  http-equiv="Content-Security-Policy"
  content="default-src 'self'; 
               script-src 'self' https://trusted.com; 
               style-src 'self' 'unsafe-inline';"
/>

<!-- Better: via HTTP header -->
<!-- Content-Security-Policy: default-src 'self'; script-src 'self' -->
```

### 2. XSS Prevention

```html
<!-- Never use innerHTML with user input -->
<!-- BAD -->
element.innerHTML = userInput;

<!-- GOOD -->
element.textContent = userInput;

<!-- Use DOMPurify for rich content -->
element.innerHTML = DOMPurify.sanitize(userInput);
```

### 3. Sandboxing iframes

```html
<!-- Restrict iframe capabilities -->
<iframe src="untrusted.html" sandbox="allow-scripts allow-same-origin">
</iframe>

<!-- More restrictive -->
<iframe src="untrusted.html" sandbox></iframe>
```

### 4. Referrer Policy

```html
<!-- Control referrer information -->
<meta name="referrer" content="no-referrer" />
<meta name="referrer" content="strict-origin-when-cross-origin" />

<!-- Per-link -->
<a href="external.com" rel="noreferrer">Link</a>
```

### 5. Subresource Integrity (SRI)

```html
<!-- Verify CDN resources -->
<script
  src="https://cdn.example.com/script.js"
  integrity="sha384-oqVuAfXRKap7fdgcCY5uykM6+R9GqQ8K/uxy9rx7HNQlGYl1kPzQho1wx4JwY8wC"
  crossorigin="anonymous"
></script>
```

### 6. rel="noopener noreferrer"

```html
<!-- Prevent window.opener access -->
<a href="external.com" target="_blank" rel="noopener noreferrer">Link</a>
```

---

## Cross-Browser Compatibility

### Feature Detection

```javascript
// Check feature support
if ("serviceWorker" in navigator) {
  // Service Worker supported
}

if (typeof IntersectionObserver !== "undefined") {
  // Intersection Observer supported
}

// Using Modernizr
if (Modernizr.webp) {
  // WebP supported
}
```

### Polyfills

```html
<!-- Polyfill for older browsers -->
<script src="https://polyfill.io/v3/polyfill.min.js"></script>

<!-- Specific polyfills -->
<script>
  if (!("Promise" in window)) {
    // Load Promise polyfill
  }
</script>
```

### Progressive Enhancement

```html
<!-- Basic HTML works without JS/CSS -->
<form action="/submit" method="POST">
  <input type="text" name="search" />
  <button type="submit">Search</button>
</form>

<!-- Enhanced with JavaScript -->
<script>
  // Enhance form with AJAX
</script>
```

### Vendor Prefixes

```html
<!-- Input types with fallbacks -->
<input type="date" placeholder="YYYY-MM-DD" />
```

---

## Advanced HTML Features

### 1. Dialog Element

```html
<dialog id="myDialog">
  <h2>Dialog Title</h2>
  <p>Dialog content</p>
  <button onclick="myDialog.close()">Close</button>
</dialog>

<script>
  const dialog = document.getElementById("myDialog");

  // Show modal
  dialog.showModal();

  // Show non-modal
  dialog.show();

  // Close
  dialog.close();

  // Listen for close
  dialog.addEventListener("close", () => {
    console.log(dialog.returnValue);
  });
</script>
```

### 2. Details and Summary

```html
<details>
  <summary>Click to expand</summary>
  <p>Hidden content revealed when expanded</p>
</details>

<details open>
  <summary>Open by default</summary>
  <p>Content</p>
</details>

<script>
  const details = document.querySelector("details");
  details.addEventListener("toggle", () => {
    if (details.open) {
      console.log("Opened");
    }
  });
</script>
```

### 3. Picture Element

```html
<picture>
  <!-- High DPI screens -->
  <source media="(min-width: 1200px)" srcset="large.jpg 1x, large-2x.jpg 2x" />

  <!-- Tablets -->
  <source media="(min-width: 768px)" srcset="medium.jpg" />

  <!-- Mobile -->
  <img src="small.jpg" alt="Responsive image" />
</picture>
```

### 4. Data Attributes

```html
<!-- Custom data attributes -->
<div data-user-id="123" data-role="admin" data-status="active">User info</div>

<script>
  const div = document.querySelector("div");

  // Access via dataset
  console.log(div.dataset.userId); // "123"
  console.log(div.dataset.role); // "admin"

  // Modify
  div.dataset.status = "inactive";

  // CSS selector
  const element = document.querySelector('[data-role="admin"]');
</script>
```

### 5. Output Element

```html
<form oninput="result.value=parseInt(a.value)+parseInt(b.value)">
  <input type="number" id="a" value="0" /> +
  <input type="number" id="b" value="0" /> =
  <output name="result" for="a b">0</output>
</form>
```

### 6. Progress and Meter

```html
<!-- Progress bar -->
<progress value="70" max="100">70%</progress>

<!-- Meter -->
<meter value="6" min="0" max="10" low="3" high="7" optimum="9">
  6 out of 10
</meter>
```

### 7. Contenteditable

```html
<!-- Editable content -->
<div contenteditable="true">This text is editable</div>

<script>
  const div = document.querySelector("[contenteditable]");

  div.addEventListener("input", (e) => {
    console.log(e.target.innerHTML);
  });

  // Prevent certain formatting
  div.addEventListener("paste", (e) => {
    e.preventDefault();
    const text = e.clipboardData.getData("text/plain");
    document.execCommand("insertText", false, text);
  });
</script>
```

### 8. Datalist

```html
<!-- Autocomplete suggestions -->
<input list="browsers" name="browser" />
<datalist id="browsers">
  <option value="Chrome"></option>
  <option value="Firefox"></option>
  <option value="Safari"></option>
  <option value="Edge"></option>
</datalist>
```

---

## Interview Tips for Senior Roles

### Depth of Knowledge Expected

1. **Not just "what" but "why"**: Understand reasoning behind HTML features
2. **Performance implications**: How HTML choices affect page performance
3. **Accessibility**: WCAG guidelines and ARIA usage
4. **Browser differences**: Cross-browser issues and solutions
5. **Real-world experience**: Examples from actual projects

### Common Senior-Level Questions

#### Architecture Questions

- How would you structure a large-scale web application's HTML?
- Explain your approach to component-based HTML architecture
- How do you handle internationalization in HTML?

#### Performance Questions

- How do you optimize HTML for performance?
- Explain critical rendering path and how HTML affects it
- What's your strategy for loading third-party scripts?

#### Accessibility Questions

- How do you ensure your HTML is accessible?
- Explain ARIA roles vs native HTML semantics
- How do you test for accessibility?

#### Modern Practices

- Experience with Web Components
- Server-side rendering (SSR) considerations
- Progressive Web App (PWA) implementation
- HTML email development challenges

### Best Practices to Mention

1. Always use semantic HTML
2. Implement proper heading hierarchy
3. Provide alt text for images
4. Use proper form labels
5. Ensure keyboard navigation
6. Optimize for performance
7. Consider mobile-first approach
8. Implement progressive enhancement
9. Follow WCAG guidelines
10. Use proper meta tags for SEO

---

## Additional Resources

### Standards and Documentation

- [W3C HTML Specification](https://html.spec.whatwg.org/)
- [MDN Web Docs](https://developer.mozilla.org/en-US/docs/Web/HTML)
- [Can I Use](https://caniuse.com/) - Browser compatibility
- [Web.dev](https://web.dev/) - Best practices

### Accessibility

- [WCAG Guidelines](https://www.w3.org/WAI/WCAG21/quickref/)
- [ARIA Authoring Practices](https://www.w3.org/WAI/ARIA/apg/)
- [WebAIM](https://webaim.org/) - Accessibility resources

### Testing Tools

- Lighthouse (Chrome DevTools)
- axe DevTools (Accessibility testing)
- WAVE (Web accessibility evaluation tool)
- HTML Validator (W3C)

### Performance Tools

- PageSpeed Insights
- WebPageTest
- Chrome DevTools Performance panel

---

## Conclusion

For a Senior Frontend Engineer role, deep understanding of HTML goes beyond just writing markup. It encompasses:

- **Semantic correctness** and meaningful structure
- **Accessibility** for all users
- **Performance optimization** at the HTML level
- **SEO best practices** for discoverability
- **Security considerations** to prevent vulnerabilities
- **Modern APIs** and progressive enhancement
- **Cross-browser compatibility** strategies

Focus on practical experience and real-world problem-solving rather than just theoretical knowledge. Be prepared to discuss trade-offs, performance implications, and architectural decisions in your answers.

Good luck with your interview preparation! 🚀
