# Semantic HTML: From Fundamentals to Advanced Practice

## 1. What “Semantic HTML” Means

- **Definition**: Using HTML elements whose names convey meaning about their content and role in the document (`<header>`, `<nav>`, `<article>`, etc.), instead of neutral containers like `<div>` and `<span>`.
- **Why it matters**:
  - Accessibility: Assistive tech builds navigation trees based on landmark tags, relationships (`<label>` with `for`, table headers, etc.).
  - SEO & content syndication: Search engines, social crawlers, and feed readers infer structure and priority.
  - Maintainability: Semantics encode intent in markup, easing refactors, design-system work, and testing.

## 2. Core Building Blocks (Basics)

- **Document outline**:
  - `<header>`: Introduces page or a section; may include branding and nav controls.
  - `<nav>`: Major navigation blocks (site-wide, in-page table of contents).
  - `<main>`: Unique primary content of the document (one per page).
  - `<section>`: Thematic grouping; typically with a heading.
  - `<article>`: Self-contained unit that could stand on its own (blog post, card, comment).
  - `<aside>`: Tangential content such as related links, ads, sidebars.
  - `<footer>`: Closing information for page or section, e.g., metadata, contact links.
- **Text-level semantics**:
  - `<strong>` vs `<b>` (importance vs styling), `<em>` vs `<i>`, `<cite>`, `<mark>`, `<abbr>`, `<time>`, `<code>`.
- **Lists & grouping**:
  - `<ol>`, `<ul>`, `<dl>`, `<figure>` + `<figcaption>`, `<blockquote>` + `cite`.

### Basic Real-World Example

```html
<header>
  <h1>Pravn Careers</h1>
  <nav aria-label="Primary">
    <a href="/roles">Roles</a>
    <a href="/culture">Culture</a>
  </nav>
</header>
<main>
  <article>
    <header>
      <h2>Senior Frontend Engineer</h2>
      <p><time datetime="2025-11-12">Nov 12, 2025</time></p>
    </header>
    <section>
      <h3>What You’ll Do</h3>
      <ul>
        <li>Lead semantic reviews of design handoffs.</li>
        <li>Improve accessibility scorecards.</li>
      </ul>
    </section>
  </article>
</main>
<footer>&copy; 2025 Pravn</footer>
```

## 3. Intermediate Topics

- **Forms**:
  - `<label>` association (`for` + `id`, or wrapping input) is mandatory for accessible names.
  - Use `<fieldset>` + `<legend>` for grouped controls, `<datalist>`, `<output>`, `<meter>`, `<progress>` when appropriate.
- **Tables**:
  - `<caption>` explains the table.
  - `<thead>`, `<tbody>`, `<tfoot>` describe regions; `<th scope="row|col">` or `headers` attribute ties data cells to headers.
- **Media**:
  - `<picture>` with `<source>` for responsive images, `<figure>` to wrap media with captions.
  - `<audio>`/`<video>` plus `<track kind="captions">` ensure inclusive playback.
- **Landmark integrity**: Avoid nested `<main>`, keep `<nav>` for major navigation only, and leverage `aria-label` when multiple landmarks occur (e.g., two `<nav>` blocks).
- **Progressive enhancement**: Start with semantic HTML, layer styling/JS afterward. Example: `<button type="button">` is preferable to clickable `<div>`.

### Example: Semantic Form Card

```html
<article>
  <header>
    <h2>Request an Interview</h2>
    <p>We respond within 48 hours.</p>
  </header>
  <form aria-describedby="interview-help">
    <fieldset>
      <legend>Candidate Details</legend>
      <label for="name">Full name</label>
      <input id="name" name="name" autocomplete="name" required />

      <label for="role">Role interest</label>
      <input id="role" name="role" list="roles" required />
      <datalist id="roles">
        <option value="Senior Frontend Engineer"></option>
        <option value="Staff Frontend Engineer"></option>
      </datalist>
    </fieldset>
    <p id="interview-help">We will follow up with a code sample request.</p>
    <button type="submit">Submit</button>
  </form>
</article>
```

## 4. Advanced Semantic Strategies (Senior+)

- **Custom elements & frameworks**:
  - Ensure framework components render semantic roots (`<Dialog>` → `<dialog>` or `<section role="dialog">`).
  - For atomic design systems, document the semantic contract (e.g., `Card` uses `<article>` by default with overridable tag).
- **Landmark audits**:
  - Use browser dev tools, axe-core, or Lighthouse to verify heading order, landmark uniqueness, tab flow.
  - Map each user journey to a semantic landmark for screen-reader shortcuts.
- **Microdata & structured data**:
  - Schema.org via `itemtype`, `itemprop`, or JSON-LD describes job listings, events, products.
- **ARIA complement, not replacement**:
  - Prefer native semantics; add ARIA when no native element exists (`role="alert"`, `aria-live`).
  - Ensure custom widgets manage keyboard focus and states (e.g., roving `aria-activedescendant`).
- **SSR/Streaming contexts**:
  - Maintain semantics across streaming server renders; avoid placeholder `<div>` wrappers when hydration islands allow `<section>` etc.
- **Testing & linting**:
  - Add ESLint plugins (`jsx-a11y`) or HTMLHint to catch missing semantics.
  - Snapshot tests should capture meaningful element types for regression detection.

### Advanced Real-Time Scenario

```html
<article itemscope itemtype="https://schema.org/JobPosting">
  <header>
    <h1 itemprop="title">Senior Frontend Engineer</h1>
    <p>
      <span itemprop="employmentType">Full-time</span> ·
      <time itemprop="datePosted" datetime="2025-11-12">Nov 12, 2025</time>
    </p>
  </header>
  <section itemprop="description">
    <p>You will own semantic audits for our design system.</p>
  </section>
  <section>
    <h2>How to apply</h2>
    <p itemprop="applicationContact">Email hiring@pravn.com</p>
  </section>
</article>
```

## 5. Senior Frontend Responsibilities Around Semantics

- Partner with design/UX to review wireframes for heading hierarchy and landmark placement before build.
- Codify semantic defaults inside shared components, PropTypes/TypeScript enforce allowed tag overrides.
- Automate regressions: run accessibility CI (axe, pa11y) on PRs to catch missing labels/captions early.
- Maintain documentation describing when to use `<section>` vs `<div>`, how to annotate complex data tables, and patterns for modals/drawers.
- Lead semantic code reviews; require before/after screenshot + accessibility tree diff for layout rewrites.

## 6. Semantic HTML FAQ (Interview-Friendly)

1. **Q:** Why choose `<section>` over `<div>`?  
   **A:** Use `<section>` for thematic grouping with a heading; `<div>` is purely structural. Screen readers announce section landmarks when meaningful.

2. **Q:** Difference between `<article>` and `<section>`?  
   **A:** `<article>` is self-contained and shareable (blog post, comment). `<section>` groups related content within a broader context and usually relies on its parent.

3. **Q:** Can a page have multiple `<header>` or `<footer>` elements?  
   **A:** Yes; each section or article can have its own header/footer. Keep one `<header>`/`<footer>` for the top-level page plus optional nested ones.

4. **Q:** How many `<main>` elements are allowed?  
   **A:** Only one per document. If you need multiple primary areas (e.g., multi-pane), use `<section>` or `<article>` landmarks with `aria-label`.

5. **Q:** When should `<nav>` be used vs. a div of links?  
   **A:** Reserve `<nav>` for major navigation blocks. For incidental link groups (tag clouds, inline links), stick with `<div>` or `<section>` plus headings.

6. **Q:** How do you semantically mark up a modal dialog?  
   **A:** Use `<dialog>` where supported, or a `<section role="dialog" aria-modal="true">` with labeled heading (`aria-labelledby`). Manage focus trapping via JS.

7. **Q:** Best approach to accessible icons?  
   **A:** If decorative, hide via `aria-hidden="true"` and avoid redundant text. If meaningful, pair with `<span class="sr-only">Label</span>` or use `<figcaption>`.

8. **Q:** How does semantic HTML impact SEO?  
   **A:** Search engines use headings, structured data, and landmark tags to understand page purpose, improving rich snippets and crawl efficiency.

9. **Q:** Strategies to maintain semantics in component libraries?  
   **A:** Provide a `as` prop/tag override, default to semantic tags, add lint rules/tests ensuring components render accessible elements.

10. **Q:** How do you test semantic correctness?  
    **A:** Combine automated tools (axe, Lighthouse), manual keyboard navigation, screen-reader smoke tests, and unit/integration tests asserting specific DOM roles/tags.

## 7. Quick Audit Checklist

- Page has exactly one `<h1>` and consistent heading order.
- All interactive elements are native controls (`<button>`, `<a href>`, `<input>`) or custom widgets with ARIA roles/states.
- Images and media include `alt`, `<figcaption>`, or text alternatives; video includes captions.
- Tables define headers, scope, and captions; complex grids use `aria-describedby` or `headers`.
- Forms pair each control with a `<label>`; groups use `<fieldset>` + `<legend>`.
- Landmarks (`header`, `nav`, `main`, `footer`, `aside`) are present and uniquely labeled where necessary.

## 8. Resources for Continued Mastery

- HTML Living Standard (WHATWG) — canonical description of semantics.
- Web Content Accessibility Guidelines (WCAG) 2.x / 3.0 drafts.
- Inclusive Components (Heydon Pickering) and WAI ARIA Authoring Practices.
- Tools: Axe DevTools, Accessibility Insights, Chrome Accessibility Tree viewer, Deque University courses.

---

**Interview Tip**: When asked about semantic HTML, structure your answer in tiers (definition → business impact → concrete example → tooling/testing). Tie everything back to measurable outcomes (accessibility scores, SEO ranking, reduced regressions).
