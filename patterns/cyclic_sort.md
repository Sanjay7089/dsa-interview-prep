# Cyclic Sort

**Usage:** Array of size **N** containing numbers **1 to N** (or 0 to N).
**Goal:** Sort in O(N) time without extra space. Finds missing/duplicate numbers.

## Core Logic

Since numbers are in range [1, N], the number `val` belongs at index `val - 1`.
Swap until the current number is in the correct spot.

```python
def cyclic_sort(nums):
    i = 0
    while i < len(nums):
        correct_idx = nums[i] - 1  # For 1-based (use nums[i] for 0-based)

        # If number is in valid range AND not at correct spot
        if 0 <= correct_idx < len(nums) and nums[i] != nums[correct_idx]:
            # Swap
            nums[i], nums[correct_idx] = nums[correct_idx], nums[i]
        else:
            i += 1
```

## Find Missing Number (0 to N)

```python
def missing_number(nums):
    i = 0
    n = len(nums)
    while i < n:
        correct_idx = nums[i]
        if correct_idx < n and nums[i] != nums[correct_idx]:
             nums[i], nums[correct_idx] = nums[correct_idx], nums[i]
        else:
            i += 1

    # Iterate to find the mismatch
    for i in range(n):
        if nums[i] != i:
            return i
    return n
```

## Find Duplicate (1 to N)

```python
def find_duplicate(nums):
    i = 0
    while i < len(nums):
        if nums[i] != i + 1:
            correct_idx = nums[i] - 1
            if nums[i] != nums[correct_idx]:
                nums[i], nums[correct_idx] = nums[correct_idx], nums[i]
            else:
                return nums[i] # Found duplicate
        else:
            i += 1
    return -1
```
