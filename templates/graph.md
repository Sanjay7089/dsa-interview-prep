# Graph Algorithms

**Representations:**

- Adjacency List: `graph = {node: [neighbors]}` (Most common)
- Adjacency Matrix: `grid[r][c]`

## 1. BFS (Shortest Path in Unweighted Graph)

_Level order traversal._

```python
from collections import deque

def bfs(start_node):
    queue = deque([start_node])
    visited = set([start_node])
    distance = 0

    while queue:
        for i in range(len(queue)): # Process level
            node = queue.popleft()
            if node == target: return distance

            for neighbor in graph[node]:
                if neighbor not in visited:
                    visited.add(neighbor)
                    queue.append(neighbor)
        distance += 1
    return -1
```

## 2. DFS (Connectivity, Cycles)

_Recursive or Iterative (Stack)._

```python
visited = set()

def dfs(node):
    if node in visited: return
    visited.add(node)

    # Process node

    for neighbor in graph[node]:
        dfs(neighbor)
```

## 3. Union-Find (Disjoint Set Union)

_Cycle detection in undirected graph, Connected components._
_Optimization: Path Compression + Union by Rank._

```python
class UnionFind:
    def __init__(self, n):
        self.parent = list(range(n))
        self.rank = [1] * n

    def find(self, n):
        p = self.parent[n]
        while p != self.parent[p]:
            self.parent[p] = self.parent[self.parent[p]] # Path compression
            p = self.parent[p]
        return p

    def union(self, n1, n2):
        p1, p2 = self.find(n1), self.find(n2)
        if p1 == p2:
            return False # Already connected (Cycle!)

        if self.rank[p1] > self.rank[p2]:
            self.parent[p2] = p1
            self.rank[p1] += self.rank[p2]
        else:
            self.parent[p1] = p2
            self.rank[p2] += self.rank[p1]
        return True
```

## 4. Topological Sort (Kahn's Algorithm)

_Course Schedule, Dependency Resolution. Requires DAG._

```python
def topo_sort(numCourses, prerequisites):
    # 1. Build Graph & Indegree
    graph = defaultdict(list)
    indegree = [0] * numCourses

    for course, pre in prerequisites:
        graph[pre].append(course)
        indegree[course] += 1

    # 2. Add 0 indegree nodes to queue
    queue = deque([i for i in range(numCourses) if indegree[i] == 0])
    order = []

    # 3. Process
    while queue:
        node = queue.popleft()
        order.append(node)

        for neighbor in graph[node]:
            indegree[neighbor] -= 1
            if indegree[neighbor] == 0:
                queue.append(neighbor)

    return order if len(order) == numCourses else [] # Cycle check
```

## 5. Dijkstra's Algorithm (Shortest Path Weighted)

_No negative edges._

```python
import heapq

def dijkstra(start, n):
    min_dist = {i: float('inf') for i in range(n)}
    min_dist[start] = 0

    # Min Heap: (distance, node)
    heap = [(0, start)]

    while heap:
        dist, node = heapq.heappop(heap)

        if dist > min_dist[node]: continue # Optimization

        for neighbor, weight in graph[node]:
            new_dist = dist + weight
            if new_dist < min_dist[neighbor]:
                min_dist[neighbor] = new_dist
                heapq.heappush(heap, (new_dist, neighbor))

    return min_dist
```
