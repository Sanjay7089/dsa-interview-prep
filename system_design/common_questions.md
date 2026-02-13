# Common System Design Questions for SDE-1

## 🎯 Preparation Checklist

### Must Prepare (Practice these before interviews!)

| #   | Question      | Type      | Time to Prep |
| --- | ------------- | --------- | ------------ |
| 1   | Parking Lot   | LLD       | 1 hour       |
| 2   | Tic-Tac-Toe   | LLD       | 45 min       |
| 3   | LRU Cache     | LLD + DSA | 45 min       |
| 4   | URL Shortener | HLD       | 1 hour       |
| 5   | Rate Limiter  | HLD       | 45 min       |

### Good to Have

| #   | Question            | Type | Time to Prep |
| --- | ------------------- | ---- | ------------ |
| 6   | Library Management  | LLD  | 45 min       |
| 7   | Snake & Ladder      | LLD  | 1 hour       |
| 8   | Notification System | HLD  | 45 min       |
| 9   | Logger System       | LLD  | 30 min       |
| 10  | Vending Machine     | LLD  | 45 min       |

---

## Quick Solutions: Rate Limiter

### Requirements

- Limit API calls per user
- Multiple strategies: Fixed Window, Sliding Window, Token Bucket
- Must be fast (< 1ms overhead)

### Solution: Token Bucket (Most Common)

```python
import time

class TokenBucket:
    """
    Token Bucket Rate Limiter

    Tokens refill at a fixed rate.
    Each request consumes one token.
    If no tokens left, request is rejected.
    """
    def __init__(self, capacity: int, refill_rate: float):
        self.capacity = capacity        # Max tokens
        self.refill_rate = refill_rate  # Tokens per second
        self.tokens = capacity
        self.last_refill = time.time()

    def _refill(self):
        now = time.time()
        elapsed = now - self.last_refill
        new_tokens = elapsed * self.refill_rate
        self.tokens = min(self.capacity, self.tokens + new_tokens)
        self.last_refill = now

    def allow_request(self) -> bool:
        self._refill()
        if self.tokens >= 1:
            self.tokens -= 1
            return True
        return False

# Usage
limiter = TokenBucket(capacity=10, refill_rate=1)  # 10 max, 1/sec refill

for i in range(15):
    allowed = limiter.allow_request()
    print(f"Request {i+1}: {'✅ Allowed' if allowed else '❌ Rejected'}")
```

### HLD Architecture for Rate Limiter

```
                        ┌──────────────────┐
Client ──→ API Gateway ──→ Rate Limiter    │
                        │  (Redis Counter) │
                        └──────────────────┘
                                │
                         ┌──────▼──────┐
                     ✅Accept     ❌Reject
                         │             │
                    App Server    429 Error
```

**Key decisions to discuss**:

- **Where?** API Gateway (centralized) vs per-service
- **Counter storage**: Redis (distributed, fast)
- **Algorithm**: Token Bucket vs Sliding Window

---

## Quick Solutions: Logger System

### Requirements

- Multiple log levels: DEBUG, INFO, WARNING, ERROR
- Multiple outputs: Console, File, Remote
- Chain of Responsibility pattern (log bubbles up)

### Solution

```python
from abc import ABC, abstractmethod
from datetime import datetime
from enum import IntEnum

class LogLevel(IntEnum):
    DEBUG = 0
    INFO = 1
    WARNING = 2
    ERROR = 3

class Logger(ABC):
    def __init__(self, level: LogLevel):
        self._level = level
        self._next_logger = None

    def set_next(self, logger: 'Logger') -> 'Logger':
        self._next_logger = logger
        return logger  # For chaining

    def log(self, level: LogLevel, message: str):
        if level >= self._level:
            self._write(level, message)
        if self._next_logger:
            self._next_logger.log(level, message)

    @abstractmethod
    def _write(self, level: LogLevel, message: str): pass

class ConsoleLogger(Logger):
    def _write(self, level, message):
        timestamp = datetime.now().strftime("%H:%M:%S")
        print(f"[{timestamp}] [{level.name}] {message}")

class FileLogger(Logger):
    def __init__(self, level, filename="app.log"):
        super().__init__(level)
        self.filename = filename

    def _write(self, level, message):
        # In real implementation, write to file
        pass

# Usage: Chain of Responsibility
console = ConsoleLogger(LogLevel.DEBUG)
file_logger = FileLogger(LogLevel.WARNING)
console.set_next(file_logger)

console.log(LogLevel.INFO, "User logged in")        # Console only
console.log(LogLevel.ERROR, "Database connection failed")  # Console + File
```

---

## Quick Solutions: Notification System (HLD)

### Requirements

- Support Email, SMS, Push notifications
- User preferences (which channels)
- Retry on failure
- Template-based messages

### Architecture

```
                    ┌────────────────┐
 Event Trigger ────►│ Notification   │
 (order placed,     │ Service        │
  payment done)     └──────┬─────────┘
                           │
                    ┌──────▼──────┐
                    │  Message    │
                    │  Queue      │
                    └──────┬──────┘
                           │
              ┌────────────┼────────────┐
              ▼            ▼            ▼
         ┌─────────┐ ┌─────────┐ ┌─────────┐
         │ Email   │ │  SMS    │ │  Push   │
         │ Worker  │ │ Worker  │ │ Worker  │
         └─────────┘ └─────────┘ └─────────┘
              │            │            │
         SendGrid      Twilio     Firebase
```

### Key Design Points

1. **Message Queue**: Decouples event from delivery (async)
2. **Workers per channel**: Scale independently
3. **User preferences**: Check before sending
4. **Retry with backoff**: If delivery fails, retry after 1s, 2s, 4s...
5. **Template engine**: Don't hardcode messages

---

## 🎯 What to Study Based on Company

### Google (SDE-1)

- **LLD**: 90% of the time
- Focus: Clean OOP, SOLID, Design Patterns
- Common: Parking Lot, File System, Cache

### Amazon (SDE-1)

- **LLD**: 70% + Basic HLD: 30%
- Focus: OOP + Leadership Principles
- Common: Parking Lot, Library, Vending Machine

### Microsoft (SDE-1)

- **LLD**: 80% + Basic HLD: 20%
- Focus: Clean code, patterns
- Common: Snake & Ladder, Tic-Tac-Toe, Elevator

### Flipkart/Swiggy (SDE-1)

- **LLD**: 60% + HLD: 40%
- Focus: E-commerce scenarios
- Common: Inventory, Cart, Order System

### General Startups (SDE-1)

- **LLD**: 50% + "Design our feature": 50%
- Focus: Practical design, API design
- Common: "How would you design X feature?"

---

## 📅 Study Plan: System Design in 15-20 Hours

### Week 1: LLD (8-10 hours)

| Day | Topic                       | Time  | Resource             |
| --- | --------------------------- | ----- | -------------------- |
| 1   | OOP + SOLID refresher       | 2 hrs | `lld_foundations.md` |
| 2   | Design Patterns (top 6)     | 2 hrs | `design_patterns.md` |
| 3   | Practice: Parking Lot       | 2 hrs | `lld_problems.md`    |
| 4   | Practice: Tic-Tac-Toe + LRU | 2 hrs | `lld_problems.md`    |

### Week 2: HLD (6-8 hours)

| Day | Topic                         | Time    | Resource          |
| --- | ----------------------------- | ------- | ----------------- |
| 5   | HLD Building Blocks           | 2 hrs   | `hld_basics.md`   |
| 6   | HLD Template + URL Shortener  | 2 hrs   | `hld_template.md` |
| 7   | Practice: Rate Limiter        | 1.5 hrs | This file         |
| 8   | Practice: Notification System | 1.5 hrs | This file         |

### Week 3: Review + Mock (2-3 hours)

- Do 1-2 mock system design sessions
- Practice drawing diagrams on paper/whiteboard
- Practice explaining trade-offs out loud

---

## 💡 Final Tips

### During the Interview

1. **Don't panic** - System design at SDE-1 is forgiving
2. **Clarify requirements FIRST** - 3 minutes well spent
3. **Think out loud** - Show your reasoning process
4. **It's OK to not know everything** - Say "I'd research X, but my instinct is Y"
5. **Draw diagrams** - Even simple boxes and arrows help
6. **Discuss trade-offs** - "I chose X over Y because..."

### Common Mistakes

1. ❌ Jumping to architecture without clarifying requirements
2. ❌ Over-engineering (don't mention Kubernetes at SDE-1!)
3. ❌ Not defining APIs or data models
4. ❌ Staying silent (keep talking!)
5. ❌ Trying to cover everything (go deep on 1-2 areas)
