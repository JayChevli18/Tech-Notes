# Phase 3 - Part 1: Asynchronous JavaScript Fundamentals

This is one of the **most important interview topics** in Node.js.

Many interviewers ask:

* What is asynchronous programming?
* What is a callback?
* What is callback hell?
* What is a Promise?
* What is async/await?
* Explain the Event Loop.

We'll build the foundation first, then move to the Event Loop in the next part.

---

# 1. Synchronous Programming

Synchronous means code executes **line by line**.

Each task must finish before the next starts.

Example:

```js
console.log("Start");

console.log("Middle");

console.log("End");
```

Output:

```text
Start
Middle
End
```

Execution:

```text
Start
 ↓
Middle
 ↓
End
```

---

### Interview Answer

> Synchronous programming executes code sequentially, where each operation must complete before the next one starts.

---

# 2. Problem with Synchronous Code

Imagine reading a huge file:

```js
const fs = require("fs");

const data = fs.readFileSync("largeFile.txt");

console.log(data);
```

What happens?

```text
Read File
   ↓
Wait...
   ↓
Wait...
   ↓
Continue
```

Everything is blocked.

No other code can run.

This is called:

```text
Blocking Operation
```

---

# 3. Asynchronous Programming

Instead of waiting, Node delegates the task and continues executing.

Example:

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

Execution:

```text
Start
 ↓
Schedule Timer
 ↓
End
 ↓
Timer
```

Node doesn't wait.

---

### Interview Answer

> Asynchronous programming allows a program to start a task and continue executing other code without waiting for that task to complete.

---

# 4. Why Node.js Uses Async Programming

Most backend work involves:

```text
Database Queries
File Reading
API Calls
Email Sending
Network Requests
```

These operations are slow compared to CPU speed.

If Node waited for each operation:

```text
Request 1
Wait
Request 2
Wait
Request 3
Wait
```

Performance would be terrible.

Async programming allows Node to handle many requests efficiently.

---

# 5. What is a Callback Function?

A callback is a function passed as an argument to another function.

Example:

```js
function greet(name, callback) {
  console.log("Hello " + name);

  callback();
}

greet("Jay", () => {
  console.log("Welcome");
});
```

Output:

```text
Hello Jay
Welcome
```

---

### Interview Answer

> A callback is a function passed as an argument to another function that is executed after a specific task is completed.

---

# 6. Callbacks in Node.js

Most old Node APIs use callbacks.

Example:

```js
const fs = require("fs");

fs.readFile("data.txt", "utf8", (err, data) => {
  if (err) {
    console.log(err);
    return;
  }

  console.log(data);
});
```

---

## Error-First Callback Pattern

Node follows:

```js
(err, result)
```

Example:

```js
fs.readFile(file, (err, data) => {
  if (err) {
    return console.log(err);
  }

  console.log(data);
});
```

First parameter:

```text
Error
```

Second parameter:

```text
Result
```

---

### Interview Question

**Q: What is an Error-First Callback?**

**Answer:**

> In Node.js callbacks, the first parameter is reserved for errors and the second parameter contains the successful result.

---

# 7. Callback Hell

One callback inside another callback inside another callback.

Example:

```js
getUser(userId, (user) => {

  getOrders(user.id, (orders) => {

    getPayment(orders[0].id, (payment) => {

      console.log(payment);

    });

  });

});
```

---

Visualization:

```text
getUser()
   ↓
 getOrders()
      ↓
   getPayment()
         ↓
      Result
```

Code starts drifting to the right.

Looks like:

```text
Pyramid of Doom
```

---

## Problems of Callback Hell

### 1. Hard to Read

```js
a(() => {
  b(() => {
    c(() => {
      d(() => {});
    });
  });
});
```

---

### 2. Hard to Debug

Finding errors becomes difficult.

---

### 3. Hard to Maintain

Adding new functionality becomes messy.

---

### Interview Answer

> Callback hell occurs when multiple asynchronous operations are nested inside each other, making code difficult to read, debug, and maintain.

---

# 8. Solution: Promises

Promises were introduced to solve callback hell.

Instead of:

```js
getUser(id, (user) => {
  getOrders(user.id, (orders) => {
    console.log(orders);
  });
});
```

We can write:

```js
getUser(id)
  .then((user) => getOrders(user.id))
  .then((orders) => console.log(orders))
  .catch((err) => console.log(err));
```

Much cleaner.

---

# 9. What is a Promise?

A Promise is an object representing the eventual completion or failure of an asynchronous operation.

---

Example:

```js
const promise = new Promise((resolve, reject) => {

  const success = true;

  if (success) {
    resolve("Success");
  } else {
    reject("Failed");
  }

});
```

---

### Interview Answer

> A Promise is an object that represents the eventual result of an asynchronous operation, either success or failure.

---

# 10. Promise States

A Promise has 3 states:

```text
Pending
Fulfilled
Rejected
```

---

## Pending

Initial state.

```text
Operation Running
```

---

## Fulfilled

Success.

```text
resolve()
```

---

## Rejected

Failure.

```text
reject()
```

---

Visualization:

```text
          Pending
         /       \
        /         \
Fulfilled      Rejected
```

---

### Interview Question

**Q: Can a Promise change state after being fulfilled?**

**Answer:**

> No. Once a Promise is fulfilled or rejected, its state becomes immutable and cannot change.

---

# 11. Consuming Promises

Using `.then()` and `.catch()`.

Example:

```js
const promise = Promise.resolve("Hello");

promise
  .then((data) => {
    console.log(data);
  })
  .catch((err) => {
    console.log(err);
  });
```

Output:

```text
Hello
```

---

# 12. Promise Chaining

Example:

```js
Promise.resolve(2)
  .then((num) => num * 2)
  .then((num) => num * 3)
  .then((num) => console.log(num));
```

Output:

```text
12
```

Flow:

```text
2
 ↓
4
 ↓
12
```

---

### Interview Answer

> Promise chaining allows multiple asynchronous operations to be executed sequentially by returning values or promises from previous then() handlers.

---

# 13. Why Promises Are Better Than Callbacks

### Callbacks

```js
a(() => {
  b(() => {
    c(() => {});
  });
});
```

---

### Promises

```js
a()
  .then(b)
  .then(c)
  .catch(handleError);
```

Benefits:

```text
Cleaner
Readable
Better Error Handling
Chainable
```

---

# Most Asked Interview Questions

### Q1: What is asynchronous programming?

**Answer:**

> Asynchronous programming allows code to continue executing while waiting for long-running operations like file reads, database queries, or API requests.

---

### Q2: What is a callback?

**Answer:**

> A callback is a function passed as an argument to another function and executed later after a task completes.

---

### Q3: What is callback hell?

**Answer:**

> Callback hell is excessive nesting of callbacks, making code difficult to read, maintain, and debug.

---

### Q4: What is a Promise?

**Answer:**

> A Promise is an object representing the eventual success or failure of an asynchronous operation.

---

### Q5: What are Promise states?

**Answer:**

```text
Pending
Fulfilled
Rejected
```

---

### Q6: Why were Promises introduced?

**Answer:**

> Promises were introduced to improve readability, simplify asynchronous code, and avoid callback hell.

---

# Quick Revision Notes

### Synchronous

```text
Line-by-line execution
Blocking
```

---

### Asynchronous

```text
Non-blocking
Continue execution while waiting
```

---

### Callback

```js
function(callback)
```

Executed later.

---

### Error-First Callback

```js
(err, result)
```

---

### Callback Hell

```text
Nested callbacks
Pyramid of Doom
```

---

### Promise

```text
Represents future result
```

---

### Promise States

```text
Pending
Fulfilled
Rejected
```

---

### Promise Methods

```js
.then()
.catch()
.finally()
```

---

## Next Topic (Phase 3 - Part 2)

Now we'll cover **Advanced Promises & async/await**:

* Promise.all()
* Promise.allSettled()
* Promise.race()
* Promise.any()
* async keyword
* await keyword
* Error handling with try/catch
* Parallel vs Sequential execution
* Common interview traps and output-based questions

This is heavily asked in Node.js and MERN interviews.
