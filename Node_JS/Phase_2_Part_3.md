# Phase 2 - Part 3: Module Resolution & Circular Dependencies

This topic is asked very frequently in Node.js interviews, especially for developers with **2+ years experience**.

---

# 1. What is Module Resolution?

Module resolution is the process Node.js uses to find a module when you write:

```js
const express = require("express");
```

or

```js
import express from "express";
```

### Interview Answer

> Module resolution is the process Node.js uses to locate and load modules based on the path or package name provided in require() or import statements.

---

# 2. Relative vs Absolute Imports

## Relative Import

Starts with:

```js
./
../
```

Example:

```js
const user = require("./user");
const helper = require("../utils/helper");
```

Node looks relative to the current file.

---

## Package Import

Example:

```js
const express = require("express");
```

Node searches inside:

```text
node_modules/
```

---

### Interview Question

**Q: What is the difference between relative and package imports?**

**Answer:**

> Relative imports use file paths like ./ or ../, while package imports refer to installed packages that Node.js resolves from node_modules.

---

# 3. How require() Resolves a Module

Suppose:

```js
require("./math");
```

Node tries:

### Step 1

```text
./math.js
```

If found → load it.

---

### Step 2

```text
./math.json
```

If found → load it.

---

### Step 3

```text
./math.node
```

Native addon.

---

### Step 4

If math is a folder:

```text
math/
```

Node checks:

```text
math/package.json
```

and reads:

```json
{
  "main": "app.js"
}
```

Then loads:

```text
math/app.js
```

---

### Step 5

If no package.json:

Looks for:

```text
math/index.js
```

---

### Interview Answer

> When resolving a module, Node.js checks files, directories, package.json entries, and index files in a specific order until it finds a matching module.

---

# 4. How Node Finds Packages in node_modules

Example:

```js
require("express");
```

Assume:

```text
project/
 ├── src/
 │    └── app.js
 └── node_modules/
```

Node searches:

```text
src/node_modules/express
```

Not found?

Move up:

```text
project/node_modules/express
```

Not found?

Move up again:

```text
parent/node_modules/express
```

Continue until root.

---

## Visualization

```text
Current Folder
      ↓
node_modules
      ↓
Parent Folder
      ↓
node_modules
      ↓
Parent Folder
      ↓
node_modules
```

---

### Interview Answer

> Node.js searches for packages by traversing upward through parent directories looking for a node_modules folder containing the requested package.

---

# 5. package.json Resolution

When importing a package:

```js
require("lodash");
```

Node opens:

```text
node_modules/lodash/package.json
```

Looks for:

```json
{
  "main": "index.js"
}
```

Loads:

```text
index.js
```

---

## Modern ESM

May use:

```json
{
  "exports": "./dist/index.js"
}
```

The `exports` field controls what consumers can import.

---

### Interview Question

**Q: What is the purpose of the main field?**

**Answer:**

> The main field specifies the entry point of a package when it is imported.

---

# 6. index.js Fallback

Folder structure:

```text
utils/
 └── index.js
```

Import:

```js
require("./utils");
```

Node automatically loads:

```text
utils/index.js
```

---

### Why Useful?

Allows cleaner imports.

Instead of:

```js
require("./utils/index.js");
```

Use:

```js
require("./utils");
```

---

# 7. What are Circular Dependencies?

A circular dependency occurs when two modules depend on each other.

---

Example:

### user.js

```js
const auth = require("./auth");

module.exports = {
  getUser() {
    return auth.validate();
  }
};
```

---

### auth.js

```js
const user = require("./user");

module.exports = {
  validate() {
    return user.getUser();
  }
};
```

---

Dependency graph:

```text
user.js
   ↓
auth.js
   ↓
user.js
```

Circular!

---

# 8. Why Circular Dependencies are Dangerous

Node loads modules only once.

During loading:

```text
Load user.js
    ↓
Load auth.js
    ↓
Needs user.js again
```

But user.js isn't finished loading yet.

Node returns a partially initialized module.

Result:

```js
undefined
```

or

```js
TypeError
```

---

### Example Error

```js
TypeError: user.getUser is not a function
```

---

### Interview Answer

> Circular dependencies occur when modules depend on each other. They can lead to partially initialized exports and runtime errors.

---

# 9. Detecting Circular Dependencies

Common symptoms:

### Undefined exports

```js
console.log(user);
```

Output:

```js
{}
```

---

### Unexpected runtime errors

```js
Cannot read property ...
```

---

### Tools

Popular tool:

```bash
npm install madge
```

Run:

```bash
madge --circular src
```

Finds circular dependencies.

---

# 10. How to Avoid Circular Dependencies

## Method 1: Extract Shared Logic

Bad:

```text
user ↔ auth
```

Create:

```text
user
  ↓
common
  ↑
auth
```

Much cleaner.

---

## Method 2: Dependency Injection

Instead of importing directly:

```js
auth(user);
```

Pass dependency as argument.

---

## Method 3: Restructure Modules

Split large files.

Example:

```text
services/
controllers/
repositories/
```

Keep responsibilities separate.

---

# Real Interview Scenario

### Question

How does Node.js resolve this?

```js
require("./utils");
```

### Answer

> Node first checks for utils.js, then utils.json, then utils.node. If utils is a directory, it checks package.json for a main entry and finally falls back to index.js.

---

### Question

What is a circular dependency?

### Answer

> A circular dependency occurs when two or more modules import each other directly or indirectly, causing partially initialized modules and potential runtime issues.

---

### Question

How does Node find packages?

### Answer

> Node searches for the requested package inside node_modules, moving upward through parent directories until it finds the package or reaches the filesystem root.

---

# Quick Revision Notes

### Module Resolution

```text
require("./math")
```

Search order:

```text
math.js
math.json
math.node
math/package.json
math/index.js
```

---

### Package Resolution

```text
node_modules
      ↑
parent/node_modules
      ↑
root
```

---

### package.json

```json
{
  "main": "index.js"
}
```

Defines package entry point.

---

### Circular Dependency

```text
A → B → A
```

Problem:

```text
Partially loaded modules
Undefined exports
Runtime errors
```

---

### Avoid Circular Dependencies

```text
Extract common code
Dependency injection
Better architecture
```

---

## Next Topic (Phase 2 - Part 4)

**Built-in Core Modules**

We'll cover:

* `fs`
* `path`
* `os`
* `events`
* `crypto`
* `stream`
* `buffer`
* `util`
* `timers`
* Most asked interview questions on core modules.
