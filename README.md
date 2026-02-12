# DSA Interview Single Source of Truth

**Goal:** Zero-search, offline-friendly, pattern-oriented reference for coding interviews.

## 🚀 Quick Navigation

### 🧠 Patterns & Strategies

_Identify the problem type and apply the standard recipe._

- [Sliding Window](./patterns/sliding_window.md) (Fixed, Variable, Aux Data)
- [Two Pointers](./patterns/two_pointers.md) (Opposite Ends, Fast/Slow)
- [Binary Search](./patterns/binary_search.md) (Standard, Answer Range, Rotated)
- [Backtracking](./patterns/backtracking.md) (Subsets, Permutations, Combinations)
- [Intervals](./patterns/intervals.md) (Merge, Insert, Overlap)
- [Cyclic Sort](./patterns/cyclic_sort.md) (1 to N, Duplicates)
- [Linked List Reversal](./patterns/linked_list.md) (In-place, K-group)
- [Matrix Traversal](./patterns/matrix.md) (BFS, DFS, Islands)

### 🏗️ Data Structure Templates

_Copy-paste ready implementations._

- [Graph Algorithms](./templates/graph.md) (BFS, DFS, Dijkstra, Union-Find, Topo Sort)
- [Trees & BST](./templates/tree.md) (Traversals, LCA, Construction)
- [Heaps / Priority Queues](./templates/heap.md) (K-th Element, Median)
- [Trie (Prefix Tree)](./templates/trie.md)
- [LRU Cache](./templates/lru_cache.md)

### 🔢 Math & Bit Manipulation

- [Number Theory](./math_geometry/number_theory.md) (GCD, LCM, Primes)
- [Bit Manipulation](./math_geometry/bit_manipulation.md) (XOR tricks, masking)

### ⚡ Cheat Sheets

- [Big O Complexities](./big_o_cheatsheet.md)
- [Python Tricks & STD Lib](./python_tricks.md)

---

## 🆘 Emergency Mental Model Checklist

When stuck, ask:

1. **Can I sort it?** (O(N log N)) -> Enables Two Pointers, Binary Search, Greedy.
2. **Is it a search problem?**
   - **Sorted?** -> Binary Search.
   - **Shortest Path?** -> BFS.
   - **Combinations/Permutations?** -> Backtracking.
3. **Is it an optimization problem?**
   - **Overlapping subproblems?** -> DP / Memoization.
   - **Local optimum leads to global?** -> Greedy.
4. **Is it about connectivity?** -> DFS, BFS, Union-Find.
5. **Top K elements?** -> Heap.
6. **Common strings/prefixes?** -> Trie, Map.
