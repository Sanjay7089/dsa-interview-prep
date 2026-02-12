# Matrix Patterns

**Common Operations:** Traversal (BFS/DFS), Islands, Borders.
**Directions:** `[(0, 1), (1, 0), (0, -1), (-1, 0)]`

## 1. Number of Islands (DFS)

_Count connected components._

```python
def numIslands(grid):
    if not grid: return 0
    rows, cols = len(grid), len(grid[0])
    islands = 0

    def dfs(r, c):
        if (r < 0 or c < 0 or r >= rows or c >= cols or
            grid[r][c] == "0"): # "0" is water or visited
            return

        grid[r][c] = "0" # Mark visited directly on grid

        # Explore neighbors
        dfs(r+1, c)
        dfs(r-1, c)
        dfs(r, c+1)
        dfs(r, c-1)

    for r in range(rows):
        for c in range(cols):
            if grid[r][c] == "1":
                dfs(r, c)
                islands += 1
    return islands
```

## 2. Check Boundaries / Valid Cell

```python
def is_valid(r, c, rows, cols):
    return 0 <= r < rows and 0 <= c < cols
```

## 3. Rotting Oranges (BFS - Shortest Path / Multi-source)

_Use BFS for simultaneous expansion._

```python
from collections import deque

def orangesRotting(grid):
    rows, cols = len(grid), len(grid[0])
    q = deque()
    fresh = 0
    time = 0

    # 1. Initialize Queue with all rotten oranges
    for r in range(rows):
        for c in range(cols):
            if grid[r][c] == 2:
                q.append((r, c))
            elif grid[r][c] == 1:
                fresh += 1

    directions = [(0,1), (0,-1), (1,0), (-1,0)]

    while q and fresh > 0:
        for i in range(len(q)): # Level by level
            r, c = q.popleft()
            for dr, dc in directions:
                nr, nc = r + dr, c + dc
                if (0 <= nr < rows and 0 <= nc < cols and
                    grid[nr][nc] == 1):
                    grid[nr][nc] = 2
                    q.append((nr, nc))
                    fresh -= 1
        time += 1

    return time if fresh == 0 else -1
```

## 4. Spiral Matrix

_Simulation._

```python
def spiralOrder(matrix):
    res = []
    l, r = 0, len(matrix[0])
    t, b = 0, len(matrix)

    while l < r and t < b:
        # Top row
        for i in range(l, r):
            res.append(matrix[t][i])
        t += 1

        # Right col
        for i in range(t, b):
            res.append(matrix[i][r - 1])
        r -= 1

        if not (l < r and t < b): break # Check remaining

        # Bottom row
        for i in range(r - 1, l - 1, -1):
            res.append(matrix[b - 1][i])
        b -= 1

        # Left col
        for i in range(b - 1, t - 1, -1):
            res.append(matrix[i][l])
        l += 1

    return res
```
