# Phase 12: Child Processes (Interview-Focused)

## Why Child Processes Exist

Node.js is single-threaded for JavaScript execution.

If you run a CPU-heavy task like:

* Image processing
* Video transcoding
* PDF generation
* Large file compression
* Data analytics

the event loop gets blocked.

To avoid this, Node can create **child processes**.

---

# Child Process Module

```js
const childProcess = require("child_process");
```

Provides:

* `exec()`
* `execFile()`
* `spawn()`
* `fork()`

---

# 1. exec()

## What is exec()?

Runs a command inside a shell and buffers the entire output in memory.

```js
const { exec } = require("child_process");

exec("dir", (err, stdout, stderr) => {
  console.log(stdout);
});
```

Linux:

```js
exec("ls -la");
```

---

## How It Works

```text
Command
   ↓
Shell
   ↓
Process
   ↓
Entire Output Stored
   ↓
Callback
```

---

## Advantages

Easy to use.

---

## Disadvantages

Entire output is stored in memory.

Bad for large outputs.

---

## Interview Question

### When should you avoid exec()?

> Avoid exec() when command output can become large because exec buffers the entire output into memory.

---

# 2. execFile()

## What is execFile()?

Runs a file directly without creating a shell.

```js
const { execFile } = require("child_process");

execFile(
  "node",
  ["app.js"],
  (err, stdout) => {
    console.log(stdout);
  }
);
```

---

## Why Use It?

More secure.

More efficient.

No shell involved.

---

## Example

```js
execFile(
  "python",
  ["script.py"]
);
```

---

## Interview Answer

### exec() vs execFile()

| exec()          | execFile()       |
| --------------- | ---------------- |
| Uses shell      | No shell         |
| Less secure     | More secure      |
| Slightly slower | Faster           |
| Command strings | Executable files |

---

# 3. spawn()

## What is spawn()?

Creates a process and streams data.

Best for huge outputs.

```js
const { spawn } = require("child_process");

const child = spawn("node", ["app.js"]);
```

---

## Reading Output

```js
child.stdout.on("data", (data) => {
  console.log(data.toString());
});
```

---

## How It Works

```text
Process
   ↓
Chunk
   ↓
Chunk
   ↓
Chunk
```

Instead of:

```text
Process
   ↓
Entire Output
```

---

## Why spawn() Is Better For Large Data

Example:

```bash
10GB log file
```

`exec()`:

```text
10GB Memory Usage
```

Potential crash.

---

`spawn()`:

```text
Chunk by Chunk
```

Memory efficient.

---

## Common Uses

* Video processing
* ffmpeg
* Database backups
* Large exports
* Streaming logs

---

## Interview Question

### spawn() vs exec()

| spawn()          | exec()       |
| ---------------- | ------------ |
| Streaming        | Buffered     |
| Large output     | Small output |
| Memory efficient | Memory heavy |
| Faster           | Simpler      |

---

# 4. fork()

## What is fork()?

Special version of `spawn()` designed specifically for Node.js processes.

Creates another Node instance.

---

### parent.js

```js
const { fork } = require("child_process");

const child = fork("./worker.js");
```

---

### worker.js

```js
console.log("Worker Started");
```

---

## Why fork() Exists

Node adds:

```text
Parent
  ↕
Built-in IPC Channel
  ↕
Child
```

making communication easy.

---

## Interview Answer

### fork() vs spawn()

| fork()               | spawn()              |
| -------------------- | -------------------- |
| Node.js only         | Any process          |
| IPC built-in         | No built-in IPC      |
| Easier communication | Generic              |
| Used for workers     | Used for any command |

---

# IPC (Inter-Process Communication)

## What is IPC?

Communication between processes.

---

### Parent Process

```js
const { fork } = require("child_process");

const child = fork("./worker.js");

child.send("Hello Child");
```

---

### Child Process

```js
process.on("message", (msg) => {
  console.log(msg);
});
```

Output:

```text
Hello Child
```

---

## Child → Parent

### Child

```js
process.send("Done");
```

---

### Parent

```js
child.on("message", (msg) => {
  console.log(msg);
});
```

---

## IPC Flow

```text
Parent
  ↓
send()
  ↓
Child

Child
  ↓
send()
  ↓
Parent
```

---

# Real-World Example

Suppose user uploads a large CSV.

Without child process:

```text
Node Server
   ↓
Parse CSV
   ↓
Event Loop Blocked
```

Users experience delays.

---

With child process:

```text
Node Server
   ↓
Fork Worker
   ↓
Worker Parses CSV
   ↓
Result Returned
```

Main server remains responsive.

---

# Common Use Cases

## Image Processing

```text
Sharp
ImageMagick
```

---

## Video Processing

```text
ffmpeg
```

---

## PDF Generation

```text
Puppeteer
```

---

## File Compression

```text
zip
tar
gzip
```

---

## Running Python Scripts

```js
spawn("python", ["script.py"]);
```

---

## Background Jobs

```text
Report Generation
Invoice Generation
Email Processing
```

---

# Child Process Lifecycle Events

## exit

```js
child.on("exit", (code) => {
  console.log(code);
});
```

---

## close

```js
child.on("close", (code) => {
  console.log(code);
});
```

---

## error

```js
child.on("error", (err) => {
  console.log(err);
});
```

---

# Most Asked Interview Questions

### Why do we need child processes in Node.js?

> To run CPU-intensive tasks or external programs without blocking the event loop.

---

### Difference between exec and spawn?

> exec buffers the entire output in memory, while spawn streams output incrementally.

---

### When should you use spawn?

> For long-running processes or commands producing large output.

---

### What is fork?

> A Node.js-specific child process that includes built-in IPC communication.

---

### What is IPC?

> IPC (Inter-Process Communication) is a mechanism that allows parent and child processes to exchange messages.

---

### Can child processes share memory?

> No. Each process has its own memory space. Communication happens through IPC.

---

# Important Interview Follow-Up: Child Process vs Worker Threads

Many interviewers ask this immediately after child processes.

| Child Process             | Worker Thread                 |
| ------------------------- | ----------------------------- |
| Separate process          | Same process                  |
| Separate memory           | Shared memory possible        |
| Higher memory usage       | Lower memory usage            |
| Can run external programs | JS execution only             |
| IPC required              | Direct communication possible |
| Better isolation          | Better performance            |

### Interview Answer

> Use child processes when you need to run external programs or want strong isolation. Use worker threads for CPU-intensive JavaScript computations because they are lighter and more memory efficient.

---

# 2-Minute Revision

```text
child_process module

exec()
- Runs command in shell
- Buffers output
- Good for small output

execFile()
- Runs executable directly
- No shell
- More secure

spawn()
- Streams output
- Memory efficient
- Best for large output

fork()
- Creates Node.js child process
- Built-in IPC

IPC
- child.send()
- process.send()
- message event

Use Cases
- Image processing
- Video processing
- Python scripts
- CSV parsing
- Report generation

Interview Favorite:
spawn vs exec
fork vs spawn
Child Process vs Worker Thread
```

The next phase should be **Phase 13: Worker Threads & Clustering**, which is one of the highest-frequency Node.js interview topics for senior MERN/backend roles.
