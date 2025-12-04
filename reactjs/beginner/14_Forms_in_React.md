# 14. Forms in React

> Handling user input - controlled components and form validation.

---

## 🎯 What Are Controlled Components?

In React, a **controlled component** is a form element whose value is controlled by React state. The component's state is the "single source of truth."

```jsx
function Form() {
  const [name, setName] = useState("");

  return (
    <input
      value={name} // Controlled by state
      onChange={(e) => setName(e.target.value)} // Updates state
    />
  );
}
```

---

## 🤔 Why Controlled Components?

Controlled components give you:

- ✅ **Single source of truth** - State is always current
- ✅ **Instant validation** - Validate as user types
- ✅ **Conditional logic** - Disable buttons, show/hide fields
- ✅ **Format input** - Transform values on the fly
- ✅ **Easy form submission** - Access all values from state

---

## 📚 Core Concepts

### 1. Basic Input Control

```jsx
import { useState } from "react";

function TextInputs() {
  const [text, setText] = useState("");
  const [email, setEmail] = useState("");
  const [password, setPassword] = useState("");

  return (
    <form>
      {/* Text input */}
      <input
        type="text"
        value={text}
        onChange={(e) => setText(e.target.value)}
        placeholder="Enter text"
      />

      {/* Email input */}
      <input
        type="email"
        value={email}
        onChange={(e) => setEmail(e.target.value)}
        placeholder="Enter email"
      />

      {/* Password input */}
      <input
        type="password"
        value={password}
        onChange={(e) => setPassword(e.target.value)}
        placeholder="Enter password"
      />
    </form>
  );
}
```

### 2. Textarea

```jsx
function TextareaExample() {
  const [bio, setBio] = useState("");
  const maxLength = 200;

  return (
    <div>
      <textarea
        value={bio}
        onChange={(e) => setBio(e.target.value)}
        placeholder="Tell us about yourself"
        rows={4}
        maxLength={maxLength}
      />
      <p>
        {bio.length}/{maxLength} characters
      </p>
    </div>
  );
}
```

### 3. Select (Dropdown)

```jsx
function SelectExample() {
  const [country, setCountry] = useState("");
  const [languages, setLanguages] = useState([]); // For multiple

  const countries = ["USA", "Canada", "UK", "Australia"];
  const availableLanguages = ["JavaScript", "Python", "Go", "Rust"];

  return (
    <div>
      {/* Single select */}
      <select value={country} onChange={(e) => setCountry(e.target.value)}>
        <option value="">Select a country</option>
        {countries.map((c) => (
          <option key={c} value={c}>
            {c}
          </option>
        ))}
      </select>

      {/* Multiple select */}
      <select
        multiple
        value={languages}
        onChange={(e) => {
          const selected = Array.from(
            e.target.selectedOptions,
            (opt) => opt.value
          );
          setLanguages(selected);
        }}
      >
        {availableLanguages.map((lang) => (
          <option key={lang} value={lang}>
            {lang}
          </option>
        ))}
      </select>
    </div>
  );
}
```

### 4. Checkbox

```jsx
function CheckboxExamples() {
  // Single checkbox (boolean)
  const [agreed, setAgreed] = useState(false);

  // Multiple checkboxes (array)
  const [selectedFruits, setSelectedFruits] = useState([]);
  const fruits = ["Apple", "Banana", "Cherry", "Date"];

  const handleFruitChange = (fruit) => {
    setSelectedFruits((prev) =>
      prev.includes(fruit) ? prev.filter((f) => f !== fruit) : [...prev, fruit]
    );
  };

  return (
    <div>
      {/* Single checkbox */}
      <label>
        <input
          type="checkbox"
          checked={agreed}
          onChange={(e) => setAgreed(e.target.checked)}
        />
        I agree to terms
      </label>

      {/* Multiple checkboxes */}
      <fieldset>
        <legend>Select fruits:</legend>
        {fruits.map((fruit) => (
          <label key={fruit}>
            <input
              type="checkbox"
              checked={selectedFruits.includes(fruit)}
              onChange={() => handleFruitChange(fruit)}
            />
            {fruit}
          </label>
        ))}
      </fieldset>

      <p>Selected: {selectedFruits.join(", ") || "None"}</p>
    </div>
  );
}
```

### 5. Radio Buttons

```jsx
function RadioExample() {
  const [plan, setPlan] = useState("");
  const [payment, setPayment] = useState("monthly");

  const plans = [
    { value: "basic", label: "Basic - $9/mo" },
    { value: "pro", label: "Pro - $19/mo" },
    { value: "enterprise", label: "Enterprise - $49/mo" },
  ];

  return (
    <div>
      <fieldset>
        <legend>Select a plan:</legend>
        {plans.map((p) => (
          <label key={p.value}>
            <input
              type="radio"
              name="plan"
              value={p.value}
              checked={plan === p.value}
              onChange={(e) => setPlan(e.target.value)}
            />
            {p.label}
          </label>
        ))}
      </fieldset>

      <fieldset>
        <legend>Billing:</legend>
        <label>
          <input
            type="radio"
            name="payment"
            value="monthly"
            checked={payment === "monthly"}
            onChange={(e) => setPayment(e.target.value)}
          />
          Monthly
        </label>
        <label>
          <input
            type="radio"
            name="payment"
            value="yearly"
            checked={payment === "yearly"}
            onChange={(e) => setPayment(e.target.value)}
          />
          Yearly (save 20%)
        </label>
      </fieldset>
    </div>
  );
}
```

### 6. Multiple Form Fields - Object State

```jsx
function RegistrationForm() {
  const [formData, setFormData] = useState({
    firstName: "",
    lastName: "",
    email: "",
    password: "",
    confirmPassword: "",
    phone: "",
    birthDate: "",
    newsletter: false,
  });

  // Generic handler for all inputs
  const handleChange = (e) => {
    const { name, value, type, checked } = e.target;
    setFormData((prev) => ({
      ...prev,
      [name]: type === "checkbox" ? checked : value,
    }));
  };

  const handleSubmit = (e) => {
    e.preventDefault();
    console.log("Form submitted:", formData);
  };

  return (
    <form onSubmit={handleSubmit}>
      <input
        name="firstName"
        value={formData.firstName}
        onChange={handleChange}
        placeholder="First Name"
      />
      <input
        name="lastName"
        value={formData.lastName}
        onChange={handleChange}
        placeholder="Last Name"
      />
      <input
        name="email"
        type="email"
        value={formData.email}
        onChange={handleChange}
        placeholder="Email"
      />
      <input
        name="password"
        type="password"
        value={formData.password}
        onChange={handleChange}
        placeholder="Password"
      />
      <input
        name="confirmPassword"
        type="password"
        value={formData.confirmPassword}
        onChange={handleChange}
        placeholder="Confirm Password"
      />
      <input
        name="phone"
        type="tel"
        value={formData.phone}
        onChange={handleChange}
        placeholder="Phone"
      />
      <input
        name="birthDate"
        type="date"
        value={formData.birthDate}
        onChange={handleChange}
      />
      <label>
        <input
          name="newsletter"
          type="checkbox"
          checked={formData.newsletter}
          onChange={handleChange}
        />
        Subscribe to newsletter
      </label>
      <button type="submit">Register</button>
    </form>
  );
}
```

### 7. Form Validation

```jsx
function ValidatedForm() {
  const [formData, setFormData] = useState({
    email: "",
    password: "",
    confirmPassword: "",
  });
  const [errors, setErrors] = useState({});
  const [touched, setTouched] = useState({});

  const validate = (name, value) => {
    switch (name) {
      case "email":
        if (!value) return "Email is required";
        if (!/^[^\s@]+@[^\s@]+\.[^\s@]+$/.test(value)) {
          return "Invalid email format";
        }
        return "";

      case "password":
        if (!value) return "Password is required";
        if (value.length < 8) return "Password must be at least 8 characters";
        if (!/(?=.*[0-9])/.test(value)) return "Password must contain a number";
        if (!/(?=.*[A-Z])/.test(value))
          return "Password must contain uppercase";
        return "";

      case "confirmPassword":
        if (!value) return "Please confirm your password";
        if (value !== formData.password) return "Passwords do not match";
        return "";

      default:
        return "";
    }
  };

  const handleChange = (e) => {
    const { name, value } = e.target;
    setFormData((prev) => ({ ...prev, [name]: value }));

    // Validate on change if field was touched
    if (touched[name]) {
      setErrors((prev) => ({ ...prev, [name]: validate(name, value) }));
    }
  };

  const handleBlur = (e) => {
    const { name, value } = e.target;
    setTouched((prev) => ({ ...prev, [name]: true }));
    setErrors((prev) => ({ ...prev, [name]: validate(name, value) }));
  };

  const handleSubmit = (e) => {
    e.preventDefault();

    // Validate all fields
    const newErrors = {};
    Object.keys(formData).forEach((key) => {
      const error = validate(key, formData[key]);
      if (error) newErrors[key] = error;
    });

    setErrors(newErrors);
    setTouched({ email: true, password: true, confirmPassword: true });

    if (Object.keys(newErrors).length === 0) {
      console.log("Form is valid!", formData);
      // Submit form
    }
  };

  const isValid =
    Object.keys(errors).every((key) => !errors[key]) &&
    Object.keys(formData).every((key) => formData[key]);

  return (
    <form onSubmit={handleSubmit}>
      <div className={`field ${errors.email && touched.email ? "error" : ""}`}>
        <label>Email</label>
        <input
          name="email"
          type="email"
          value={formData.email}
          onChange={handleChange}
          onBlur={handleBlur}
        />
        {touched.email && errors.email && (
          <span className="error-message">{errors.email}</span>
        )}
      </div>

      <div
        className={`field ${
          errors.password && touched.password ? "error" : ""
        }`}
      >
        <label>Password</label>
        <input
          name="password"
          type="password"
          value={formData.password}
          onChange={handleChange}
          onBlur={handleBlur}
        />
        {touched.password && errors.password && (
          <span className="error-message">{errors.password}</span>
        )}
      </div>

      <div
        className={`field ${
          errors.confirmPassword && touched.confirmPassword ? "error" : ""
        }`}
      >
        <label>Confirm Password</label>
        <input
          name="confirmPassword"
          type="password"
          value={formData.confirmPassword}
          onChange={handleChange}
          onBlur={handleBlur}
        />
        {touched.confirmPassword && errors.confirmPassword && (
          <span className="error-message">{errors.confirmPassword}</span>
        )}
      </div>

      <button type="submit" disabled={!isValid}>
        Submit
      </button>
    </form>
  );
}
```

### 8. Uncontrolled Components (Using Refs)

Sometimes you need uncontrolled components:

```jsx
import { useRef } from "react";

function UncontrolledForm() {
  const nameRef = useRef();
  const emailRef = useRef();
  const fileRef = useRef();

  const handleSubmit = (e) => {
    e.preventDefault();
    console.log({
      name: nameRef.current.value,
      email: emailRef.current.value,
      file: fileRef.current.files[0],
    });
  };

  return (
    <form onSubmit={handleSubmit}>
      {/* Uncontrolled - value not in state */}
      <input ref={nameRef} defaultValue="John" />
      <input ref={emailRef} type="email" defaultValue="" />

      {/* File inputs are always uncontrolled */}
      <input ref={fileRef} type="file" />

      <button type="submit">Submit</button>
    </form>
  );
}
```

---

## 💻 Demo Code

### Complete Form Example

```jsx
import { useState } from "react";

function ContactForm() {
  const [formData, setFormData] = useState({
    name: "",
    email: "",
    subject: "",
    message: "",
    priority: "normal",
    category: "",
    subscribe: false,
    attachments: [],
  });

  const [errors, setErrors] = useState({});
  const [isSubmitting, setIsSubmitting] = useState(false);
  const [submitStatus, setSubmitStatus] = useState(null);

  const categories = [
    { value: "", label: "Select a category" },
    { value: "general", label: "General Inquiry" },
    { value: "support", label: "Technical Support" },
    { value: "sales", label: "Sales Question" },
    { value: "feedback", label: "Feedback" },
  ];

  const validateField = (name, value) => {
    switch (name) {
      case "name":
        return value.length < 2 ? "Name must be at least 2 characters" : "";
      case "email":
        return !/^[^\s@]+@[^\s@]+\.[^\s@]+$/.test(value) ? "Invalid email" : "";
      case "subject":
        return value.length < 5 ? "Subject must be at least 5 characters" : "";
      case "message":
        return value.length < 20
          ? "Message must be at least 20 characters"
          : "";
      case "category":
        return !value ? "Please select a category" : "";
      default:
        return "";
    }
  };

  const handleChange = (e) => {
    const { name, value, type, checked, files } = e.target;

    let newValue;
    if (type === "checkbox") {
      newValue = checked;
    } else if (type === "file") {
      newValue = Array.from(files);
    } else {
      newValue = value;
    }

    setFormData((prev) => ({ ...prev, [name]: newValue }));

    // Clear error when user starts typing
    if (errors[name]) {
      setErrors((prev) => ({ ...prev, [name]: "" }));
    }
  };

  const handleBlur = (e) => {
    const { name, value } = e.target;
    const error = validateField(name, value);
    setErrors((prev) => ({ ...prev, [name]: error }));
  };

  const validateForm = () => {
    const newErrors = {};
    const fieldsToValidate = [
      "name",
      "email",
      "subject",
      "message",
      "category",
    ];

    fieldsToValidate.forEach((field) => {
      const error = validateField(field, formData[field]);
      if (error) newErrors[field] = error;
    });

    setErrors(newErrors);
    return Object.keys(newErrors).length === 0;
  };

  const handleSubmit = async (e) => {
    e.preventDefault();

    if (!validateForm()) return;

    setIsSubmitting(true);
    setSubmitStatus(null);

    try {
      // Simulate API call
      await new Promise((resolve) => setTimeout(resolve, 2000));

      console.log("Form submitted:", formData);
      setSubmitStatus("success");

      // Reset form
      setFormData({
        name: "",
        email: "",
        subject: "",
        message: "",
        priority: "normal",
        category: "",
        subscribe: false,
        attachments: [],
      });
    } catch (error) {
      setSubmitStatus("error");
    } finally {
      setIsSubmitting(false);
    }
  };

  const handleReset = () => {
    setFormData({
      name: "",
      email: "",
      subject: "",
      message: "",
      priority: "normal",
      category: "",
      subscribe: false,
      attachments: [],
    });
    setErrors({});
    setSubmitStatus(null);
  };

  return (
    <div className="contact-form-container">
      <h2>Contact Us</h2>

      {submitStatus === "success" && (
        <div className="alert success">
          Message sent successfully! We'll get back to you soon.
        </div>
      )}

      {submitStatus === "error" && (
        <div className="alert error">
          Something went wrong. Please try again.
        </div>
      )}

      <form onSubmit={handleSubmit}>
        {/* Name */}
        <div className={`form-group ${errors.name ? "has-error" : ""}`}>
          <label htmlFor="name">Name *</label>
          <input
            id="name"
            name="name"
            value={formData.name}
            onChange={handleChange}
            onBlur={handleBlur}
            placeholder="Your name"
            disabled={isSubmitting}
          />
          {errors.name && <span className="error">{errors.name}</span>}
        </div>

        {/* Email */}
        <div className={`form-group ${errors.email ? "has-error" : ""}`}>
          <label htmlFor="email">Email *</label>
          <input
            id="email"
            name="email"
            type="email"
            value={formData.email}
            onChange={handleChange}
            onBlur={handleBlur}
            placeholder="your@email.com"
            disabled={isSubmitting}
          />
          {errors.email && <span className="error">{errors.email}</span>}
        </div>

        {/* Category */}
        <div className={`form-group ${errors.category ? "has-error" : ""}`}>
          <label htmlFor="category">Category *</label>
          <select
            id="category"
            name="category"
            value={formData.category}
            onChange={handleChange}
            onBlur={handleBlur}
            disabled={isSubmitting}
          >
            {categories.map((cat) => (
              <option key={cat.value} value={cat.value}>
                {cat.label}
              </option>
            ))}
          </select>
          {errors.category && <span className="error">{errors.category}</span>}
        </div>

        {/* Priority */}
        <div className="form-group">
          <label>Priority</label>
          <div className="radio-group">
            {["low", "normal", "high", "urgent"].map((priority) => (
              <label key={priority} className="radio-label">
                <input
                  type="radio"
                  name="priority"
                  value={priority}
                  checked={formData.priority === priority}
                  onChange={handleChange}
                  disabled={isSubmitting}
                />
                {priority.charAt(0).toUpperCase() + priority.slice(1)}
              </label>
            ))}
          </div>
        </div>

        {/* Subject */}
        <div className={`form-group ${errors.subject ? "has-error" : ""}`}>
          <label htmlFor="subject">Subject *</label>
          <input
            id="subject"
            name="subject"
            value={formData.subject}
            onChange={handleChange}
            onBlur={handleBlur}
            placeholder="Brief subject"
            disabled={isSubmitting}
          />
          {errors.subject && <span className="error">{errors.subject}</span>}
        </div>

        {/* Message */}
        <div className={`form-group ${errors.message ? "has-error" : ""}`}>
          <label htmlFor="message">Message *</label>
          <textarea
            id="message"
            name="message"
            value={formData.message}
            onChange={handleChange}
            onBlur={handleBlur}
            placeholder="Your message..."
            rows={5}
            disabled={isSubmitting}
          />
          <div className="char-count">
            {formData.message.length}/500 characters
          </div>
          {errors.message && <span className="error">{errors.message}</span>}
        </div>

        {/* File Upload */}
        <div className="form-group">
          <label htmlFor="attachments">Attachments</label>
          <input
            id="attachments"
            name="attachments"
            type="file"
            onChange={handleChange}
            multiple
            accept=".pdf,.doc,.docx,.jpg,.png"
            disabled={isSubmitting}
          />
          {formData.attachments.length > 0 && (
            <ul className="file-list">
              {formData.attachments.map((file, index) => (
                <li key={index}>{file.name}</li>
              ))}
            </ul>
          )}
        </div>

        {/* Subscribe */}
        <div className="form-group">
          <label className="checkbox-label">
            <input
              type="checkbox"
              name="subscribe"
              checked={formData.subscribe}
              onChange={handleChange}
              disabled={isSubmitting}
            />
            Subscribe to our newsletter
          </label>
        </div>

        {/* Buttons */}
        <div className="button-group">
          <button type="submit" className="btn-primary" disabled={isSubmitting}>
            {isSubmitting ? "Sending..." : "Send Message"}
          </button>
          <button
            type="button"
            className="btn-secondary"
            onClick={handleReset}
            disabled={isSubmitting}
          >
            Reset
          </button>
        </div>
      </form>
    </div>
  );
}

export default ContactForm;
```

---

## ✅ Best Practices

### 1. Use Controlled Components

```jsx
// ✅ Controlled - React is source of truth
<input value={value} onChange={e => setValue(e.target.value)} />

// ⚠️ Uncontrolled - DOM is source of truth (use sparingly)
<input ref={inputRef} defaultValue="initial" />
```

### 2. Single onChange Handler for Multiple Fields

```jsx
const handleChange = (e) => {
  const { name, value, type, checked } = e.target;
  setFormData((prev) => ({
    ...prev,
    [name]: type === "checkbox" ? checked : value,
  }));
};
```

### 3. Validate on Blur, Not Just Submit

```jsx
<input
  onBlur={(e) => validateField(e.target.name, e.target.value)}
  onChange={handleChange}
/>
```

### 4. Disable Submit While Processing

```jsx
<button type="submit" disabled={isSubmitting || !isValid}>
  {isSubmitting ? "Submitting..." : "Submit"}
</button>
```

---

## ❌ Common Mistakes

### 1. Missing Value Prop

```jsx
// ❌ Warning: changing from uncontrolled to controlled
<input onChange={(e) => setValue(e.target.value)} />

// ✅ Always include value
<input value={value} onChange={(e) => setValue(e.target.value)} />
```

### 2. Using Value with File Inputs

```jsx
// ❌ Wrong - file inputs can't be controlled
<input type="file" value={file} />

// ✅ Correct - use refs for file inputs
<input type="file" ref={fileRef} onChange={handleFileChange} />
```

### 3. Forgetting preventDefault

```jsx
// ❌ Page reloads on submit
<form onSubmit={() => handleSubmit()}>

// ✅ Prevent default form behavior
<form onSubmit={(e) => { e.preventDefault(); handleSubmit(); }}>
```

---

## 🏋️ Practice Exercises

### Exercise 1: Login Form

Create a login form with email/password validation.

### Exercise 2: Multi-Step Form

Build a wizard with Previous/Next navigation and validation per step.

### Exercise 3: Dynamic Form

Create a form where fields are added/removed dynamically.

---

## 🎤 Interview Q&A

**Q1: What is a controlled component?**

> A controlled component is a form element whose value is controlled by React state. The state is the single source of truth, and onChange updates the state, which then updates the input.

**Q2: Controlled vs Uncontrolled components?**

> Controlled: React state controls the value. Uncontrolled: DOM controls the value (accessed via refs). Controlled is preferred for better predictability and instant validation.

**Q3: How do you handle multiple form fields efficiently?**

> Use an object for state and a single onChange handler with dynamic key: `setFormData({...formData, [e.target.name]: e.target.value})`.

**Q4: Why use preventDefault in form submission?**

> Without it, the form submits traditionally, causing a page reload that loses React state. preventDefault lets React handle the submission without page reload.

**Q5: When would you use uncontrolled components?**

> File inputs (must be uncontrolled), integrating with non-React code, or when you only need the value on submit and don't need instant validation.

---

## 📎 References

- [React Docs - Forms](https://react.dev/reference/react-dom/components/input)
- [React Docs - Controlled Components](https://react.dev/learn/sharing-state-between-components#controlled-and-uncontrolled-components)
- [Form Validation](https://react.dev/reference/react-dom/components/input#controlling-an-input-with-a-state-variable)

---

**Previous Topic**: [← 13. Lists and Keys](./13_Lists_and_Keys.md)

**Next Topic**: [15. React Router Basics →](./15_React_Router_Basics.md)
