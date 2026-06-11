That's a good approach. For MERN interviews, spending 3–4 days on every Node topic is unnecessary. From now on, we can do **one phase = one interview-focused summary**, covering only what is commonly asked.

---

# Phase 9: HTTP Server Development (Interview Edition)

## 1. What is the HTTP Module?

Node provides a built-in `http` module that allows you to create web servers without Express.

```js
const http = require("http");
```

### Why Important?

Express is built on top of Node's HTTP module.

Interviewers often ask:

> What does Express use internally?

Answer:

> Express internally uses Node's HTTP module and wraps request/response handling with additional features.

---

# 2. Creating a Server

```js
const http = require("http");

const server = http.createServer((req, res) => {
  res.end("Hello World");
});

server.listen(3000);
```

### Interview Answer

> `http.createServer()` creates an HTTP server and receives request and response objects for every incoming request.

---

# 3. Request Object (`req`)

Contains information sent by the client.

### Common Properties

```js
req.method
req.url
req.headers
```

Example:

```js
console.log(req.method);
console.log(req.url);
console.log(req.headers);
```

### Frequently Asked

#### How do you get request data?

```js
req.on("data", chunk => {});
req.on("end", () => {});
```

Node receives request bodies as streams.

---

# 4. Response Object (`res`)

Used to send data back to the client.

### Common Methods

```js
res.write()
res.end()
res.setHeader()
res.writeHead()
```

Example:

```js
res.statusCode = 200;
res.end("Success");
```

---

# 5. Manual Routing

Before Express:

```js
if (req.url === "/") {
  res.end("Home");
}
else if (req.url === "/users") {
  res.end("Users");
}
else {
  res.statusCode = 404;
  res.end("Not Found");
}
```

### Interview Question

#### Why do people use Express?

Answer:

> Managing routes manually becomes difficult as applications grow.

---

# 6. HTTP Methods

Most common:

```text
GET
POST
PUT
PATCH
DELETE
```

### Interview Answers

#### GET

Retrieve data

#### POST

Create data

#### PUT

Replace entire resource

#### PATCH

Partially update resource

#### DELETE

Remove resource

---

# 7. Headers

Headers contain metadata.

Request Header:

```http
Authorization: Bearer token
Content-Type: application/json
```

Response Header:

```js
res.setHeader(
  "Content-Type",
  "application/json"
);
```

### Important Headers

```text
Authorization
Content-Type
Cookie
Set-Cookie
Accept
User-Agent
```

---

# 8. Content-Type

Very common interview question.

JSON:

```js
res.setHeader(
  "Content-Type",
  "application/json"
);
```

HTML:

```js
res.setHeader(
  "Content-Type",
  "text/html"
);
```

File:

```js
res.setHeader(
  "Content-Type",
  "application/pdf"
);
```

### Why Important?

Browser decides how to handle data using Content-Type.

---

# 9. Status Codes

Most important ones:

| Code | Meaning               |
| ---- | --------------------- |
| 200  | Success               |
| 201  | Created               |
| 204  | No Content            |
| 400  | Bad Request           |
| 401  | Unauthorized          |
| 403  | Forbidden             |
| 404  | Not Found             |
| 409  | Conflict              |
| 500  | Internal Server Error |

---

## Interview Question

### Difference between 401 and 403?

401:

```text
Not Authenticated
```

403:

```text
Authenticated
But No Permission
```

---

# 10. Request Body Parsing

Raw Node:

```js
let body = "";

req.on("data", chunk => {
  body += chunk;
});

req.on("end", () => {
  const data = JSON.parse(body);
});
```

### Interview Question

Why use `express.json()`?

Answer:

> It automatically parses incoming JSON request bodies.

---

# 11. Cookies

Cookies are small pieces of data stored in the browser.

Set cookie:

```js
res.setHeader(
  "Set-Cookie",
  "token=abc123"
);
```

Browser sends back:

```http
Cookie: token=abc123
```

### Common Uses

```text
Authentication
Session Management
Preferences
Tracking
```

---

# 12. Cookie Flags (Very Important)

```http
Set-Cookie:
token=abc123;
HttpOnly;
Secure;
SameSite=Strict
```

### HttpOnly

JavaScript cannot access cookie.

Prevents XSS attacks.

---

### Secure

Only sent over HTTPS.

---

### SameSite

Helps prevent CSRF attacks.

---

# 13. HTTPS

HTTP:

```text
Plain Text
```

HTTPS:

```text
Encrypted
```

Uses:

```text
SSL/TLS Certificates
```

### Interview Question

Why HTTPS?

Answer:

> HTTPS encrypts communication between client and server, protecting sensitive information from interception.

---

# 14. Keep-Alive

Without Keep-Alive:

```text
Request
↓
Connection Open
↓
Response
↓
Connection Close
```

With Keep-Alive:

```text
Connection Reused
```

Benefits:

```text
Faster
Less TCP Overhead
Better Performance
```

---

# 15. HTTP Request Lifecycle

Memorize this.

```text
Client
 ↓
DNS Lookup
 ↓
TCP Connection
 ↓
HTTP Request
 ↓
Node Server
 ↓
Route Handler
 ↓
Database
 ↓
Response
 ↓
Browser
```

Senior interviewers love asking:

> Explain what happens when a user hits an API endpoint.

---

# 16. Most Asked Interview Questions

### What is the difference between HTTP and HTTPS?

HTTP:

```text
Unencrypted
```

HTTPS:

```text
Encrypted using TLS/SSL
```

---

### What is the difference between PUT and PATCH?

PUT:

```text
Replace Entire Resource
```

PATCH:

```text
Update Specific Fields
```

---

### What is the difference between 401 and 403?

```text
401 → Not Logged In

403 → Logged In But Not Allowed
```

---

### Why is Express preferred over the HTTP module?

```text
Routing
Middleware
Validation
Error Handling
Better Developer Experience
```

---

### What is Keep-Alive?

```text
Reuse Existing TCP Connection
```

---

### What are Cookies used for?

```text
Authentication
Sessions
User Preferences
```

---

# Quick Revision (5-Minute Interview Revision)

```text
http.createServer()
→ Creates server

req
→ Incoming request

res
→ Outgoing response

req.method
req.url
req.headers

res.end()
res.setHeader()

Manual routing
→ if/else

GET
→ Read

POST
→ Create

PUT
→ Replace

PATCH
→ Update

DELETE
→ Remove

Content-Type
→ Data format

200 Success
201 Created
401 Unauthorized
403 Forbidden
404 Not Found
500 Server Error

Cookies
→ Browser storage

HttpOnly
→ Prevent JS access

Secure
→ HTTPS only

HTTPS
→ Encrypted HTTP

Keep-Alive
→ Reuse TCP connection

Express
→ Built on Node's HTTP module
```

---

### Phase 9 Complete ✅

For MERN interviews, the next high-value phase is **Phase 10: Error Handling**, where we'll cover in one pass:

* Operational vs Programming Errors
* Custom Error Classes
* Centralized Error Handling
* Async Error Handling
* Try/Catch Best Practices
* Error Middleware (Express)
* Production Error Responses
* Logging (Winston/Pino)
* Most Asked Backend Interview Questions

This phase is asked much more frequently than deep HTTP internals.
