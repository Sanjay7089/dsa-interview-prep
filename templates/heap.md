# Heaps / Priority Queues

**Usage:** Top K elements, Median, Scheduling.
**Complexity:** Insert O(log N), Pop Max/Min O(log N), Peek O(1).
**Python:** `heapq` implements a **Min Heap**.

## 1. Basics (Min Heap)

```python
import heapq

min_heap = []
heapq.heappush(min_heap, 10)
heapq.heappush(min_heap, 1) # [1, 10]

val = heapq.heappop(min_heap) # 1
peek = min_heap[0] # 10 (Don't use [1] etc, only [0] is guaranteed min)

# Heapify (O(N) - faster than pushing N times)
arr = [5, 3, 1, 4, 2]
heapq.heapify(arr) # arr is now a valid heap
```

## 2. Max Heap (Trick)

_Python only has Min Heap. To simulate Max Heap, **negate values**._

```python
nums = [1, 10, 5]
max_heap = [-n for n in nums]
heapq.heapify(max_heap)

largest = -heapq.heappop(max_heap) # 10
```

## 3. Top K Frequent Elements

```python
from collections import Counter
import heapq

def topKFrequent(nums, k):
    count = Counter(nums)
    # 1. Use nlargest (optimized)
    return heapq.nlargest(k, count.keys(), key=count.get)

    # 2. Or manual Heap (O(N log K) to keep heap size K)
    heap = []
    for num, freq in count.items():
        heapq.heappush(heap, (freq, num))
        if len(heap) > k:
            heapq.heappop(heap)
    return [num for freq, num in heap]
```

## 4. Find Median from Data Stream

_Two Heaps: Small Half (Max Heap), Large Half (Min Heap)._

```python
class MedianFinder:
    def __init__(self):
        self.small = [] # Max Heap (negated)
        self.large = [] # Min Heap

    def addNum(self, num):
        # Always push to max heap first
        heapq.heappush(self.small, -num)

        # Balance: Move max of small to large
        val = -heapq.heappop(self.small)
        heapq.heappush(self.large, val)

        # maintain size property: small >= large
        if len(self.small) < len(self.large):
            val = heapq.heappop(self.large)
            heapq.heappush(self.small, -val)

    def findMedian(self):
        if len(self.small) > len(self.large):
            return -self.small[0]
        else:
            return (-self.small[0] + self.large[0]) / 2.0
```
