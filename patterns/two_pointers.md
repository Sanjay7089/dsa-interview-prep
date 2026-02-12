# Two Pointers

**Usage:** Sorted arrays (usually), pairs, or linked lists.
**Time Complexity:** O(N).
**Space Complexity:** O(1).

## 1. Opposite Ends (Sorted Input)

_Find two numbers that sum to target, or reverse/swap logic._

```python
def two_sum_sorted(arr, target):
    left, right = 0, len(arr) - 1

    while left < right:
        curr_sum = arr[left] + arr[right]

        if curr_sum == target:
            return [left, right]
        elif curr_sum < target:
            left += 1  # Need larger sum
        else:
            right -= 1 # Need smaller sum

    return []
```

## 2. Fast & Slow Pointers (Tortoise & Hare)

_Cycle detection, finding middle of Linked List._

### Linked List Cycle

```python
def has_cycle(head):
    slow, fast = head, head
    while fast and fast.next:
        slow = slow.next
        fast = fast.next.next
        if slow == fast:
            return True  # Cycle detected
    return False
```

### Middle of Linked List

```python
def find_middle(head):
    slow, fast = head, head
    while fast and fast.next:
        slow = slow.next
        fast = fast.next.next
    return slow # Slow is at middle
```

## 3. Merging Sorted Arrays

_Basis for Merge Sort._

```python
def merge_sorted(arr1, arr2):
    p1, p2 = 0, 0
    p1_end, p2_end = len(arr1), len(arr2)
    result = []

    while p1 < p1_end and p2 < p2_end:
        if arr1[p1] < arr2[p2]:
            result.append(arr1[p1])
            p1 += 1
        else:
            result.append(arr2[p2])
            p2 += 1

    # Append remaining
    result.extend(arr1[p1:])
    result.extend(arr2[p2:])
    return result
```
