# Linked List Patterns

**Complexity:** O(N) Time, O(1) Space (usually).

## 1. Fast & Slow Pointers

_See [Two Pointers](./two_pointers.md) for Cycle Detection & Middle._

## 2. Reverse Linked List (Iterative)

_Gold Standard._

```python
def reverse_list(head):
    prev, curr = None, head
    while curr:
        temp = curr.next  # Save next
        curr.next = prev  # Reverse
        prev = curr       # Move prev
        curr = temp       # Move curr
    return prev # New head
```

### Recursive Reverse

```python
def reverse_recursive(head):
    if not head or not head.next:
        return head

    new_head = reverse_recursive(head.next)
    head.next.next = head # Reverse pointer
    head.next = None
    return new_head
```

## 3. Reverse Nodes in K-Group (Hard)

_Reverse every k nodes._

```python
def reverseKGroup(head, k):
    # Dummy node simplifies edge cases
    dummy = ListNode(0, head)
    groupPrev = dummy

    while True:
        kth = get_kth(groupPrev, k)
        if not kth:
            break
        groupNext = kth.next

        # Reverse Group logic
        prev, curr = kth.next, groupPrev.next # prev is initially groupNext

        while curr != groupNext:
            temp = curr.next
            curr.next = prev
            prev = curr
            curr = temp

        # Connect with previous part
        temp = groupPrev.next # Needs to point to next group
        groupPrev.next = kth
        groupPrev = temp # Move for next group

    return dummy.next

def get_kth(curr, k):
    while curr and k > 0:
        curr = curr.next
        k -= 1
    return curr
```

## 4. Merge Two Sorted Lists

```python
def mergeTwoLists(l1, l2):
    dummy = ListNode()
    tail = dummy

    while l1 and l2:
        if l1.val < l2.val:
            tail.next = l1
            l1 = l1.next
        else:
            tail.next = l2
            l2 = l2.next
        tail = tail.next

    if l1: tail.next = l1
    if l2: tail.next = l2

    return dummy.next
```
