# Bit Manipulation

**Usage:** Low-level optimization, States, Unique IDs.
**Operators:** `&` (AND), `|` (OR), `^` (XOR), `~` (NOT), `<<` (Left Shift), `>>` (Right Shift).

## 1. Common Tricks

- **Check even/odd:** `(n & 1) == 0` (Even), `== 1` (Odd)
- **Multiply/Divide by 2:** `n << 1` (Mult), `n >> 1` (Div)
- **Get i-th bit:** `(n >> i) & 1`
- **Set i-th bit:** `n | (1 << i)`
- **Clear i-th bit:** `n & ~(1 << i)`
- **Toggle i-th bit:** `n ^ (1 << i)`

## 2. XOR Properties

`x ^ x = 0`
`x ^ 0 = x`

### Find Single Number in Array of Duplicates (Appearing Twice)

```python
def singleNumber(nums):
    res = 0
    for n in nums:
        res ^= n
    return res
```

## 3. Number of Set Bits (Hamming Weight)

_Kernighan's Algorithm: `n & (n-1)` removes the rightmost set bit._

```python
def hammingWeight(n):
    count = 0
    while n:
        n = n & (n - 1)
        count += 1
    return count
```

### Python Note

Python integers are infinite precision. Negative numbers have infinite 1s padding.
To restrict to 32-bit: `n & 0xFFFFFFFF`.
