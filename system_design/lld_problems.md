# LLD Practice Problems (with Solutions)

## 🎯 Top 10 LLD Questions for SDE-1

| #   | Problem            | Difficulty | Frequency  |
| --- | ------------------ | ---------- | ---------- |
| 1   | Parking Lot        | ⭐⭐       | ⭐⭐⭐⭐⭐ |
| 2   | Tic-Tac-Toe        | ⭐⭐       | ⭐⭐⭐⭐⭐ |
| 3   | Library Management | ⭐⭐       | ⭐⭐⭐⭐   |
| 4   | Snake & Ladder     | ⭐⭐⭐     | ⭐⭐⭐⭐   |
| 5   | LRU Cache          | ⭐⭐⭐     | ⭐⭐⭐⭐⭐ |
| 6   | Elevator System    | ⭐⭐⭐     | ⭐⭐⭐     |
| 7   | ATM System         | ⭐⭐       | ⭐⭐⭐     |
| 8   | File System        | ⭐⭐⭐     | ⭐⭐⭐     |
| 9   | Logger System      | ⭐⭐       | ⭐⭐⭐     |
| 10  | Vending Machine    | ⭐⭐       | ⭐⭐⭐     |

---

## How to Approach ANY LLD Problem (5-Step Framework)

### Step 1: Clarify Requirements (2-3 min)

- Ask: What are the core features?
- Ask: What entities exist?
- Ask: What scale? (for SDE-1, usually small scale)

### Step 2: Identify Entities (Classes) (2-3 min)

- Nouns in requirements = Classes
- Verbs = Methods

### Step 3: Define Relationships (2 min)

- Has-a (composition) vs Is-a (inheritance)
- One-to-many? Many-to-many?

### Step 4: Draw Class Diagram (3-5 min)

- Just boxes with class name, key attributes, key methods
- Show connections

### Step 5: Write Code (15-20 min)

- Start with enums / constants
- Then core classes
- Then service/manager class
- Add strategy or pattern if appropriate

---

## Problem 1: Parking Lot Design

### Requirements

- Multiple floors, multiple spots per floor
- 3 spot types: Compact, Large, Handicapped
- 3 vehicle types: Motorcycle, Car, Bus
- Track which spots are free/occupied
- Allow park and unpark

### Solution

```python
from enum import Enum
from abc import ABC, abstractmethod
from datetime import datetime

# Enums
class VehicleType(Enum):
    MOTORCYCLE = 1
    CAR = 2
    BUS = 3

class SpotType(Enum):
    COMPACT = 1
    LARGE = 2
    HANDICAPPED = 3

# Vehicle hierarchy
class Vehicle:
    def __init__(self, license_plate: str, vehicle_type: VehicleType):
        self.license_plate = license_plate
        self.vehicle_type = vehicle_type

class Car(Vehicle):
    def __init__(self, license_plate):
        super().__init__(license_plate, VehicleType.CAR)

class Bus(Vehicle):
    def __init__(self, license_plate):
        super().__init__(license_plate, VehicleType.BUS)

# Parking Spot
class ParkingSpot:
    def __init__(self, spot_id: int, spot_type: SpotType, floor: int):
        self.spot_id = spot_id
        self.spot_type = spot_type
        self.floor = floor
        self.vehicle = None  # None = available

    @property
    def is_available(self):
        return self.vehicle is None

    def park(self, vehicle: Vehicle):
        if not self.is_available:
            raise Exception("Spot already occupied")
        self.vehicle = vehicle

    def unpark(self):
        vehicle = self.vehicle
        self.vehicle = None
        return vehicle

# Parking Ticket
class ParkingTicket:
    def __init__(self, vehicle: Vehicle, spot: ParkingSpot):
        self.ticket_id = id(self)
        self.vehicle = vehicle
        self.spot = spot
        self.entry_time = datetime.now()
        self.exit_time = None

# Parking Lot (Main Service)
class ParkingLot:
    def __init__(self, name: str, floors: int, spots_per_floor: int):
        self.name = name
        self.spots = []
        self.tickets = {}  # license_plate -> ticket

        # Initialize spots
        for floor in range(floors):
            for i in range(spots_per_floor):
                spot_type = SpotType.COMPACT if i < spots_per_floor // 2 else SpotType.LARGE
                self.spots.append(ParkingSpot(floor * spots_per_floor + i, spot_type, floor))

    def _find_available_spot(self, vehicle: Vehicle) -> ParkingSpot:
        """Find first available matching spot"""
        required = SpotType.COMPACT if vehicle.vehicle_type == VehicleType.CAR else SpotType.LARGE
        for spot in self.spots:
            if spot.is_available and spot.spot_type == required:
                return spot
        return None

    def park_vehicle(self, vehicle: Vehicle) -> ParkingTicket:
        if vehicle.license_plate in self.tickets:
            raise Exception("Vehicle already parked")

        spot = self._find_available_spot(vehicle)
        if not spot:
            raise Exception("No available spot")

        spot.park(vehicle)
        ticket = ParkingTicket(vehicle, spot)
        self.tickets[vehicle.license_plate] = ticket
        return ticket

    def unpark_vehicle(self, license_plate: str) -> ParkingTicket:
        if license_plate not in self.tickets:
            raise Exception("Vehicle not found")

        ticket = self.tickets.pop(license_plate)
        ticket.spot.unpark()
        ticket.exit_time = datetime.now()
        return ticket

    def get_available_spots(self) -> int:
        return sum(1 for s in self.spots if s.is_available)

# Usage
lot = ParkingLot("City Parking", floors=3, spots_per_floor=10)
car = Car("KA-01-1234")
ticket = lot.park_vehicle(car)
print(f"Parked at spot {ticket.spot.spot_id}, floor {ticket.spot.floor}")
print(f"Available spots: {lot.get_available_spots()}")
lot.unpark_vehicle("KA-01-1234")
print(f"Available spots after unpark: {lot.get_available_spots()}")
```

**Key design points to mention in interview:**

- Single Responsibility: Vehicle, Spot, Ticket, ParkingLot all separate
- Open/Closed: Easy to add new vehicle or spot types
- Could add Strategy pattern for pricing (hourly, flat rate)

---

## Problem 2: Tic-Tac-Toe

### Requirements

- 2 players, N×N board
- Players alternate turns placing X or O
- Win conditions: row, column, or diagonal complete

### Solution

```python
from enum import Enum

class Mark(Enum):
    EMPTY = "."
    X = "X"
    O = "O"

class Player:
    def __init__(self, name: str, mark: Mark):
        self.name = name
        self.mark = mark

class Board:
    def __init__(self, size: int = 3):
        self.size = size
        self.grid = [[Mark.EMPTY] * size for _ in range(size)]
        self.moves_count = 0
        # Track sums for O(1) win check
        self.rows = [0] * size
        self.cols = [0] * size
        self.diag = 0
        self.anti_diag = 0

    def place_mark(self, row: int, col: int, mark: Mark) -> bool:
        """Place mark and return True if this move wins"""
        if not (0 <= row < self.size and 0 <= col < self.size):
            raise ValueError("Invalid position")
        if self.grid[row][col] != Mark.EMPTY:
            raise ValueError("Position already taken")

        self.grid[row][col] = mark
        self.moves_count += 1

        # +1 for X, -1 for O
        val = 1 if mark == Mark.X else -1

        self.rows[row] += val
        self.cols[col] += val
        if row == col:
            self.diag += val
        if row + col == self.size - 1:
            self.anti_diag += val

        # Check win: if any sum equals ±size, someone won
        target = self.size * val
        return (self.rows[row] == target or
                self.cols[col] == target or
                self.diag == target or
                self.anti_diag == target)

    def is_full(self) -> bool:
        return self.moves_count == self.size * self.size

    def display(self):
        for row in self.grid:
            print(" | ".join(m.value for m in row))
            print("-" * (self.size * 4 - 3))

class TicTacToe:
    def __init__(self, size: int = 3):
        self.board = Board(size)
        self.players = [
            Player("Player 1", Mark.X),
            Player("Player 2", Mark.O),
        ]
        self.current_turn = 0

    @property
    def current_player(self) -> Player:
        return self.players[self.current_turn]

    def play(self, row: int, col: int) -> str:
        """Returns status: 'continue', 'win', or 'draw'"""
        player = self.current_player
        won = self.board.place_mark(row, col, player.mark)

        if won:
            return f"{player.name} ({player.mark.value}) wins!"

        if self.board.is_full():
            return "Draw!"

        self.current_turn = 1 - self.current_turn  # Toggle 0/1
        return "continue"

# Usage
game = TicTacToe()
print(game.play(0, 0))  # X plays center-top-left
print(game.play(1, 1))  # O plays center
print(game.play(0, 1))  # X plays
print(game.play(2, 2))  # O plays
print(game.play(0, 2))  # X plays - wins row 0!
game.board.display()
```

**Key design points:**

- O(1) win check using row/col/diagonal sums (interview gold!)
- Clean separation: Board handles grid logic, Game handles turns
- Extensible: Easy to add NxN board, AI player

---

## Problem 3: LRU Cache (LeetCode #146!)

### Requirements

- Fixed capacity cache
- get(key): Return value or -1
- put(key, value): Insert/update, evict LRU if full
- Both operations O(1)

### Solution

```python
class Node:
    """Doubly linked list node"""
    def __init__(self, key=0, val=0):
        self.key = key
        self.val = val
        self.prev = None
        self.next = None

class LRUCache:
    def __init__(self, capacity: int):
        self.capacity = capacity
        self.cache = {}  # key -> Node

        # Dummy head and tail (sentinel nodes)
        self.head = Node()
        self.tail = Node()
        self.head.next = self.tail
        self.tail.prev = self.head

    def _remove(self, node: Node):
        """Remove node from linked list"""
        node.prev.next = node.next
        node.next.prev = node.prev

    def _add_to_front(self, node: Node):
        """Add node right after head (most recently used)"""
        node.prev = self.head
        node.next = self.head.next
        self.head.next.prev = node
        self.head.next = node

    def get(self, key: int) -> int:
        if key not in self.cache:
            return -1

        node = self.cache[key]
        self._remove(node)       # Remove from current position
        self._add_to_front(node) # Move to front (recently used)
        return node.val

    def put(self, key: int, value: int):
        if key in self.cache:
            self._remove(self.cache[key])

        node = Node(key, value)
        self.cache[key] = node
        self._add_to_front(node)

        if len(self.cache) > self.capacity:
            # Evict LRU (node before tail)
            lru = self.tail.prev
            self._remove(lru)
            del self.cache[lru.key]

# Usage
cache = LRUCache(2)
cache.put(1, 1)
cache.put(2, 2)
print(cache.get(1))   # 1
cache.put(3, 3)        # Evicts key 2
print(cache.get(2))   # -1 (evicted!)
cache.put(4, 4)        # Evicts key 1
print(cache.get(1))   # -1 (evicted!)
print(cache.get(3))   # 3
print(cache.get(4))   # 4
```

**Key design points:**

- HashMap (O(1) lookup) + Doubly Linked List (O(1) insert/remove) = O(1) for both get/put
- Sentinel nodes (dummy head/tail) simplify edge cases
- This is BOTH an LLD AND a DSA question - know it cold!

---

## 🎯 Practice Order

### Must-Do (Practice these 3 before interviews)

1. **Parking Lot** - Classic OOP, covers SOLID principles
2. **Tic-Tac-Toe** - O(1) win check is a great talking point
3. **LRU Cache** - Also LeetCode #146, dual purpose!

### Nice to Have (If you have extra time)

4. Library Management - Simple CRUD operations
5. Snake & Ladder - State management, randomness
6. Vending Machine - State pattern

### Approach Template for New Problems

```
1. What are the ENTITIES? (nouns → classes)
2. What are the ACTIONS? (verbs → methods)
3. What RELATIONSHIPS exist? (has-a, is-a)
4. What PATTERNS apply? (factory, strategy, observer?)
5. What are the EDGE CASES?
```
