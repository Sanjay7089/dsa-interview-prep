# LLD Foundations - OOP & SOLID

## 🧱 OOP Concepts (Quick Recap)

### The 4 Pillars

```python
# 1. ENCAPSULATION - Bundle data + methods, hide internals
class BankAccount:
    def __init__(self, owner, balance=0):
        self._owner = owner        # protected
        self.__balance = balance    # private (name mangling)

    def deposit(self, amount):
        if amount > 0:
            self.__balance += amount

    def get_balance(self):  # controlled access
        return self.__balance


# 2. ABSTRACTION - Show WHAT, hide HOW
from abc import ABC, abstractmethod

class PaymentProcessor(ABC):
    @abstractmethod
    def process_payment(self, amount): pass

    @abstractmethod
    def refund(self, transaction_id): pass

class StripePayment(PaymentProcessor):
    def process_payment(self, amount):
        # Stripe-specific implementation hidden
        return f"Charged ${amount} via Stripe"

    def refund(self, transaction_id):
        return f"Refunded {transaction_id}"


# 3. INHERITANCE - Reuse + extend behavior
class Vehicle:
    def __init__(self, brand, speed):
        self.brand = brand
        self.speed = speed

    def move(self):
        return f"{self.brand} moving at {self.speed}"

class Car(Vehicle):
    def __init__(self, brand, speed, num_doors):
        super().__init__(brand, speed)
        self.num_doors = num_doors


# 4. POLYMORPHISM - Same interface, different behavior
class Dog:
    def speak(self): return "Woof!"

class Cat:
    def speak(self): return "Meow!"

def make_speak(animal):  # Works with any object that has speak()
    return animal.speak()

# Duck typing in Python - no need for common base class!
print(make_speak(Dog()))  # Woof!
print(make_speak(Cat()))  # Meow!
```

---

## 🏛️ SOLID Principles

### S - Single Responsibility Principle

> A class should have only ONE reason to change.

```python
# ❌ BAD - Does too many things
class User:
    def __init__(self, name, email):
        self.name = name
        self.email = email

    def save_to_db(self): pass       # DB logic
    def send_email(self): pass       # Email logic
    def generate_report(self): pass  # Report logic


# ✅ GOOD - Each class has one job
class User:
    def __init__(self, name, email):
        self.name = name
        self.email = email

class UserRepository:
    def save(self, user): pass

class EmailService:
    def send(self, user, message): pass

class ReportGenerator:
    def generate(self, user): pass
```

### O - Open/Closed Principle

> Open for EXTENSION, closed for MODIFICATION.

```python
# ❌ BAD - Must modify class to add new shape
class AreaCalculator:
    def calculate(self, shape):
        if shape.type == "circle":
            return 3.14 * shape.radius ** 2
        elif shape.type == "rectangle":
            return shape.width * shape.height
        # Must add elif for every new shape!


# ✅ GOOD - Extend by adding new classes
class Shape(ABC):
    @abstractmethod
    def area(self): pass

class Circle(Shape):
    def __init__(self, radius):
        self.radius = radius
    def area(self):
        return 3.14 * self.radius ** 2

class Rectangle(Shape):
    def __init__(self, w, h):
        self.w, self.h = w, h
    def area(self):
        return self.w * self.h

# Adding Triangle? Just add a new class! No modification needed.
class Triangle(Shape):
    def __init__(self, base, height):
        self.base, self.height = base, height
    def area(self):
        return 0.5 * self.base * self.height
```

### L - Liskov Substitution Principle

> Subclass should be replaceable for parent without breaking things.

```python
# ❌ BAD - Square breaks Rectangle contract
class Rectangle:
    def set_width(self, w): self.width = w
    def set_height(self, h): self.height = h

class Square(Rectangle):  # Square IS-A Rectangle? Not really!
    def set_width(self, w):
        self.width = w
        self.height = w  # Breaks expectation!


# ✅ GOOD - Use composition or separate hierarchy
class Shape(ABC):
    @abstractmethod
    def area(self): pass

class Rectangle(Shape):
    def __init__(self, w, h):
        self.w, self.h = w, h
    def area(self): return self.w * self.h

class Square(Shape):
    def __init__(self, side):
        self.side = side
    def area(self): return self.side ** 2
```

### I - Interface Segregation Principle

> Don't force clients to depend on methods they don't use.

```python
# ❌ BAD - Fat interface
class Worker(ABC):
    @abstractmethod
    def work(self): pass

    @abstractmethod
    def eat(self): pass  # Robots don't eat!

class Robot(Worker):
    def work(self): return "Working..."
    def eat(self): raise NotImplementedError  # Forced to implement!


# ✅ GOOD - Segregated interfaces
class Workable(ABC):
    @abstractmethod
    def work(self): pass

class Eatable(ABC):
    @abstractmethod
    def eat(self): pass

class Human(Workable, Eatable):
    def work(self): return "Working..."
    def eat(self): return "Eating..."

class Robot(Workable):  # Only implements what it needs
    def work(self): return "Working..."
```

### D - Dependency Inversion Principle

> Depend on ABSTRACTIONS, not concrete classes.

```python
# ❌ BAD - High-level depends on low-level
class MySQLDatabase:
    def save(self, data): pass

class UserService:
    def __init__(self):
        self.db = MySQLDatabase()  # Tightly coupled!


# ✅ GOOD - Depend on abstraction
class Database(ABC):
    @abstractmethod
    def save(self, data): pass

class MySQLDatabase(Database):
    def save(self, data): print("Saved to MySQL")

class MongoDB(Database):
    def save(self, data): print("Saved to MongoDB")

class UserService:
    def __init__(self, db: Database):  # Injected! Can swap easily
        self.db = db

# Usage
service = UserService(MySQLDatabase())
service = UserService(MongoDB())  # Easy to switch!
```

---

## 🎯 Interview Tip

When asked "Design X":

1. **Identify the entities** (nouns = classes)
2. **Identify relationships** (has-a, is-a)
3. **Apply SOLID** where it makes sense
4. **Don't over-engineer** - Keep it simple, explain trade-offs
