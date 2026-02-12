# Dynamic Programming

**Concept:** Recursion + Caching (Memoization) OR Iteration + Table (Tabulation).
**Key:** Identify "States" that uniquely define a subproblem.

## 1. 1D DP (Climbing Stairs, House Robber)

_State: Index `i`._

```python
# Top-Down (Memoization)
def rob(nums):
    memo = {}
    def dp(i):
        if i >= len(nums): return 0
        if i in memo: return memo[i]

        # Choice: Rob this one (idx i + 2) OR Skip (idx i + 1)
        memo[i] = max(nums[i] + dp(i + 2), dp(i + 1))
        return memo[i]
    return dp(0)

# Bottom-Up (Tabulation - Space Optimized)
def rob_space_opt(nums):
    rob1, rob2 = 0, 0
    for n in nums:
        # [rob1, rob2, n, ...]
        temp = max(n + rob1, rob2)
        rob1 = rob2
        rob2 = temp
    return rob2
```

## 2. 0/1 Knapsack (Subset Sum)

_State: Index `i`, Remaining Capacity `w`._

```python
def knapsack(weights, values, capacity):
    # dp[w] = max value with capacity w
    dp = [0] * (capacity + 1)

    for i in range(len(weights)):
        # Reverse loop to avoid using same item multiple times in one step
        for w in range(capacity, weights[i] - 1, -1):
            dp[w] = max(dp[w], values[i] + dp[w - weights[i]])

    return dp[capacity]
```

## 3. Unbounded Knapsack (Coin Change)

_State: Index `i`, Remaining Amount `a`. infinite usage allowed._

```python
def coinChange(coins, amount):
    # dp[a] = min coins to make amount a
    dp = [float('inf')] * (amount + 1)
    dp[0] = 0

    for a in range(1, amount + 1):
        for c in coins:
            if a - c >= 0:
                dp[a] = min(dp[a], 1 + dp[a - c])

    return dp[amount] if dp[amount] != float('inf') else -1
```

## 4. Longest Common Subsequence (LCS)

_State: Index `i` in s1, Index `j` in s2._

```python
def longestCommonSubsequence(text1, text2):
    m, n = len(text1), len(text2)
    dp = [[0] * (n + 1) for _ in range(m + 1)]

    for i in range(1, m + 1):
        for j in range(1, n + 1):
            if text1[i - 1] == text2[j - 1]:
                dp[i][j] = 1 + dp[i - 1][j - 1]
            else:
                dp[i][j] = max(dp[i - 1][j], dp[i][j - 1])

    return dp[m][n]
```

## 5. Palindromes (Longest Palindromic Substring)

_State: Expand from center._

```python
def longestPalindrome(s):
    res = ""

    def expand(l, r):
        while l >= 0 and r < len(s) and s[l] == s[r]:
            l -= 1
            r += 1
        return s[l + 1 : r]

    for i in range(len(s)):
        # Odd length (center is i)
        s1 = expand(i, i)
        # Even length (center is i, i+1)
        s2 = expand(i, i + 1)

        res = max(res, s1, s2, key=len)
    return res
```
