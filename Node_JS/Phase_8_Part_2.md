# Phase 8: Process & Timers

# Part 2 — Advanced Process Object

The `process` object is one of the most important global objects in Node.js. Senior interviewers often ask questions around process lifecycle, memory monitoring, environment variables, and graceful shutdown.

---

# 1. What is the Process Object?

## Definition

`process` is a global object that provides information and control over the currently running Node.js process.

---

## Example

```js
console.log(process);
```

No import required.

---

## Interview Answer

> The process object is a global Node.js object that represents the currently running Node.js process and provides access to environment variables, command-line arguments, memory usage, process lifecycle events, and system information.

---

# 2. process.pid

## Definition

Returns the Process ID.

---

## Example

```js
console.log(process.pid);
```

Output:

```text
12345
```

---

## Why Useful?

* Monitoring
* Logging
* Debugging
* Process Management

---

## Interview Question

### Q:

What is a PID?

### Answer:

> PID stands for Process ID, which uniquely identifies a running process within the operating system.

---

# 3. process.ppid

## Definition

Parent Process ID.

---

## Example

```js
console.log(process.ppid);
```

---

## Example Scenario

```text
Terminal
   ↓
Node Process
```

Terminal becomes parent.

---

## Interview Answer

> process.ppid returns the process ID of the parent process that started the current Node.js process.

---

# 4. process.argv

## Definition

Command-line arguments.

---

## Example

```bash
node app.js Jay 25
```

---

Node receives:

```js
console.log(process.argv);
```

Output:

```js
[
 '/usr/bin/node',
 '/project/app.js',
 'Jay',
 '25'
]
```

---

## Access Arguments

```js
const name = process.argv[2];
const age = process.argv[3];
```

---

## Example

```js
console.log(`Hello ${name}`);
```

Run:

```bash
node app.js Jay
```

Output:

```text
Hello Jay
```

---

## Interview Answer

> process.argv is an array containing command-line arguments passed to the Node.js process.

---

# 5. process.env

## Definition

Environment variables.

---

## Example

```js
console.log(process.env.PORT);
```

---

## .env File

```env
PORT=5000
DB_URL=postgres://...
JWT_SECRET=mysecret
```

---

Using dotenv:

```js
require("dotenv").config();
```

---

Access:

```js
process.env.JWT_SECRET
```

---

## Why Use Environment Variables?

Never hardcode:

```js
const secret =
  "my-super-secret-key";
```

Bad practice.

---

Good:

```js
const secret =
  process.env.JWT_SECRET;
```

---

## Interview Answer

> Environment variables allow applications to store configuration and sensitive data separately from source code.

---

# 6. process.cwd()

## Definition

Current Working Directory.

---

## Example

```js
console.log(process.cwd());
```

Output:

```text
/project
```

---

## Difference from __dirname

Suppose:

```text
/project
 └── src
      └── app.js
```

---

### __dirname

```js
console.log(__dirname);
```

Output:

```text
/project/src
```

---

### process.cwd()

```js
console.log(process.cwd());
```

Output:

```text
/project
```

---

## Interview Answer

> process.cwd() returns the directory from which Node.js was started, whereas __dirname returns the directory of the current file.

---

# 7. process.uptime()

## Definition

How long the process has been running.

---

## Example

```js
console.log(process.uptime());
```

Output:

```text
152.3
```

(seconds)

---

## Real Use

Health monitoring.

---

## Interview Answer

> process.uptime() returns the number of seconds the current Node.js process has been running.

---

# 8. process.memoryUsage()

Very Popular Interview Question.

---

## Example

```js
console.log(
  process.memoryUsage()
);
```

Output:

```js
{
  rss: 55000000,
  heapTotal: 12000000,
  heapUsed: 9000000,
  external: 3000000,
  arrayBuffers: 200000
}
```

---

## Important Fields

### heapUsed

```js
memory.heapUsed
```

Actual memory currently used by JS objects.

---

### heapTotal

```js
memory.heapTotal
```

Memory allocated for V8 heap.

---

### rss

```js
memory.rss
```

Total memory used by process.

---

## Monitoring Example

```js
setInterval(() => {

  const memory =
    process.memoryUsage();

  console.log(
    memory.heapUsed / 1024 / 1024
  );

}, 5000);
```

---

## Interview Answer

> process.memoryUsage() provides detailed memory statistics including heap usage, total allocated heap, and overall process memory consumption.

---

# 9. process.hrtime()

High-resolution timer.

---

## Why?

Date.now() is not precise enough for benchmarking.

---

## Example

```js
const start =
  process.hrtime.bigint();

for(let i=0;i<1000000;i++) {}

const end =
  process.hrtime.bigint();

console.log(end - start);
```

Output:

```text
3521456n
```

Nanoseconds.

---

## Interview Answer

> process.hrtime() provides high-resolution timing suitable for benchmarking and performance measurement.

---

# 10. process.platform

Returns OS platform.

---

## Example

```js
console.log(process.platform);
```

Possible values:

```text
win32
linux
darwin
```

---

# 11. process.version

Returns Node version.

---

## Example

```js
console.log(process.version);
```

Output:

```text
v22.x.x
```

---

# 12. process.arch

Returns CPU architecture.

---

## Example

```js
console.log(process.arch);
```

Output:

```text
x64
```

---

# 13. process.exit()

Terminates process.

---

## Example

```js
process.exit(0);
```

---

## Exit Codes

Success:

```js
process.exit(0);
```

Error:

```js
process.exit(1);
```

---

## Common Exit Codes

```text
0 → Success

1 → Generic Error
```

---

## Interview Answer

> process.exit() immediately terminates the Node.js process and returns an exit code to the operating system.

---

# 14. Why process.exit() Can Be Dangerous

Example:

```js
console.log("Saving");

process.exit(0);
```

Pending operations may never complete.

---

Bad:

```js
await saveToDatabase();

process.exit(0);
```

Can still interrupt cleanup.

---

Prefer graceful shutdown.

---

# 15. Process Lifecycle Overview

```text
Start
 ↓
Execute Code
 ↓
Event Loop
 ↓
No Work Left
 ↓
Exit
```

---

# Frequently Asked Interview Questions

### Q1: Difference between process.cwd() and __dirname?

**Answer:**

```text
process.cwd()
→ Where node started

__dirname
→ Current file location
```

---

### Q2: Why use process.env?

**Answer:**

Store configuration and secrets outside source code.

---

### Q3: What does process.memoryUsage() return?

**Answer:**

Memory statistics such as heapUsed, heapTotal, and rss.

---

### Q4: What is process.hrtime() used for?

**Answer:**

Precise performance measurements and benchmarking.

---

### Q5: Difference between PID and PPID?

**Answer:**

```text
PID  → Current Process

PPID → Parent Process
```

---

### Q6: Why avoid frequent process.exit()?

**Answer:**

It can terminate the application before pending work finishes.

---

# Quick Revision Notes

```text
process
→ Current Node process

process.pid
→ Process ID

process.ppid
→ Parent Process ID

process.argv
→ Command-line arguments

process.env
→ Environment variables

process.cwd()
→ Current working directory

__dirname
→ Current file directory

process.uptime()
→ Process runtime

process.memoryUsage()
→ Memory statistics

heapUsed
→ Actual JS memory used

rss
→ Total process memory

process.hrtime()
→ High precision timer

process.platform
→ OS platform

process.version
→ Node version

process.arch
→ CPU architecture

process.exit(0)
→ Success

process.exit(1)
→ Error
```

---

## Next: Phase 8 — Part 3 (Process Lifecycle & Production Error Handling)

We'll cover the most important production interview topics:

* `beforeExit`
* `exit`
* `uncaughtException`
* `unhandledRejection`
* Signals (`SIGINT`, `SIGTERM`)
* Graceful Shutdown
* PM2 interview questions
* Docker & Kubernetes shutdown handling
* Real production examples.
