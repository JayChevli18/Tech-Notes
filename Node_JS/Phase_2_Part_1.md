# Phase 2 (Part 1): Module System

The Module System is one of the most frequently asked Node.js interview topics because every Node.js application uses modules.

---

# 1. What is a Module?

A module is simply a **separate file containing code** that can be reused in other files.

Example:

### math.js

```js
function add(a, b) {
    return a + b;
}

module.exports = add;
```

### app.js

```js
const add = require("./math");

console.log(add(5, 3));
```

Output:

```text
8
```

---

### Why Use Modules?

Without modules:

```text
Everything in one file
↓
Hard to maintain
↓
Hard to debug
```

With modules:

```text
User Module
Product Module
Order Module
Payment Module
```

Code becomes organized and reusable.

---

### Interview Answer

> A module is a reusable piece of code encapsulated in a separate file. It helps organize applications into smaller, maintainable units.

---

# 2. Types of Modules in Node.js

## 1. Built-in Modules

Provided by Node.js.

Examples:

```js
fs
path
http
crypto
os
events
```

Usage:

```js
const fs = require("fs");
```

---

## 2. Custom Modules

Created by developers.

Example:

```js
const userService = require("./userService");
```

---

## 3. Third-Party Modules

Installed through npm.

Example:

```bash
npm install express
```

```js
const express = require("express");
```

---

# 3. What is CommonJS?

CommonJS is the traditional module system used by Node.js.

It uses:

```js
require()
module.exports
exports
```

---

# 4. `require()`

Used to import modules.

Example:

### math.js

```js
function add(a, b) {
    return a + b;
}

module.exports = add;
```

### app.js

```js
const add = require("./math");

console.log(add(2, 3));
```

Output:

```text
5
```

---

### Interview Answer

> `require()` is used in CommonJS to load and import modules.

---

# 5. `module.exports`

Used to export values from a module.

Example:

### math.js

```js
function add(a, b) {
    return a + b;
}

module.exports = add;
```

---

Import:

```js
const add = require("./math");
```

---

# Exporting an Object

### math.js

```js
function add(a, b) {
    return a + b;
}

function subtract(a, b) {
    return a - b;
}

module.exports = {
    add,
    subtract
};
```

---

### app.js

```js
const math = require("./math");

console.log(math.add(5, 2));
console.log(math.subtract(5, 2));
```

Output:

```text
7
3
```

---

# 6. `exports`

A shortcut to `module.exports`.

Example:

```js
exports.add = (a, b) => a + b;
exports.subtract = (a, b) => a - b;
```

Import:

```js
const math = require("./math");
```

---

# 7. `exports` vs `module.exports`

Very common interview question.

---

## Works

```js
exports.add = add;
exports.subtract = subtract;
```

---

## Works

```js
module.exports = {
    add,
    subtract
};
```

---

## Doesn't Work

```js
exports = {
    add,
    subtract
};
```

Why?

Because:

```js
exports
```

is just a reference to:

```js
module.exports
```

When you reassign `exports`, that connection breaks.

---

### Interview Answer

> `exports` is a reference to `module.exports`. Reassigning `exports` breaks the reference, so `module.exports` should be used when exporting a new object or function.

---

# 8. How `require()` Works Internally

Suppose:

```js
const math = require("./math");
```

Node.js:

### Step 1

Locate file.

```text
./math.js
```

---

### Step 2

Read file contents.

---

### Step 3

Wrap code inside a function.

Internally:

```js
(function(exports, require, module, __filename, __dirname) {

    // Your code

});
```

This is called the **Module Wrapper Function**.

---

### Step 4

Execute code.

---

### Step 5

Return `module.exports`.

---

### Interview Answer

> When a module is required, Node.js loads the file, wraps it in a module wrapper function, executes it, and returns the value of `module.exports`.

---

# 9. Module Wrapper Function

Every Node.js file is wrapped like:

```js
(function(exports, require, module, __filename, __dirname) {

});
```

This is why these variables are available:

```js
exports
require
module
__dirname
__filename
```

without importing them.

---

### Interview Question

### Q: How are `__dirname` and `require()` available globally?

**Answer:**

> Node.js wraps every module inside a module wrapper function and passes these variables as arguments.

---

# 10. Module Caching

Very important interview topic.

Node.js loads a module only once.

Example:

### counter.js

```js
console.log("Counter Loaded");

module.exports = {};
```

---

### app.js

```js
require("./counter");
require("./counter");
require("./counter");
```

Output:

```text
Counter Loaded
```

Only once.

---

### Why?

Because Node.js caches modules after first load.

---

### Benefits

* Better performance
* Avoid repeated file reads
* Shared state

---

### Interview Answer

> Node.js caches modules after the first load. Subsequent calls to `require()` return the cached module instead of reloading it from disk.

---

# 11. Module Cache Example

### counter.js

```js
let count = 0;

count++;

module.exports = count;
```

---

### app.js

```js
const a = require("./counter");
const b = require("./counter");

console.log(a);
console.log(b);
```

Output:

```text
1
1
```

Not:

```text
1
2
```

because same cached module is returned.

---

# 12. Interview Questions

### Q: What is a Module?

> A reusable piece of code contained in a separate file.

---

### Q: Why use Modules?

> To improve code organization, maintainability, and reusability.

---

### Q: What is CommonJS?

> The default Node.js module system that uses `require()` and `module.exports`.

---

### Q: Difference between `exports` and `module.exports`?

> `exports` is a reference to `module.exports`. Reassigning `exports` breaks the link.

---

### Q: What is Module Caching?

> Node.js stores loaded modules in memory and reuses them for future `require()` calls.

---

### Q: What does `require()` return?

> It returns the value assigned to `module.exports`.

---

# Quick Revision Notes

### CommonJS

Import:

```js
const module = require("./module");
```

Export:

```js
module.exports = value;
```

---

### `exports`

```js
exports.add = add;
```

---

### Module Cache

```js
require("./module");
require("./module");
```

Loaded only once.

---

### Wrapper Function

```js
(function(exports, require, module, __filename, __dirname) {})
```

---

## Phase 2 Progress

✅ What is a Module?
✅ Types of Modules
✅ CommonJS
✅ require()
✅ module.exports
✅ exports
✅ Module Wrapper Function
✅ Module Caching

### Next: ES Modules (Modern JavaScript Module System)

We'll cover:

* `import`
* `export`
* Named Exports
* Default Exports
* CommonJS vs ES Modules
* When to use each
* Interview questions and tricky scenarios.
