# Phase 4 - Part 2: Advanced Event Loop & Real Interview Questions

This is where interviewers test whether you **actually understand the Event Loop** or have just memorized definitions.

For each question:

1. Execute synchronous code first
2. Process `process.nextTick()`
3. Process Promise microtasks
4. Process macrotasks (`setTimeout`, `setImmediate`, etc.)

---

# Question 1

```js
console.log("A");

setTimeout(() => {
  console.log("B");
}, 0);

Promise.resolve().then(() => {
  console.log("C");
});

console.log("D");
```

### Output

```text
A
D
C
B
```

### Explanation

```text
Sync:
A
D

Microtasks:
C

Macrotasks:
B
```

---

# Question 2

```js
console.log("1");

process.nextTick(() => {
  console.log("2");
});

Promise.resolve().then(() => {
  console.log("3");
});

console.log("4");
```

### Output

```text
1
4
2
3
```

### Why?

Priority:

```text
Sync
↓
nextTick
↓
Promise
```

---

# Question 3

```js
Promise.resolve().then(() => {
  console.log("Promise 1");
});

process.nextTick(() => {
  console.log("Tick");
});

Promise.resolve().then(() => {
  console.log("Promise 2");
});
```

### Output

```text
Tick
Promise 1
Promise 2
```

### Why?

Node drains:

```text
nextTick Queue
```

before

```text
Promise Queue
```

---

# Question 4

```js
console.log("Start");

setTimeout(() => {
  console.log("Timeout");
}, 0);

setImmediate(() => {
  console.log("Immediate");
});

console.log("End");
```

### Output?

Trick Question.

Possible:

```text
Start
End
Timeout
Immediate
```

OR

```text
Start
End
Immediate
Timeout
```

---

### Interview Answer

> When scheduled from the main module, the order between setTimeout(fn, 0) and setImmediate() is not guaranteed.

---

# Question 5

```js
setTimeout(() => {
  console.log("Timer");

  Promise.resolve().then(() => {
    console.log("Promise");
  });

}, 0);
```

### Output

```text
Timer
Promise
```

### Why?

After timer callback completes:

```text
Microtasks execute immediately
```

before next macrotask.

---

# Question 6

```js
Promise.resolve()
  .then(() => {
    console.log("A");

    Promise.resolve()
      .then(() => {
        console.log("B");
      });
  });

console.log("C");
```

### Output

```text
C
A
B
```

---

### Why?

Sync:

```text
C
```

Microtask:

```text
A
```

New microtask added:

```text
B
```

---

# Question 7

```js
process.nextTick(() => {
  console.log("A");

  process.nextTick(() => {
    console.log("B");
  });
});

Promise.resolve().then(() => {
  console.log("C");
});
```

### Output

```text
A
B
C
```

---

### Important Concept

Node completely drains:

```text
nextTick Queue
```

before touching Promises.

---

# Question 8

```js
Promise.resolve().then(() => {
  console.log("A");

  process.nextTick(() => {
    console.log("B");
  });
});

Promise.resolve().then(() => {
  console.log("C");
});
```

### Output

```text
A
C
B
```

### Why?

Initial Promise queue:

```text
A
C
```

Execute:

```text
A
```

Adds:

```text
nextTick(B)
```

But Node finishes current Promise queue first.

Then:

```text
B
```

---

# Question 9

```js
async function test() {
  console.log("1");

  await Promise.resolve();

  console.log("2");
}

console.log("3");

test();

console.log("4");
```

### Output

```text
3
1
4
2
```

---

### Why?

`await` splits execution.

Equivalent to:

```js
Promise.resolve().then(() => {
  console.log("2");
});
```

---

# Question 10

```js
async function test() {
  console.log("A");

  await Promise.resolve();

  console.log("B");

  await Promise.resolve();

  console.log("C");
}

test();

console.log("D");
```

### Output

```text
A
D
B
C
```

---

### Execution

Sync:

```text
A
D
```

Microtasks:

```text
B
C
```

---

# Question 11

```js
setTimeout(() => {
  console.log("A");
}, 0);

Promise.resolve().then(() => {
  console.log("B");
});

process.nextTick(() => {
  console.log("C");
});

console.log("D");
```

### Output

```text
D
C
B
A
```

---

### Priority

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

# Question 12

```js
setTimeout(() => {
  console.log("Timer 1");
}, 0);

setTimeout(() => {
  console.log("Timer 2");
}, 0);
```

### Output

```text
Timer 1
Timer 2
```

Usually FIFO order.

---

# Question 13

```js
Promise.resolve()
  .then(() => {
    console.log("1");
  })
  .then(() => {
    console.log("2");
  })
  .then(() => {
    console.log("3");
  });
```

### Output

```text
1
2
3
```

---

### Why?

Each `.then()` schedules the next microtask.

---

# Question 14

```js
console.log("Start");

setTimeout(() => {
  console.log("Timer");
}, 0);

for (let i = 0; i < 1000000000; i++) {}

console.log("End");
```

### Output

```text
Start
End
Timer
```

---

### Why?

The loop blocks the Event Loop.

Even though timer is:

```js
0
```

it cannot execute until stack becomes empty.

---

### Interview Question

**Does setTimeout(fn, 0) mean immediate execution?**

Answer:

> No. It executes only after the current call stack and microtasks complete.

---

# Event Loop Starvation

Very important senior-level concept.

---

## Example

```js
function loop() {
  process.nextTick(loop);
}

loop();
```

---

What happens?

```text
nextTick
 ↓
nextTick
 ↓
nextTick
 ↓
Forever
```

---

Result:

```text
Event Loop Starvation
```

Timers never run.

Promises never run.

I/O never runs.

---

### Interview Answer

> Event Loop starvation occurs when high-priority tasks continuously schedule themselves, preventing lower-priority tasks from executing.

---

# Real Node.js Event Loop Order

Simplified:

```text
Sync Code

↓

process.nextTick()

↓

Promise Microtasks

↓

Timers Phase
(setTimeout)

↓

Poll Phase
(I/O)

↓

Check Phase
(setImmediate)

↓

Close Callbacks
```

---

# Master Interview Priority Rule

Memorize:

```text
1. Synchronous Code

2. process.nextTick()

3. Promise Microtasks

4. Timers (setTimeout)

5. I/O

6. setImmediate()
```

This single order solves most Event Loop questions.

---

# Most Asked Interview Questions

### Q1

Why does Promise execute before setTimeout?

**Answer:**

> Promise callbacks are microtasks and have higher priority than macrotasks like setTimeout.

---

### Q2

Why does process.nextTick execute before Promise?

**Answer:**

> Node.js maintains a special nextTick queue that is processed before the Promise microtask queue.

---

### Q3

Can setTimeout(fn, 0) execute immediately?

**Answer:**

> No. It executes after the current call stack and microtasks complete.

---

### Q4

What is Event Loop starvation?

**Answer:**

> Event Loop starvation happens when high-priority tasks repeatedly schedule themselves and prevent lower-priority tasks from executing.

---

### Q5

Which has higher priority?

```js
process.nextTick()
```

or

```js
Promise.then()
```

**Answer:**

```text
process.nextTick()
```

---

# Quick Revision Notes

### Execution Order

```text
Sync
↓
process.nextTick
↓
Promises
↓
Timers
↓
I/O
↓
setImmediate
```

---

### Microtasks

```js
Promise.then()
catch()
finally()
queueMicrotask()
```

---

### nextTick

```js
process.nextTick()
```

Highest priority queue.

---

### await

Creates a microtask.

---

### setTimeout

Macrotask.

---

### setImmediate

Check phase.

---

### Starvation

```js
process.nextTick(loop)
```

can block Event Loop forever.

---

## Phase 4 Status

✅ Synchronous vs Asynchronous
✅ Callbacks
✅ Callback Hell
✅ Promises
✅ async/await
✅ Promise APIs
✅ Event Loop
✅ Microtasks vs Macrotasks
✅ process.nextTick()
✅ setImmediate()
✅ Advanced Output Questions

---
