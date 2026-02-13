# HLD Building Blocks for SDE-1

## 🎯 What SDE-1 Needs to Know About HLD

You don't need to design YouTube at scale. You need to:

1. Know the **building blocks** (what each component does)
2. Know **when to use what** (trade-offs)
3. Draw a **basic architecture diagram**
4. Discuss **APIs** and **data models**

---

## 🧱 The Building Blocks

### 1. Client → Server Communication

```
Client (Browser/App)
    │
    ├── REST API (HTTP/HTTPS)
    │     - GET, POST, PUT, DELETE
    │     - Stateless, simple, most common
    │     - Good for: CRUD operations
    │
    ├── WebSocket
    │     - Full-duplex, persistent connection
    │     - Good for: Chat, live updates, gaming
    │
    └── GraphQL
          - Client specifies what data it wants
          - Good for: Complex data with relationships
```

**Interview tip**: Default to REST. Mention WebSocket only for real-time features.

### 2. Load Balancer

```
         ┌──────────────┐
Client ──│ Load Balancer │──┬── Server 1
         └──────────────┘  ├── Server 2
                           └── Server 3
```

**What it does**: Distributes traffic across multiple servers.

**Algorithms**:

- **Round Robin**: Rotate through servers sequentially
- **Least Connections**: Send to server with fewest active connections
- **IP Hash**: Same client always goes to same server (sticky sessions)

**When to mention**: When the system has high traffic or needs reliability.

### 3. Database

```
┌─────────────────────────────────────────┐
│              DATABASES                   │
├──────────────────┬──────────────────────┤
│   SQL (RDBMS)    │      NoSQL           │
├──────────────────┼──────────────────────┤
│ MySQL, Postgres  │ MongoDB (Document)   │
│                  │ Redis (Key-Value)    │
│ ✅ ACID          │ DynamoDB (Key-Value) │
│ ✅ Joins         │ Cassandra (Column)   │
│ ✅ Structured    │ Neo4j (Graph)        │
│ ❌ Hard to scale │                      │
│   horizontally   │ ✅ Scales easily     │
│                  │ ✅ Flexible schema   │
│ Use when:        │ ❌ No joins (usually)│
│ Relations matter │ ❌ Eventual consist. │
│ Transactions     │                      │
│ Structured data  │ Use when:            │
│                  │ High write throughput│
│                  │ Flexible schema      │
│                  │ Horizontal scaling   │
└──────────────────┴──────────────────────┘
```

**Interview default**: Start with SQL unless you have a reason for NoSQL.

### 4. Cache

```
Client ──→ App Server ──→ Cache (Redis) ──→ Database
                          (check here first)
```

**What it does**: Store frequently accessed data in memory for fast reads.

**Key concepts**:

- **Cache Hit**: Data found in cache → fast!
- **Cache Miss**: Not in cache → go to DB, then store in cache
- **TTL (Time-to-Live)**: How long data stays in cache
- **Eviction**: LRU (oldest unused removed), LFU, FIFO

**When to use**: Read-heavy workloads, expensive computations.

**Common tool**: **Redis** (in-memory key-value store).

### 5. Message Queue

```
Producer ──→ [ Queue ] ──→ Consumer
             (RabbitMQ,     (processes
              Kafka,         async)
              SQS)
```

**What it does**: Decouples sender from receiver. Handles async processing.

**When to use**:

- Sending emails/notifications (don't block the user!)
- Processing orders
- Any task that can be done later

**Example**: User places order → Queue → Payment processing, Inventory update, Email confirmation (all async).

### 6. CDN (Content Delivery Network)

```
User in India ──→ CDN Edge Server (Mumbai) ──→ Origin Server (US)
                  (faster! cached copy)
```

**What it does**: Serves static content (images, CSS, JS) from servers geographically close to users.

**When to mention**: Systems serving media, images, static assets.

---

## 📊 When to Use What (Decision Framework)

### "Should I use SQL or NoSQL?"

```
Need Transactions?  →  SQL
Need Joins?         →  SQL
Flexible Schema?    →  NoSQL
Write-heavy?        →  NoSQL
Read-heavy?         →  Either + Cache
Relationships?      →  SQL (or Graph DB)
Key-Value lookup?   →  Redis / DynamoDB
```

### "Do I need a cache?"

```
Read-heavy (>80% reads)?  →  Yes (Redis)
Same data read often?     →  Yes
Data changes rarely?      →  Yes
Can tolerate stale data?  →  Yes
```

### "Do I need a message queue?"

```
Can the task be done later?     →  Yes
Is the task slow/expensive?     →  Yes
Need to decouple services?      →  Yes
Need guaranteed delivery?       →  Yes (Kafka/RabbitMQ)
```

---

## 🏗️ Common Architecture Patterns

### 1. Monolithic (Simple)

```
┌──────────────────────────┐
│       Application        │
│  ┌─────┬─────┬────────┐  │
│  │Auth │Users│Products│  │
│  └─────┴─────┴────────┘  │
│         Database          │
└──────────────────────────┘
```

**Good for**: MVPs, small teams, simple apps.

### 2. Microservices (Scalable)

```
┌──────┐  ┌──────┐  ┌─────────┐
│Auth  │  │Users │  │Products │
│ API  │  │ API  │  │  API    │
│ DB   │  │ DB   │  │  DB     │
└──────┘  └──────┘  └─────────┘
     └──────┼──────────┘
         API Gateway
```

**Good for**: Large teams, independent scaling.

### 3. Client-Server + Cache + Queue

```
                    ┌────────┐
Client ──→ LB ──→  │Server  │──→ Cache (Redis)
                    │        │──→ DB (Postgres)
                    │        │──→ Queue ──→ Worker
                    └────────┘
```

**Most common pattern for SDE-1 interviews!**

---

## 📐 Data Modeling Basics

### Example: E-Commerce

```
Users Table          Orders Table           Products Table
┌────────────┐      ┌──────────────┐      ┌──────────────┐
│ id (PK)    │      │ id (PK)      │      │ id (PK)      │
│ name       │      │ user_id (FK) │      │ name         │
│ email      │◄────│ product_id(FK)│────►│ price        │
│ created_at │      │ quantity     │      │ stock        │
└────────────┘      │ total_price  │      │ category     │
                    │ status       │      └──────────────┘
                    │ created_at   │
                    └──────────────┘
```

**Interview tip**: Always define your tables/models. Show primary keys and foreign keys.

---

## 📡 API Design Basics

### REST API Conventions

```
GET    /users          → List all users
GET    /users/{id}     → Get specific user
POST   /users          → Create new user
PUT    /users/{id}     → Update user
DELETE /users/{id}     → Delete user

GET    /users/{id}/orders  → Get user's orders
```

### Example API for a URL Shortener

```
POST   /api/shorten
  Body: { "long_url": "https://very-long-url.com/..." }
  Response: { "short_url": "https://tiny.url/abc123", "expires_at": "..." }

GET    /api/{short_code}
  Response: 301 Redirect to original URL

GET    /api/stats/{short_code}
  Response: { "clicks": 1234, "created_at": "..." }
```

**Interview tip**: Define your APIs early. Show request/response payloads.

---

## 📏 Back-of-Envelope Estimation

### Numbers You Should Know

| Metric                 | Value           |
| ---------------------- | --------------- |
| Read from memory       | 100 ns          |
| Read from SSD          | 100 μs          |
| Read from disk         | 10 ms           |
| Send 1 MB over network | 10 ms           |
| 1 Million requests/day | ~12 req/sec     |
| 1 Billion requests/day | ~12,000 req/sec |
| 1 MB = 10^6 bytes      | —               |
| 1 GB = 10^9 bytes      | —               |
| 1 TB = 10^12 bytes     | —               |

### Quick Estimation Example: URL Shortener

```
Assumptions:
- 100M new URLs/month
- Read:Write = 10:1
- URL stored = ~500 bytes

Writes: 100M / (30 × 24 × 3600) ≈ 40 writes/sec
Reads:  40 × 10 = 400 reads/sec

Storage (5 years): 100M × 12 × 5 × 500B = 3 TB
```

**SDE-1 tip**: You probably won't be asked this, but knowing it shows maturity.

---

## 🎯 Cheat Sheet: Most Common HLD Components

```
1. Load Balancer     → Distribute traffic
2. API Gateway       → Single entry point, rate limiting
3. Cache (Redis)     → Fast reads, reduce DB load
4. Database (SQL)    → Persistent storage
5. Message Queue     → Async processing
6. CDN               → Serve static files fast
7. Object Storage    → Store files/images (S3)
8. Search Engine     → Full-text search (Elasticsearch)
```
