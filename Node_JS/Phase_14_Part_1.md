# Phase 14: Clustering & Scaling Node.js Applications (Interview-Focused)

## Why Clustering Exists

Node.js runs JavaScript in a single main thread.

Suppose your machine has:

```text
8 CPU Cores
```

A normal Node.js server uses only:

```text
1 CPU Core
```

The other 7 cores remain mostly unused.

Clustering allows us to run multiple Node.js processes and utilize all CPU cores.

---

# What is Clustering?

Clustering is the process of creating multiple Node.js processes (workers) that share the same server port.

Built-in module:

```js
const cluster = require("cluster");
```

---

## Without Clustering

```text
Client Requests
        │
        ▼
   Node Process
        │
     CPU Core 1
```

Only one core handles all traffic.

---

## With Clustering

```text
                Master Process
                       │
      ┌────────┬───────┼────────┬───────┐
      ▼        ▼       ▼        ▼
 Worker1  Worker2  Worker3  Worker4

      CPU1    CPU2    CPU3    CPU4
```

Requests are distributed among workers.

---

# Basic Cluster Example

```js
const cluster = require("cluster");
const os = require("os");

if (cluster.isPrimary) {
  const cpuCount = os.cpus().length;

  for (let i = 0; i < cpuCount; i++) {
    cluster.fork();
  }
} else {
  require("./server");
}
```

---

## How It Works

### Master Process

Responsible for:

* Creating workers
* Monitoring workers
* Restarting failed workers

### Worker Processes

Responsible for:

* Handling HTTP requests
* Running application code

---

## Request Flow

```text
Client
   │
   ▼
Master Process
   │
   ▼
Worker Selected
   │
   ▼
Response
```

---

# Why Not Worker Threads?

Common interview question.

### Worker Threads

```text
Same Process
Multiple Threads
```

Used for:

* CPU-heavy calculations
* Data processing

---

### Cluster

```text
Multiple Processes
```

Used for:

* More HTTP traffic
* Better CPU utilization
* Higher throughput

---

# Auto Restart Failed Workers

Production systems restart crashed workers.

```js
cluster.on("exit", (worker) => {
  console.log(`Worker ${worker.process.pid} died`);

  cluster.fork();
});
```

---

## Benefit

```text
Worker Crashes
      │
      ▼
New Worker Created
```

Application stays available.

---

# Load Balancing

When multiple workers exist:

```text
Worker 1
Worker 2
Worker 3
Worker 4
```

Requests need distribution.

Node cluster provides built-in load balancing.

Commonly:

```text
Round Robin
```

---

## Example

```text
Request 1 → Worker 1
Request 2 → Worker 2
Request 3 → Worker 3
Request 4 → Worker 4
```

---

# Sticky Sessions Problem

Very common interview question.

Imagine login sessions stored in memory:

```js
const sessions = {};
```

User logs in:

```text
Request → Worker 1
```

Session exists only in Worker 1.

Next request:

```text
Request → Worker 3
```

Worker 3 cannot find session.

---

## Solution 1 (Best Practice)

Store sessions in:

Redis

instead of worker memory.

```text
All Workers
      │
      ▼
    Redis
```

---

## Solution 2

Sticky sessions at load balancer level.

Less common today.

---

# Cluster vs PM2

Another favorite interview question.

## Cluster Module

Built into Node.

```js
cluster.fork();
```

You manage everything.

---

## PM2

[PM2](https://pm2.keymetrics.io/?utm_source=chatgpt.com)

Production process manager.

Provides:

* Clustering
* Auto restart
* Monitoring
* Log management
* Zero downtime deployment

---

## Example

```bash
pm2 start app.js -i max
```

`max` means:

```text
Use all CPU cores
```

---

# Scaling Levels

Interviewers often ask:

### 1. Vertical Scaling

Increase machine power.

```text
2 CPU → 8 CPU
8GB RAM → 32GB RAM
```

---

### 2. Horizontal Scaling

Add more servers.

```text
Server 1
Server 2
Server 3
```

Behind a load balancer.

---

## Which Is Better?

Usually:

```text
Horizontal Scaling
```

because it removes single points of failure.

---

# Node.js Scaling Architecture

Typical production architecture:

```text
Internet
    │
    ▼
Load Balancer
    │
 ┌──┼──┐
 ▼  ▼  ▼
Node Node Node
App  App  App
    │
    ▼
 Redis
    │
    ▼
Database
```

---

# Cluster vs Worker Thread

| Cluster                | Worker Thread          |
| ---------------------- | ---------------------- |
| Multiple processes     | Multiple threads       |
| Separate memory        | Shared memory possible |
| Scale HTTP traffic     | Scale CPU computation  |
| Higher memory usage    | Lower memory usage     |
| Better fault isolation | Less isolation         |

---

## Interview Answer

### When would you use Cluster?

> I use clustering when I need to utilize multiple CPU cores and increase the number of requests my Node.js application can handle. Each worker runs as a separate process and can serve requests independently.

---

# Most Asked Interview Questions

### Why do we need clustering?

> A Node.js process typically utilizes one CPU core. Clustering allows multiple worker processes to use all available CPU cores.

---

### What is the difference between a worker and a cluster worker?

> Worker Threads are threads inside the same process used for CPU-intensive tasks. Cluster workers are separate Node.js processes used to handle more incoming traffic.

---

### What happens if a worker crashes?

> The master process can detect the failure and create a new worker automatically.

---

### Why can clustering cause session issues?

> Sessions stored in memory exist only inside a specific worker process. Requests routed to another worker won't have access to that session data.

---

### How do you solve session sharing across cluster workers?

> Store sessions in a shared store such as Redis instead of process memory.

---

### Cluster vs PM2?

> Cluster is a Node.js module. PM2 is a production process manager that can internally use clustering while also providing monitoring, logging, restarts, and deployment features.

---

# System Design Interview Connection

Interviewers often ask:

> "Your Node.js API handles 5,000 requests/sec and CPU usage is maxed out. What would you do?"

Expected answer:

```text
1. Enable clustering
2. Use all CPU cores
3. Add Redis for shared sessions/cache
4. Add load balancer
5. Scale horizontally if needed
```

---

# 2-Minute Revision

```text
Cluster
- Multiple Node processes
- Uses all CPU cores
- Built with cluster module

Master Process
- Creates workers
- Monitors workers

Workers
- Handle requests

Benefits
- Higher throughput
- Better CPU utilization
- Fault tolerance

Problems
- Session sharing

Solutions
- Redis
- Sticky sessions

Cluster vs Worker Thread
- Cluster = More traffic
- Worker Thread = More computation

Cluster vs PM2
- Cluster = Core Node feature
- PM2 = Production manager

Interview Favorite
- Session problem
- Cluster vs Worker Thread
- Scaling Node APIs
```
