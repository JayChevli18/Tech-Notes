# Phase 5 – Part 3: Large File Handling, Directory Operations, Recursive Operations & Real Interview Scenarios

This is where interviewers start testing whether you've worked on real backend applications.

---

# 1. Large File Handling

## Problem with `readFile()`

Consider a file:

```text
users.csv
5 GB
```

If you do:

```js
const data = await fs.readFile("users.csv");
```

Node tries to load the entire file into memory.

Result:

```text
5 GB RAM usage
↓
Slow
↓
Possible crash
```

---

## Why is this a problem?

Imagine:

```text
Server RAM = 2 GB
File Size = 5 GB
```

Node cannot fit the file into memory.

---

## Interview Question

### Why shouldn't we use `readFile()` for large files?

Answer:

> Because readFile() loads the entire file into memory, which can cause high memory usage and application crashes for large files.

---

# Solution: Streams

Instead of:

```text
Entire file
↓
Memory
```

Use:

```text
Chunk 1
Chunk 2
Chunk 3
...
```

Read small pieces.

We'll deeply cover Streams in Phase 6.

---

## Example

```js
const stream =
  fs.createReadStream("big.csv");
```

Reads chunks instead of the whole file.

---

## Interview Answer

> For large files, createReadStream() is preferred because it processes data in chunks rather than loading the entire file into memory.

---

# 2. Directory Operations

Backend applications often work with folders.

Examples:

```text
uploads/
images/
logs/
reports/
backups/
```

---

# Reading Directories

## readdir()

Lists directory contents.

### Example

```js
const files =
  await fs.readdir("./uploads");

console.log(files);
```

Output:

```js
[
  "image1.jpg",
  "image2.jpg",
  "file.pdf"
]
```

---

## Interview Answer

> readdir() returns the names of files and folders inside a directory.

---

# Synchronous Version

```js
const files =
  fs.readdirSync("./uploads");
```

---

# Removing Directories

## rm()

Modern approach.

```js
await fs.rm("temp");
```

---

### Recursive Delete

```js
await fs.rm("temp", {
  recursive: true,
  force: true
});
```

---

## What does force do?

Without force:

```text
Directory not found
↓
Throws error
```

With force:

```text
Ignore if missing
```

---

## Interview Question

### Difference between unlink() and rm()?

Answer:

```text
unlink()
↓
File only

rm()
↓
Files and directories
```

---

# Check Whether File Exists

Older code:

```js
fs.existsSync("user.txt");
```

Returns:

```js
true
false
```

---

### Example

```js
if (fs.existsSync("user.txt")) {
  console.log("Exists");
}
```

---

## Interview Note

Modern code often uses:

```js
try {
  await fs.access("user.txt");
} catch {
  console.log("Not Found");
}
```

because it works better with async flows.

---

# 3. Recursive Operations

A very common interview topic.

---

## What is Recursion?

Function calling itself.

Example:

```text
folder
 ├─ folder
 │   ├─ folder
 │   └─ file
```

Need to keep exploring.

---

# Recursive Directory Traversal

Imagine:

```text
uploads
 ├─ images
 │   ├─ a.jpg
 │   └─ b.jpg
 ├─ docs
 │   └─ x.pdf
```

Need all files.

---

## Example

```js
async function walk(dir) {
  const items =
    await fs.readdir(dir);

  for (const item of items) {
    console.log(item);
  }
}
```

Real implementations recursively explore nested folders.

---

## Real World Usage

Used in:

```text
Webpack
Vite
ESLint
Backup systems
Search tools
```

---

# Recursive Folder Creation

```js
await fs.mkdir(
  "uploads/images/profile",
  {
    recursive: true
  }
);
```

Creates:

```text
uploads
uploads/images
uploads/images/profile
```

automatically.

---

# Recursive Folder Deletion

```js
await fs.rm(
  "uploads",
  {
    recursive: true,
    force: true
  }
);
```

Deletes everything.

---

# 4. Access Control

Sometimes interviewers ask this.

---

## access()

Checks permissions.

```js
await fs.access("user.txt");
```

If accessible:

```text
No error
```

If not:

```text
Throws error
```

---

### Example

```js
try {
  await fs.access("user.txt");

  console.log("Accessible");
} catch {
  console.log("Cannot access");
}
```

---

## Why Use access()?

Before:

```text
Read file
Write file
Delete file
```

You may want to verify permissions.

---

# 5. Real Interview Scenario #1

## Read a JSON File

File:

```json
{
  "name": "Jay"
}
```

Code:

```js
const content =
  await fs.readFile(
    "user.json",
    "utf8"
  );

const user =
  JSON.parse(content);

console.log(user.name);
```

---

## Common Interview Question

### Why parse after reading?

Answer:

> readFile() returns text. JSON.parse() converts that text into a JavaScript object.

---

# 6. Real Interview Scenario #2

## Append Logs

```js
await fs.appendFile(
  "app.log",
  "User logged in\n"
);
```

Real use:

```text
Audit logs
Access logs
Error logs
```

---

# 7. Real Interview Scenario #3

## Upload Folder Creation

When a server starts:

```js
await fs.mkdir(
  "uploads",
  {
    recursive: true
  }
);
```

Ensures folder exists.

---

# 8. Real Interview Scenario #4

## Delete Temporary Files

```js
await fs.rm(
  "temp",
  {
    recursive: true,
    force: true
  }
);
```

Common in:

```text
Image processing
File conversion
Background jobs
```

---

# 9. Real Interview Scenario #5

## Process Large CSV

Bad:

```js
await fs.readFile(
  "users.csv"
);
```

Good:

```js
fs.createReadStream(
  "users.csv"
);
```

Reason:

```text
Lower memory usage
Better performance
```

---

# Senior-Level Interview Question

### How would you process a 10 GB file?

Good answer:

> I would avoid readFile() and use streams so the file is processed chunk-by-chunk. This keeps memory consumption low and allows efficient handling of very large files.

---

# Common Interview Questions

### Q1. How do you list files in a directory?

```js
await fs.readdir(path);
```

---

### Q2. How do you remove a directory recursively?

```js
await fs.rm(path, {
  recursive: true,
  force: true
});
```

---

### Q3. How do you check if a file exists?

```js
await fs.access(path);
```

or

```js
fs.existsSync(path);
```

---

### Q4. Why is `createReadStream()` better for large files?

Answer:

> It reads data in chunks and avoids loading the entire file into memory.

---

### Q5. What is recursive directory traversal?

Answer:

> It is the process of visiting a directory and all its nested subdirectories to access every file and folder.

---

# Quick Revision Notes

### List Files

```js
await fs.readdir()
```

---

### Delete Directory

```js
await fs.rm()
```

---

### Check Access

```js
await fs.access()
```

---

### Create Nested Folders

```js
await fs.mkdir(
  path,
  { recursive: true }
)
```

---

### Large Files

❌ Bad

```js
await fs.readFile()
```

✅ Good

```js
fs.createReadStream()
```

---

### File Exists

```js
await fs.access()
```

or

```js
fs.existsSync()
```

---

### Real Production Rule

```text
Small Files
↓
readFile()

Large Files
↓
Streams
```

---

# Phase 5 Summary (Everything to Remember)

### Basic Operations

```js
readFile()
writeFile()
appendFile()
unlink()
rename()
mkdir()
```

### Promise APIs

```js
fs/promises
```

### Metadata

```js
stat()
```

### File Watching

```js
watch()
```

### Directories

```js
readdir()
rm()
```

### Large Files

```js
createReadStream()
```

### Open Files

```js
open()
close()
```

### Access Checks

```js
access()
```

### Most Important Interview Rule

```text
Never use readFile()
for very large files.

Use Streams.
```

---

Next we'll start **Phase 6: Buffers & Streams**, one of the most frequently asked Node.js interview topics because it directly explains how Node handles files, network data, uploads, downloads, and APIs efficiently.
