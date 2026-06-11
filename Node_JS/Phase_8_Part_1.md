# Phase 8: Process & Timers

# Part 1 — Timers (`setTimeout`, `setInterval`, `setImmediate`) & `process.nextTick()`

This section is extremely important because interviewers often test your understanding of the Event Loop through timer-related questions.

---

# 1. setTimeout()

## Definition

Schedules a callback to execute after a minimum delay.

---

## Syntax

```js
setTimeout(callback, delay);
```

---

## Example

```js
console.log("Start");

setTimeout(() => {
  console.log("Timer");
}, 2000);

console.log("End");
```

Output:

```text
Start
End
Timer
```

---

## Important Interview Concept

Many developers think:

```js
setTimeout(fn, 2000)
```

means:

> Execute exactly after 2 seconds.

Wrong.

Correct:

> Execute after at least 2 seconds and when the call stack is empty.

---

## Example

```js
setTimeout(() => {
  console.log("Timer");
}, 0);

for(let i = 0; i < 1000000000; i++) {}
```

Output:

```text
(after loop finishes)
Timer
```

---

## Interview Answer

> setTimeout schedules a callback after a minimum delay. The callback is moved to the callback queue and executes only when the call stack becomes empty.

---

# 2. setInterval()

## Definition

Executes a callback repeatedly after a fixed interval.

---

## Example

```js
setInterval(() => {
  console.log("Running...");
}, 1000);
```

Output:

```text
Running...
Running...
Running...
```

---

## Stop Interval

```js
const id = setInterval(() => {
  console.log("Running");
}, 1000);

clearInterval(id);
```

---

## Common Interview Question

### Q:

Can intervals overlap?

### Example

```js
setInterval(() => {

  console.log("Start");

  const start = Date.now();

  while(Date.now() - start < 3000) {}

  console.log("End");

}, 1000);
```

---

### Answer

Yes.

The interval keeps scheduling callbacks every second.

If execution takes longer than the interval, callbacks accumulate and timing becomes inaccurate.

---

## Better Approach

```js
function poll() {

  setTimeout(async () => {

    await doWork();

    poll();

  }, 1000);

}

poll();
```

---

## Interview Answer

> setInterval can cause overlapping executions when the callback takes longer than the interval duration. Recursive setTimeout is often safer for polling systems.

---

# 3. clearTimeout()

Cancel timeout.

---

## Example

```js
const id = setTimeout(() => {
  console.log("Hello");
}, 5000);

clearTimeout(id);
```

Output:

```text
Nothing
```

---

# 4. clearInterval()

Cancel interval.

---

## Example

```js
const id = setInterval(() => {
  console.log("Tick");
}, 1000);

clearInterval(id);
```

---

# 5. setImmediate()

## Definition

Schedules execution during the Event Loop's Check phase.

---

## Example

```js
setImmediate(() => {
  console.log("Immediate");
});
```

---

## Why Was It Created?

Sometimes we want:

```text
Current I/O
↓
Finish
↓
Run callback
```

without waiting for a timer.

---

# 6. setTimeout(0) vs setImmediate()

One of the most famous Node interview questions.

---

## Example

```js
setTimeout(() => {
  console.log("Timeout");
}, 0);

setImmediate(() => {
  console.log("Immediate");
});
```

Output:

```text
Not guaranteed
```

Could be:

```text
Timeout
Immediate
```

or

```text
Immediate
Timeout
```

---

## Why?

Depends on Event Loop state.

---

## Inside I/O

```js
const fs = require("fs");

fs.readFile("test.txt", () => {

  setTimeout(() => {
    console.log("Timeout");
  }, 0);

  setImmediate(() => {
    console.log("Immediate");
  });

});
```

Output:

```text
Immediate
Timeout
```

Always.

---

## Interview Answer

> Outside I/O, the execution order between setTimeout(0) and setImmediate() is not guaranteed. Inside an I/O callback, setImmediate() executes first because it runs in the Check phase immediately after Poll.

---

# 7. process.nextTick()

Most asked Event Loop interview topic.

---

## Definition

Schedules a callback in the Next Tick Queue.

---

## Example

```js
process.nextTick(() => {
  console.log("Tick");
});

console.log("Hello");
```

Output:

```text
Hello
Tick
```

---

## Why Special?

It runs before the Event Loop continues.

---

## Example

```js
setTimeout(() => {
  console.log("Timer");
}, 0);

process.nextTick(() => {
  console.log("Tick");
});
```

Output:

```text
Tick
Timer
```

---

# 8. process.nextTick() vs Promise

Very Popular Interview Question

---

## Example

```js
Promise.resolve()
  .then(() => {
    console.log("Promise");
  });

process.nextTick(() => {
  console.log("Tick");
  });
```

Output:

```text
Tick
Promise
```

---

## Why?

Priority:

```text
process.nextTick Queue
        ↓
Promise Microtask Queue
        ↓
Event Loop
```

---

## Interview Answer

> process.nextTick callbacks execute before Promise microtasks and before the Event Loop moves to the next phase.

---

# 9. Event Loop Execution Order

Memorize This

```js
console.log("Start");

setTimeout(() => {
  console.log("Timeout");
}, 0);

setImmediate(() => {
  console.log("Immediate");
});

Promise.resolve()
  .then(() => {
    console.log("Promise");
  });

process.nextTick(() => {
  console.log("Tick");
});

console.log("End");
```

Expected:

```text
Start
End
Tick
Promise
Timeout/Immediate
```

Last two may swap.

---

# 10. nextTick Starvation

Dangerous Interview Topic.

---

## Example

```js
function loop() {

  process.nextTick(loop);

}

loop();
```

---

Problem:

```text
Timer Never Runs

I/O Never Runs

Event Loop Blocked
```

---

## Why?

Node empties the nextTick queue completely before moving to another phase.

---

## Interview Answer

> Excessive use of process.nextTick can starve the Event Loop because Node processes the entire nextTick queue before handling timers, I/O, or Promise callbacks.

---

# 11. Real Production Uses

## Delay Work

```js
process.nextTick(() => {
  initialize();
});
```

---

## Post-Constructor Events

```js
class User {

  constructor() {

    process.nextTick(() => {
      this.emit("ready");
    });

  }

}
```

---

## Error Propagation

```js
if (!valid) {

  process.nextTick(() => {
    callback(new Error("Invalid"));
  });

}
```

---

# Frequently Asked Interview Questions

### Q1: Does setTimeout execute exactly after the specified delay?

**Answer:** No. It executes after at least the specified delay and only when the call stack is empty.

---

### Q2: Difference between setTimeout and setInterval?

**Answer:**

* setTimeout → runs once
* setInterval → runs repeatedly

---

### Q3: Difference between setImmediate and setTimeout(0)?

**Answer:**

Outside I/O → not guaranteed.

Inside I/O → setImmediate executes first.

---

### Q4: Which executes first?

```js
process.nextTick()
Promise.then()
```

**Answer:** `process.nextTick()`.

---

### Q5: Why can process.nextTick be dangerous?

**Answer:** It can starve the Event Loop and prevent timers and I/O from executing.

---

### Q6: Which queue has the highest priority?

**Answer:**

```text
process.nextTick Queue
```

---

# Quick Revision Notes

```text
setTimeout()
→ Runs once

setInterval()
→ Runs repeatedly

clearTimeout()
→ Cancel timeout

clearInterval()
→ Cancel interval

setTimeout(0)
≠ immediate execution

setImmediate()
→ Check Phase

Inside I/O:
setImmediate > setTimeout(0)

process.nextTick()
→ Highest priority queue

nextTick > Promise

Too many nextTicks
→ Event Loop starvation

setInterval may overlap

Recursive setTimeout is safer for polling
```

---

## Next: Phase 8 — Part 2 (Advanced Process Object)

We'll cover:

* `process.argv`
* `process.env`
* `process.cwd()`
* `process.pid`
* `process.ppid`
* `process.memoryUsage()`
* `process.uptime()`
* `process.hrtime()`
* `beforeExit`
* `exit`
* Production-level process interview questions.
