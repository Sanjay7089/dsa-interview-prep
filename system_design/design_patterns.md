# Design Patterns for SDE-1 Interviews

## 🎯 Which Patterns to Know?

You do NOT need all 23 GoF patterns. For SDE-1, know these **8 patterns**:

| Pattern       | Category   | When Asked            | Frequency  |
| ------------- | ---------- | --------------------- | ---------- |
| **Singleton** | Creational | DB connection, Config | ⭐⭐⭐⭐⭐ |
| **Factory**   | Creational | Object creation       | ⭐⭐⭐⭐⭐ |
| **Strategy**  | Behavioral | Swappable algorithms  | ⭐⭐⭐⭐   |
| **Observer**  | Behavioral | Event systems         | ⭐⭐⭐⭐   |
| **Decorator** | Structural | Extend behavior       | ⭐⭐⭐     |
| **Builder**   | Creational | Complex objects       | ⭐⭐⭐     |
| **Iterator**  | Behavioral | Custom traversal      | ⭐⭐       |
| **State**     | Behavioral | State machines        | ⭐⭐       |

---

## 1. Singleton

> Only ONE instance of a class ever exists.

**When to use**: Database connection, Logger, Config manager.

```python
class DatabaseConnection:
    _instance = None

    def __new__(cls):
        if cls._instance is None:
            cls._instance = super().__new__(cls)
            cls._instance.connection = "Connected to DB"
        return cls._instance

# Usage
db1 = DatabaseConnection()
db2 = DatabaseConnection()
print(db1 is db2)  # True - same instance!
```

**Interview tip**: Mention thread-safety concern. In Python, use `threading.Lock()` for thread-safe singleton.

---

## 2. Factory

> Create objects without exposing creation logic.

**When to use**: Multiple types to create based on input.

```python
from abc import ABC, abstractmethod

class Notification(ABC):
    @abstractmethod
    def send(self, message): pass

class EmailNotification(Notification):
    def send(self, message):
        return f"Email: {message}"

class SMSNotification(Notification):
    def send(self, message):
        return f"SMS: {message}"

class PushNotification(Notification):
    def send(self, message):
        return f"Push: {message}"

# Factory
class NotificationFactory:
    @staticmethod
    def create(channel: str) -> Notification:
        creators = {
            "email": EmailNotification,
            "sms": SMSNotification,
            "push": PushNotification,
        }
        if channel not in creators:
            raise ValueError(f"Unknown channel: {channel}")
        return creators[channel]()

# Usage
notif = NotificationFactory.create("email")
print(notif.send("Hello!"))  # Email: Hello!
```

---

## 3. Strategy

> Swap algorithms at runtime.

**When to use**: Multiple ways to do the same thing (sort, pay, route).

```python
from abc import ABC, abstractmethod

class PricingStrategy(ABC):
    @abstractmethod
    def calculate(self, base_price: float) -> float: pass

class RegularPricing(PricingStrategy):
    def calculate(self, base_price):
        return base_price

class PremiumPricing(PricingStrategy):
    def calculate(self, base_price):
        return base_price * 0.80  # 20% discount

class StudentPricing(PricingStrategy):
    def calculate(self, base_price):
        return base_price * 0.50  # 50% discount

# Context
class ShoppingCart:
    def __init__(self, strategy: PricingStrategy):
        self._strategy = strategy
        self.items = []

    def set_strategy(self, strategy: PricingStrategy):
        self._strategy = strategy

    def add_item(self, price):
        self.items.append(price)

    def total(self):
        raw = sum(self.items)
        return self._strategy.calculate(raw)

# Usage
cart = ShoppingCart(RegularPricing())
cart.add_item(100)
cart.add_item(50)
print(f"Regular: ${cart.total()}")    # $150

cart.set_strategy(StudentPricing())
print(f"Student: ${cart.total()}")    # $75
```

---

## 4. Observer

> When one object changes, notify all dependents.

**When to use**: Event systems, notifications, pub/sub.

```python
from abc import ABC, abstractmethod

class EventManager:
    def __init__(self):
        self._subscribers = {}  # event_type -> list of listeners

    def subscribe(self, event_type: str, listener):
        if event_type not in self._subscribers:
            self._subscribers[event_type] = []
        self._subscribers[event_type].append(listener)

    def unsubscribe(self, event_type: str, listener):
        self._subscribers[event_type].remove(listener)

    def notify(self, event_type: str, data):
        for listener in self._subscribers.get(event_type, []):
            listener.update(event_type, data)

class Listener(ABC):
    @abstractmethod
    def update(self, event_type, data): pass

# Concrete listeners
class EmailAlert(Listener):
    def update(self, event_type, data):
        print(f"Email alert for '{event_type}': {data}")

class SlackAlert(Listener):
    def update(self, event_type, data):
        print(f"Slack alert for '{event_type}': {data}")

# Usage
manager = EventManager()
manager.subscribe("order_placed", EmailAlert())
manager.subscribe("order_placed", SlackAlert())

manager.notify("order_placed", {"order_id": 123, "amount": 99.99})
# Email alert for 'order_placed': {'order_id': 123, 'amount': 99.99}
# Slack alert for 'order_placed': {'order_id': 123, 'amount': 99.99}
```

---

## 5. Decorator

> Add behavior to objects dynamically without modifying them.

**When to use**: Adding features on top (logging, auth, caching).

```python
from abc import ABC, abstractmethod

class Coffee(ABC):
    @abstractmethod
    def cost(self) -> float: pass

    @abstractmethod
    def description(self) -> str: pass

class BasicCoffee(Coffee):
    def cost(self): return 50
    def description(self): return "Basic Coffee"

# Decorators
class MilkDecorator(Coffee):
    def __init__(self, coffee: Coffee):
        self._coffee = coffee
    def cost(self):
        return self._coffee.cost() + 20
    def description(self):
        return self._coffee.description() + " + Milk"

class SugarDecorator(Coffee):
    def __init__(self, coffee: Coffee):
        self._coffee = coffee
    def cost(self):
        return self._coffee.cost() + 10
    def description(self):
        return self._coffee.description() + " + Sugar"

# Usage - Stack decorators!
coffee = BasicCoffee()
coffee = MilkDecorator(coffee)
coffee = SugarDecorator(coffee)

print(f"{coffee.description()}: ₹{coffee.cost()}")
# Basic Coffee + Milk + Sugar: ₹80
```

---

## 6. Builder

> Construct complex objects step by step.

**When to use**: Object with many optional parameters.

```python
class QueryBuilder:
    def __init__(self, table):
        self._table = table
        self._conditions = []
        self._order_by = None
        self._limit = None
        self._columns = ["*"]

    def select(self, *columns):
        self._columns = list(columns)
        return self  # Return self for chaining!

    def where(self, condition):
        self._conditions.append(condition)
        return self

    def order_by(self, column, direction="ASC"):
        self._order_by = f"{column} {direction}"
        return self

    def limit(self, n):
        self._limit = n
        return self

    def build(self) -> str:
        query = f"SELECT {', '.join(self._columns)} FROM {self._table}"
        if self._conditions:
            query += f" WHERE {' AND '.join(self._conditions)}"
        if self._order_by:
            query += f" ORDER BY {self._order_by}"
        if self._limit:
            query += f" LIMIT {self._limit}"
        return query

# Usage - Method chaining
query = (QueryBuilder("users")
    .select("name", "email")
    .where("age > 18")
    .where("city = 'Delhi'")
    .order_by("name")
    .limit(10)
    .build())

print(query)
# SELECT name, email FROM users WHERE age > 18 AND city = 'Delhi' ORDER BY name LIMIT 10
```

---

## 🎯 Interview Cheat Sheet

| You hear...                                 | Use this pattern |
| ------------------------------------------- | ---------------- |
| "Only one instance"                         | **Singleton**    |
| "Multiple types based on input"             | **Factory**      |
| "Swap algorithm at runtime"                 | **Strategy**     |
| "Notify when something changes"             | **Observer**     |
| "Add features dynamically"                  | **Decorator**    |
| "Complex object, many options"              | **Builder**      |
| "Walk through a collection"                 | **Iterator**     |
| "Object behaves differently based on state" | **State**        |
