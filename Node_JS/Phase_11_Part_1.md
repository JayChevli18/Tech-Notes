# Phase 11: Networking (Interview-Focused)

Networking is one of the favorite areas for backend interviews because it helps interviewers judge whether you understand what happens between the browser and your Node.js server.

---

# 1. TCP (Transmission Control Protocol)

## What is TCP?

TCP is a **connection-oriented protocol** that guarantees:

* Reliable delivery
* Ordered delivery
* Error checking
* Data retransmission if packets are lost

Most web applications use TCP.

Examples:

* HTTP/HTTPS
* Database connections
* SSH
* FTP

---

## How TCP Works

### 1. Three-Way Handshake

Before sending data:

```text
Client ------ SYN ------> Server
Client <--- SYN+ACK ----- Server
Client ------ ACK ------> Server
```

Connection established.

---

### 2. Data Transfer

Data is broken into packets.

TCP ensures:

```text
1
2
3
4
5
```

arrive in correct order.

If packet 3 is lost:

```text
1
2
4
5
```

TCP requests packet 3 again.

---

## TCP Characteristics

| Feature             | TCP               |
| ------------------- | ----------------- |
| Reliable            | ✅                 |
| Ordered             | ✅                 |
| Connection Required | ✅                 |
| Fast                | ❌ Slightly slower |
| Error Recovery      | ✅                 |

---

## TCP in Node.js

Node provides the built-in `net` module.

### TCP Server

```js
const net = require("net");

const server = net.createServer((socket) => {
  socket.write("Hello Client");
});

server.listen(3000);
```

---

### TCP Client

```js
const net = require("net");

const client = net.createConnection({
  port: 3000
});

client.on("data", (data) => {
  console.log(data.toString());
});
```

---

## Interview Answer

**What is TCP?**

> TCP is a connection-oriented transport layer protocol that guarantees reliable and ordered data delivery through acknowledgements, retransmissions, and error checking. HTTP and HTTPS run on top of TCP.

---

# 2. UDP (User Datagram Protocol)

## What is UDP?

UDP is a **connectionless protocol**.

No handshake.

No guarantee that packets arrive.

No guarantee of ordering.

No retransmission.

---

## UDP Characteristics

| Feature        | UDP |
| -------------- | --- |
| Reliable       | ❌   |
| Ordered        | ❌   |
| Handshake      | ❌   |
| Fast           | ✅   |
| Retransmission | ❌   |

---

## Where UDP Is Used

* Online gaming
* Video calls
* Voice calls
* Live streaming
* DNS

Reason:

Speed is more important than perfect reliability.

---

## UDP in Node.js

Built-in module:

```js
const dgram = require("dgram");
```

### UDP Server

```js
const dgram = require("dgram");

const server = dgram.createSocket("udp4");

server.on("message", (msg) => {
  console.log(msg.toString());
});

server.bind(3000);
```

---

### UDP Client

```js
const dgram = require("dgram");

const client = dgram.createSocket("udp4");

client.send(
  "Hello",
  3000,
  "localhost"
);
```

---

## Interview Answer

**TCP vs UDP?**

| TCP       | UDP              |
| --------- | ---------------- |
| Reliable  | Unreliable       |
| Ordered   | Unordered        |
| Handshake | No Handshake     |
| Slower    | Faster           |
| HTTP      | Gaming/Streaming |

---

# 3. DNS (Domain Name System)

## What is DNS?

DNS converts:

```text
google.com
```

into

```text
142.250.xxx.xxx
```

(IP Address)

Computers communicate using IP addresses, not domain names.

---

## DNS Resolution Process

```text
Browser
   ↓
DNS Resolver
   ↓
Root Server
   ↓
TLD Server (.com)
   ↓
Authoritative DNS
   ↓
IP Address
```

---

## Example

User visits:

```text
https://google.com
```

Browser first asks DNS:

```text
What is google.com?
```

DNS returns:

```text
142.x.x.x
```

Then TCP connection begins.

---

## DNS Record Types

### A Record

Maps domain → IPv4

```text
google.com → 142.250.x.x
```

---

### AAAA Record

Maps domain → IPv6

---

### CNAME

Alias

```text
www.example.com
    ↓
example.com
```

---

### MX

Mail server

---

### TXT

Verification records

---

## DNS Lookup in Node.js

```js
const dns = require("dns");

dns.lookup(
  "google.com",
  (err, address) => {
    console.log(address);
  }
);
```

---

## Interview Answer

**What happens when you type google.com in a browser?**

> Browser performs DNS resolution, obtains the server IP address, establishes a TCP connection, performs TLS handshake for HTTPS, sends the HTTP request, receives the response, and renders the page.

---

# 4. HTTPS

## What is HTTPS?

HTTPS means:

```text
HTTP + TLS/SSL
```

It encrypts communication between client and server.

---

## Why HTTPS?

Without HTTPS:

```text
Username
Password
Credit Card
```

travel in plain text.

Anyone intercepting traffic can read them.

---

## HTTPS Benefits

### Encryption

Nobody can read data.

### Integrity

Data cannot be modified.

### Authentication

Confirms server identity.

---

## Default Ports

| Protocol | Port |
| -------- | ---- |
| HTTP     | 80   |
| HTTPS    | 443  |

---

## Interview Answer

**Difference between HTTP and HTTPS?**

| HTTP        | HTTPS     |
| ----------- | --------- |
| Unencrypted | Encrypted |
| Port 80     | Port 443  |
| Less Secure | Secure    |
| No TLS      | Uses TLS  |

---

# 5. TLS/SSL Basics

## What is SSL?

SSL is the old protocol.

Today we use:

```text
TLS
```

but people still say SSL certificates.

---

## Why TLS Exists

Imagine:

```text
Client → Server
```

Need:

* Encryption
* Authentication
* Integrity

TLS provides all three.

---

## TLS Handshake (Simplified)

### Step 1

Client says:

```text
Hello
```

and sends supported encryption algorithms.

---

### Step 2

Server responds:

```text
Hello
```

and sends certificate.

---

### Step 3

Client verifies certificate.

---

### Step 4

Both generate session keys.

---

### Step 5

Encrypted communication starts.

---

## Certificates

Issued by:

* Let's Encrypt
* DigiCert
* GlobalSign

Certificate proves:

```text
This server really owns this domain.
```

---

## Public Key vs Private Key

### Public Key

Shared with everyone.

Used for encryption.

---

### Private Key

Stored secretly on server.

Used for decryption.

---

## Interview Answer

**What is TLS?**

> TLS is a cryptographic protocol that secures communication over the internet by providing encryption, authentication, and data integrity. HTTPS uses TLS on top of HTTP.

---

# Most Asked Interview Questions

### 1. TCP vs UDP?

Reliability vs speed.

---

### 2. Why does HTTP use TCP?

Because requests and responses must arrive completely and in order.

---

### 3. What happens when you enter a URL?

```text
DNS Lookup
↓
TCP Handshake
↓
TLS Handshake
↓
HTTP Request
↓
Server Processing
↓
HTTP Response
↓
Browser Rendering
```

---

### 4. What is a TLS handshake?

Process where client and server establish encrypted communication and verify identity.

---

### 5. Why is HTTPS secure?

Because TLS provides:

* Encryption
* Authentication
* Integrity

---

# Quick Revision (2-Minute Interview Cheat Sheet)

```text
TCP
- Reliable
- Ordered
- Connection-oriented
- Used by HTTP/HTTPS

UDP
- Fast
- Connectionless
- No delivery guarantee
- Used by gaming, streaming, DNS

DNS
- Domain -> IP
- A, AAAA, CNAME, MX records

HTTPS
- HTTP + TLS
- Port 443
- Encrypted communication

TLS
- Encryption
- Authentication
- Integrity

Browser Request Flow
DNS
↓
TCP
↓
TLS
↓
HTTP
↓
Response
```

Next phase should be **Phase 12: Child Processes & Worker Threads**, which is another very common Node.js interview topic because interviewers often ask how Node handles CPU-intensive tasks.
