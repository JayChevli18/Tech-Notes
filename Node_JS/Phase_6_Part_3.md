# Phase 6 – Part 3: Stream Internals, Pipeline, Finished, Object Mode, Custom Streams & Advanced Interview Questions

This is where interviewers start checking whether you've used Streams in production.

---

# 1. How Streams Work Internally

When you create a stream:

```js
const stream = fs.createReadStream("big.txt");
```

Node does **not** read the whole file.

Instead:

```text
File
 ↓
Chunk 1
 ↓
Chunk 2
 ↓
Chunk 3
 ↓
End
```

Each chunk is loaded into memory only when needed.

---

## Internal Flow

```text
Data Source
     ↓
 Internal Buffer
     ↓
 Stream
     ↓
 Consumer
```

Example:

```text
File
 ↓
Readable Stream
 ↓
Application
```

---

## Interview Answer

> Streams internally use buffers and process data chunk-by-chunk, allowing efficient memory usage and flow control.

---

# 2. What is `pipeline()`?

One of the most important modern Stream APIs.

---

## Problem with pipe()

```js
readStream.pipe(writeStream);
```

Looks good.

But:

```text
Read Error?
Write Error?
Cleanup?
```

Must be handled manually.

---

## Solution: pipeline()

```js
const { pipeline } =
  require("stream");
```

---

### Example

```js
pipeline(
  readStream,
  writeStream,
  (err) => {
    if (err) {
      console.log(err);
    }

    console.log("Done");
  }
);
```

---

## Why Better?

Automatically:

```text
Error Handling
Cleanup
Resource Management
```

---

## Interview Answer

> pipeline() safely connects streams and automatically handles errors and cleanup across the entire stream chain.

---

# 3. Promise-Based Pipeline

Modern Node:

```js
const {
  pipeline
} = require("stream/promises");
```

---

### Example

```js
await pipeline(
  readStream,
  writeStream
);
```

Cleaner.

---

## Interview Tip

For new projects:

```text
pipeline()
```

is preferred over:

```text
pipe()
```

---

# 4. What is `finished()`?

Checks whether a stream has completed.

---

### Import

```js
const {
  finished
} = require("stream");
```

---

### Example

```js
finished(
  stream,
  (err) => {
    console.log(
      "Completed"
    );
  }
);
```

---

## Use Cases

```text
File Uploads
Downloads
Logging
Background Jobs
```

Need confirmation when stream ends.

---

## Interview Answer

> finished() executes a callback when a stream completes, closes, or errors.

---

# 5. Object Mode

Very common interview question.

---

## Default Stream Mode

Normally streams handle:

```text
Buffer
String
```

Example:

```js
<Buffer 48 65 6c 6c 6f>
```

---

## Problem

Suppose we want:

```js
{
  id: 1,
  name: "Jay"
}
```

Not raw bytes.

---

## Solution

```js
objectMode: true
```

---

### Example

```js
const stream =
  new Readable({
    objectMode: true
  });
```

---

## Now Streams Can Handle

```js
{
  id: 1
}

{
  id: 2
}

{
  id: 3
}
```

as chunks.

---

## Real Usage

```text
Database Records
JSON Objects
Message Queues
ETL Pipelines
```

---

## Interview Answer

> Object Mode allows streams to process JavaScript objects instead of Buffers or strings.

---

# 6. Creating Custom Readable Streams

Interviewers sometimes ask this.

---

### Example

```js
const {
  Readable
} = require("stream");

const stream =
  new Readable({
    read() {
      this.push("Hello");
      this.push("World");
      this.push(null);
    }
  });
```

---

### Consume

```js
stream.on(
  "data",
  chunk => {
    console.log(
      chunk.toString()
    );
  }
);
```

Output:

```text
Hello
World
```

---

# 7. Creating Custom Writable Streams

---

### Example

```js
const {
  Writable
} = require("stream");

const stream =
  new Writable({
    write(
      chunk,
      encoding,
      callback
    ) {
      console.log(
        chunk.toString()
      );

      callback();
    }
  });
```

---

### Write

```js
stream.write("Hello");
stream.write("World");
```

Output:

```text
Hello
World
```

---

## Interview Answer

> Custom writable streams implement a write() method that processes incoming chunks.

---

# 8. Creating Custom Transform Streams

Very popular interview topic.

---

### Example

Convert to uppercase.

```js
const {
  Transform
} = require("stream");
```

---

```js
const upper =
  new Transform({
    transform(
      chunk,
      encoding,
      callback
    ) {
      callback(
        null,
        chunk
          .toString()
          .toUpperCase()
      );
    }
  });
```

---

### Usage

```js
readStream.pipe(upper);
```

---

Input:

```text
hello
```

Output:

```text
HELLO
```

---

## Interview Answer

> Transform streams modify data while it flows through the stream pipeline.

---

# 9. Flowing vs Paused Mode

Advanced interview topic.

---

## Paused Mode

```js
const stream =
  fs.createReadStream(
    "big.txt"
  );
```

Initially:

```text
Paused
```

No data emitted.

---

## Flowing Mode

When:

```js
stream.on("data", ...)
```

or

```js
stream.pipe(...)
```

Data starts flowing automatically.

---

### Visualization

```text
Paused
 ↓
data listener
 ↓
Flowing
```

---

## Pause Stream

```js
stream.pause();
```

---

## Resume Stream

```js
stream.resume();
```

---

## Interview Answer

> Streams operate in either paused mode, where data must be explicitly read, or flowing mode, where data is automatically emitted.

---

# 10. Real Production Examples

---

## Example 1: File Compression

```text
File
 ↓
Readable
 ↓
Gzip Transform
 ↓
Writable
```

---

## Example 2: CSV Processing

```text
CSV File
 ↓
Readable
 ↓
Parser Transform
 ↓
Database
```

---

## Example 3: Image Processing

```text
Image
 ↓
Resize Transform
 ↓
Storage
```

---

## Example 4: API Response Streaming

```text
Database
 ↓
Stream
 ↓
HTTP Response
```

Avoids loading huge datasets into memory.

---

# Advanced Interview Questions

---

## Q1. Why is `pipeline()` preferred over `pipe()`?

Answer:

> pipeline() automatically propagates errors and cleans up resources, making it safer for production use.

---

## Q2. What is Object Mode?

Answer:

> Object Mode allows streams to process JavaScript objects instead of raw binary data.

---

## Q3. Difference between Readable and Transform Streams?

| Readable  | Transform             |
| --------- | --------------------- |
| Read only | Read + Modify + Write |
| Source    | Processor             |

---

## Q4. What happens when `push(null)` is called?

Answer:

> It signals the end of the readable stream.

---

## Q5. What is the purpose of `callback()` in Writable or Transform streams?

Answer:

> It informs Node.js that processing of the current chunk has completed and the next chunk can be processed.

---

## Q6. What happens if callback() is never called?

Answer:

```text
Stream hangs
Backpressure builds
Processing stops
```

---

## Q7. Difference between `pipe()` and `pipeline()`?

| pipe()                | pipeline()      |
| --------------------- | --------------- |
| Basic connection      | Production-safe |
| Manual error handling | Automatic       |
| Less safe             | Preferred       |

---

## Q8. How do Streams achieve memory efficiency?

Answer:

> Streams process data incrementally in chunks rather than loading the entire dataset into memory.

---

# Quick Revision Notes

### Production Stream API

```js
await pipeline(
  source,
  destination
);
```

---

### Stream Completion

```js
finished(stream);
```

---

### Object Mode

```js
{
  objectMode: true
}
```

---

### End Readable Stream

```js
push(null);
```

---

### Writable Stream

```js
write(chunk, enc, cb)
```

---

### Transform Stream

```js
transform(
  chunk,
  enc,
  cb
)
```

---

### Pause

```js
stream.pause();
```

---

### Resume

```js
stream.resume();
```

---

### Most Important Senior-Level Point

```text
Buffers
↓
Store Data

Streams
↓
Move Data

Pipeline
↓
Move Data Safely
```

---

# Phase 6 Summary (Buffers & Streams)

### Buffers

```js
Buffer.from()
Buffer.alloc()
Buffer.concat()
buffer.toString()
```

Used for:

```text
Binary Data
Files
Images
Network Packets
```

---

### Streams

```js
Readable
Writable
Duplex
Transform
```

---

### Stream APIs

```js
pipe()
pipeline()
finished()
```

---

### Performance Topics

```text
Backpressure
highWaterMark
Chunk Processing
```

---

### Advanced Topics

```text
Object Mode
Custom Streams
Flowing Mode
Paused Mode
```

### Golden Interview Answer

> Buffers store binary data in memory, while Streams process data incrementally in chunks. Streams are preferred for large files and network communication because they provide memory efficiency, support backpressure, and enable scalable data processing.

---

## Next Phase: Phase 7 – Events & EventEmitter

This is another extremely common Node.js interview area because much of Node.js itself (Streams, HTTP, Process, Sockets) is built on top of the EventEmitter pattern.
