# Phase 4 - Part 1: Event Loop Deep Dive

This is one of the most frequently asked Node.js interview topics.

Interviewers often ask:

* Explain the Event Loop.
* Why is Node.js non-blocking?
* Difference between `setTimeout()` and `setImmediate()`.
* Difference between `process.nextTick()` and Promise.
* Predict the output of this code.

If you understand this topic well, you'll answer many tricky JavaScript and Node.js questions confidently.

---

# 1. Why Do We Need the Event Loop?

Node.js executes JavaScript on a **single thread**.

Question:

If JavaScript is single-threaded, how can Node handle:

```text
1000 API Requests
Database Queries
File Reads
Network Calls
```

Answer:

```text
Event Loop
```

The Event Loop coordinates asynchronous operations and executes callbacks when they are ready.

---

### Interview Answer

> The Event Loop is a mechanism that allows Node.js to perform non-blocking asynchronous operations despite JavaScript being single-threaded.

---

# 2. Event Loop Architecture

```text
Call Stack
     ↓
Node APIs
     ↓
Queues
     ↓
Event Loop
     ↓
Call Stack
```

---

Example:

```js
setTimeout(() => {
  console.log("Timer");
}, 1000);
```

Flow:

```text
setTimeout()
      ↓
Node API
      ↓
Wait 1 second
      ↓
Callback Queue
      ↓
Event Loop
      ↓
Call Stack
      ↓
Execute Callback
```

---

# 3. Call Stack

The Call Stack tracks which function is currently executing.

Example:

```js
function one() {
  two();
}

function two() {
  three();
}

function three() {
  console.log("Hello");
}

one();
```

Stack:

```text
one()
 ↓
two()
 ↓
three()
```

Then:

```text
three() removed
two() removed
one() removed
```

---

### Interview Answer

> The Call Stack is a data structure that keeps track of currently executing functions.

---

# 4. What Happens with setTimeout?

Example:

```js
console.log("Start");

setTimeout(() => {
  console.log("Timer");
}, 1000);

console.log("End");
```

Execution:

### Step 1

```text
Start
```

Printed.

---

### Step 2

```js
setTimeout(...)
```

Moves timer registration to Node APIs.

---

### Step 3

```text
End
```

Printed.

---

### Step 4

After 1 second:

Callback enters queue.

---

### Step 5

Event Loop checks:

```text
Is Call Stack Empty?
```

If yes:

Execute callback.

---

Output:

```text
Start
End
Timer
```

---

### Important Interview Point

`setTimeout(fn, 1000)` means:

```text
Execute after AT LEAST 1000ms
```

NOT:

```text
Execute exactly after 1000ms
```

---

# 5. Callback Queue (Macrotask Queue)

Stores callbacks from:

```text
setTimeout
setInterval
setImmediate
I/O callbacks
```

Example:

```js
setTimeout(() => {
  console.log("A");
}, 0);
```

After timer expires:

```text
Callback Queue
    ↓
Event Loop
    ↓
Call Stack
```

---

# 6. Microtask Queue

Higher priority than Callback Queue.

Contains:

```text
Promise.then()
Promise.catch()
Promise.finally()
queueMicrotask()
```

Example:

```js
Promise.resolve()
  .then(() => {
    console.log("Promise");
  });
```

Callback goes to:

```text
Microtask Queue
```

---

### Important Rule

Microtasks execute before Macrotasks.

---

# 7. First Output-Based Question

```js
console.log("Start");

setTimeout(() => {
  console.log("Timer");
}, 0);

Promise.resolve()
  .then(() => {
    console.log("Promise");
  });

console.log("End");
```

---

### Predict Output

Execution:

```text
Start
End
Promise
Timer
```

---

### Why?

Order:

```text
Sync Code
↓
Microtasks
↓
Macrotasks
```

---

# 8. Event Loop Priority

Node generally executes:

```text
1. Synchronous Code

2. process.nextTick Queue

3. Promise Microtask Queue

4. Macrotask Queue
```

This order is extremely important.

---

# 9. process.nextTick()

Node-specific feature.

Example:

```js
process.nextTick(() => {
  console.log("Next Tick");
});
```

---

### Special Rule

`process.nextTick()` executes before Promise callbacks.

---

Example:

```js
Promise.resolve()
  .then(() => {
    console.log("Promise");
  });

process.nextTick(() => {
  console.log("Next Tick");
});
```

Output:

```text
Next Tick
Promise
```

---

### Interview Answer

> process.nextTick() callbacks are executed immediately after the current operation completes and before the Event Loop proceeds to other microtasks.

---

# 10. Second Output-Based Question

```js
console.log("Start");

process.nextTick(() => {
  console.log("Tick");
});

Promise.resolve()
  .then(() => {
    console.log("Promise");
  });

setTimeout(() => {
  console.log("Timer");
}, 0);

console.log("End");
```

Output:

```text
Start
End
Tick
Promise
Timer
```

---

Execution:

```text
Sync
↓
nextTick
↓
Promise
↓
Timer
```

---

# 11. setImmediate()

Node-specific API.

Example:

```js
setImmediate(() => {
  console.log("Immediate");
});
```

Callback goes into:

```text
Check Phase Queue
```

of the Event Loop.

---

### Interview Answer

> setImmediate() schedules a callback to run during the Check phase of the Event Loop.

---

# 12. setTimeout vs setImmediate

Example:

```js
setTimeout(() => {
  console.log("Timeout");
}, 0);

setImmediate(() => {
  console.log("Immediate");
});
```

---

### Output?

Trick Question.

Can be:

```text
Timeout
Immediate
```

OR

```text
Immediate
Timeout
```

Depends on timing and context.

---

### Important Interview Answer

> The execution order between setTimeout(fn, 0) and setImmediate() is not always guaranteed when scheduled from the main module.

---

# 13. Event Loop Phases

Simplified:

```text
Timers
 ↓
Pending Callbacks
 ↓
Idle/Prepare
 ↓
Poll
 ↓
Check
 ↓
Close Callbacks
```

---

### Timers Phase

Executes:

```text
setTimeout
setInterval
```

---

### Poll Phase

Handles:

```text
I/O Operations
```

---

### Check Phase

Executes:

```text
setImmediate
```

---

# 14. Common Interview Question

### Why does Promise run before setTimeout?

Example:

```js
setTimeout(() => {
  console.log("Timeout");
}, 0);

Promise.resolve()
  .then(() => {
    console.log("Promise");
  });
```

Output:

```text
Promise
Timeout
```

Reason:

```text
Microtask Queue
     ↑
Higher Priority
```

than

```text
Macrotask Queue
```

---

### Interview Answer

> Promise callbacks are placed in the microtask queue, which is processed before the macrotask queue containing setTimeout callbacks.

---

# 15. Tricky Output Question

```js
console.log("1");

setTimeout(() => {
  console.log("2");
}, 0);

Promise.resolve()
  .then(() => {
    console.log("3");
  });

process.nextTick(() => {
  console.log("4");
});

console.log("5");
```

Output:

```text
1
5
4
3
2
```

---

Explanation:

```text
Sync:
1
5

nextTick:
4

Promise:
3

Timer:
2
```

---

# 16. Another Interview Question

```js
async function test() {
  console.log("A");

  await Promise.resolve();

  console.log("B");
}

console.log("C");

test();

console.log("D");
```

Output:

```text
C
A
D
B
```

---

Why?

`await` creates a microtask.

Execution:

```text
Sync:
C
A
D

Microtask:
B
```

---

# Most Asked Interview Questions

### Q1: What is the Event Loop?

**Answer:**

> The Event Loop is a mechanism that manages asynchronous operations and executes callbacks when the Call Stack becomes available.

---

### Q2: What is the Call Stack?

**Answer:**

> The Call Stack tracks function execution order in JavaScript.

---

### Q3: What is the difference between Microtasks and Macrotasks?

**Answer:**

> Microtasks such as Promise callbacks execute before Macrotasks such as setTimeout and setImmediate callbacks.

---

### Q4: Which executes first: Promise or setTimeout?

**Answer:**

> Promise callbacks execute first because they are placed in the microtask queue.

---

### Q5: Which executes first: process.nextTick or Promise?

**Answer:**

> process.nextTick executes before Promise callbacks in Node.js.

---

### Q6: Does setTimeout(fn, 0) run immediately?

**Answer:**

> No. It runs after the current execution and microtasks complete.

---

# Quick Revision Notes

### Event Loop

```text
Handles async operations
```

---

### Call Stack

```text
Executes functions
```

---

### Microtask Queue

```text
Promise.then()
catch()
finally()
queueMicrotask()
```

---

### nextTick Queue

```text
process.nextTick()
```

Highest priority in Node.js.

---

### Macrotask Queue

```text
setTimeout()
setInterval()
```

---

### Priority Order

```text
1. Sync Code

2. process.nextTick()

3. Promise Microtasks

4. Macrotasks
```

---

### setImmediate

```text
Check Phase
```

---

### Important Interview Rule

```text
process.nextTick
      ↓
Promises
      ↓
setTimeout
```

Memorize this order.

---

## Next Topic (Phase 4 - Part 1)

We'll cover **Advanced Event Loop & Real Interview Output Questions**:

* 15–20 tricky output questions
* Nested Promises
* Nested `process.nextTick()`
* `setImmediate()` vs `setTimeout()`
* I/O callbacks and Event Loop phases
* Starvation problems
* Real interview coding questions

This is where most 3–5 year Node.js interview questions come from.
