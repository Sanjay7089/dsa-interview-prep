# Python Tricks & Standard Lib

**Goal:** Write less code, avoid bugs.

## 1. `collections`

- **Counter:** Frequency map.
  ```python
  from collections import Counter
  counts = Counter("hello") # {'l': 2, 'h': 1, ...}
  vals = counts.most_common(2) # Top 2 frequent
  ```
- **defaultdict:** Avoid `KeyError`.
  ```python
  from collections import defaultdict
  graph = defaultdict(list)
  graph[1].append(2) # Auto-initializes list
  ```
- **deque:** O(1) append/pop from both ends.
  ```python
  from collections import deque
  q = deque([1, 2, 3])
  q.popleft() # O(1) vs list.pop(0) is O(N)
  ```

## 2. `functools`

- **cache / lru_cache:** Memoization decorator.
  ```python
  from functools import cache

  @cache # Automatically memoizes results
  def fib(n):
      if n < 2: return n
      return fib(n-1) + fib(n-2)
  ```

## 3. Sorting with Keys

```python
# Sort by length
words.sort(key=len)

# Sort by multiple criteria (tuple comparison)
# e.g., Sort by count (desc), then word (asc)
words.sort(key=lambda w: (-count[w], w))
```

## 4. `bisect` (Binary Search Module)

_Don't write binary search if you don't have to._

```python
import bisect
nums = [1, 3, 4, 4, 6]

# Find insertion point (left) - Lower Bound
idx = bisect.bisect_left(nums, 4) # 2

# Find insertion point (right) - Upper Bound
idx = bisect.bisect_right(nums, 4) # 4
```

## 5. Infinite Values

```python
max_val = float('inf')
min_val = float('-inf')
```

## 6. Zipping & Unzipping

```python
names = ["Alice", "Bob"]
scores = [90, 80]
paired = list(zip(names, scores)) # [("Alice", 90), ...]

# Matrix Transpose
matrix = [[1, 2], [3, 4]]
transposed = list(zip(*matrix)) # [(1, 3), (2, 4)]
```
