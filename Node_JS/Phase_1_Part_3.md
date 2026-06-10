# Phase 1 (Part 3): Global Objects & Process Object

These are small topics, but interviewers frequently ask them because they're used in almost every Node.js project.

---

# 1. What are Global Objects?

Global objects are available everywhere in your Node.js application without importing them.

Example:

```js
console.log("Hello");
```

You didn't import `console`, yet it works because it's globally available.

---

## Common Global Objects

```js
global
process
console
setTimeout
setInterval
setImmediate
__dirname
__filename
Buffer
```

---

# 2. What is `global`?

In browsers, the global object is:

```js
window
```

In Node.js:

```js
global
```

Example:

```js
global.myName = "Jay";

console.log(global.myName);
```

Output:

```text
Jay
```

---

### Interview Question

### Q: What is the difference between `window` and `global`?

**Answer:**

* `window` is the global object in browsers.
* `global` is the global object in Node.js.

---

# 3. What is `__dirname`?

Returns the absolute path of the current directory.

Example:

Project Structure:

```text
project
│
├── app.js
```

app.js

```js
console.log(__dirname);
```

Output:

```text
C:\project
```

---

### Real Use Case

Reading files:

```js
const path = require("path");

const filePath = path.join(__dirname, "data.txt");
```

This works regardless of where the application is started from.

---

### Interview Answer

> `__dirname` returns the absolute path of the directory containing the currently executing file.

---

# 4. What is `__filename`?

Returns the absolute path of the current file.

Example:

```js
console.log(__filename);
```

Output:

```text
C:\project\app.js
```

---

### Difference

```text
__dirname  → Folder Path
__filename → File Path
```

Example:

```text
C:\project           ← __dirname
C:\project\app.js    ← __filename
```

---

# 5. What is the Process Object?

The `process` object provides information about the currently running Node.js process.

Example:

```js
console.log(process);
```

It contains:

* Environment Variables
* Process ID
* Memory Usage
* Current Working Directory
* Node Version
* Command Line Arguments

---

### Interview Answer

> The process object is a global object that provides information and control over the current Node.js process.

---

# 6. `process.pid`

Returns process ID.

Example:

```js
console.log(process.pid);
```

Output:

```text
12345
```

Every running Node process gets a unique PID.

---

# 7. `process.version`

Returns Node.js version.

Example:

```js
console.log(process.version);
```

Output:

```text
v22.0.0
```

---

# 8. `process.platform`

Returns OS platform.

Example:

```js
console.log(process.platform);
```

Output:

```text
win32
```

Possible values:

```text
win32
linux
darwin
```

(`darwin` = macOS)

---

# 9. `process.cwd()`

CWD = Current Working Directory

Example:

```js
console.log(process.cwd());
```

Output:

```text
C:\project
```

---

## Important Difference

### `process.cwd()`

Returns where Node was started.

### `__dirname`

Returns location of current file.

---

Example:

```text
project
│
├── src
│   └── app.js
```

Running:

```bash
node src/app.js
```

Output:

```js
console.log(process.cwd());
```

```text
project
```

Output:

```js
console.log(__dirname);
```

```text
project/src
```

---

### Interview Question

### Q: Difference between `process.cwd()` and `__dirname`?

**Answer:**

* `process.cwd()` returns the directory where Node was started.
* `__dirname` returns the directory of the current file.

---

# 10. Environment Variables

Very important for backend interviews.

Environment variables store configuration outside code.

Examples:

```text
DATABASE_URL
JWT_SECRET
PORT
API_KEY
```

---

## Access Environment Variables

```js
console.log(process.env.PORT);
```

---

Example:

```env
PORT=5000
```

Code:

```js
console.log(process.env.PORT);
```

Output:

```text
5000
```

---

## Why Use Environment Variables?

Bad:

```js
const password = "mySecretPassword";
```

Good:

```js
const password = process.env.DB_PASSWORD;
```

Secrets stay outside source code.

---

### Interview Answer

> Environment variables are used to store configuration and sensitive information such as database credentials, API keys, and JWT secrets outside the application code.

---

# 11. Command Line Arguments

Node.js can receive arguments.

Example:

```bash
node app.js Jay
```

Code:

```js
console.log(process.argv);
```

Output:

```js
[
  'node',
  'app.js',
  'Jay'
]
```

---

Access user argument:

```js
console.log(process.argv[2]);
```

Output:

```text
Jay
```

---

### Real Use Case

```bash
node app.js production
```

```js
const env = process.argv[2];

console.log(env);
```

Output:

```text
production
```

---

# 12. Exiting Process

Terminate application.

Example:

```js
process.exit();
```

---

Exit with success:

```js
process.exit(0);
```

---

Exit with error:

```js
process.exit(1);
```

---

### Standard Convention

```text
0 → Success
1 → Failure
```

---

# 13. Memory Usage

Check application memory.

Example:

```js
console.log(process.memoryUsage());
```

Output:

```js
{
  rss: 30000000,
  heapTotal: 10000000,
  heapUsed: 5000000
}
```

---

### Why Important?

Used for:

* Performance monitoring
* Memory leak detection
* Production debugging

---

# 14. Process Events

Node can listen to process events.

Example:

```js
process.on("exit", () => {
  console.log("Application Closing");
});
```

---

Output:

```text
Application Closing
```

before application exits.

---

# 15. Graceful Shutdown (Important)

Real-world servers should not abruptly stop.

Bad:

```text
Server Stops
Active Requests Lost
```

---

Good:

```text
Receive Signal
Finish Requests
Close Database
Exit
```

---

Example:

```js
process.on("SIGINT", () => {
  console.log("Shutting Down");

  process.exit(0);
});
```

Press:

```bash
Ctrl + C
```

Output:

```text
Shutting Down
```

---

### What is SIGINT?

Signal Interrupt.

Generated when:

```bash
Ctrl + C
```

is pressed.

---

## Common Signals

### SIGINT

```bash
Ctrl + C
```

---

### SIGTERM

Sent by:

* Docker
* Kubernetes
* PM2
* Linux Systems

when shutting down applications.

---

### Interview Answer

> Graceful shutdown ensures the application completes ongoing work, closes database connections, releases resources, and then exits safely when receiving signals like SIGINT or SIGTERM.

---

# Frequently Asked Interview Questions

### Q1: What are global objects in Node.js?

**Answer:**

Objects available throughout the application without importing, such as `global`, `process`, `console`, `Buffer`, and timers.

---

### Q2: What is `__dirname`?

**Answer:**

Returns the absolute path of the current file's directory.

---

### Q3: What is `__filename`?

**Answer:**

Returns the absolute path of the currently executing file.

---

### Q4: Difference between `process.cwd()` and `__dirname`?

**Answer:**

* `process.cwd()` → Directory where Node was started.
* `__dirname` → Directory of current file.

---

### Q5: What is `process.env`?

**Answer:**

An object containing environment variables used for configuration and secrets.

---

### Q6: Why use environment variables?

**Answer:**

To keep sensitive data like database credentials, API keys, and JWT secrets outside source code.

---

### Q7: What is graceful shutdown?

**Answer:**

A process of safely terminating an application by completing ongoing requests and releasing resources before exiting.

---

# Phase 1 Status

✅ Introduction to Node.js
✅ Why Node.js
✅ Features & Use Cases
✅ Runtime Environment
✅ V8 Engine
✅ JIT Compilation
✅ Node.js Architecture
✅ Event-Driven Architecture
✅ Non-Blocking I/O
✅ Libuv
✅ Thread Pool
✅ Global Objects
✅ Process Object
✅ Environment Variables
✅ Graceful Shutdown

**Phase 1 is now complete.**

Next, we'll start **Phase 2: Modules & Core Node APIs**, beginning with one of the most asked interview topics:

1. CommonJS (`require`, `module.exports`, `exports`)
2. ES Modules (`import`, `export`)
3. CommonJS vs ES Modules
4. Module Resolution
5. Circular Dependencies

These concepts appear in almost every Node.js interview.
