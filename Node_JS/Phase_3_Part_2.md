# Phase 3 - Part 2: Advanced Promises & async/await

This section is asked in almost every MERN/Node.js interview.

Interviewers expect you to know:

* `async/await`
* Promise utility methods
* Error handling
* Parallel vs Sequential execution

---

# 1. async Keyword

The `async` keyword makes a function return a Promise.

Example:

```js
async function greet() {
  return "Hello";
}
```

What actually happens:

```js
Promise.resolve("Hello");
```

---

## Verify

```js
async function greet() {
  return "Hello";
}

console.log(greet());
```

Output:

```js
Promise { 'Hello' }
```

---

### Interview Answer

> An async function always returns a Promise. If a normal value is returned, JavaScript automatically wraps it in a resolved Promise.

---

# 2. await Keyword

`await` waits for a Promise to settle.

Example:

```js
function fetchUser() {
  return Promise.resolve("Jay");
}

async function getUser() {
  const user = await fetchUser();

  console.log(user);
}
```

Output:

```text
Jay
```

---

### Without await

```js
const user = fetchUser();

console.log(user);
```

Output:

```js
Promise { 'Jay' }
```

---

### Interview Answer

> The await keyword pauses execution inside an async function until the Promise is resolved or rejected.

---

# 3. Converting Promise Chain to async/await

### Promise Version

```js
getUser()
  .then(user => getOrders(user.id))
  .then(orders => console.log(orders))
  .catch(err => console.log(err));
```

---

### async/await Version

```js
try {
  const user = await getUser();

  const orders = await getOrders(user.id);

  console.log(orders);
} catch (err) {
  console.log(err);
}
```

Cleaner and easier to read.

---

# 4. Error Handling in async/await

Use:

```js
try {
}
catch {
}
```

Example:

```js
async function test() {
  try {
    throw new Error("Something went wrong");
  } catch (err) {
    console.log(err.message);
  }
}
```

Output:

```text
Something went wrong
```

---

### Interview Question

**Q: How do you handle errors with async/await?**

**Answer:**

> Errors are handled using try/catch blocks around awaited operations.

---

# 5. Sequential Execution

Example:

```js
const user = await getUser();

const orders = await getOrders(user.id);

const payment = await getPayment(orders[0].id);
```

Flow:

```text
User
 ↓
Orders
 ↓
Payment
```

Each operation waits for the previous one.

---

### Problem

If each API takes:

```text
1 second
```

Total:

```text
3 seconds
```

---

# 6. Parallel Execution

Suppose operations are independent.

Bad:

```js
const users = await getUsers();

const products = await getProducts();

const orders = await getOrders();
```

Time:

```text
1s + 1s + 1s = 3s
```

---

Better:

```js
const [users, products, orders] =
  await Promise.all([
    getUsers(),
    getProducts(),
    getOrders()
  ]);
```

Time:

```text
1 second
```

(all run together)

---

### Interview Answer

> Use Promise.all() when multiple asynchronous operations are independent and can run simultaneously.

---

# 7. Promise.all()

Runs all promises concurrently.

Returns:

```text
All results
```

Only if all succeed.

---

Example:

```js
const result = await Promise.all([
  Promise.resolve("A"),
  Promise.resolve("B"),
  Promise.resolve("C")
]);

console.log(result);
```

Output:

```js
["A", "B", "C"]
```

---

## Failure Case

```js
await Promise.all([
  Promise.resolve("A"),
  Promise.reject("Failed"),
  Promise.resolve("C")
]);
```

Result:

```text
Immediately Rejects
```

---

### Interview Question

**Q: When does Promise.all() fail?**

**Answer:**

> Promise.all() rejects immediately if any Promise in the array rejects.

---

# 8. Promise.allSettled()

Waits for all promises.

Even if some fail.

---

Example:

```js
const result = await Promise.allSettled([
  Promise.resolve("A"),
  Promise.reject("Error")
]);
```

Output:

```js
[
  { status: "fulfilled", value: "A" },
  { status: "rejected", reason: "Error" }
]
```

---

### Use Case

```text
Bulk Email Sending
Notifications
Reports
```

You want results for everything.

---

### Interview Answer

> Promise.allSettled() waits for all promises to complete and returns both successful and failed results.

---

# 9. Promise.race()

Returns the first settled Promise.

Success or failure.

---

Example:

```js
Promise.race([
  slowAPI(),
  fastAPI()
]);
```

Whichever finishes first wins.

---

### Use Case

Timeout mechanism.

```js
Promise.race([
  fetchData(),
  timeoutPromise()
]);
```

---

### Interview Answer

> Promise.race() returns the result of the first Promise that settles, whether fulfilled or rejected.

---

# 10. Promise.any()

Returns first successful Promise.

Ignores failures.

---

Example:

```js
Promise.any([
  Promise.reject(),
  Promise.resolve("Server 2"),
  Promise.resolve("Server 3")
]);
```

Output:

```text
Server 2
```

---

### Use Case

Multiple backup servers.

```text
Server A
Server B
Server C
```

Take first successful response.

---

### Interview Answer

> Promise.any() resolves with the first fulfilled Promise and ignores rejected Promises.

---

# 11. Promise.race vs Promise.any

### Promise.race

Returns first completed Promise.

```text
Success OR Failure
```

---

### Promise.any

Returns first successful Promise.

```text
Success Only
```

---

Example:

```js
Promise.race([
  Promise.reject("Error"),
  Promise.resolve("Success")
]);
```

Output:

```text
Error
```

---

Example:

```js
Promise.any([
  Promise.reject("Error"),
  Promise.resolve("Success")
]);
```

Output:

```text
Success
```

---

# 12. Promise.all vs Promise.allSettled

### Promise.all

If one fails:

```text
Entire operation fails
```

---

### Promise.allSettled

```text
Waits for all
Returns all results
```

---

### Quick Comparison

| Method             | Stops On Failure? | Returns            |
| ------------------ | ----------------- | ------------------ |
| Promise.all        | Yes               | All success values |
| Promise.allSettled | No                | Status of all      |
| Promise.race       | First settled     | One result         |
| Promise.any        | First success     | One success result |

---

# 13. Common Interview Mistake

### Bad

```js
const users = await getUsers();
const products = await getProducts();
const orders = await getOrders();
```

Independent operations executed sequentially.

---

### Better

```js
const [users, products, orders] =
  await Promise.all([
    getUsers(),
    getProducts(),
    getOrders()
  ]);
```

---

### Interview Question

**Q: Why is Promise.all faster?**

**Answer:**

> Promise.all executes independent asynchronous operations concurrently instead of waiting for each one to finish before starting the next.

---

# 14. async/await Interview Trap

Question:

```js
async function test() {
  return 100;
}

console.log(test());
```

Output?

Answer:

```js
Promise { 100 }
```

Because async always returns a Promise.

---

# 15. Another Interview Trap

Question:

```js
async function test() {
  throw new Error("Failed");
}

test();
```

What happens?

Answer:

```text
Rejected Promise
```

Equivalent:

```js
return Promise.reject(
  new Error("Failed")
);
```

---

# Most Asked Interview Questions

### Q1: Does async always return a Promise?

**Answer:**

> Yes, every async function always returns a Promise.

---

### Q2: What does await do?

**Answer:**

> await pauses execution inside an async function until the Promise settles.

---

### Q3: Difference between Promise.all and Promise.allSettled?

**Answer:**

> Promise.all fails immediately if any Promise rejects, while Promise.allSettled waits for all Promises and returns their statuses.

---

### Q4: Difference between Promise.race and Promise.any?

**Answer:**

> Promise.race returns the first settled Promise, while Promise.any returns the first fulfilled Promise.

---

### Q5: Why use Promise.all?

**Answer:**

> To run independent asynchronous operations concurrently and improve performance.

---

# Quick Revision Notes

### async

```js
async function fn() {}
```

Always returns Promise.

---

### await

```js
const data = await promise;
```

Waits for Promise.

---

### Error Handling

```js
try {
}
catch {
}
```

---

### Promise.all

```text
All succeed
OR fail immediately
```

---

### Promise.allSettled

```text
Wait for all
Success + Failure
```

---

### Promise.race

```text
First settled wins
```

---

### Promise.any

```text
First successful wins
```

---

### Performance Rule

❌ Sequential:

```js
await a();
await b();
await c();
```

✅ Parallel:

```js
await Promise.all([
  a(),
  b(),
  c()
]);
```

---

# Next Topic (Phase 3 - Part 3)

Now comes the **most important Node.js interview topic**:

### Event Loop Deep Dive

We'll cover:

* Call Stack
* Web APIs / Node APIs
* Callback Queue
* Event Loop
* Microtask Queue
* Macrotask Queue
* `process.nextTick()`
* `setImmediate()`
* Promise Queue
* Execution Priority Order
* 15+ Output-Based Interview Questions

This topic is asked in almost every Node.js interview, especially for 2–5 years experience.
