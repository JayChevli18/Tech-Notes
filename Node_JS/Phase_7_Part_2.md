# Phase 7: Events & EventEmitter

# Part 2 — EventEmitter Internals, Error Events, Memory Leaks & Advanced Concepts

---

# 1. How EventEmitter Works Internally

## Definition

`EventEmitter` maintains an internal object where:

```js
{
  login: [listener1, listener2],
  logout: [listener3]
}
```

Each event name maps to an array of listener functions.

---

## Example

```js
const EventEmitter = require("events");

const emitter = new EventEmitter();

emitter.on("login", () => {
  console.log("Listener 1");
});

emitter.on("login", () => {
  console.log("Listener 2");
});

emitter.emit("login");
```

Output:

```text
Listener 1
Listener 2
```

Node internally stores:

```js
{
  login: [
    listener1,
    listener2
  ]
}
```

---

## Interview Answer

> Internally, EventEmitter stores listeners in a map-like structure where each event name points to an array of callback functions. When emit() is called, Node executes all listeners registered for that event in order.

---

# 2. Are EventEmitter Listeners Synchronous?

## Very Important Interview Question

Many developers answer this incorrectly.

---

## Example

```js
const EventEmitter = require("events");

const emitter = new EventEmitter();

emitter.on("login", () => {
  console.log("A");
});

emitter.on("login", () => {
  console.log("B");
});

console.log("Before");

emitter.emit("login");

console.log("After");
```

Output:

```text
Before
A
B
After
```

---

## Why?

Because listeners execute synchronously.

---

## Interview Answer

> EventEmitter listeners are executed synchronously by default in the order they were registered. If asynchronous behavior is required, developers must explicitly use setImmediate(), setTimeout(), or Promises.

---

# 3. Making Listeners Asynchronous

## Example

```js
emitter.on("login", () => {
  setImmediate(() => {
    console.log("Async Work");
  });
});
```

Output:

```text
After emit
Async Work
```

---

# 4. Error Events (Most Asked)

## Why Special?

The `"error"` event behaves differently.

---

## Dangerous Example

```js
const EventEmitter = require("events");

const emitter = new EventEmitter();

emitter.emit("error", new Error("DB Failed"));
```

Output:

```text
Unhandled 'error' event
```

Application crashes.

---

## Correct Way

```js
emitter.on("error", (err) => {
  console.log(err.message);
});

emitter.emit(
  "error",
  new Error("DB Failed")
);
```

Output:

```text
DB Failed
```

---

## Interview Answer

> The error event is special in Node.js. If an error event is emitted without a registered error listener, Node treats it as an uncaught exception and terminates the process.

---

## Real World

```js
stream.on("error", handler);

socket.on("error", handler);

server.on("error", handler);
```

Always register error handlers.

---

# 5. Listener Memory Leak Warning

## Example

```js
for(let i = 0; i < 20; i++) {
  emitter.on("login", () => {});
}
```

Output:

```text
MaxListenersExceededWarning
```

---

## Why?

Node assumes:

```text
Too many listeners
Possible memory leak
```

---

## Default Limit

```js
10
```

listeners per event.

---

## Check Limit

```js
emitter.getMaxListeners();
```

---

## Increase Limit

```js
emitter.setMaxListeners(20);
```

or

```js
EventEmitter.defaultMaxListeners = 20;
```

---

## Interview Answer

> Node.js warns when more than 10 listeners are attached to the same event because excessive listeners may indicate a memory leak. The warning does not stop execution but should be investigated.

---

# 6. Common Cause of Memory Leaks

Bad:

```js
app.get("/users", () => {
  emitter.on("login", handler);
});
```

Every request adds another listener.

Eventually:

```text
1000 listeners
Memory growth
Warning
```

---

Good:

```js
emitter.on("login", handler);
```

Register once during startup.

---

## Interview Question

### Q:

What is the most common EventEmitter memory leak?

### Answer:

> Repeatedly attaching listeners without removing them, especially inside request handlers, loops, or recurring functions.

---

# 7. removeAllListeners()

## Example

```js
emitter.removeAllListeners("login");
```

Remove all login listeners.

---

All events:

```js
emitter.removeAllListeners();
```

---

## Use Carefully

Can break application behavior.

---

# 8. prependListener()

Normally:

```js
A
B
```

---

Example

```js
emitter.on("login", () => {
  console.log("B");
});

emitter.prependListener(
  "login",
  () => {
    console.log("A");
  }
);

emitter.emit("login");
```

Output:

```text
A
B
```

---

## Why?

Adds listener to beginning of listener list.

---

# 9. Custom EventEmitter Class

Very Common Interview Topic

---

## Example

```js
const EventEmitter = require("events");

class UserService extends EventEmitter {

  createUser(name) {

    const user = { name };

    this.emit(
      "userCreated",
      user
    );

    return user;
  }
}

const service =
  new UserService();

service.on(
  "userCreated",
  (user) => {
    console.log(user);
  }
);

service.createUser("Jay");
```

---

## Why?

Allows services/modules to emit events.

---

## Interview Answer

> By extending EventEmitter, custom classes can publish domain-specific events and allow other parts of the application to subscribe without tight coupling.

---

# 10. Event-Driven Architecture Example

Without Events

```js
createUser();
sendEmail();
createAuditLog();
updateAnalytics();
```

Everything depends on everything.

---

With Events

```js
emitter.emit(
  "userCreated",
  user
);
```

Subscribers:

```js
sendEmail()
createAuditLog()
updateAnalytics()
```

---

Benefits:

```text
Loose Coupling
Scalability
Maintainability
Extensibility
```

---

# 11. Pub/Sub Pattern

EventEmitter implements:

```text
Publisher
    ↓
 Event
    ↓
Subscribers
```

Example:

```text
User Service
      ↓
 userCreated
      ↓
Email Service

Analytics Service

Audit Service
```

---

## Interview Answer

> EventEmitter follows the Publish-Subscribe pattern, where publishers emit events without knowing who consumes them, resulting in loosely coupled systems.

---

# 12. EventEmitter vs Callback

### Callback

```js
createUser(() => {
  sendEmail();
});
```

Tight coupling.

---

### EventEmitter

```js
createUser();

emitter.emit(
  "userCreated"
);
```

Loose coupling.

---

# 13. EventEmitter vs Message Queue

Very Popular Senior Interview Question.

| EventEmitter      | Message Queue      |
| ----------------- | ------------------ |
| Same process      | Different services |
| In-memory         | Persistent         |
| Fast              | Slightly slower    |
| Lost on crash     | Survives restart   |
| Node process only | Distributed        |

Examples:

* EventEmitter
* Redis Pub/Sub
* RabbitMQ
* Apache Kafka

---

## Interview Answer

> EventEmitter is suitable for communication inside a single Node.js process, while message queues are used for communication across multiple services or servers.

---

# Frequently Asked Interview Questions

### Q1: Are EventEmitter listeners synchronous or asynchronous?

**Answer:** Synchronous by default.

---

### Q2: What happens if an error event has no listener?

**Answer:** Node crashes with an uncaught error.

---

### Q3: Why does MaxListenersExceededWarning occur?

**Answer:** More than 10 listeners attached to the same event, indicating a potential memory leak.

---

### Q4: What design pattern does EventEmitter implement?

**Answer:** Publish-Subscribe (Pub/Sub).

---

### Q5: Why extend EventEmitter?

**Answer:** To create reusable classes that emit custom domain events.

---

### Q6: EventEmitter vs RabbitMQ?

**Answer:** EventEmitter is in-process; RabbitMQ is distributed and persistent.

---

# Quick Revision Notes

```text
EventEmitter stores listeners internally.

emit() executes listeners synchronously.

error event without listener
→ Process crash

Default max listeners
→ 10

setMaxListeners()
→ Change limit

Memory leak
→ Repeated listener registration

prependListener()
→ Execute first

removeAllListeners()
→ Remove listeners

Custom classes can extend EventEmitter.

EventEmitter implements Pub/Sub.

EventEmitter = Same Process

RabbitMQ/Kafka = Distributed Systems
```

---

## Next Phase 8: Process & Timers

We'll cover:

1. Timers (`setTimeout`, `setInterval`, `setImmediate`)
2. `process.nextTick()`
3. Timer internals
4. `unref()` and `ref()`
5. Process lifecycle events
6. `beforeExit`, `exit`
7. `uncaughtException`
8. `unhandledRejection`
9. Graceful shutdown in production
10. PM2, Docker, Kubernetes interview questions.
