# 02 - Setup and Configuration

## 📖 Setting Up TypeScript

There are multiple ways to set up TypeScript depending on your project needs.

---

## 🎯 Installation Methods

### 1. Global Installation

```bash
# Install TypeScript globally
npm install -g typescript

# Verify installation
tsc --version
# Output: Version 5.x.x

# Compile a file
tsc hello.ts
```

### 2. Project-Local Installation (Recommended)

```bash
# Initialize npm project
npm init -y

# Install TypeScript as dev dependency
npm install --save-dev typescript

# Create tsconfig.json
npx tsc --init

# Run compiler
npx tsc
```

### 3. Using Vite (Modern Projects)

```bash
# Create new project with TypeScript
npm create vite@latest my-app -- --template vanilla-ts

# Or for React
npm create vite@latest my-app -- --template react-ts

cd my-app
npm install
npm run dev
```

---

## 📚 tsconfig.json

The `tsconfig.json` file configures how TypeScript compiles your code.

### Basic Configuration

```json
{
  "compilerOptions": {
    "target": "ES2022",
    "module": "ESNext",
    "moduleResolution": "bundler",
    "strict": true,
    "esModuleInterop": true,
    "skipLibCheck": true,
    "forceConsistentCasingInFileNames": true,
    "outDir": "./dist",
    "rootDir": "./src"
  },
  "include": ["src/**/*"],
  "exclude": ["node_modules"]
}
```

### Key Options Explained

```json
{
  "compilerOptions": {
    // Target JavaScript version
    "target": "ES2022", // ES5, ES6, ES2020, ES2022, ESNext

    // Module system
    "module": "ESNext", // CommonJS, ESNext, ES2020
    "moduleResolution": "bundler", // node, bundler, node16

    // Strict type checking (highly recommended)
    "strict": true,

    // Individual strict flags (all enabled by "strict": true)
    "noImplicitAny": true, // Error on implicit any
    "strictNullChecks": true, // null/undefined are distinct types
    "strictFunctionTypes": true, // Strict function type checking
    "strictBindCallApply": true, // Strict bind/call/apply
    "strictPropertyInitialization": true, // Class property init required

    // Additional checks
    "noUnusedLocals": true, // Error on unused local variables
    "noUnusedParameters": true, // Error on unused parameters
    "noImplicitReturns": true, // Error when not all paths return

    // Output
    "outDir": "./dist", // Output directory
    "rootDir": "./src", // Root source directory
    "declaration": true, // Generate .d.ts files
    "sourceMap": true, // Generate source maps

    // Interop
    "esModuleInterop": true, // CommonJS/ES module interop
    "allowSyntheticDefaultImports": true, // Allow default imports

    // Other
    "skipLibCheck": true, // Skip type checking of .d.ts files
    "forceConsistentCasingInFileNames": true // Case-sensitive imports
  }
}
```

---

## 💻 Project Setups

### 1. Node.js Project

```bash
# Create project
mkdir my-node-app && cd my-node-app
npm init -y

# Install dependencies
npm install --save-dev typescript @types/node ts-node

# Create tsconfig.json
npx tsc --init
```

**tsconfig.json for Node.js:**

```json
{
  "compilerOptions": {
    "target": "ES2022",
    "module": "NodeNext",
    "moduleResolution": "NodeNext",
    "strict": true,
    "esModuleInterop": true,
    "skipLibCheck": true,
    "outDir": "./dist",
    "rootDir": "./src"
  },
  "include": ["src/**/*"]
}
```

**package.json scripts:**

```json
{
  "scripts": {
    "build": "tsc",
    "start": "node dist/index.js",
    "dev": "ts-node src/index.ts",
    "watch": "tsc --watch"
  }
}
```

### 2. React Project (Vite)

```bash
npm create vite@latest my-react-app -- --template react-ts
cd my-react-app
npm install
npm run dev
```

**tsconfig.json for React:**

```json
{
  "compilerOptions": {
    "target": "ES2020",
    "useDefineForClassFields": true,
    "lib": ["ES2020", "DOM", "DOM.Iterable"],
    "module": "ESNext",
    "skipLibCheck": true,
    "moduleResolution": "bundler",
    "allowImportingTsExtensions": true,
    "resolveJsonModule": true,
    "isolatedModules": true,
    "noEmit": true,
    "jsx": "react-jsx",
    "strict": true,
    "noUnusedLocals": true,
    "noUnusedParameters": true,
    "noFallthroughCasesInSwitch": true
  },
  "include": ["src"],
  "references": [{ "path": "./tsconfig.node.json" }]
}
```

### 3. Simple Vanilla TypeScript

```bash
mkdir my-ts-project && cd my-ts-project
npm init -y
npm install --save-dev typescript
```

**Project structure:**

```
my-ts-project/
├── src/
│   └── index.ts
├── dist/
├── package.json
└── tsconfig.json
```

**tsconfig.json:**

```json
{
  "compilerOptions": {
    "target": "ES2022",
    "module": "ESNext",
    "strict": true,
    "outDir": "./dist",
    "rootDir": "./src"
  },
  "include": ["src/**/*"]
}
```

---

## 🔧 VS Code Setup

### Essential Extensions

1. **TypeScript** - Built-in support
2. **ESLint** - Linting
3. **Prettier** - Code formatting
4. **Error Lens** - Inline error display

### VS Code Settings

```json
// .vscode/settings.json
{
  "typescript.tsdk": "node_modules/typescript/lib",
  "editor.formatOnSave": true,
  "editor.defaultFormatter": "esbenp.prettier-vscode",
  "editor.codeActionsOnSave": {
    "source.fixAll.eslint": "explicit"
  },
  "typescript.preferences.importModuleSpecifier": "relative"
}
```

### ESLint Configuration

```bash
npm install --save-dev eslint @typescript-eslint/parser @typescript-eslint/eslint-plugin
```

```javascript
// eslint.config.js (ESLint 9 flat config)
import eslint from "@eslint/js";
import tseslint from "typescript-eslint";

export default tseslint.config(
  eslint.configs.recommended,
  ...tseslint.configs.recommended
);
```

---

## 📊 Compiler Commands

### Basic Commands

```bash
# Compile single file
tsc hello.ts

# Compile with tsconfig.json
tsc

# Watch mode
tsc --watch

# Compile specific config
tsc --project tsconfig.prod.json

# Show config without compiling
tsc --showConfig

# Check types without emitting
tsc --noEmit
```

### Running TypeScript

```bash
# Using ts-node (development)
npx ts-node src/index.ts

# Using tsx (faster, recommended)
npx tsx src/index.ts

# Using node with loader (Node.js 20+)
node --import tsx src/index.ts

# Compile and run
tsc && node dist/index.js
```

---

## 🧩 Type Declaration Files

### What are .d.ts files?

Type declaration files provide type information for JavaScript libraries.

```typescript
// types.d.ts
declare module "my-library" {
  export function doSomething(value: string): number;
}
```

### Installing Type Definitions

```bash
# Many packages have built-in types
npm install axios  # Has types included

# Some need @types packages
npm install --save-dev @types/lodash
npm install --save-dev @types/node
npm install --save-dev @types/express
```

### DefinitelyTyped

Most popular type definitions are in the DefinitelyTyped repository:

```bash
# Pattern: @types/package-name
npm install --save-dev @types/react
npm install --save-dev @types/react-dom
```

---

## ⚠️ Common Setup Issues

### 1. Module Resolution Errors

```typescript
// Error: Cannot find module './utils'
import { helper } from "./utils";
```

**Fix:** Check `moduleResolution` in tsconfig.json:

```json
{
  "compilerOptions": {
    "moduleResolution": "bundler" // or "node", "node16"
  }
}
```

### 2. Missing Types for Package

```
Could not find a declaration file for module 'some-package'
```

**Fix:**

```bash
# Try installing types
npm install --save-dev @types/some-package

# Or create declaration
// src/types/some-package.d.ts
declare module 'some-package';
```

### 3. Strict Mode Errors

```typescript
// Error: Parameter 'x' implicitly has an 'any' type
function process(x) {
  return x * 2;
}
```

**Fix:** Add type annotation:

```typescript
function process(x: number): number {
  return x * 2;
}
```

---

## 🏋️ Practice Exercises

### Exercise 1: Set Up a Node.js Project

Create a new TypeScript Node.js project with:

- tsconfig.json with strict mode
- src/index.ts that logs "Hello, TypeScript!"
- Build and run scripts in package.json

### Exercise 2: Configure Strict Mode

Start with `"strict": false` and enable options one by one to understand what each does.

---

## ❓ Interview Questions

**Q1: What is tsconfig.json?**

> tsconfig.json is the TypeScript configuration file that specifies compiler options, file includes/excludes, and project settings for the TypeScript compiler.

**Q2: What does "strict": true enable?**

> It enables all strict type-checking options: noImplicitAny, strictNullChecks, strictFunctionTypes, strictBindCallApply, strictPropertyInitialization, noImplicitThis, and alwaysStrict.

**Q3: What are .d.ts files?**

> Declaration files that provide type information for JavaScript libraries. They describe the shape of existing JavaScript code without implementing it.

**Q4: How do you run TypeScript code?**

> Compile with tsc then run with node, or use ts-node/tsx to run directly. Build tools like Vite handle compilation automatically.

**Q5: What's the difference between "module" and "moduleResolution"?**

> "module" specifies the output module format (CommonJS, ESNext). "moduleResolution" specifies how TypeScript resolves import paths (node, bundler, node16).

---

## 🔑 Key Takeaways

> ### 💡 Summary
>
> - **tsconfig.json** configures the TypeScript compiler
> - **Enable strict mode** for maximum type safety
> - **Use project-local installation** over global
> - **Install @types packages** for library type definitions
> - **VS Code** provides excellent TypeScript support
> - **tsx or ts-node** for running TS directly

---

**Next:** [03 - Basic Types](./03_Basic_Types.md) - Learn the fundamental TypeScript types!
