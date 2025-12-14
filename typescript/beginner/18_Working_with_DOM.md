# 18 - Working with DOM

## 📖 TypeScript and the DOM

TypeScript provides built-in types for DOM manipulation, making browser code type-safe.

```typescript
// TypeScript knows DOM element types
const button = document.querySelector("button"); // HTMLButtonElement | null
const input = document.getElementById("email"); // HTMLElement | null

// Type assertion for specific elements
const canvas = document.querySelector("canvas") as HTMLCanvasElement;
const ctx = canvas.getContext("2d");
```

---

## 🎯 Selecting Elements

### querySelector and querySelectorAll

```typescript
// querySelector returns Element | null
const div = document.querySelector("div");
const button = document.querySelector(".btn");

// Type assertion for specific element type
const input = document.querySelector("input") as HTMLInputElement;
const canvas = document.querySelector("canvas") as HTMLCanvasElement;

// querySelectorAll returns NodeListOf<Element>
const items = document.querySelectorAll(".item");
const buttons = document.querySelectorAll("button");

// Iterate with proper typing
buttons.forEach((btn) => {
  btn.disabled = true; // TypeScript knows it's HTMLButtonElement
});
```

### getElementById

```typescript
// Returns HTMLElement | null
const element = document.getElementById("myId");

// Type assertion needed for specific element
const form = document.getElementById("myForm") as HTMLFormElement;
const video = document.getElementById("player") as HTMLVideoElement;

// Null check
const el = document.getElementById("app");
if (el) {
  el.innerHTML = "Hello";
}

// Non-null assertion (use when you're certain)
const app = document.getElementById("app")!;
app.innerHTML = "Hello";
```

---

## 📚 Common DOM Types

```typescript
// Element types
HTMLElement; // Generic HTML element
HTMLDivElement; // <div>
HTMLSpanElement; // <span>
HTMLInputElement; // <input>
HTMLButtonElement; // <button>
HTMLFormElement; // <form>
HTMLAnchorElement; // <a>
HTMLImageElement; // <img>
HTMLCanvasElement; // <canvas>
HTMLVideoElement; // <video>
HTMLAudioElement; // <audio>
HTMLSelectElement; // <select>
HTMLTextAreaElement; // <textarea>

// Event types
Event; // Generic event
MouseEvent; // click, mouseenter, etc.
KeyboardEvent; // keydown, keyup, etc.
FocusEvent; // focus, blur
InputEvent; // input
SubmitEvent; // form submit
DragEvent; // drag and drop
TouchEvent; // touch events
WheelEvent; // scroll wheel
```

---

## 🔷 Event Handling

```typescript
// Click event
const button = document.querySelector("button");
button?.addEventListener("click", (event: MouseEvent) => {
  console.log(`Clicked at (${event.clientX}, ${event.clientY})`);
});

// Input event
const input = document.querySelector("input") as HTMLInputElement;
input.addEventListener("input", (event: Event) => {
  const target = event.target as HTMLInputElement;
  console.log("Value:", target.value);
});

// Form submit
const form = document.querySelector("form") as HTMLFormElement;
form.addEventListener("submit", (event: SubmitEvent) => {
  event.preventDefault();
  const formData = new FormData(form);
  console.log(Object.fromEntries(formData));
});

// Keyboard event
document.addEventListener("keydown", (event: KeyboardEvent) => {
  if (event.key === "Escape") {
    console.log("Escape pressed");
  }
  if (event.ctrlKey && event.key === "s") {
    event.preventDefault();
    console.log("Save shortcut");
  }
});
```

---

## 💻 Practical Examples

### 1. Form Handling

```typescript
interface FormData {
  username: string;
  email: string;
  password: string;
}

function setupForm(formId: string): void {
  const form = document.getElementById(formId) as HTMLFormElement | null;

  if (!form) {
    console.error(`Form ${formId} not found`);
    return;
  }

  form.addEventListener("submit", (e: SubmitEvent) => {
    e.preventDefault();

    const formData: FormData = {
      username: (form.elements.namedItem("username") as HTMLInputElement).value,
      email: (form.elements.namedItem("email") as HTMLInputElement).value,
      password: (form.elements.namedItem("password") as HTMLInputElement).value,
    };

    validateAndSubmit(formData);
  });
}

function validateAndSubmit(data: FormData): void {
  if (!data.email.includes("@")) {
    showError("Invalid email");
    return;
  }
  if (data.password.length < 8) {
    showError("Password too short");
    return;
  }
  console.log("Submitting:", data);
}

function showError(message: string): void {
  const errorDiv = document.querySelector(".error") as HTMLDivElement;
  if (errorDiv) {
    errorDiv.textContent = message;
    errorDiv.style.display = "block";
  }
}
```

### 2. Dynamic List

```typescript
interface ListItem {
  id: number;
  text: string;
  completed: boolean;
}

class TodoList {
  private container: HTMLUListElement;
  private items: ListItem[] = [];
  private nextId = 1;

  constructor(containerId: string) {
    const el = document.getElementById(containerId);
    if (!(el instanceof HTMLUListElement)) {
      throw new Error("Container must be a <ul> element");
    }
    this.container = el;
  }

  addItem(text: string): void {
    const item: ListItem = {
      id: this.nextId++,
      text,
      completed: false,
    };
    this.items.push(item);
    this.render();
  }

  toggleItem(id: number): void {
    const item = this.items.find((i) => i.id === id);
    if (item) {
      item.completed = !item.completed;
      this.render();
    }
  }

  removeItem(id: number): void {
    this.items = this.items.filter((i) => i.id !== id);
    this.render();
  }

  private render(): void {
    this.container.innerHTML = "";

    this.items.forEach((item) => {
      const li = document.createElement("li");
      li.className = item.completed ? "completed" : "";
      li.innerHTML = `
        <span>${item.text}</span>
        <button data-action="toggle" data-id="${item.id}">✓</button>
        <button data-action="delete" data-id="${item.id}">×</button>
      `;

      li.addEventListener("click", (e: MouseEvent) => {
        const target = e.target as HTMLElement;
        const action = target.dataset.action;
        const id = Number(target.dataset.id);

        if (action === "toggle") this.toggleItem(id);
        if (action === "delete") this.removeItem(id);
      });

      this.container.appendChild(li);
    });
  }
}
```

### 3. Canvas Drawing

```typescript
class DrawingCanvas {
  private canvas: HTMLCanvasElement;
  private ctx: CanvasRenderingContext2D;
  private isDrawing = false;
  private lastX = 0;
  private lastY = 0;

  constructor(canvasId: string) {
    const canvas = document.getElementById(canvasId);
    if (!(canvas instanceof HTMLCanvasElement)) {
      throw new Error("Element must be a canvas");
    }

    const ctx = canvas.getContext("2d");
    if (!ctx) {
      throw new Error("Could not get 2D context");
    }

    this.canvas = canvas;
    this.ctx = ctx;

    this.setupEventListeners();
  }

  private setupEventListeners(): void {
    this.canvas.addEventListener("mousedown", this.startDrawing.bind(this));
    this.canvas.addEventListener("mousemove", this.draw.bind(this));
    this.canvas.addEventListener("mouseup", this.stopDrawing.bind(this));
    this.canvas.addEventListener("mouseout", this.stopDrawing.bind(this));
  }

  private startDrawing(e: MouseEvent): void {
    this.isDrawing = true;
    [this.lastX, this.lastY] = this.getCoords(e);
  }

  private draw(e: MouseEvent): void {
    if (!this.isDrawing) return;

    const [x, y] = this.getCoords(e);

    this.ctx.beginPath();
    this.ctx.moveTo(this.lastX, this.lastY);
    this.ctx.lineTo(x, y);
    this.ctx.stroke();

    [this.lastX, this.lastY] = [x, y];
  }

  private stopDrawing(): void {
    this.isDrawing = false;
  }

  private getCoords(e: MouseEvent): [number, number] {
    const rect = this.canvas.getBoundingClientRect();
    return [e.clientX - rect.left, e.clientY - rect.top];
  }

  clear(): void {
    this.ctx.clearRect(0, 0, this.canvas.width, this.canvas.height);
  }
}
```

---

## 🔧 Type Guards for DOM

```typescript
// Check element type
function isInputElement(el: Element): el is HTMLInputElement {
  return el.tagName === "INPUT";
}

function isButtonElement(el: Element): el is HTMLButtonElement {
  return el.tagName === "BUTTON";
}

// Usage
document.addEventListener("click", (e: MouseEvent) => {
  const target = e.target as Element;

  if (isInputElement(target)) {
    console.log("Input value:", target.value);
  }

  if (isButtonElement(target)) {
    console.log("Button clicked:", target.textContent);
  }
});

// Generic element getter
function getElement<T extends HTMLElement>(
  selector: string,
  type: new () => T
): T | null {
  const el = document.querySelector(selector);
  return el instanceof type ? el : null;
}

// Usage
const input = getElement("input", HTMLInputElement);
const canvas = getElement("canvas", HTMLCanvasElement);
```

---

## ⚠️ Common Mistakes

### 1. Forgetting Null Checks

```typescript
// ❌ Crashes if element doesn't exist
const button = document.querySelector("button");
button.addEventListener("click", () => {}); // Error: possibly null

// ✅ Check for null
const button = document.querySelector("button");
if (button) {
  button.addEventListener("click", () => {});
}

// Or use optional chaining
button?.addEventListener("click", () => {});
```

### 2. Wrong Type Assertion

```typescript
// ❌ Wrong type assertion
const input = document.querySelector("input") as HTMLButtonElement;
input.disabled = true; // Works, but...
input.value = "test"; // TypeScript thinks there's no .value

// ✅ Correct type assertion
const input = document.querySelector("input") as HTMLInputElement;
input.value = "test"; // OK
```

---

## 🏋️ Practice Exercises

### Exercise 1: Modal Component

Create a type-safe modal that opens/closes with proper event handling.

### Exercise 2: Tabs Component

Build a tabs component with TypeScript DOM types.

---

## ❓ Interview Questions

**Q1: How do you type DOM elements?**

> Use specific HTML element types like `HTMLInputElement`, `HTMLButtonElement`. Use type assertions when TypeScript can't infer the exact type.

**Q2: How do you handle null from querySelector?**

> Use null checks (`if (el)`), optional chaining (`el?.method()`), non-null assertion (`el!`), or type guard functions.

**Q3: How do you type event handlers?**

> Use specific event types: `MouseEvent`, `KeyboardEvent`, `SubmitEvent`, etc. Cast `event.target` to the expected element type.

**Q4: What's the difference between Element and HTMLElement?**

> `Element` is the base type for all elements. `HTMLElement` is for HTML elements specifically. Use specific types like `HTMLInputElement` when possible.

**Q5: How do you type custom events?**

> Use `CustomEvent<T>` with a type parameter for the detail: `new CustomEvent<{userId: number}>('user-selected', {detail: {userId: 1}})`.

---

## 🔑 Key Takeaways

> ### 💡 Summary
>
> - **Use specific types**: `HTMLInputElement`, not just `Element`
> - **Handle null**: querySelector returns `Element | null`
> - **Type assertions**: when you know more than TypeScript
> - **Event typing**: use `MouseEvent`, `KeyboardEvent`, etc.
> - **Type guards**: for runtime type checking
> - **Avoid any**: type your DOM interactions properly

---

## 🎉 Congratulations!

You've completed the **TypeScript Beginner Curriculum**!

### What's Next?

- Build projects using TypeScript
- Explore advanced TypeScript (mapped types, conditional types)
- Learn TypeScript with React, Node.js, or other frameworks
- Practice with [Type Challenges](https://github.com/type-challenges/type-challenges)

**Keep coding with confidence! 🚀**
