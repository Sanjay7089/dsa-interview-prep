# Sliding Window

**Usage:** Contiguous subarrays or substrings.
**Time Complexity:** O(N) (each element added/removed at most once).

## 1. Fixed Size Window

_Find the best window of size `k`._

```python
def fixed_sliding_window(arr, k):
    curr_sum = 0
    max_sum = float('-inf')

    for i in range(len(arr)):
        curr_sum += arr[i]  # Add new element

        if i >= k - 1:
            max_sum = max(max_sum, curr_sum)
            curr_sum -= arr[i - (k - 1)]  # Remove old element

    return max_sum
```

## 2. Variable Size Window (Grow & Shrink)

_Find the longest/shortest window that satisfies a condition._

**Logic:**

1. Be greedy: Expand `right` pointer to find a valid window.
2. Be cautious: Shrink `left` pointer to optimize (make it smaller) or restore validity.

### Template: Longest Subarray

```python
def longest_window(nums):
    left = 0
    longest = 0
    curr_state = 0  # Could be sum, or a hash map

    for right in range(len(nums)):
        # 1. Add right element to state
        curr_state += nums[right]

        # 2. Shrink valid window if condition is broken
        while curr_state > LIMIT:
            curr_state -= nums[left]
            left += 1

        # 3. Update result (window is valid here)
        longest = max(longest, right - left + 1)

    return longest
```

### Template: Shortest Subarray

```python
def shortest_window(nums, target):
    left = 0
    shortest = float('inf')
    curr_sum = 0

    for right in range(len(nums)):
        curr_sum += nums[right]

        # 2. Shrink window while condition IS MET to find minimum
        while curr_sum >= target:
            shortest = min(shortest, right - left + 1)
            curr_sum -= nums[left]
            left += 1

    return shortest if shortest != float('inf') else 0
```

## 3. String Counting (with HashMap)

_Example: Longest substring with at most K distinct characters._

```python
from collections import defaultdict

def longest_k_distinct(s, k):
    counts = defaultdict(int)
    left = 0
    longest = 0

    for right, char in enumerate(s):
        counts[char] += 1

        # If more than k distinct chars, shrink
        while len(counts) > k:
            left_char = s[left]
            counts[left_char] -= 1
            if counts[left_char] == 0:
                del counts[left_char]
            left += 1

        longest = max(longest, right - left + 1)

    return longest
```
