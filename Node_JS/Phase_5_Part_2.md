# Phase 5 – Part 2: `fs.promises`, File Descriptors, Stats Object & File Watching

This section contains topics that interviewers often ask after basic `fs` questions.

---

# 1. What is `fs.promises`?

### Problem with Callbacks

Traditional `fs` API:

```js
fs.readFile("user.txt", "utf8", (err, data) => {
  if (err) {
    console.log(err);
    return;
  }

  console.log(data);
});
```

Problems:

* Callback nesting
* Harder error handling
* Less readable

---

## Solution: `fs.promises`

Node provides a Promise-based File System API.

```js
const fs = require("fs").promises;
```

or

```js
const fs = require("fs/promises");
```

---

## Interview Answer

> fs.promises is a Promise-based version of the fs module that allows file operations using Promises and async/await instead of callbacks.

---

# 2. Reading Files with `fs.promises`

### Example

```js
const fs = require("fs/promises");

const data = await fs.readFile(
  "user.txt",
  "utf8"
);

console.log(data);
```

Returns:

```js
Promise<string>
```

---

## Equivalent Callback Version

```js
fs.readFile(
  "user.txt",
  "utf8",
  callback
);
```

---

# 3. Writing Files with `fs.promises`

```js
await fs.writeFile(
  "user.txt",
  "Hello World"
);
```

---

# 4. Deleting Files

```js
await fs.unlink("user.txt");
```

---

# 5. Renaming Files

```js
await fs.rename(
  "old.txt",
  "new.txt"
);
```

---

# 6. Using async/await with File Operations

### Example

```js
const fs = require("fs/promises");

async function main() {
  try {
    const data =
      await fs.readFile(
        "user.txt",
        "utf8"
      );

    console.log(data);
  } catch (err) {
    console.log(err);
  }
}

main();
```

---

## Interview Question

### Why use async/await?

Answer:

> Async/await makes asynchronous code look synchronous, improving readability and maintainability.

---

# 7. Multiple File Operations

### Sequential

```js
await fs.readFile("a.txt");
await fs.readFile("b.txt");
await fs.readFile("c.txt");
```

Execution:

```text
A
↓
B
↓
C
```

Slow.

---

### Parallel

```js
await Promise.all([
  fs.readFile("a.txt"),
  fs.readFile("b.txt"),
  fs.readFile("c.txt")
]);
```

Execution:

```text
A
B
C
```

Started together.

---

## Interview Question

### How to read multiple files efficiently?

Answer:

> Use Promise.all() to execute file operations concurrently instead of awaiting them one by one.

---

# 8. File Descriptors

This is a favorite interview topic.

---

## What is a File Descriptor?

A file descriptor is a numeric identifier used by the operating system to track an open file.

Example:

```text
3
4
5
```

Each number represents an opened file.

---

## Real Life Analogy

Imagine a library.

```text
File = Book

File Descriptor = Library Token
```

You use the token to access the book.

---

## Opening a File

```js
const fileHandle =
  await fs.open("user.txt");
```

---

### Returned Object

```js
FileHandle
```

Contains:

```js
fileHandle.fd
```

Example:

```text
17
```

The actual descriptor number.

---

## Closing a File

```js
await fileHandle.close();
```

---

## Why Close Files?

Open files consume OS resources.

Not closing them may cause:

```text
Memory leaks
Resource exhaustion
Performance issues
```

---

## Interview Answer

> A file descriptor is an OS-level identifier used to reference an open file. Node.js exposes it through FileHandle objects.

---

# 9. FileHandle Operations

### Open

```js
const handle =
  await fs.open("user.txt");
```

---

### Read

```js
const data =
  await handle.readFile("utf8");
```

---

### Write

```js
await handle.writeFile(
  "Hello"
);
```

---

### Close

```js
await handle.close();
```

---

## Best Practice

```js
const handle =
  await fs.open("user.txt");

try {
  const data =
    await handle.readFile();
} finally {
  await handle.close();
}
```

Always close files.

---

# 10. Stats Object

Used to retrieve file metadata.

---

## stat()

```js
const stats =
  await fs.stat("user.txt");
```

Returns:

```js
Stats
```

Object.

---

## Example

```js
console.log(stats);
```

Output:

```text
size
birthtime
mtime
ctime
mode
```

---

# Important Properties

## File Size

```js
stats.size
```

Example:

```text
1024
```

bytes.

---

## Created Time

```js
stats.birthtime
```

---

## Modified Time

```js
stats.mtime
```

---

## Is File?

```js
stats.isFile()
```

Returns:

```js
true
false
```

---

## Is Directory?

```js
stats.isDirectory()
```

Returns:

```js
true
false
```

---

## Example

```js
const stats =
  await fs.stat("user.txt");

console.log(
  stats.isFile()
);
```

Output:

```text
true
```

---

## Interview Question

### Difference between stat() and readFile()?

Answer:

```text
stat()
↓
Metadata

readFile()
↓
Actual content
```

---

# 11. File Watching

Very common interview topic.

---

## What is File Watching?

Monitoring a file or folder for changes.

Examples:

```text
VS Code
Webpack
Nodemon
```

All use file watching.

---

## fs.watch()

```js
fs.watch(
  "user.txt",
  (eventType, filename) => {
    console.log(eventType);
  }
);
```

---

### Possible Events

```text
change
rename
```

---

## Example

```js
fs.watch(
  "user.txt",
  (eventType) => {
    console.log(eventType);
  }
);
```

When file changes:

```text
change
```

---

# Watching Directories

```js
fs.watch(
  "./uploads",
  (event, file) => {
    console.log(
      event,
      file
    );
  }
);
```

---

## Real World Usage

### Nodemon

```text
File changed
↓
Restart server
```

---

### Webpack

```text
Source changed
↓
Rebuild bundle
```

---

### Logging Systems

```text
Log updated
↓
Process changes
```

---

# Limitations of fs.watch()

Interviewers sometimes ask this.

Problems:

```text
Platform dependent
Can miss events
Not always reliable
```

Because implementation differs across OSes.

---

## Better Alternative

Popular package:

```bash
npm install chokidar
```

Used by:

```text
Vite
Webpack
Nodemon
Many production tools
```

---

## Interview Question

### Why do many tools use Chokidar instead of fs.watch()?

Answer:

> fs.watch() behaves differently across operating systems and may miss events. Chokidar provides a more reliable and consistent file watching experience.

---

# Common Interview Questions

### Q1. Why use fs.promises?

**Answer**

> To use Promises and async/await instead of callbacks.

---

### Q2. What does fs.stat() return?

**Answer**

> A Stats object containing metadata such as file size, creation time, modification time, and file type information.

---

### Q3. What is a File Descriptor?

**Answer**

> A numeric OS-level identifier representing an open file.

---

### Q4. Why close FileHandles?

**Answer**

> To release operating system resources and prevent resource leaks.

---

### Q5. What is fs.watch() used for?

**Answer**

> It monitors files or directories and notifies the application when changes occur.

---

# Quick Revision Notes

### Promise-Based FS

```js
const fs =
 require("fs/promises");
```

---

### Read File

```js
await fs.readFile()
```

---

### Write File

```js
await fs.writeFile()
```

---

### Delete File

```js
await fs.unlink()
```

---

### Rename File

```js
await fs.rename()
```

---

### Metadata

```js
await fs.stat()
```

---

### Check File

```js
stats.isFile()
```

---

### Check Directory

```js
stats.isDirectory()
```

---

### Open File

```js
await fs.open()
```

---

### Close File

```js
await handle.close()
```

---

### Watch File

```js
fs.watch()
```

---

### Production Preference

```text
Callbacks
↓
Good

Promises + async/await
↓
Better
```

---

Next is **Phase 5 – Part 3: Large File Handling, Directory Operations, Recursive Operations, and Real Interview Scenarios**, where we'll cover topics that frequently appear in 2–5 years experience Node.js interviews.
