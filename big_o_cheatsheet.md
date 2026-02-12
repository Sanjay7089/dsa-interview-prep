# Big O Complexity Cheat Sheet

## 📊 Common Data Structure Operations

| Data Structure         |  Access  | Search  | Insertion | Deletion |
| ---------------------- | :------: | :-----: | :-------: | :------: |
| **Array**              |   O(1)   |  O(N)   |   O(N)    |   O(N)   |
| **Stack**              |   O(N)   |  O(N)   |   O(1)    |   O(1)   |
| **Queue**              |   O(N)   |  O(N)   |   O(1)    |   O(1)   |
| **Singly Linked List** |   O(N)   |  O(N)   |  O(1)\*   |  O(1)\*  |
| **Doubly Linked List** |   O(N)   |  O(N)   |   O(1)    |   O(1)   |
| **Hash Table**         |   N/A    |  O(1)   |   O(1)    |   O(1)   |
| **BST**                |   O(N)   |  O(N)   |   O(N)    |   O(N)   |
| **AVL / Red-Black**    | O(logN)  | O(logN) |  O(logN)  | O(logN)  |
| **Binary Heap**        | O(1)\*\* |  O(N)   |  O(logN)  | O(logN)  |

_\* Assuming you have a reference to the node._
\*\*\* Find Min/Max is O(1).

## 📉 Array Sorting Algorithms

| Algorithm     | Time (Best) | Time (Avg) | Time (Worst) |  Space   |
| ------------- | :---------: | :--------: | :----------: | :------: |
| **Quicksort** | O(N log N)  | O(N log N) |    O(N^2)    | O(log N) |
| **Mergesort** | O(N log N)  | O(N log N) |  O(N log N)  |   O(N)   |
| **Heapsort**  | O(N log N)  | O(N log N) |  O(N log N)  |   O(1)   |
| **TimSort**   |    O(N)     | O(N log N) |  O(N log N)  |   O(N)   |

> **Note:** Python's `sort()` and `sorted()` use **TimSort**.

## 📏 Input Size vs. Time Complexity Limits

_Approximations for 1 second execution time (typically ~10^8 operations)._

| Input Size (N) | Max Complexity   | Example Algo                |
| :------------- | :--------------- | :-------------------------- |
| **N <= 10**    | O(N!) or O(N^6)  | Permutations                |
| **N <= 20**    | O(2^N)           | Subsets, Recursion          |
| **N <= 100**   | O(N^4)           | Deep DP                     |
| **N <= 500**   | O(N^3)           | Floyd-Warshall, Matrix Mult |
| **N <= 2,000** | O(N^2)           | 2-Nested Loops, Bubble Sort |
| **N <= 10^5**  | O(N log N)       | Sorting, Heap, Map/Set      |
| **N <= 10^6**  | O(N)             | Hash Map, Two Pointers      |
| **N > 10^8**   | O(log N) or O(1) | Binary Search, Math         |

## 🧠 Master Theorem Reference

`T(n) = a * T(n/b) + f(n)`

1. **Case 1:** `f(n) < n^log_b(a)` -> **O(n^log_b(a))** (Recursion dominates)
2. **Case 2:** `f(n) = n^log_b(a)` -> **O(n^log_b(a) \* log n)** (Balanced)
3. **Case 3:** `f(n) > n^log_b(a)` -> **O(f(n))** (Work outside recursion dominates)
