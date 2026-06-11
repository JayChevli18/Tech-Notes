# Phase 10: Error Handling (Interview Edition)

Error handling is one of the most important backend interview topics because it directly affects reliability, debugging, and production stability.

---

# 1. Why Error Handling Matters

Without proper error handling:

```text
Application Crash
↓
Poor User Experience
↓
Security Risks
↓
Hard-to-Debug Issues
```

Interview Answer:

> Error handling ensures applications fail gracefully, provide meaningful responses to users, and prevent unexpected crashes.

---

# 2. Types of Errors

## A. Operational Errors

Expected errors that can happen during normal application execution.

Examples:

```text
Database Connection Failure
Invalid User Input
File Not Found
API Timeout
Network Failure
```

Example:

```js
const user = await User.findById(id);

if (!user) {
  throw new Error("User not found");
}
```

---

## B. Programming Errors

Bugs in the code.

Examples:

```text
Undefined Variable
Null Reference
Syntax Error
Logic Error
```

Example:

```js
const user = undefined;

console.log(user.name);
```

---

## Interview Question

### Difference Between Operational and Programming Errors?

Answer:

> Operational errors are expected runtime issues such as invalid input or database failures, while programming errors are bugs caused by incorrect code and usually require fixing the application.

---

# 3. try...catch

Basic error handling mechanism.

```js
try {
  const user = JSON.parse(data);
} catch (error) {
  console.log(error.message);
}
```

---

## Interview Answer

> try...catch is used to handle synchronous exceptions and prevent application crashes.

---

# 4. Async Error Handling

Common mistake:

```js
try {
  fs.readFile("file.txt", (err, data) => {
    throw err;
  });
} catch (err) {
  console.log(err);
}
```

This won't work.

---

Correct:

```js
fs.readFile("file.txt", (err, data) => {
  if (err) {
    console.log(err);
  }
});
```

---

With async/await:

```js
try {
  const user = await getUser();
} catch (err) {
  console.log(err);
}
```

---

## Interview Question

### Does try...catch work with Promises?

Answer:

> Yes, when using await. Without await, promise rejections must be handled using .catch().

---

# 5. Custom Error Classes

Very common interview topic.

---

Instead of:

```js
throw new Error("User not found");
```

Use:

```js
class NotFoundError extends Error {

  constructor(message) {
    super(message);
    this.statusCode = 404;
  }

}
```

Usage:

```js
throw new NotFoundError("User not found");
```

---

Benefits:

```text
Better Error Categorization
Custom Status Codes
Cleaner Error Handling
```

---

## Interview Answer

> Custom error classes allow applications to represent specific business errors and attach additional metadata such as HTTP status codes.

---

# 6. Centralized Error Handling

Bad:

```js
try {
} catch(err) {
}

try {
} catch(err) {
}

try {
} catch(err) {
}
```

Every route handles errors separately.

---

Better:

```js
Route
 ↓
Throw Error
 ↓
Global Error Handler
 ↓
Response
```

---

Express Example:

```js
app.use((err, req, res, next) => {

  res.status(err.statusCode || 500)
     .json({
       message: err.message
     });

});
```

---

## Interview Answer

> Centralized error handling improves consistency, reduces duplicate code, and simplifies maintenance.

---

# 7. Express Error Middleware

Special middleware with 4 parameters:

```js
(err, req, res, next)
```

Example:

```js
app.use((err, req, res, next) => {

  res.status(500).json({
    message: err.message
  });

});
```

---

## Interview Question

### Why 4 Arguments?

Answer:

> Express recognizes error middleware by the presence of four arguments: err, req, res, and next.

---

# 8. Async Errors in Express

Bad:

```js
app.get("/users", async (req, res) => {

  const users =
    await User.find();

  res.json(users);

});
```

If query fails:

```text
Unhandled Rejection
```

---

Common Solution:

```js
const asyncHandler =
  fn =>
    (req, res, next) =>
      Promise.resolve(
        fn(req, res, next)
      ).catch(next);
```

Usage:

```js
app.get(
  "/users",
  asyncHandler(async (req, res) => {

    const users =
      await User.find();

    res.json(users);

  })
);
```

---

Interview Answer:

> Async route handlers should forward errors to centralized error middleware using next(error) or an async wrapper.

---

# 9. Error Response Structure

Bad:

```json
{
  "error": "Something went wrong"
}
```

---

Better:

```json
{
  "success": false,
  "message": "User not found",
  "code": "USER_NOT_FOUND"
}
```

---

Benefits:

```text
Consistency
Frontend Friendly
Easier Debugging
```

---

# 10. Stack Trace

Example:

```js
console.log(error.stack);
```

Contains:

```text
File
Line Number
Call Stack
```

---

Interview Answer:

> A stack trace shows the sequence of function calls that led to an error and helps identify the root cause.

---

# 11. Logging Errors

Never rely only on:

```js
console.log(error);
```

Production apps use loggers.

Popular choices:

* Pino
* Winston

---

Example:

```js
logger.error(error);
```

---

Why?

```text
Structured Logs
Log Levels
Log Rotation
Monitoring
```

---

# 12. What Not to Expose

Bad:

```json
{
  "message": "Database connection failed",
  "stack": "..."
}
```

Security risk.

---

Production Response:

```json
{
  "message": "Internal Server Error"
}
```

Detailed error goes to logs only.

---

Interview Answer:

> Internal implementation details and stack traces should never be exposed to clients because they can leak sensitive information.

---

# 13. Error Handling Best Practices

### Use Custom Errors

```js
ValidationError
NotFoundError
UnauthorizedError
```

---

### Use Centralized Error Handling

One place for all errors.

---

### Log Everything

Useful for debugging.

---

### Return Proper Status Codes

```text
400 Validation Error
401 Unauthorized
403 Forbidden
404 Not Found
500 Internal Error
```

---

### Avoid Swallowing Errors

Bad:

```js
try {
}
catch(err) {}
```

Error disappears.

---

# Most Asked Interview Questions

### What is centralized error handling?

> A pattern where all application errors are processed by a single global handler.

---

### Difference between Error and Custom Error?

> Custom errors extend Error and can include additional information such as status codes and business-specific metadata.

---

### Why use custom error classes?

> To categorize errors and handle them consistently.

---

### Why should stack traces not be sent to clients?

> They may expose internal application details and create security risks.

---

### How do you handle async errors in Express?

> Use async wrappers or pass errors to next() so they reach the centralized error middleware.

---

### What is the difference between operational and programming errors?

> Operational errors are expected runtime failures; programming errors are bugs in the application code.

---

# Quick Revision (5-Minute Interview Revision)

```text
Operational Errors
→ Expected runtime issues

Programming Errors
→ Code bugs

try/catch
→ Handle synchronous errors

await + try/catch
→ Handle async errors

Custom Error Classes
→ Better error categorization

Centralized Error Handler
→ One place for all errors

Express Error Middleware
→ (err, req, res, next)

Async Errors
→ next(err) or async wrapper

Don't expose stack traces

Use Winston/Pino

Log errors

Use proper status codes

400 Bad Request
401 Unauthorized
403 Forbidden
404 Not Found
500 Internal Server Error
```

---

## Phase 10 Complete ✅

### High-Value Remaining Phases for MERN Interviews

After this, the most commonly asked backend topics are:

1. **Testing (Jest, Supertest)**
2. **Authentication & JWT**
3. **Caching (Redis)**
4. **Message Queues (RabbitMQ)**
5. **Scaling & Performance**
6. **Microservices Basics**
7. **Docker Basics**
8. **System Design for Node.js APIs**

If your goal is cracking MERN interviews quickly, I'd actually recommend jumping next to **Authentication & JWT**, because it's asked in almost every Node.js backend interview.
