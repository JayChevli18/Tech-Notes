# Phase 2 - Part 4: Built-in Core Modules

Node.js comes with many built-in modules. These modules are already installed with Node.js, so you don't need npm to use them.

---

# 1. What are Core Modules?

Core modules are built-in modules provided by Node.js.

Example:

```js
const fs = require("fs");
const path = require("path");
const os = require("os");
```

No installation required.

---

### Interview Answer

> Core modules are built-in modules provided by Node.js that offer functionality such as file handling, path manipulation, operating system information, events, cryptography, and streams.

---

# 2. File System (fs)

Most frequently asked module in interviews.

Used for:

* Reading files
* Writing files
* Deleting files
* Creating folders

---

## Read File

### Async (Preferred)

```js
const fs = require("fs");

fs.readFile("data.txt", "utf8", (err, data) => {
  console.log(data);
});
```

Non-blocking.

---

### Sync

```js
const data = fs.readFileSync("data.txt", "utf8");
```

Blocking.

---

### Interview Question

**Q: Difference between readFile and readFileSync?**

**Answer:**

> readFile is asynchronous and non-blocking, while readFileSync is synchronous and blocks the event loop until the operation completes.

---

## Write File

```js
fs.writeFile("data.txt", "Hello", (err) => {
  console.log("Saved");
});
```

---

## Append File

```js
fs.appendFile("log.txt", "New Log\n", () => {});
```

---

## Delete File

```js
fs.unlink("data.txt", () => {});
```

---

## Create Folder

```js
fs.mkdir("uploads", () => {});
```

---

## Check File Exists

Modern approach:

```js
fs.access("data.txt", (err) => {
  if (!err) {
    console.log("Exists");
  }
});
```

---

# 3. Path Module

Used to work with file and folder paths.

---

## Join Paths

```js
const path = require("path");

const file = path.join("uploads", "users", "image.png");
```

Output:

```text
uploads/users/image.png
```

(Correct separator for OS)

---

## Get Extension

```js
path.extname("photo.jpg");
```

Output:

```text
.jpg
```

---

## Get File Name

```js
path.basename("/users/jay/file.txt");
```

Output:

```text
file.txt
```

---

## Get Directory

```js
path.dirname("/users/jay/file.txt");
```

Output:

```text
/users/jay
```

---

### Interview Question

**Q: Why use path.join instead of string concatenation?**

**Answer:**

> path.join automatically uses the correct path separator for the operating system and avoids path formatting issues.

---

# 4. OS Module

Provides operating system information.

---

## Platform

```js
const os = require("os");

console.log(os.platform());
```

Output:

```text
win32
linux
darwin
```

---

## CPU Information

```js
os.cpus();
```

---

## Free Memory

```js
os.freemem();
```

---

## Total Memory

```js
os.totalmem();
```

---

## Hostname

```js
os.hostname();
```

---

### Interview Answer

> The OS module provides information about the operating system such as CPU details, memory usage, hostname, and platform.

---

# 5. Events Module

Very important.

Node.js is event-driven.

---

## EventEmitter

```js
const EventEmitter = require("events");

const emitter = new EventEmitter();
```

---

## Listen Event

```js
emitter.on("login", () => {
  console.log("User Logged In");
});
```

---

## Trigger Event

```js
emitter.emit("login");
```

Output:

```text
User Logged In
```

---

## Pass Data

```js
emitter.on("login", (user) => {
  console.log(user);
});

emitter.emit("login", "Jay");
```

Output:

```text
Jay
```

---

### Interview Question

**Q: Difference between on() and emit()?**

**Answer:**

> on() registers an event listener, while emit() triggers the event and executes its listeners.

---

# 6. Crypto Module

Used for:

* Password hashing
* Encryption
* Tokens
* Random values

---

## Generate Random Bytes

```js
const crypto = require("crypto");

crypto.randomBytes(16).toString("hex");
```

---

## Hash Data

```js
crypto
  .createHash("sha256")
  .update("password")
  .digest("hex");
```

---

Output:

```text
5e884898...
```

---

### Important Interview Point

Never store plain passwords.

Use:

```text
bcrypt
argon2
```

instead of simple hashing.

---

### Interview Answer

> The crypto module provides cryptographic functionality such as hashing, encryption, random value generation, and secure token creation.

---

# 7. Buffer Module

Very commonly asked.

A Buffer stores binary data.

JavaScript normally works with text.

Buffers allow handling:

```text
Images
Videos
PDFs
Network packets
```

---

## Create Buffer

```js
const buffer = Buffer.from("Hello");
```

---

## Convert Back

```js
buffer.toString();
```

Output:

```text
Hello
```

---

## Buffer Example

```js
console.log(Buffer.from("ABC"));
```

Output:

```text
<Buffer 41 42 43>
```

---

### Interview Question

**Q: Why do we need Buffers?**

**Answer:**

> Buffers allow Node.js to handle binary data efficiently because JavaScript does not natively support raw binary data manipulation.

---

# 8. Streams

One of the most important interview topics.

---

## Problem

Suppose file size:

```text
5 GB
```

Reading entire file:

```js
fs.readFile()
```

Loads everything into memory.

Bad idea.

---

## Stream Solution

Read small chunks.

```text
Chunk 1
Chunk 2
Chunk 3
Chunk 4
```

Much more memory efficient.

---

## Read Stream

```js
const stream = fs.createReadStream("largeFile.txt");

stream.on("data", (chunk) => {
  console.log(chunk);
});
```

---

### Stream Types

#### Readable

```js
fs.createReadStream()
```

Read data.

---

#### Writable

```js
fs.createWriteStream()
```

Write data.

---

#### Duplex

Can read and write.

Example:

```text
TCP Socket
```

---

#### Transform

Modify data while streaming.

Example:

```text
Compression
Encryption
```

---

### Interview Answer

> Streams process data in chunks rather than loading everything into memory, making them ideal for large files and real-time data processing.

---

# 9. Util Module

Provides helper utilities.

---

## Promisify

Convert callback code into Promise-based code.

Old:

```js
fs.readFile("data.txt", (err, data) => {});
```

---

New:

```js
const util = require("util");

const readFile = util.promisify(fs.readFile);

const data = await readFile("data.txt");
```

---

### Interview Question

**Q: Why use util.promisify()?**

**Answer:**

> It converts callback-based functions into Promise-based functions, allowing the use of async/await.

---

# 10. Timers Module

Actually available globally.

---

## setTimeout

```js
setTimeout(() => {
  console.log("Hello");
}, 1000);
```

---

## setInterval

```js
setInterval(() => {
  console.log("Running");
}, 1000);
```

---

## clearTimeout

```js
const id = setTimeout(() => {}, 1000);

clearTimeout(id);
```

---

### Important Interview Question

**Q: Does setTimeout(fn, 1000) execute exactly after 1 second?**

**Answer:**

> No. It executes after at least 1 second and only when the call stack is empty.

---

# Most Asked Core Module Interview Questions

### Q1: Which Node.js module is used for file operations?

**Answer:**

```js
fs
```

---

### Q2: Which module provides OS information?

**Answer:**

```js
os
```

---

### Q3: Why use Streams?

**Answer:**

> Streams process data in chunks, reducing memory usage and improving performance.

---

### Q4: What is EventEmitter?

**Answer:**

> EventEmitter is a class from the events module used to create and handle custom events.

---

### Q5: What is a Buffer?

**Answer:**

> A Buffer is a temporary memory area used to store and manipulate binary data.

---

### Q6: What is util.promisify?

**Answer:**

> It converts callback-based functions into Promise-based functions.

---

# Quick Revision Notes

### fs

```text
Read files
Write files
Delete files
Create folders
```

---

### path

```text
join()
basename()
dirname()
extname()
```

---

### os

```text
platform()
cpus()
freemem()
totalmem()
```

---

### events

```text
on()
emit()
EventEmitter
```

---

### crypto

```text
Hashing
Encryption
Random values
```

---

### buffer

```text
Stores binary data
```

---

### streams

```text
Readable
Writable
Duplex
Transform
```

---

### util

```text
promisify()
```

---

### timers

```text
setTimeout()
setInterval()
clearTimeout()
```

---

## Next Topic (Phase 3 - Part 1)

**Asynchronous JavaScript Deep Dive**

This is one of the most important interview sections. We'll cover:

* Synchronous vs Asynchronous
* Callback Functions
* Callback Hell
* Promises
* Promise States
* Promise Chaining
* async/await
* Event Loop
* Microtask Queue
* Macrotask Queue
* `process.nextTick()`
* `setImmediate()`
* Execution Order Interview Questions
* Tricky Output-Based Questions

These topics are asked in almost every Node.js interview from 0–5 years experience.
