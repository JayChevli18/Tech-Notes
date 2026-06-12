# Phase 13: Worker Threads (Interview-Focused)

## Why Worker Threads Exist

Node.js is excellent for I/O operations because of its event loop.

But CPU-intensive tasks can block the event loop:

```js
for(let i = 0; i < 1_000_000_000; i++) {
  // heavy computation
}
```

While this runs:

* API requests wait
* Responses get delayed
* Entire application feels slow

Worker Threads solve this problem.

---

# What Are Worker Threads?

Worker Threads allow JavaScript code to run in parallel on separate threads.

Introduced to handle CPU-bound workloads.

Module:

```js
const { Worker } = require("worker_threads");
```

---

## Main Thread vs Worker Thread

```text
Main Thread
     │
     ├── Worker 1
     │
     ├── Worker 2
     │
     └── Worker 3
```

Each worker:

* Has its own event loop
* Executes JS independently
* Doesn't block the main thread

---

# Basic Example

## main.js

```js
const { Worker } = require("worker_threads");

const worker = new Worker("./worker.js");

worker.on("message", (result) => {
  console.log(result);
});
```

---

## worker.js

```js
const { parentPort } = require("worker_threads");

let sum = 0;

for(let i = 0; i < 1e9; i++) {
  sum += i;
}

parentPort.postMessage(sum);
```

---

## Flow

```text
Main Thread
     │
Create Worker
     │
Worker Computes
     │
Send Result
     │
Main Thread Continues
```

---

# Message Passing

Workers communicate using messages.

Similar to browser Web Workers.

---

## Parent → Worker

### main.js

```js
worker.postMessage(100);
```

---

### worker.js

```js
parentPort.on("message", (data) => {
  console.log(data);
});
```

Output:

```text
100
```

---

## Worker → Parent

### worker.js

```js
parentPort.postMessage("Done");
```

---

### main.js

```js
worker.on("message", (msg) => {
  console.log(msg);
});
```

---

## Communication Flow

```text
Main Thread
      │
postMessage()
      │
Worker

Worker
      │
postMessage()
      │
Main Thread
```

---

# Shared Memory

Normally messages are copied between threads.

Copying large data can be expensive.

Example:

```js
100MB Array
```

Copying it repeatedly hurts performance.

---

## SharedArrayBuffer

Allows multiple threads to access the same memory.

```js
const sharedBuffer =
  new SharedArrayBuffer(4);
```

---

## Example

```js
const shared =
  new SharedArrayBuffer(4);

const arr =
  new Int32Array(shared);

arr[0] = 100;
```

Both worker and main thread can access:

```js
arr[0]
```

without copying.

---

## Why Shared Memory Matters

Instead of:

```text
Thread A
   ↓ Copy
Thread B
```

You get:

```text
Thread A
     ↓
Shared Memory
     ↑
Thread B
```

Much faster.

---

# Atomics

When multiple threads modify shared memory:

```text
Worker A
Worker B
```

Race conditions can occur.

Node provides:

```js
Atomics
```

for safe synchronization.

Example:

```js
Atomics.add(arr, 0, 1);
```

---

## Interview Answer

### Why do we need Atomics?

> Atomics prevent race conditions when multiple worker threads access shared memory simultaneously.

---

# Thread Pools

Very common interview topic.

---

## Misconception

Many developers think:

```text
Node = Single Thread
```

Not entirely true.

---

## Libuv Thread Pool

Node already has a built-in thread pool.

Used for:

* File system operations
* DNS lookups
* Crypto operations
* Compression

Default size:

```text
4 Threads
```

Can be changed:

```bash
UV_THREADPOOL_SIZE=8
```

---

## Example

```js
fs.readFile(...)
```

does not run on the JS thread.

It goes to Libuv's thread pool.

---

## Worker Threads vs Thread Pool

### Thread Pool

Used internally by Node.

```text
Developer
    ↓
Node Uses Pool Automatically
```

---

### Worker Threads

Created manually.

```text
Developer
     ↓
Creates Workers
```

---

# Real-World Use Cases

## Image Processing

```text
Resize
Crop
Compress
```

---

## Video Encoding

```text
FFmpeg
Transcoding
```

---

## PDF Generation

```text
Large reports
```

---

## Data Analytics

```text
Millions of records
```

---

## Machine Learning

```text
Predictions
Tensor operations
```

---

## Cryptocurrency

```text
Hashing
Mining calculations
```

---

# Worker Threads vs Child Processes

This is one of the most asked interview questions.

| Worker Thread          | Child Process         |
| ---------------------- | --------------------- |
| Same process           | Separate process      |
| Lightweight            | Heavyweight           |
| Faster startup         | Slower startup        |
| Shared memory possible | No shared memory      |
| Less memory            | More memory           |
| JS computation         | External programs too |

---

## Visual

### Worker Thread

```text
Node Process
     │
     ├── Thread 1
     ├── Thread 2
     └── Thread 3
```

---

### Child Process

```text
Node Process
     │
     ├── Process A
     ├── Process B
     └── Process C
```

Each process has its own memory.

---

# When to Use Worker Threads

Use when:

✅ CPU-intensive JavaScript

✅ Parallel computation

✅ Large calculations

✅ Data processing

---

Examples:

```text
Image processing
Data analytics
Hashing
ML calculations
```

---

# When to Use Child Processes

Use when:

✅ Running Python

✅ Running FFmpeg

✅ Running shell commands

✅ Strong isolation required

---

Examples:

```text
Python scripts
Bash scripts
External executables
```

---

# Most Asked Interview Questions

### Why were Worker Threads introduced?

> To handle CPU-intensive JavaScript workloads without blocking the event loop.

---

### Do Worker Threads share memory?

> Yes. Using SharedArrayBuffer and Atomics.

---

### Are Worker Threads truly parallel?

> Yes. They run on separate OS threads and can execute simultaneously on multiple CPU cores.

---

### Worker Thread vs Child Process?

> Worker Threads are lighter and better for CPU-intensive JavaScript. Child Processes are better when running external programs or when stronger isolation is required.

---

### Does Node already use threads?

> Yes. Libuv uses a thread pool internally for operations like file system access, crypto, DNS lookups, and compression.

---

### Can Worker Threads access variables from the main thread?

> No. Each thread has its own JavaScript context. Data must be passed through messages or shared memory.

---

# The Interview Favorite Question

### "Your Node.js API becomes slow while generating a huge PDF. How would you fix it?"

Expected answer:

> PDF generation is CPU-intensive and can block the event loop. I would move that work to a Worker Thread so the main thread remains responsive to incoming requests. If the PDF generation relies on an external tool, I might use a Child Process instead.

---

# 2-Minute Revision

```text
Worker Threads
- Parallel JavaScript execution
- Separate OS threads
- Prevent event loop blocking

Communication
- postMessage()
- message event

Shared Memory
- SharedArrayBuffer
- Atomics

Libuv Thread Pool
- Internal Node thread pool
- Default size = 4
- Used for fs, crypto, DNS

Worker Thread
- Lightweight
- CPU-intensive JS

Child Process
- Heavyweight
- External programs

Interview Favorite:
Worker Thread vs Child Process
SharedArrayBuffer
Libuv Thread Pool
CPU-intensive task handling
```

The next logical phase is **Phase 14: Clustering & Scaling Node.js Applications**, where interviewers typically ask: *"Your server runs on an 8-core machine. How do you utilize all CPU cores?"* That's where clustering comes in.
