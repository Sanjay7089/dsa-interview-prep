# Binary Search

**Usage:** SORTED arrays, or monotonic functions (T, T, ..., F, F).
**Time Complexity:** O(log N).

## 1. Standard Template (Find Exact Value)

```python
def binary_search(nums, target):
    left, right = 0, len(nums) - 1

    while left <= right:
        mid = left + (right - left) // 2  # Prevent overflow (not really needed in Python)

        if nums[mid] == target:
            return mid
        elif nums[mid] < target:
            left = mid + 1
        else:
            right = mid - 1

    return -1
```

## 2. Find Boundary (First True / Min satisfying condition)

_Example: First Bad Version, Lower Bound._

**Logic:**

- If `condition(mid)` is True, it _might_ be the answer, but there could be one to the left. `right = mid`.
- If `condition(mid)` is False, answer must be to the right. `left = mid + 1`.

```python
def first_bad_version(n):
    left, right = 1, n # or 0, len(arr)
    ans = -1

    while left <= right:
        mid = left + (right - left) // 2

        if is_bad(mid):
            ans = mid
            right = mid - 1 # Look for earlier bad version
        else:
            left = mid + 1

    return ans
```

_Note: Sometimes `while left < right` is used with `right = mid`. That avoids the `ans` variable but requires careful initialization._

### Alternative: While Left < Right (No `ans` variable)

```python
def lower_bound(nums, target):
    left, right = 0, len(nums)
    # Notice right is len(nums) not len-1, to allow returning 'insert position' at end

    while left < right:
        mid = left + (right - left) // 2
        if nums[mid] >= target:
            right = mid # Target might be here, or to the left
        else:
            left = mid + 1

    return left # Points to first index >= target
```

## 3. Search in Rotated Sorted Array

_Key: Determine which half is sorted._

```python
def search_rotated(nums, target):
    left, right = 0, len(nums) - 1

    while left <= right:
        mid = (left + right) // 2
        if nums[mid] == target: return mid

        # Check if left half is sorted
        if nums[left] <= nums[mid]:
            if nums[left] <= target < nums[mid]: # Target in left half
                right = mid - 1
            else:
                left = mid + 1
        # Right half must be sorted
        else:
            if nums[mid] < target <= nums[right]: # Target in right half
                left = mid + 1
            else:
                right = mid - 1
    return -1
```
