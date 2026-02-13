# HLD Interview Template

## 🎯 Use This Framework for ANY HLD Question

When the interviewer says "Design X", follow this structure.
You have ~35-45 minutes. Manage your time.

---

## Step 1: Requirements (3-5 min)

### Functional Requirements (What does the system DO?)

Ask these questions:

- What are the core features?
- Who are the users? (end user, admin?)
- How many users?
- What inputs/outputs?

### Non-Functional Requirements (Quality attributes)

- **Availability**: Should it be 99.9% uptime?
- **Latency**: Should reads be < 200ms?
- **Consistency**: Is eventual consistency OK?
- **Scale**: How many users? QPS?

### Template

```
Functional:
1. User can ___
2. User can ___
3. System should ___

Non-Functional:
- Read-heavy / Write-heavy
- Availability > Consistency OR Consistency > Availability
- Scale: ___M users, ___K QPS
```

---

## Step 2: API Design (3-5 min)

Define the key endpoints:

```
POST   /api/resource        → Create
GET    /api/resource/{id}   → Read
PUT    /api/resource/{id}   → Update
DELETE /api/resource/{id}   → Delete
```

Show request/response:

```json
POST /api/messages
Request:  { "sender_id": 1, "receiver_id": 2, "content": "Hello" }
Response: { "message_id": "abc123", "timestamp": "2024-01-01T..." }
```

---

## Step 3: Data Model (3-5 min)

Define your database tables:

```
Table: users
- id (PK, UUID)
- name (VARCHAR)
- email (VARCHAR, UNIQUE)
- created_at (TIMESTAMP)

Table: messages
- id (PK, UUID)
- sender_id (FK → users)
- receiver_id (FK → users)
- content (TEXT)
- created_at (TIMESTAMP)
```

Mention:

- SQL vs NoSQL choice (and WHY)
- Primary keys, foreign keys
- Indexes for common queries

---

## Step 4: High-Level Architecture (10-15 min)

Draw and explain:

```
┌────────┐     ┌──────────────┐     ┌──────────┐
│ Client │────►│ Load Balancer │────►│ API      │
│        │     └──────────────┘     │ Servers  │
└────────┘                          └──────────┘
                                      │    │
                              ┌───────┘    └──────┐
                              ▼                    ▼
                        ┌──────────┐        ┌──────────┐
                        │  Cache   │        │ Database │
                        │ (Redis)  │        │ (SQL)    │
                        └──────────┘        └──────────┘
```

Add components as needed:

- **Cache**: For read-heavy systems
- **Queue**: For async tasks
- **CDN**: For static content
- **Search**: For text search

---

## Step 5: Deep Dive (10-15 min)

The interviewer will ask to go deeper on one area.
Common deep dives:

### "How do you handle scaling?"

- Horizontal scaling (add more servers)
- Database sharding (split data by user_id, region)
- Read replicas (for read-heavy)
- Caching layer (Redis)

### "How do you handle failures?"

- Retries with exponential backoff
- Circuit breaker pattern
- Database replication
- Health checks + auto-restart

### "How do you ensure consistency?"

- ACID transactions (SQL)
- Two-phase commit (distributed)
- Eventual consistency + conflict resolution
- Idempotency keys (prevent duplicate actions)

---

## 📋 Example: Design a URL Shortener (Full Walkthrough)

### Step 1: Requirements

```
Functional:
1. User submits long URL → gets short URL
2. User visits short URL → redirected to original
3. Optional: analytics (click count)

Non-Functional:
- Read-heavy (100:1 read to write)
- Low latency redirects (< 100ms)
- Available > Consistent
- Scale: 100M URLs/month
```

### Step 2: APIs

```
POST /api/shorten
  Request:  { "url": "https://very-long-url.com/page?q=xyz" }
  Response: { "short_url": "https://short.ly/abc123" }

GET /{short_code}
  Response: 301 Redirect

GET /api/analytics/{short_code}
  Response: { "clicks": 1234, "created_at": "..." }
```

### Step 3: Data Model

```
Table: urls
- id (PK, BIGINT AUTO_INCREMENT)
- short_code (VARCHAR(7), UNIQUE, INDEXED)
- original_url (TEXT)
- user_id (FK, nullable)
- click_count (INT, default 0)
- created_at (TIMESTAMP)
- expires_at (TIMESTAMP, nullable)
```

**Key ID Generation**: Base62 encoding of auto-increment ID

```
ID     = 12345
Base62 = "dnh"  (a-z, A-Z, 0-9 = 62 chars)
URL    = https://short.ly/dnh
```

### Step 4: Architecture

```
                                    ┌───────────┐
          ┌──────────────┐     ┌───►│  Cache    │
Client ──►│ Load Balancer │────►│    │  (Redis)  │
          └──────────────┘     │    └───────────┘
                               │         │
                          ┌─────────┐    │ miss
                          │ App     │◄───┘
                          │ Server  │
                          └─────────┘
                               │
                          ┌─────────┐
                          │  SQL    │
                          │ (MySQL) │
                          └─────────┘
```

**Flow**:

1. **Write**: Generate short_code, store in DB + Cache
2. **Read**: Check Cache → if miss, check DB → cache result → redirect

### Step 5: Deep Dive Options

- **How to generate unique short codes?** → Base62 of auto-increment ID, or hash + collision check
- **How to scale?** → Database sharding by first char of short_code
- **How to handle hot URLs?** → Multi-layer cache (CDN + Redis)

---

## 🎯 Quick Reference: Common HLD Questions for SDE-1

### Commonly Asked

1. **URL Shortener** (most common!)
2. **Rate Limiter** (API throttling)
3. **Chat/Messaging System** (basic)
4. **News Feed** (simple version)
5. **Notification System**

### Occasionally Asked

6. File Storage (simplified Dropbox)
7. Paste bin
8. Task Scheduler

### Rarely Asked at SDE-1

9. ~~YouTube/Netflix~~ (too complex)
10. ~~Twitter/Instagram~~ (too complex)
11. ~~Distributed Database~~ (too complex)

---

## 💡 Interview Tips

1. **Always start with requirements** - Don't jump to architecture
2. **Draw diagrams** - Visual > verbal
3. **Discuss trade-offs** - "I chose SQL because..."
4. **Be honest about unknowns** - "I know this needs sharding, but I'm not sure about the exact strategy"
5. **Drive the conversation** - Don't wait for the interviewer to guide you
6. **Start simple, then iterate** - Begin with monolith, then add complexity
