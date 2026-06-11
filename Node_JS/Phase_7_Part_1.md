# Phase 7 – Part 1: Events & EventEmitter Deep Dive

One of the most important things to understand about Node.js is:

> **Node.js is event-driven.**

Many core modules are built on events:

```text
HTTP Server
Streams
Process
Sockets
File System
```

All heavily use **EventEmitter** internally.

---

# 1. What is an Event?

An event is simply:

```text
Something Happened
```

Examples:

```text
User Logged In
File Uploaded
Request Received
Database Connected
Order Created
```

---

## Real Life Example

```text
Door Bell Rings
↓
Event Occurred
↓
Someone Opens Door
```

In Node:

```text
Event Occurred
↓
Listener Executes
```

---

## Interview Answer

> An event is a signal that indicates something has happened in the application and can trigger one or more listeners.

---

# 2. What is Event-Driven Architecture?

Instead of:

```text
Call Function Directly
```

We do:

```text
Emit Event
↓
Interested Listeners React
```

---

### Traditional Approach

```js
sendEmail();
updateAnalytics();
createLog();
```

Everything tightly coupled.

---

### Event-Driven Approach

```js
emit("userRegistered");
```

Listeners handle:

```text
Send Email
Update Analytics
Create Log
```

Independently.

---

## Benefits

### Loose Coupling

Modules don't depend directly on each other.

### Scalability

Easy to add new listeners.

### Maintainability

Cleaner architecture.

---

## Interview Answer

> Event-driven architecture allows components to communicate through events, reducing coupling and improving scalability.

---

# 3. What is EventEmitter?

Node provides:

```js
const EventEmitter =
  require("events");
```

---

### Definition

EventEmitter is a class used to create, emit, and listen to events.

---

## Interview Answer

> EventEmitter is a core Node.js class that enables event-driven programming by allowing objects to emit events and register listeners.

---

# 4. Creating an EventEmitter

```js
const EventEmitter =
  require("events");

const emitter =
  new EventEmitter();
```

Now:

```text
emitter
↓
Can Emit Events
Can Listen To Events
```

---

# 5. Listening to Events

Use:

```js
emitter.on()
```

---

### Example

```js
emitter.on(
  "login",
  () => {
    console.log(
      "User Logged In"
    );
  }
);
```

Listener registered.

Nothing executes yet.

---

# 6. Emitting Events

Use:

```js
emitter.emit()
```

---

### Example

```js
emitter.emit("login");
```

Output:

```text
User Logged In
```

---

### Flow

```text
emit("login")
       ↓
listener found
       ↓
execute callback
```

---

## Interview Question

### What happens if no listener exists?

```js
emitter.emit("login");
```

Result:

```text
Nothing
```

No error.

---

# 7. Passing Data with Events

Very common.

---

### Example

```js
emitter.on(
  "login",
  (user) => {
    console.log(
      user
    );
  }
);
```

Emit:

```js
emitter.emit(
  "login",
  "Jay"
);
```

Output:

```text
Jay
```

---

## Multiple Arguments

```js
emitter.emit(
  "orderCreated",
  101,
  500
);
```

Listener:

```js
emitter.on(
  "orderCreated",
  (id, amount) => {
    console.log(
      id,
      amount
    );
  }
);
```

Output:

```text
101 500
```

---

## Interview Answer

> EventEmitter allows arbitrary data to be passed to listeners through emit() arguments.

---

# 8. Multiple Listeners

Multiple callbacks can listen to the same event.

---

### Example

```js
emitter.on(
  "signup",
  () => {
    console.log(
      "Send Email"
    );
  }
);

emitter.on(
  "signup",
  () => {
    console.log(
      "Create Log"
    );
  }
);

emitter.on(
  "signup",
  () => {
    console.log(
      "Analytics"
    );
  }
);
```

Emit:

```js
emitter.emit("signup");
```

Output:

```text
Send Email
Create Log
Analytics
```

---

## Interview Question

### Can one event have multiple listeners?

Answer:

> Yes. EventEmitter supports multiple listeners for the same event and executes them in registration order.

---

# 9. once()

Runs only once.

---

### Example

```js
emitter.once(
  "connect",
  () => {
    console.log(
      "Connected"
    );
  }
);
```

---

Emit:

```js
emitter.emit("connect");
emitter.emit("connect");
emitter.emit("connect");
```

Output:

```text
Connected
```

Only first time.

---

## Real Usage

```text
Database Connected
Server Started
Application Initialized
```

One-time events.

---

## Interview Answer

> once() registers a listener that automatically removes itself after the first execution.

---

# 10. Removing Listeners

Use:

```js
emitter.off()
```

or

```js
emitter.removeListener()
```

---

### Example

```js
function login() {
  console.log("Login");
}

emitter.on(
  "login",
  login
);

emitter.off(
  "login",
  login
);
```

Listener removed.

---

Emit:

```js
emitter.emit("login");
```

Nothing happens.

---

# 11. listenerCount()

Check number of listeners.

---

### Example

```js
console.log(
  emitter.listenerCount(
    "login"
  )
);
```

Output:

```text
3
```

---

## Real Usage

Debugging.

---

# 12. eventNames()

Returns registered events.

---

### Example

```js
console.log(
  emitter.eventNames()
);
```

Output:

```js
[
  "login",
  "logout"
]
```

---

# 13. listeners()

Returns actual listener functions.

---

### Example

```js
console.log(
  emitter.listeners(
    "login"
  )
);
```

Useful for debugging.

---

# 14. Real-World Example

User Registration System

```js
emitter.on(
  "userRegistered",
  sendEmail
);

emitter.on(
  "userRegistered",
  createLog
);

emitter.on(
  "userRegistered",
  updateAnalytics
);
```

Later:

```js
emitter.emit(
  "userRegistered",
  user
);
```

Everything executes automatically.

---

### Architecture

```text
Register User
      ↓
Emit Event
      ↓
Email
      ↓
Logs
      ↓
Analytics
```

Clean separation.

---

# Common Interview Questions

### Q1. What is EventEmitter?

Answer:

> EventEmitter is a Node.js class used to emit and listen for events.

---

### Q2. Difference between on() and once()?

| on()                | once()        |
| ------------------- | ------------- |
| Executes every time | Executes once |
| Stays registered    | Auto removes  |

---

### Q3. Can one event have multiple listeners?

Answer:

> Yes.

---

### Q4. What happens if emit() is called with no listeners?

Answer:

> Nothing happens and no error is thrown.

---

### Q5. Why is EventEmitter important?

Answer:

> Many Node.js modules such as Streams, HTTP, Process, and Sockets are built on top of EventEmitter.

---

# Quick Revision Notes

### Create Emitter

```js
const EventEmitter =
  require("events");

const emitter =
  new EventEmitter();
```

---

### Listen

```js
emitter.on()
```

---

### Emit

```js
emitter.emit()
```

---

### One-Time Listener

```js
emitter.once()
```

---

### Remove Listener

```js
emitter.off()
```

---

### Count Listeners

```js
emitter.listenerCount()
```

---

### Event Names

```js
emitter.eventNames()
```

---

### Core Idea

```text
Emit Event
     ↓
Listener Executes
```

---

### Most Important Interview Answer

> EventEmitter implements the publish-subscribe pattern in Node.js, enabling loosely coupled communication between different parts of an application.

---

Next: **Phase 7 – Part 2: EventEmitter Internals, Error Events, Memory Leaks, Max Listeners, Custom EventEmitter Classes, and Advanced Event-Based Architecture Questions** — commonly asked in 2–5+ years Node.js interviews.
