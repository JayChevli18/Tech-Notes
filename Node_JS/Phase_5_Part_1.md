# Phase 5 – Part 1: File System (`fs`) Deep Dive

The `fs` module is one of the most important Node.js modules because almost every backend application works with files.

---

# 1. What is the `fs` Module?

### Definition

`fs` stands for **File System**.

It allows Node.js applications to interact with files and directories on the operating system.

### Why was it created?

Without `fs`, Node.js wouldn't be able to:

* Read files
* Write files
* Create folders
* Delete files
* Rename files
* Manage file metadata

### Importing fs

CommonJS:

```js
const fs = require("fs");
```

ES Modules:

```js
import fs from "fs";
```

---

## Interview Answer

> The fs module is a built-in Node.js module used for interacting with the operating system's file system. It provides methods to read, write, update, delete, and manage files and directories.

---

# 2. Sync vs Async File Operations

Node.js provides two versions of most file operations.

### Synchronous

```js
fs.readFileSync()
```

### Asynchronous

```js
fs.readFile()
```

---

## Synchronous Example

```js
const data = fs.readFileSync("test.txt", "utf8");

console.log(data);
console.log("Finished");
```

Execution:

```text
Read file
↓
Print file
↓
Finished
```

Node waits until file reading completes.

---

## Asynchronous Example

```js
fs.readFile("test.txt", "utf8", (err, data) => {
  console.log(data);
});

console.log("Finished");
```

Output:

```text
Finished
File Content
```

---

## Which Should Be Used?

### Use Sync

* Scripts
* CLI tools
* Startup configuration loading

### Use Async

* APIs
* Production servers
* High-concurrency applications

---

## Interview Question

### Why should synchronous file operations be avoided in production?

**Answer:**

> Synchronous operations block the event loop, preventing Node.js from handling other requests until the operation completes.

---

# 3. Reading Files

## readFile()

Used to read an entire file asynchronously.

### Syntax

```js
fs.readFile(path, options, callback);
```

---

### Example

```js
fs.readFile("user.txt", "utf8", (err, data) => {
  if (err) {
    console.log(err);
    return;
  }

  console.log(data);
});
```

---

### Error-First Callback

```js
(err, data)
```

Node convention:

```js
if (err) {
   // handle error
}
```

---

## readFileSync()

```js
const data = fs.readFileSync("user.txt", "utf8");

console.log(data);
```

---

## Interview Question

### Why specify "utf8"?

Without it:

```js
const data = fs.readFileSync("user.txt");
```

Returns:

```js
<Buffer 48 65 6c 6c 6f>
```

With utf8:

```js
Hello
```

---

## Interview Answer

> The utf8 encoding converts the file contents into a readable string instead of returning a Buffer object.

---

# 4. Writing Files

## writeFile()

Creates a file if it doesn't exist.

Overwrites if it already exists.

---

### Example

```js
fs.writeFile(
  "user.txt",
  "Hello World",
  (err) => {
    if (err) {
      console.log(err);
      return;
    }

    console.log("File Created");
  }
);
```

---

### Result

```text
user.txt

Hello World
```

---

## Overwrite Behavior

Existing file:

```text
Hello
```

Run:

```js
fs.writeFile("user.txt", "New Content", callback);
```

Result:

```text
New Content
```

Old content is removed.

---

## writeFileSync()

```js
fs.writeFileSync(
  "user.txt",
  "Hello"
);
```

---

## Interview Question

### Difference between writeFile and appendFile?

**Answer:**

`writeFile()` replaces content.

`appendFile()` adds content at the end.

---

# 5. Appending Files

## appendFile()

Adds data to the end of an existing file.

---

### Example

File:

```text
Hello
```

Code:

```js
fs.appendFile(
  "user.txt",
  "\nWelcome",
  (err) => {
    if(err) return;

    console.log("Updated");
  }
);
```

Result:

```text
Hello
Welcome
```

---

## appendFileSync()

```js
fs.appendFileSync(
  "user.txt",
  "\nNew Line"
);
```

---

## Real World Usage

* Logs
* Audit records
* Activity tracking

---

# 6. Deleting Files

## unlink()

Deletes a file.

---

### Example

```js
fs.unlink(
  "user.txt",
  (err) => {
    if(err) {
      console.log(err);
      return;
    }

    console.log("Deleted");
  }
);
```

---

## unlinkSync()

```js
fs.unlinkSync("user.txt");
```

---

## Interview Question

### Why is it called unlink?

Historically in Unix systems:

```text
File Name
   ↓
Remove Link
   ↓
File Deleted
```

The filename link is removed from the filesystem.

---

# 7. Renaming Files

## rename()

Used for:

* Rename file
* Move file

---

### Example

```js
fs.rename(
  "old.txt",
  "new.txt",
  (err) => {
    if(err) return;

    console.log("Renamed");
  }
);
```

---

### Move File

```js
fs.rename(
  "old.txt",
  "./backup/old.txt",
  callback
);
```

---

## renameSync()

```js
fs.renameSync(
  "old.txt",
  "new.txt"
);
```

---

## Interview Question

### Can rename() move files?

Yes.

Because changing the path effectively moves the file.

---

# 8. Creating Directories

## mkdir()

Creates folders.

---

### Example

```js
fs.mkdir(
  "uploads",
  (err) => {
    if(err) return;

    console.log("Folder Created");
  }
);
```

---

## Nested Folders

```js
fs.mkdir(
  "uploads/images/profile",
  { recursive: true },
  (err) => {}
);
```

---

### Without recursive

```text
Error:
ENOENT
```

If parent folders don't exist.

---

## mkdirSync()

```js
fs.mkdirSync(
  "uploads",
  { recursive: true }
);
```

---

## Real World Usage

Common folders:

```text
uploads/
logs/
backups/
temp/
```

---

# Common Interview Questions

### Q1. Why is fs asynchronous by default?

**Answer:**

> To prevent blocking the event loop and allow Node.js to handle multiple operations concurrently.

---

### Q2. Which is preferred in production?

**Answer:**

> Asynchronous file operations because they do not block the event loop.

---

### Q3. What happens if readFile() cannot find a file?

```js
fs.readFile("abc.txt", callback);
```

Error:

```text
ENOENT
```

File does not exist.

---

### Q4. Does writeFile() create files automatically?

Yes.

If the file doesn't exist, Node creates it.

---

### Q5. Difference between readFile() and createReadStream()?

Short answer:

```text
readFile()
↓
Loads entire file into memory

createReadStream()
↓
Reads file in chunks
```

We'll cover streams in Phase 6.

---

# Quick Revision Notes

### Import

```js
const fs = require("fs");
```

### Read

```js
fs.readFile()
fs.readFileSync()
```

### Write

```js
fs.writeFile()
fs.writeFileSync()
```

### Append

```js
fs.appendFile()
fs.appendFileSync()
```

### Delete

```js
fs.unlink()
fs.unlinkSync()
```

### Rename

```js
fs.rename()
fs.renameSync()
```

### Create Folder

```js
fs.mkdir()
fs.mkdirSync()
```

### Production Rule

```text
Sync APIs
→ Block Event Loop

Async APIs
→ Preferred
```

---

Next we'll cover **Phase 5 – Part 2: `fs.promises`, File Descriptors, Stats Object, and File Watching (`fs.watch`)**, which are frequently asked in mid-level and senior Node.js interviews.
