# Phase 6 – Part 2: Streams Deep Dive

This is one of the **most important Node.js interview topics**.

Many interviewers ask:

* What are streams?
* Why are streams used?
* What is pipe()?
* What is backpressure?
* Types of streams?
* Difference between Buffer and Stream?

If you understand Streams well, you'll understand how Node handles:

```text
File Uploads
File Downloads
Video Streaming
HTTP Requests
HTTP Responses
TCP Sockets
Real-time Data
```

---

# 1. What is a Stream?

### Definition

A Stream is a way to process data **piece by piece (chunks)** instead of loading everything into memory.

---

## Without Streams

Imagine:

```text
5 GB File
↓
Load Entire File
↓
Memory
↓
Process
```

Problem:

```text
Huge Memory Usage
Slow
Possible Crash
```

---

## With Streams

```text
Chunk 1
↓
Process

Chunk 2
↓
Process

Chunk 3
↓
Process
```

Only a small part is in memory at a time.

---

## Interview Answer

> A stream is an abstraction for handling data incrementally in chunks rather than loading the entire data into memory at once.

---

# 2. Why Streams Exist

Suppose:

```text
Video Size = 10 GB
```

Using:

```js
fs.readFile()
```

would try to load all 10 GB.

Bad idea.

Streams solve this problem.

---

## Benefits

### Low Memory Usage

```text
Small Chunks
↓
Less RAM
```

---

### Better Performance

Can start processing immediately.

---

### Scalability

Can handle huge files.

---

# 3. Types of Streams

Node has 4 types.

```text
Readable
Writable
Duplex
Transform
```

This is a very common interview question.

---

# 4. Readable Streams

Used for reading data.

Examples:

```text
Files
HTTP Requests
TCP Data
```

---

## Create Readable Stream

```js
const fs = require("fs");

const stream =
  fs.createReadStream("big.txt");
```

---

## Reading Data

```js
stream.on("data", chunk => {
  console.log(chunk);
});
```

Each chunk is usually a Buffer.

---

### Events

Readable streams emit:

```text
data
end
error
close
```

---

## Example

```js
stream.on("data", chunk => {
  console.log(chunk.length);
});

stream.on("end", () => {
  console.log("Done");
});
```

---

## Interview Answer

> A readable stream is used to consume data from a source incrementally.

---

# 5. Writable Streams

Used for writing data.

Examples:

```text
Files
HTTP Responses
Logs
```

---

## Create Writable Stream

```js
const stream =
  fs.createWriteStream("output.txt");
```

---

## Write Data

```js
stream.write("Hello");
stream.write("World");
```

---

## End Stream

```js
stream.end();
```

---

## Interview Answer

> A writable stream is used to write data incrementally to a destination.

---

# 6. Duplex Streams

Can read and write.

---

## Examples

```text
TCP Socket
WebSocket
```

---

### Visualization

```text
Read
 ↑
Socket
 ↓
Write
```

Both directions.

---

## Interview Answer

> Duplex streams support both reading and writing operations simultaneously.

---

# 7. Transform Streams

Special type of Duplex stream.

Reads data and transforms it.

---

## Example

```text
Input
↓
Transform
↓
Output
```

---

### Common Examples

```text
Compression
Encryption
Parsing
```

---

## Example

```text
hello
↓
UPPERCASE
↓
HELLO
```

---

## Interview Answer

> Transform streams are duplex streams that modify data while it passes through them.

---

# 8. Stream Events

Important interview topic.

---

## data

Fired when a chunk arrives.

```js
stream.on("data", chunk => {
  console.log(chunk);
});
```

---

## end

Fired when reading finishes.

```js
stream.on("end", () => {
  console.log("Finished");
});
```

---

## error

```js
stream.on("error", err => {
  console.log(err);
});
```

---

## close

```js
stream.on("close", () => {
  console.log("Closed");
});
```

---

# 9. pipe()

One of the most asked interview questions.

---

## Problem

Without pipe:

```js
readStream.on("data", chunk => {
  writeStream.write(chunk);
});
```

Manual copying.

---

## Solution

```js
readStream.pipe(writeStream);
```

---

### Visualization

```text
Read Stream
      ↓
     pipe
      ↓
Write Stream
```

---

## Example

```js
const read =
  fs.createReadStream("input.txt");

const write =
  fs.createWriteStream("output.txt");

read.pipe(write);
```

---

## What Happens?

```text
Read chunk
↓
Write chunk
↓
Read chunk
↓
Write chunk
```

Automatically.

---

## Interview Answer

> pipe() connects a readable stream to a writable stream and automatically manages data flow between them.

---

# 10. Backpressure

This is a favorite senior-level interview topic.

---

## Problem

Imagine:

```text
Read Speed = 100 MB/s

Write Speed = 10 MB/s
```

Reader is much faster.

---

### Result

```text
Data keeps arriving
↓
Memory fills up
↓
Crash
```

---

# What is Backpressure?

Mechanism that prevents a fast producer from overwhelming a slow consumer.

---

## Visualization

```text
Producer
   ↓
Too Fast
   ↓
Consumer
```

Backpressure says:

```text
WAIT
Consumer is busy
```

---

## How Node Handles It

Internally:

```js
stream.write(chunk);
```

returns:

```js
true
false
```

---

### true

```text
Keep Writing
```

---

### false

```text
Buffer Full
Stop Temporarily
```

---

Node pauses reading until writable stream catches up.

---

## Interview Answer

> Backpressure is a flow-control mechanism that prevents writable streams from being overwhelmed by faster readable streams.

---

# 11. highWaterMark

Often asked with backpressure.

---

## What is It?

Internal buffer size threshold.

Example:

```js
fs.createReadStream(
  "big.txt",
  {
    highWaterMark: 64 * 1024
  }
);
```

---

### Meaning

```text
64 KB
```

per chunk.

---

## Larger Value

```text
Fewer Chunks
More Memory
```

---

## Smaller Value

```text
More Chunks
Less Memory
```

---

## Interview Answer

> highWaterMark defines the maximum amount of data that can be buffered before backpressure is applied.

---

# 12. Difference Between Buffer and Stream

Very common interview question.

| Buffer      | Stream      |
| ----------- | ----------- |
| Entire data | Chunks      |
| More memory | Less memory |
| Simple      | Efficient   |
| Small files | Large files |

---

## Example

### Buffer

```js
await fs.readFile("5gb.csv");
```

Loads everything.

---

### Stream

```js
fs.createReadStream("5gb.csv");
```

Loads chunks.

---

# 13. Real-World Examples

## File Upload

```text
Client
↓
Stream
↓
Server
```

---

## Video Streaming

```text
Netflix
↓
Chunks
↓
Browser
```

---

## File Download

```text
Server
↓
Stream
↓
Client
```

---

## Database Export

```text
DB
↓
Stream
↓
CSV
```

---

# Common Interview Questions

### Q1. Why use Streams?

Answer:

> Streams reduce memory consumption by processing data in chunks instead of loading everything into memory.

---

### Q2. What are the 4 stream types?

Answer:

```text
Readable
Writable
Duplex
Transform
```

---

### Q3. What is pipe()?

Answer:

> pipe() connects a readable stream to a writable stream and automatically transfers data between them.

---

### Q4. What is Backpressure?

Answer:

> A mechanism that prevents fast producers from overwhelming slow consumers.

---

### Q5. What is highWaterMark?

Answer:

> It defines the buffer threshold at which backpressure starts being applied.

---

### Q6. Difference between Buffer and Stream?

Answer:

```text
Buffer → Entire Data

Stream → Chunks
```

---

# Quick Revision Notes

### Read Stream

```js
fs.createReadStream()
```

---

### Write Stream

```js
fs.createWriteStream()
```

---

### Copy File

```js
read.pipe(write);
```

---

### Events

```text
data
end
error
close
```

---

### Stream Types

```text
Readable
Writable
Duplex
Transform
```

---

### Backpressure

```text
Fast Producer
↓
Slow Consumer
↓
Flow Control
```

---

### HighWaterMark

```js
{
  highWaterMark: 64 * 1024
}
```

---

### Golden Interview Rule

```text
Small File
↓
readFile()

Large File
↓
Streams
```

---

## Senior-Level Interview Answer

> Streams are memory-efficient abstractions that process data incrementally. They are essential for handling large files, network communication, uploads, downloads, and real-time data processing while supporting flow control through backpressure.

---

Next: **Phase 6 – Part 3: Stream Internals, Pipeline, Finished, Object Mode, Custom Streams, and Advanced Stream Interview Questions** (commonly asked for 2–5+ years Node.js developers).
