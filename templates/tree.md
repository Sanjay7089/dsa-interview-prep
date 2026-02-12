# Tree Algorithms

**Traversals:**

- In-order (Left, Root, Right) -> Sorted for BST
- Pre-order (Root, Left, Right) -> Serialize/Deserialize
- Post-order (Left, Right, Root) -> Delete, Bottom-up calculation

## 1. Depth First Search (Recursive)

```python
def dfs(root):
    if not root: return

    # Pre-order
    dfs(root.left)
    # In-order
    dfs(root.right)
    # Post-order
```

## 2. Iterative Traversals (Using Stack)

### Pre-order

```python
stack = [root]
while stack:
    node = stack.pop()
    if node:
        # Process node
        stack.append(node.right) # Push right first
        stack.append(node.left)
```

### In-order

```python
stack = []
curr = root
while curr or stack:
    while curr:
        stack.append(curr)
        curr = curr.left
    curr = stack.pop()
    # Process curr
    curr = curr.right
```

## 3. Level Order Traversal (BFS)

```python
q = deque([root])
while q:
    for i in range(len(q)):
        node = q.popleft()
        if node.left: q.append(node.left)
        if node.right: q.append(node.right)
```

## 4. Lowest Common Ancestor (LCA)

### BST (Binary Search Tree)

```python
def lca_bst(root, p, q):
    curr = root
    while curr:
        if p.val > curr.val and q.val > curr.val:
            curr = curr.right
        elif p.val < curr.val and q.val < curr.val:
            curr = curr.left
        else:
            return curr # Split point
```

### Binary Tree (General)

```python
def lca(root, p, q):
    if not root or root == p or root == q:
        return root

    left = lca(root.left, p, q)
    right = lca(root.right, p, q)

    if left and right: return root
    return left if left else right
```

## 5. Validate BST

_Pass range (min, max) down._

```python
def isValidBST(root):
    def valid(node, left, right):
        if not node: return True
        if not (left < node.val < right):
            return False

        return (valid(node.left, left, node.val) and
                valid(node.right, node.val, right))

    return valid(root, float('-inf'), float('inf'))
```
