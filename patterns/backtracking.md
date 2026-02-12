# Backtracking

**Usage:** Finding ALL solutions (Permutations, Subsets, Combinations, Sudoku).
**Time Complexity:** Exponential (O(2^N), O(N!), etc.).

## General Template

```python
def backtrack(candidate):
    if find_solution(candidate):
        output(candidate)
        return

    for next_candidate in list_of_candidates:
        if is_valid(next_candidate):
            place(next_candidate)
            backtrack(next_candidate)
            remove(next_candidate) # Backtrack!
```

## 1. Subsets (Power Set)

_All possible combinations of any length._

```python
def subsets(nums):
    res = []

    def backtrack(start, path):
        res.append(path[:]) # Add copy

        for i in range(start, len(nums)):
            path.append(nums[i])
            backtrack(i + 1, path)
            path.pop()

    backtrack(0, [])
    return res
```

### With Duplicates (Sort first!)

```python
nums.sort()
# Inside loop:
if i > start and nums[i] == nums[i-1]:
    continue
```

## 2. Permutations

_Ordering matters. [1,2] != [2,1]._

```python
def permutations(nums):
    res = []

    def backtrack(path, used): # or use a Set for 'used'
        if len(path) == len(nums):
            res.append(path[:])
            return

        for i in range(len(nums)):
            if not used[i]:
                used[i] = True
                path.append(nums[i])
                backtrack(path, used)
                path.pop()
                used[i] = False

    backtrack([], [False] * len(nums))
    return res
```

## 3. Combinations

_k elements from n._

```python
def combine(n, k):
    res = []

    def backtrack(start, path):
        if len(path) == k:
            res.append(path[:])
            return

        for i in range(start, n + 1):
            path.append(i)
            backtrack(i + 1, path)
            path.pop()

    backtrack(1, [])
    return res
```
