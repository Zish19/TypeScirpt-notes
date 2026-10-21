# TypeScript Notes

A practical, beginner-to-intermediate guide to TypeScript: core ideas, types, classes, generics, async code, tooling and common pitfalls.

## Table of Contents

1. [Introduction](#1-introduction)
2. [Setup and Tooling](#2-setup-and-tooling)
3. [Type Basics and Inference](#3-type-basics-and-inference)
4. [Special Types](#4-special-types)
5. [Arrays, Tuples, Enums and Index Signatures](#5-arrays-tuples-enums-and-index-signatures)
6. [Functions](#6-functions)
7. [Casting (Type Assertions)](#7-casting-type-assertions)
8. [Classes](#8-classes)
9. [Generics and Utility Types](#9-generics-and-utility-types)
10. [Null and Undefined](#10-null-and-undefined)
11. [Promises and Async/Await](#11-promises-and-asyncawait)
12. [Error Handling](#12-error-handling)
13. [TypeScript in Real Projects](#13-typescript-in-real-projects)
14. [Migrating from JavaScript](#14-migrating-from-javascript)
15. [Common Pitfalls](#15-common-pitfalls)

---

## 1. Introduction

### What is TypeScript?

TypeScript is a **syntactic superset of JavaScript**: it shares JavaScript's base syntax and adds a type system on top. Any valid JavaScript is also valid TypeScript.

### Why use TypeScript?

- It performs **compile-time type checking**: types are verified *before* the code runs, not while it runs.
- It catches whole classes of bugs early, and gives better autocomplete and safer refactoring.

### The TypeScript compiler

TypeScript is compiled (`tsc`) into JavaScript, so the output runs anywhere JavaScript runs: browsers, Node.js, and more.

> **Note:** With a `tsconfig.json` that sets `rootDir: "src"` and `outDir: "build"`, you place `.ts` files in `src/` and the compiled `.js` appears in `build/`.

---

## 2. Setup and Tooling

**Prerequisites:** a recent Node.js LTS (v18+ recommended) and npm. Verify with:

```bash
node -v
npm -v
```

You write TypeScript (`.ts`) during development and compile it to JavaScript (`.js`) for Node.js to run in production.

```bash
npm install --save-dev typescript @types/node
npx tsc --init
```

What these do:

- `typescript` adds the compiler (`tsc`).
- `@types/node` provides Node.js type definitions.
- `npx tsc --init` creates a `tsconfig.json`.

### Option highlights

| Option | Purpose |
| --- | --- |
| `rootDir` / `outDir` | Keep source (`src`) separate from build output (`dist`) |
| `strict` | Enables the safest set of type checks |
| `esModuleInterop` | Smoother interop between CommonJS and ES modules |
| `sourceMap` | Generates maps so you can debug compiled code |

**CommonJS vs ESM:** these notes use `module: "commonjs"`. If you use ESM (`"type": "module"` in `package.json`), set `module` to `nodenext` or `node16` and use `import`/`export` consistently.

> **Warning:** Use `ts-node` and `nodemon` for development only. For production, compile with `tsc` and run Node on the JS output.

> **Tip:** Most IDEs (including VS Code) support debugging TypeScript with breakpoints when source maps are enabled.

---

## 3. Type Basics and Inference

> **Best practice:** Use explicit types for function parameters and return types. It makes code more maintainable and self-documenting.

**Type inference** works best when variables are initialized at declaration:

```ts
let count = 5;        // inferred as number
let name = "Dylan";   // inferred as string
```

A variable declared without an initial value and without an annotation falls back to `any` (and `noImplicitAny`, part of `strict`, will flag that as an error).

> **Watch out:** Inference is convenient, but explicit types help in larger codebases and public APIs.

---

## 4. Special Types

These types are part of TypeScript's type system and help make code more type-safe and self-documenting.

### `any`

Opts out of type checking entirely.

When it is acceptable:

- Migrating JavaScript code to TypeScript
- Dynamic content whose type is genuinely unknown
- Opting out of checking for one specific case

`any` gets you past errors, but TypeScript can no longer provide type safety, and tools that rely on type data (such as autocomplete) stop working. Remember: it should be avoided at "any" cost.

### `unknown`

The type-safe counterpart of `any`.

- An `unknown` value **must be type-checked before use**.
- You can't access properties on it without narrowing or an assertion.
- You can't call or construct values of type `unknown`.

Use it for:

- Data from external sources (APIs, user input)
- Keeping type safety while still allowing flexibility
- Migrating from JavaScript in a type-safe way

**Narrowing with type guards:**

```ts
function print(value: unknown) {
  if (typeof value === "string") {
    console.log(value.toUpperCase()); // value is string here
  }
}
```

### `never`

Represents values that never occur.

Common use cases:

- Functions that never return (they always throw or loop forever)
- Type guards that can never match
- Exhaustiveness checking in discriminated unions and `switch` statements
- Generic types where certain cases are impossible

```ts
function fail(message: string): never {
  throw new Error(message);
}
```

### `undefined` and `null`

- `undefined`: a variable has been declared but not assigned a value.
- `null`: an explicit assignment meaning "no value / no object".
- Each has its own type (`undefined` and `null`).
- With `strictNullChecks` enabled, you must handle them explicitly.

These are most useful with `strictNullChecks` on, which ensures `null` and `undefined` are only assignable to themselves (and `any`/`unknown`). Enable it in `tsconfig.json`:

```json
{
  "compilerOptions": {
    "strictNullChecks": true
  }
}
```

---

## 5. Arrays, Tuples, Enums and Index Signatures

### Typed arrays and tuples

A **tuple** is an array with a fixed length and a fixed type per position. **Order matters**: putting values in the wrong order is a compile-time error.

```ts
let ourTuple: [number, boolean, string];
ourTuple = [5, false, "Coding God was here"]; // OK
ourTuple = [false, "Coding God was here", 5]; // Error
```

### Readonly tuples

If you have used React, you have almost certainly used tuples. `useState` returns a tuple of a value and a setter function:

```ts
const [firstName, setFirstName] = useState("Dylan");
```

Because of the tuple's structure, TypeScript knows the first item is a `string` and the second is a function.

### Named tuples

Named tuples give context to what each index represents:

```ts
const graph: [x: number, y: number] = [55.2, 41.3];
```

### Index signatures

Describe objects whose keys aren't known ahead of time:

```ts
const scores: { [key: string]: number } = {};
```

They can also be expressed with the `Record` utility type:

```ts
const scores: Record<string, number> = {};
```

> **Note:** In JavaScript all object keys are stored as strings, even numeric ones. TypeScript still distinguishes arrays from objects to help catch logic errors.

> **Important:** When combining explicit properties with an index signature, each explicit property's type must be assignable to the index signature's value type.

### Enums

String enums are the safer, more readable option:

```ts
enum Direction {
  Up = "UP",
  Down = "DOWN",
}
```

Technically you can mix string and numeric members, but doing so is not recommended.

---

## 6. Functions

### Return types

If you don't declare a return type, TypeScript infers it from the values returned.

```ts
function add(a: number, b: number): number {
  return a + b;
}
```

### Parameters

If you don't declare a parameter type, TypeScript uses `any` unless more type information is available (for example from default values or a type alias). Under `strict`, an untyped parameter is an error.

---

## 7. Casting (Type Assertions)

### Casting with `as`

Casting **does not change the data**, only how TypeScript views it:

```ts
let x: unknown = 4;
console.log((x as string).length); // undefined: x is still a number
```

TypeScript still blocks casts that look clearly wrong:

```ts
console.log((4 as string).length);
// Error: Conversion of type 'number' to type 'string' may be a mistake...
// If this was intentional, convert the expression to 'unknown' first.
```

To force it, go through `unknown`: `(4 as unknown as string)`. Use this sparingly.

### Casting with `<>`

```ts
const value = <string>someValue;
```

This syntax does **not** work in TSX files (such as React components), so prefer `as`.

---

## 8. Classes

### Member visibility

| Modifier | Access |
| --- | --- |
| `public` (default) | From anywhere |
| `private` | Only inside the class |
| `protected` | Inside the class and any subclasses |

The `this` keyword in a class usually refers to the instance of the class.

> **Note:** TypeScript's `private` is enforced at compile time. Modern TypeScript also supports native JavaScript private fields (`#field`); both work.

### Implementing interfaces

A class can implement several interfaces, separated by commas:

```ts
class Rectangle implements Shape, Colored {
  // ...
}
```

### `override`

The `override` keyword is optional by default. It only helps prevent accidentally "overriding" a method that doesn't exist in the parent. Turn on `noImplicitOverride` to require it.

### Abstract classes

Abstract classes can't be instantiated directly because they don't have all their members implemented. Subclasses must complete them.

### `readonly`

`readonly` is enforced at compile time. Because everything compiles down to JavaScript, the property could still be overwritten at runtime.

---

## 9. Generics and Utility Types

Generics let you write reusable code that works across types.

### Functions

TypeScript can infer the generic parameter from the function arguments:

```ts
function identity<T>(value: T): T {
  return value;
}
identity("hello"); // T inferred as string
```

### Classes

Inference also works when the type parameter is used in a constructor parameter.

### Type aliases and interfaces

```ts
type Wrapped<T> = { value: T };

interface Wrapped2<T> {
  value: T;
}
```

### Constraints and defaults

`extends` constrains a generic, and can be combined with a default value:

```ts
function logLength<T extends { length: number }>(item: T) {
  console.log(item.length);
}

type Box<T extends object = {}> = { content: T };
```

### `Record`

`Record<string, number>` is equivalent to `{ [key: string]: number }`.

---

## 10. Null and Undefined

By default, `null`/`undefined` checking is **disabled**; enable it by setting `strictNullChecks` to `true`. The rest of this section assumes it is enabled.

- Values must be set unless `undefined` is explicitly part of the type.

```ts
let name: string | undefined;
```

### Non-null assertion

The `!` operator tells TypeScript a value is not `null`/`undefined`. Just like casting, this can be unsafe and should be used with care.

```ts
const el = document.getElementById("app")!;
```

---

## 11. Promises and Async/Await

### Promise types

- `Promise<T>`: `T` is the type of the resolved value
- `Promise<void>`: resolves with no value
- `Promise<never>`: never resolves (rare)

### Promise state flow

```
pending → fulfilled (with value: T)   // success
pending → rejected  (with reason: any) // error
```

TypeScript tracks these states through the type system, helping you handle both success and failure.

### Async function return types

All `async` functions return a Promise, so the declared return type must be a Promise:

```ts
async function getString(): string {}           // Error: must return Promise
async function getString(): Promise<string> {}  // Correct
```

### Benefits of async/await

- **Readability:** sequential code that is easier to follow
- **Error handling:** `try/catch` works for sync and async errors
- **Debugging:** more synchronous-looking stack traces
- **Type safety:** full inference and checking

### Promise combination methods

| Method | Behavior |
| --- | --- |
| `Promise.all()` | Waits for all promises to resolve |
| `Promise.race()` | Returns the first settled promise |
| `Promise.allSettled()` | Waits for all to settle (success or failure) |
| `Promise.any()` | Returns the first fulfilled promise |

> **Warning:** When `Promise.all` receives promises of different types, TypeScript infers an array of the union of those types. For more precise typing, use a tuple, type assertions, or define the expected structure.

---

## 12. Error Handling

### Strategies

- **Try/catch blocks:** for async/await code
- **Error boundaries:** for React components
- **Result types:** a functional approach with success/failure values
- **Error subclassing:** for domain-specific errors

### Patterns for robust code

- Error boundaries in React for component-level handling
- Result objects instead of exceptions for expected cases
- A global handler for uncaught exceptions
- Error logging to capture and report problems

### `unknown` in `catch`

With `strict` (specifically `useUnknownInCatchVariables`, available from TypeScript 4.4), caught errors are typed `unknown`. Always narrow before accessing properties:

```ts
try {
  // ...
} catch (error) {
  if (error instanceof Error) {
    console.log(error.message);
  }
}
```

---

## 13. TypeScript in Real Projects

### With Node.js and Express

TypeScript adds:

- Typed `Request`, `Response` and `NextFunction` for handlers
- A `User` interface to guarantee the shape of data
- Safer refactoring and better autocomplete for route params and bodies

> **Note:** A `test` script is optional and assumes Jest is set up. Omit it otherwise.

### With a database (TypeORM example)

Install packages (use `pg` for PostgreSQL):

```bash
npm install typeorm reflect-metadata pg
```

Enable decorators in `tsconfig.json`:

```json
{
  "compilerOptions": {
    "experimentalDecorators": true,
    "emitDecoratorMetadata": true
  }
}
```

Import `reflect-metadata` **once** at app startup. `emitDecoratorMetadata` emits type metadata for decorators, which libraries like TypeORM and class-validator rely on.

### With React and Vite

Add Vite's ambient types to avoid missing definitions:

```ts
// src/vite-env.d.ts
/// <reference types="vite/client" />
```

Or in `tsconfig.json`:

```json
{
  "compilerOptions": {
    "types": ["vite/client"]
  }
}
```

**About `React.FC`:** prefer directly typed function components. `React.FC` is optional and isn't required.

**Path aliases (optional):**

```json
{
  "compilerOptions": {
    "baseUrl": ".",
    "paths": {
      "@/*": ["src/*"]
    }
  }
}
```

Only configure this if your tooling (Vite, `tsconfig-paths`, etc.) supports path aliases.

### Using untyped npm packages

If a package ships without types, editors such as VS Code will often suggest installing the matching `@types/...` package.

### Namespaces vs modules

Namespaces are still fully supported, but modern applications use ES modules (`import`/`export`) for better modularity and tree-shaking. Understanding namespaces is still useful for legacy codebases and some library work.

---

## 14. Migrating from JavaScript

### JSDoc type checking

JSDoc plus TypeScript lets you type-check `.js` files without converting them to `.ts`. This is great for gradual migration.

```js
// @ts-check

/**
 * @param {number} a
 * @param {number} b
 * @returns {number}
 */
function add(a, b) {
  return a + b;
}
```

Things that commonly go wrong:

- Missing `// @ts-check`: checking won't happen
- Incorrect JSDoc syntax: one typo can disable checking
- Type conflicts between sources
- Inference gaps where TypeScript can't work out a type
- Performance cost on large JS files with complex types

> **Remember:** For new projects or complete migrations, `.ts` files give the best TypeScript experience.

> **Recommendation:** For large projects, migrate gradually to minimize disruption and keep the work manageable.

---

## 15. Common Pitfalls

### Not handling promise rejections

```ts
// Bad: unhandled promise rejection
fetchData();

// Good
fetchData().catch(console.error);
```

### Catching without narrowing

```ts
// Bad: error is 'unknown'
try { /* ... */ } catch (error) {
  console.log(error.message); // Error: Property 'message' does not exist on type 'unknown'
}

// Good
try { /* ... */ } catch (error) {
  if (error instanceof Error) {
    console.log(error.message);
  }
}
```

### Swallowing errors

```ts
// Bad: error silently ignored
function saveData(data: Data) {
  try {
    database.save(data);
  } catch {
    // Ignore
  }
}

// Better: log and notify
function saveData(data: Data) {
  try {
    database.save(data);
  } catch (error) {
    console.error("Failed to save data:", error);
    showError("Failed to save data. Please try again.");
  }
}
```

---

Found a mistake or want to add something? Pull requests are welcome.
