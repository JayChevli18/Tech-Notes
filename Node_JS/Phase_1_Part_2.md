# Phase 1 (Part 2): Node.js Architecture

This section is extremely important because interviewers often ask:

> "If Node.js is single-threaded, how does it handle thousands of requests simultaneously?"

To answer that properly, you need to understand Node.js architecture.

---

# 1. High-Level Node.js Architecture

Node.js consists of:

```text
┌─────────────────────┐
│ Your JavaScript Code│
└──────────┬──────────┘
           │
           ▼
┌─────────────────────┐
│     V8 Engine       │
└──────────┬──────────┘
           │
           ▼
┌─────────────────────┐
│     Node APIs       │
│ (fs, http, crypto)  │
└──────────┬──────────┘
           │
           ▼
┌─────────────────────┐
│       Libuv         │
└──────────┬──────────┘
           │
           ▼
┌─────────────────────┐
│ Event Loop & OS     │
└─────────────────────┘
```

### Components

1. V8 Engine
2. Node APIs
3. Libuv
4. Event Loop
5. Operating System

---

# 2. What is V8 Engine?

V8 is the JavaScript engine used by Node.js.

Its job:

```text
JavaScript
      ↓
V8 Engine
      ↓
Machine Code
      ↓
CPU
```

Example:

```js
const sum = 10 + 20;
```

V8 converts this into machine code that the CPU understands.

---

### What V8 Does

* Executes JavaScript
* Handles Memory Allocation
* Garbage Collection
* JIT Compilation

---

### What V8 Does NOT Do

V8 cannot:

```js
read files
create servers
access databases
make OS calls
```

These capabilities come from Node.js APIs.

---

# 3. Node.js APIs

Node provides built-in modules:

```js
fs
http
path
crypto
os
```

Example:

```js
const fs = require("fs");

fs.readFile("data.txt", () => {
  console.log("File Read");
});
```

JavaScript itself cannot read files.

Node.js provides this functionality.

---

# 4. What is Event-Driven Architecture?

Node.js works based on events.

Real-world example:

```text
Doorbell Rings
      ↓
Event Generated
      ↓
You Open Door
```

Node.js example:

```text
Request Arrives
      ↓
Event Generated
      ↓
Callback Executes
```

---

Example:

```js
const EventEmitter = require("events");

const emitter = new EventEmitter();

emitter.on("login", () => {
  console.log("User Logged In");
});

emitter.emit("login");
```

Output:

```text
User Logged In
```

---

### Interview Answer

> Event-driven architecture means the application responds to events. When an event occurs, Node.js executes the registered callback function.

---

# 5. What is Blocking I/O?

Suppose you're reading a large file.

Blocking code:

```js
const fs = require("fs");

const data = fs.readFileSync("largeFile.txt");

console.log(data);
console.log("Done");
```

Execution:

```text
Read File
Wait...
Wait...
File Complete
Done
```

Program cannot continue until file reading finishes.

This is called **Blocking I/O**.

---

# 6. What is Non-Blocking I/O?

Node.js prefers asynchronous operations.

Example:

```js
const fs = require("fs");

fs.readFile("largeFile.txt", () => {
  console.log("File Read");
});

console.log("Done");
```

Output:

```text
Done
File Read
```

Node doesn't wait.

It continues executing other tasks.

---

### Interview Answer

> Non-blocking I/O allows Node.js to continue executing other operations while waiting for I/O tasks like file reading, database queries, or network requests to complete.

---

# 7. Is Node.js Single Threaded?

This is one of the most common interview questions.

### Short Answer

✅ JavaScript execution is single-threaded.

But...

❌ The entire Node.js runtime is not single-threaded.

---

### Example

```js
console.log("A");
console.log("B");
console.log("C");
```

Execution:

```text
A
B
C
```

Only one statement executes at a time.

This is why JavaScript is called single-threaded.

---

# 8. Then How Does Node.js Handle Multiple Requests?

Suppose 1000 users call an API:

```text
1000 Requests
      ↓
Node Server
```

Many people think:

```text
1 Request = 1 Thread
```

That's how traditional servers often work.

Node.js works differently.

```text
1000 Requests
       ↓
 Event Loop
       ↓
 Handles Concurrently
```

This is why Node.js is highly scalable.

---

# 9. What is Libuv?

Libuv is the hidden hero of Node.js.

### Definition

Libuv is a C library responsible for:

* Event Loop
* Thread Pool
* Async Operations
* OS Interaction

---

### Responsibilities

#### File System Operations

```js
fs.readFile()
```

#### DNS Operations

```js
dns.lookup()
```

#### Crypto Operations

```js
crypto.pbkdf2()
```

#### Network Operations

```js
http.createServer()
```

All are managed through Libuv.

---

### Interview Answer

> Libuv is a C library used by Node.js to provide asynchronous I/O, thread pool management, and the event loop.

---

# 10. What is Thread Pool?

Node.js has a background worker pool.

Default size:

```text
4 Threads
```

Managed by Libuv.

---

Example:

```js
fs.readFile()
```

Flow:

```text
JavaScript
      ↓
Libuv
      ↓
Thread Pool
      ↓
Read File
      ↓
Callback
```

JavaScript thread remains free.

---

### Thread Pool Handles

* File System
* Crypto
* DNS

Examples:

```js
fs.readFile()
crypto.pbkdf2()
dns.lookup()
```

---

# 11. What is Concurrency?

### Concurrency

Multiple tasks making progress together.

Example:

```text
Cooking Food
Talking on Phone
Watching Oven
```

Not necessarily at the exact same instant.

---

Node.js achieves concurrency using:

```text
Event Loop
+
Callbacks
+
Libuv
```

---

# 12. Concurrency vs Parallelism

### Concurrency

```text
Task A
Task B
Task A
Task B
```

Tasks share time.

---

### Parallelism

```text
Task A    Task B
Running Simultaneously
```

Requires multiple CPU cores.

---

### Interview Answer

> Concurrency means managing multiple tasks at the same time, while parallelism means executing multiple tasks simultaneously using multiple CPU cores.

---

# 13. Why Node.js is Good for APIs?

Most API operations are:

```text
Database Queries
File Reads
HTTP Requests
Cache Reads
```

These are I/O-bound tasks.

Node.js excels at handling I/O-bound workloads because of its non-blocking architecture.

---

# 14. Why Node.js is Bad for CPU-Intensive Tasks?

Example:

```js
for(let i = 0; i < 10000000000; i++) {}
```

During this loop:

```text
Event Loop Blocked
```

No other request can be processed.

---

Examples:

* Video Encoding
* Image Processing
* AI Training
* Heavy Calculations

---

Solution:

* Worker Threads
* Child Processes

(We'll cover later.)

---

# Most Important Interview Question

### Q: If Node.js is single-threaded, how can it handle thousands of requests?

### Perfect Answer

> JavaScript execution in Node.js runs on a single thread, but Node.js uses Libuv, the Event Loop, and the operating system's asynchronous capabilities to handle I/O operations efficiently. Long-running I/O tasks are delegated to the OS or Libuv's thread pool, allowing the main thread to remain free and process other requests concurrently.

---

# Quick Revision Notes

### V8 Engine

* Executes JavaScript
* JIT Compilation
* Memory Management

### Node APIs

* fs
* http
* crypto
* path

### Event-Driven Architecture

* Responds to events using callbacks

### Blocking I/O

* Waits for task completion

### Non-Blocking I/O

* Continues execution while task runs

### Libuv

* Event Loop
* Thread Pool
* Async Operations

### Thread Pool

* Default: 4 threads
* Handles fs, crypto, DNS

### Concurrency

* Multiple tasks progress together

### Parallelism

* Multiple tasks execute simultaneously

### Why Node.js is Fast

* V8 Engine
* Event Loop
* Non-Blocking I/O
* Libuv

At this point, you've completed the **core architecture fundamentals** that interviewers ask most often. The next section in Phase 1 is **Global Objects and the `process` object**, where we'll cover `global`, `process`, `__dirname`, `__filename`, environment variables, process lifecycle, and frequently asked interview questions around them.
