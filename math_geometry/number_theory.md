# Number Theory

**Usage:** Math problems, Encryption, Hashing.

## 1. GCD & LCM

_Greatest Common Divisor & Least Common Multiple._

```python
import math

# Built-in
gcd_val = math.gcd(a, b)
lcm_val = math.lcm(a, b) # Python 3.9+

# Manual GCD (Euclidean Algorithm)
def gcd(a, b):
    while b:
        a, b = b, a % b
    return a
```

## 2. Primes (Sieve of Eratosthenes)

_Find all primes up to N. O(N log log N)._

```python
def sieve(n):
    primes = [True] * (n + 1)
    primes[0] = primes[1] = False

    for i in range(2, int(n**0.5) + 1):
        if primes[i]:
            for j in range(i*i, n + 1, i):
                primes[j] = False

    return [i for i, is_prime in enumerate(primes) if is_prime]
```

## 3. Modular Arithmetic

_(a + b) % m = ((a % m) + (b % m)) % m_
_(a _ b) % m = ((a % m) _ (b % m)) % m_

### Modular Exponentiation (Power)

`pow(base, exp, mod)` is O(log exp).

```python
# Manual Implementation
def power(x, y, p):
    res = 1
    x = x % p
    while y > 0:
        if (y % 2) == 1: # If y is odd
            res = (res * x) % p
        y = y >> 1 # y = y // 2
        x = (x * x) % p
    return res
```
