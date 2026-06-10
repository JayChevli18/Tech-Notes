# Phase 2 (Part 2): ES Modules (ESM)

ES Modules are the **modern JavaScript module system** introduced in ES6.

Today, most modern Node.js, React, Next.js, and frontend applications use ES Modules.

---

# 1. What are ES Modules?

Instead of:

```js
const math = require("./math");
```

ES Modules use:

```js
import math from "./math.js";
```

Instead of:

```js
module.exports = add;
```

ES Modules use:

```js
export default add;
```

---

### Interview Answer

> ES Modules (ESM) are the standard JavaScript module system introduced in ES6. They use `import` and `export` keywords for sharing code between files.

---

# 2. Enabling ES Modules in Node.js

Node.js uses CommonJS by default.

To use ES Modules:

### package.json

```json
{
  "type": "module"
}
```

Now Node.js will treat `.js` files as ES Modules.

---

# 3. Named Exports

Export multiple values individually.

### math.js

```js
export const add = (a, b) => a + b;

export const subtract = (a, b) => a - b;
```

---

### app.js

```js
import { add, subtract } from "./math.js";

console.log(add(5, 3));
console.log(subtract(5, 3));
```

Output:

```text
8
2
```

---

## Why Curly Braces?

Because you're importing specific named exports.

```js
import { add } from "./math.js";
```

Node looks for:

```js
export const add = ...
```

---

# 4. Importing Multiple Named Exports

```js
import { add, subtract } from "./math.js";
```

---

# 5. Alias Imports

Sometimes names conflict.

Example:

```js
import { add as sum } from "./math.js";

console.log(sum(5, 3));
```

Output:

```text
8
```

---

### Interview Question

### Q: Why use aliases?

**Answer:**

> Aliases help avoid naming conflicts and improve readability by renaming imported values.

---

# 6. Import Everything

### math.js

```js
export const add = (a, b) => a + b;
export const subtract = (a, b) => a - b;
```

---

### app.js

```js
import * as math from "./math.js";

console.log(math.add(5, 3));
```

Output:

```text
8
```

---

# 7. Default Export

A module can have one default export.

### math.js

```js
const add = (a, b) => a + b;

export default add;
```

---

### app.js

```js
import add from "./math.js";

console.log(add(5, 3));
```

Output:

```text
8
```

---

### Important

No curly braces.

```js
import add from "./math.js";
```

Not:

```js
import { add } from "./math.js";
```

---

# 8. Default Export with Custom Name

Example:

### math.js

```js
export default function add(a, b) {
    return a + b;
}
```

---

### app.js

```js
import myFunction from "./math.js";
```

This works.

Why?

Because default exports can be renamed during import.

---

### Interview Question

### Q: Can default exports be renamed while importing?

**Answer:**

> Yes. Default exports can be imported with any name because they don't depend on the original exported identifier.

---

# 9. Named + Default Export Together

### math.js

```js
export const subtract = (a, b) => a - b;

export default function add(a, b) {
    return a + b;
}
```

---

### app.js

```js
import add, { subtract } from "./math.js";

console.log(add(5, 3));
console.log(subtract(5, 3));
```

Output:

```text
8
2
```

---

# 10. Exporting Later

Instead of:

```js
export const add = (a, b) => a + b;
```

You can do:

```js
const add = (a, b) => a + b;

export { add };
```

---

Multiple exports:

```js
const add = (a, b) => a + b;
const subtract = (a, b) => a - b;

export {
    add,
    subtract
};
```

---

# 11. Re-exporting

Useful in larger applications.

### math.js

```js
export const add = (a, b) => a + b;
```

---

### index.js

```js
export { add } from "./math.js";
```

---

### app.js

```js
import { add } from "./index.js";
```

This is called a **Barrel Export Pattern**.

---

# 12. Dynamic Imports

Imports modules at runtime.

Instead of:

```js
import math from "./math.js";
```

Use:

```js
const math = await import("./math.js");
```

---

Example:

```js
const math = await import("./math.js");

console.log(math.add(5, 3));
```

---

### Why Use Dynamic Imports?

* Lazy Loading
* Performance Optimization
* Conditional Loading

---

### Interview Answer

> Dynamic imports allow modules to be loaded at runtime rather than at application startup.

---

# 13. CommonJS vs ES Modules

This is one of the most asked interview questions.

| CommonJS            | ES Modules          |
| ------------------- | ------------------- |
| require()           | import              |
| module.exports      | export              |
| Synchronous Loading | Static Loading      |
| Node.js Default     | JavaScript Standard |
| Older System        | Modern System       |

---

### CommonJS Example

```js
const math = require("./math");
```

---

### ES Module Example

```js
import math from "./math.js";
```

---

# 14. Major Differences

## CommonJS

Loaded during execution.

```js
if (condition) {
    const module = require("./module");
}
```

Allowed.

---

## ES Modules

Static imports.

```js
import module from "./module.js";
```

Must be at the top level.

---

### Why?

Because JavaScript can analyze dependencies before execution.

This enables:

* Tree Shaking
* Better Optimization
* Faster Bundling

---

# 15. Tree Shaking

Huge interview topic for frontend/backend developers.

Suppose:

```js
export const add = () => {};
export const subtract = () => {};
export const multiply = () => {};
```

---

Import only:

```js
import { add } from "./math.js";
```

Unused exports:

```js
subtract
multiply
```

can be removed during build.

This optimization is called **Tree Shaking**.

---

### Interview Answer

> Tree Shaking is a build optimization that removes unused code from bundles. ES Modules support Tree Shaking because imports and exports are statically analyzable.

---

# 16. When to Use CommonJS vs ES Modules?

### CommonJS

Mostly found in:

* Legacy Node.js projects
* Older codebases

---

### ES Modules

Mostly found in:

* Modern Node.js projects
* React
* Next.js
* Modern libraries

---

### Interview Answer

> For new projects, ES Modules are generally preferred because they are the JavaScript standard and support modern optimizations like Tree Shaking.

---

# Common Interview Questions

### Q1: What are ES Modules?

> ES Modules are the standard JavaScript module system that uses `import` and `export`.

---

### Q2: Difference between Named Export and Default Export?

**Named Export**

```js
export const add = () => {};
```

Import:

```js
import { add } from "./math.js";
```

---

**Default Export**

```js
export default add;
```

Import:

```js
import add from "./math.js";
```

---

### Q3: Can a module have multiple default exports?

**Answer:**

❌ No

Only one default export is allowed per module.

---

### Q4: Can a module have multiple named exports?

**Answer:**

✅ Yes

Unlimited named exports.

---

### Q5: Why are ES Modules preferred?

**Answer:**

* Standard JavaScript
* Static Analysis
* Tree Shaking
* Better Tooling Support

---

### Q6: What is Dynamic Import?

**Answer:**

A mechanism to load modules at runtime using:

```js
await import("./module.js");
```

---

# Quick Revision Notes

### Named Export

```js
export const add = () => {};
```

Import:

```js
import { add } from "./math.js";
```

---

### Default Export

```js
export default add;
```

Import:

```js
import add from "./math.js";
```

---

### Import Everything

```js
import * as math from "./math.js";
```

---

### Dynamic Import

```js
const module = await import("./module.js");
```

---

### Tree Shaking

Removes unused exports during build.

---

## Phase 2 Progress

✅ What is a Module?
✅ CommonJS
✅ require()
✅ module.exports
✅ exports
✅ Module Wrapper Function
✅ Module Caching
✅ ES Modules
✅ Named Exports
✅ Default Exports
✅ Dynamic Imports
✅ Tree Shaking
✅ CommonJS vs ES Modules

### Next (Very Important)

**Phase 2 Part 3: Module Resolution & Circular Dependencies**

This is where interviewers test whether you understand:

* How `require()` actually finds files
* How Node.js searches `node_modules`
* What happens when modules depend on each other
* Circular dependency problems and how to avoid them

These are common 2–5 years experience interview questions.
