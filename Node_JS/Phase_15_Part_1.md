# Phase 15: Memory Management (Interview-Focused)

## Why Memory Management Matters

A common production issue is:

```text
Application starts fine
      ↓
Memory usage increases
      ↓
RAM becomes full
      ↓
Application slows down
      ↓
Crash (Out Of Memory)
```

Interviewers often ask:

> "How would you identify and prevent memory leaks in Node.js?"

To answer that, you need to understand Stack, Heap, Garbage Collection, and common leak patterns.

---

# Memory Layout in Node.js

```text
┌─────────────────┐
│     Stack       │
├─────────────────┤
│      Heap       │
└─────────────────┘
```

---

# 1. Stack Memory

## What is Stack Memory?

Stores:

* Function calls
* Primitive values
* Local variables
* Execution context

Example:

```js
function greet() {
  const name = "Jay";
}

greet();
```

Stack stores:

```text
greet()
name = "Jay"
```

When function finishes:

```text
Stack Frame Removed
```

Memory automatically released.

---

## Stack Characteristics

| Feature               | Stack   |
| --------------------- | ------- |
| Fast                  | ✅       |
| Automatically managed | ✅       |
| Small size            | ✅       |
| Stores primitives     | Usually |
| Function calls        | ✅       |

---

## Interview Answer

### What is stack memory?

> Stack memory stores function execution contexts, local variables, and primitive values. Memory is automatically allocated and freed when functions enter and exit.

---

# 2. Heap Memory

## What is Heap Memory?

Heap stores:

* Objects
* Arrays
* Functions
* Large data structures

Example:

```js
const user = {
  name: "Jay",
  age: 25
};
```

Object lives in heap memory.

---

## How It Works

```js
const user = {
  name: "Jay"
};
```

```text
Stack
 └── user
        │
        ▼

Heap
 └── Object
```

Variable contains a reference.

Actual object lives in heap.

---

## Heap Characteristics

| Feature           | Heap |
| ----------------- | ---- |
| Dynamic size      | ✅    |
| Stores objects    | ✅    |
| Slower access     | ✅    |
| Garbage collected | ✅    |

---

## Interview Answer

### What is heap memory?

> Heap memory stores dynamically allocated objects, arrays, and functions. JavaScript variables typically hold references to heap objects.

---

# Stack vs Heap

| Stack            | Heap              |
| ---------------- | ----------------- |
| Function calls   | Objects           |
| Fast             | Slower            |
| Fixed size       | Dynamic size      |
| Auto cleanup     | Garbage collected |
| Primitive values | Reference types   |

---

# 3. Garbage Collection (GC)

## What is Garbage Collection?

JavaScript automatically frees memory that is no longer reachable.

Example:

```js
let user = {
  name: "Jay"
};

user = null;
```

Now:

```text
No References
      ↓
Garbage Collector Removes Object
```

---

## Reachability Concept

Object survives if reachable.

```js
const user = {
  name: "Jay"
};
```

Reachable:

```text
Global
  ↓
user
  ↓
Object
```

---

Not reachable:

```js
let user = {
  name: "Jay"
};

user = null;
```

```text
Object
(No references)
```

GC removes it.

---

## Mark and Sweep

Modern V8 uses a variation of Mark-and-Sweep.

### Mark

Find reachable objects.

```text
Global
 ↓
Object A
 ↓
Object B
```

Mark them.

---

### Sweep

Remove everything unmarked.

```text
Reachable -> Keep

Unreachable -> Delete
```

---

## Interview Answer

### How does JavaScript garbage collection work?

> V8 uses a Mark-and-Sweep style garbage collector. Reachable objects are marked and retained, while unreachable objects are removed from heap memory.

---

# What Is a Memory Leak?

Memory leak means:

```text
Memory No Longer Needed
          ↓
Still Referenced
          ↓
Cannot Be Garbage Collected
```

Memory usage continuously grows.

---

# 4. Common Memory Leak Causes

---

# A. Global Variables

## Problem

```js
users = [];
```

Notice:

```js
users
```

without:

```js
const
let
var
```

becomes global.

---

## Why It's Dangerous

Global variables live for the entire process lifetime.

```text
Application Running
        ↓
Global Still Exists
```

Never collected.

---

## Fix

```js
const users = [];
```

---

## Interview Answer

> Global variables can cause memory leaks because they remain reachable for the lifetime of the application.

---

# B. Closures

## Problem

```js
function createUser() {
  const largeData =
    new Array(1000000);

  return function () {
    console.log("Hello");
  };
}
```

---

## What Happens

Closure keeps reference to:

```js
largeData
```

even if not needed.

```text
Closure
   ↓
largeData
```

GC cannot remove it.

---

## Fix

Avoid capturing unnecessary data.

---

## Interview Answer

> Closures can cause leaks when they unintentionally retain references to large objects that are no longer needed.

---

# C. Event Listeners

## Problem

```js
emitter.on("data", handler);
```

Repeatedly adding listeners:

```js
emitter.on("data", handler);
emitter.on("data", handler);
emitter.on("data", handler);
```

without removal.

---

## Result

```text
Listener
   ↓
Object References
   ↓
Memory Retained
```

---

## Fix

```js
emitter.off("data", handler);
```

or:

```js
emitter.once(...)
```

---

## Interview Answer

> Unremoved event listeners can keep objects referenced and prevent garbage collection.

---

# D. Timers

## Problem

```js
setInterval(() => {
  console.log("running");
}, 1000);
```

Interval runs forever.

---

## Leak Scenario

```js
setInterval(() => {
  processLargeObject(data);
}, 1000);
```

Timer keeps reference alive.

---

## Fix

```js
clearInterval(timer);
```

when no longer needed.

---

## Interview Answer

> Long-running timers may keep references alive and prevent memory from being released.

---

# Detecting Memory Leaks

## Process Memory Usage

```js
console.log(
  process.memoryUsage()
);
```

Output:

```js
{
  rss: ...,
  heapTotal: ...,
  heapUsed: ...
}
```

---

## Most Important

```js
process.memoryUsage().heapUsed
```

If it continuously grows:

```text
100 MB
200 MB
500 MB
1 GB
```

you may have a leak.

---

# Heap Snapshots

Capture memory state.

Used to find:

* Large objects
* Detached references
* Retained memory

Commonly through:

```text
Chrome DevTools
```

---

# Node Inspector

Run:

```bash
node --inspect app.js
```

Then connect DevTools.

Analyze:

* Heap snapshots
* Allocation timelines
* Memory growth

---

# Useful Production Tools

Common tools:

* Chrome DevTools
* Node Inspector
* Heap Snapshots
* Clinic.js
* PM2 Monitoring

---

# Memory Leak Investigation Process

Interviewers love this answer.

```text
Observe Memory Growth
        ↓
Check process.memoryUsage()
        ↓
Take Heap Snapshot
        ↓
Find Retained Objects
        ↓
Identify References
        ↓
Remove Leak
```

---

# Most Asked Interview Questions

### What is a memory leak?

> A memory leak occurs when memory is no longer needed but is still referenced, preventing garbage collection.

---

### Stack vs Heap?

> Stack stores execution contexts and local variables. Heap stores objects, arrays, and dynamically allocated data.

---

### How does garbage collection work?

> V8 uses Mark-and-Sweep style garbage collection to remove unreachable objects.

---

### How can closures cause memory leaks?

> Closures may accidentally retain large objects through references, preventing garbage collection.

---

### How do you detect memory leaks?

> Monitor heap usage, capture heap snapshots, analyze retained objects, and inspect memory growth over time.

---

### Why can event listeners leak memory?

> Event listeners keep references to callback functions and related objects until they are removed.

---

# Real Production Example

```js
const cache = {};
```

Every request:

```js
cache[user.id] = user;
```

But entries are never removed.

Result:

```text
100 Users
1000 Users
10000 Users
500000 Users
```

Heap continuously grows.

Eventually:

```text
FATAL ERROR:
JavaScript heap out of memory
```

This is a classic memory leak.

---

# 2-Minute Revision

```text
Stack
- Function calls
- Local variables
- Fast
- Auto cleanup

Heap
- Objects
- Arrays
- Functions
- Garbage collected

GC
- Mark and Sweep
- Removes unreachable objects

Memory Leak
- Object still referenced
- Cannot be collected

Common Causes
- Global variables
- Closures
- Event listeners
- Timers

Detection
- process.memoryUsage()
- Heap snapshots
- Chrome DevTools
- Node Inspector

Interview Favorite
- Stack vs Heap
- GC process
- Memory leak causes
- Leak debugging strategy
```

The next high-value phase for Node.js interviews is **Phase 16: Streams Deep Dive & Backpressure (Advanced)**, because senior-level interviewers often move from "what are streams?" to "how does backpressure work internally?".
