# ⚡ DSA Patterns: Bit Manipulation & Basic Maths

> Bit manipulation looks like black magic when you start. Then it turns into 8–10 reusable patterns. This doc maps every pattern with intuition, code, and interview triggers.

---

## 📚 Table of Contents

1. [Learning Order](#learning-order)
2. [Bit Manipulation Patterns](#bit-manipulation-patterns)
   - [Pattern 1: Check / Set / Unset / Toggle Bit](#pattern-1-check--set--unset--toggle-bit)
   - [Pattern 2: Odd / Even Detection](#pattern-2-odd--even-detection)
   - [Pattern 3: Power of Two](#pattern-3-power-of-two)
   - [Pattern 4: Remove Rightmost Set Bit](#pattern-4-remove-rightmost-set-bit)
   - [Pattern 5: Count Set Bits](#pattern-5-count-set-bits)
   - [Pattern 6: XOR Cancellation — Single Number I](#pattern-6-xor-cancellation--single-number-i)
   - [Pattern 7: XOR Partition — Two Unique Numbers](#pattern-7-xor-partition--two-unique-numbers)
   - [Pattern 8: Minimum Bit Flips](#pattern-8-minimum-bit-flips)
   - [Pattern 9: Power Set via Bitmask](#pattern-9-power-set-via-bitmask)
   - [Pattern 10: XOR from 1 to N (Range XOR)](#pattern-10-xor-from-1-to-n-range-xor)
   - [Pattern 11: Division Without / or *](#pattern-11-division-without--or-)
   - [Pattern 12: Fast Exponentiation (Binary Exponentiation)](#pattern-12-fast-exponentiation-binary-exponentiation)
   - [Pattern 13: Set Rightmost Unset Bit](#pattern-13-set-rightmost-unset-bit)
   - [Pattern 14: Swap Two Numbers (XOR Swap)](#pattern-14-swap-two-numbers-xor-swap)
   - [Pattern 15: Bitmask DP Foundation](#pattern-15-bitmask-dp-foundation)
3. [Basic Maths Patterns](#basic-maths-patterns)
   - [Pattern M1: Prime Checking](#pattern-m1-prime-checking)
   - [Pattern M2: Sieve of Eratosthenes](#pattern-m2-sieve-of-eratosthenes)
   - [Pattern M3: GCD & LCM (Euclidean Algorithm)](#pattern-m3-gcd--lcm-euclidean-algorithm)
   - [Pattern M4: Count Digits](#pattern-m4-count-digits)
   - [Pattern M5: Digit Sum & Digital Root](#pattern-m5-digit-sum--digital-root)
   - [Pattern M6: Reverse a Number](#pattern-m6-reverse-a-number)
   - [Pattern M7: Palindrome Number](#pattern-m7-palindrome-number)
   - [Pattern M8: Armstrong Number](#pattern-m8-armstrong-number)
   - [Pattern M9: Modular Arithmetic](#pattern-m9-modular-arithmetic)
   - [Pattern M10: Count Trailing Zeros in N!](#pattern-m10-count-trailing-zeros-in-n)
4. [Interview Recognition Guide](#interview-recognition-guide)
5. [Quick Reference Cheatsheet](#quick-reference-cheatsheet)

---

## Learning Order

Master bit manipulation in this order — each layer builds on the last:

| Priority | Pattern | Why First? |
|----------|---------|------------|
| 1 | Basic bit operations | Everything else uses these |
| 2 | XOR patterns | Most frequently asked |
| 3 | Set bit counting | Appears in many variants |
| 4 | Power-of-two tricks | Constant-time elegance |
| 5 | Power set generation | Gateway to bitmask DP |
| 6 | Binary exponentiation | Critical for maths problems |
| 7 | Range XOR | Once you know XOR, this is free |
| 8 | Division using bits | Good to know, rarely hard |
| 9 | Bitmask DP basics | Advanced — learn after DP |

---

## Bit Manipulation Patterns

---

### Pattern 1: Check / Set / Unset / Toggle Bit

> **Core Idea:** Use a **mask** — a number with only the i-th bit set — and combine it with AND / OR / XOR.

Think of bits as switches at positions 0, 1, 2, 3...

```
13 = 1 1 0 1
     ↑ ↑ ↑ ↑
     3 2 1 0  ← positions
```

---

#### ✅ Check if i-th bit is set

```cpp
bool isSet(int n, int i) {
    return (n & (1 << i)) != 0;
}
```

**Walkthrough:**
```
n = 13 = 1101,  i = 2
mask   = 0100

  1101
& 0100
------
  0100   ≠ 0  →  bit IS set
```

---

#### ✅ Set i-th bit (force to 1)

```cpp
int setBit(int n, int i) {
    return n | (1 << i);
}
```

OR forces the target bit to 1 regardless of its current value.

---

#### ✅ Unset i-th bit (force to 0)

```cpp
int unsetBit(int n, int i) {
    return n & ~(1 << i);
}
```

`~mask` flips all bits — AND with that clears only the i-th bit.

---

#### ✅ Toggle i-th bit (flip)

```cpp
int toggleBit(int n, int i) {
    return n ^ (1 << i);
}
```

XOR flips: `0 → 1`, `1 → 0`.

**Complexity:** `O(1)` for all four operations.

---

### Pattern 2: Odd / Even Detection

> **Core Idea:** The last (0th) bit encodes parity. If it's 1 → odd; if 0 → even.

```cpp
bool isOdd(int n) {
    return (n & 1) == 1;
}
```

**Walkthrough:**
```
7 = 111
    &
    001
    ---
    001  →  odd ✓

8 = 1000
    &
    0001
    ----
    0000  →  even ✓
```

**Complexity:** `O(1)` — faster than `n % 2` on some architectures.

---

### Pattern 3: Power of Two

> **Core Idea:** Powers of two have **exactly one set bit**. `n & (n-1)` strips the rightmost set bit — for a power of two, that makes it zero.

```
1 = 0001
2 = 0010
4 = 0100
8 = 1000
```

```cpp
bool isPowerOfTwo(int n) {
    return n > 0 && (n & (n - 1)) == 0;
}
```

**Walkthrough:**
```
8 = 1000
7 = 0111

  1000
& 0111
------
  0000  →  power of two ✓

6 = 0110
5 = 0101

  0110
& 0101
------
  0100  ≠ 0  →  not a power of two ✓
```

**Complexity:** `O(1)`.

---

### Pattern 4: Remove Rightmost Set Bit

> **Core Idea:** `n & (n-1)` removes the lowest set bit. The key building block for bit counting and many other tricks.

```cpp
int removeRightmostSetBit(int n) {
    return n & (n - 1);
}
```

**Walkthrough:**
```
12 = 1100
11 = 1011

  1100
& 1011
------
  1000   ←  rightmost set bit removed
```

**Why it works:** Subtracting 1 flips the rightmost set bit and all trailing zeros. ANDing clears all of those.

**Complexity:** `O(1)`.

---

### Pattern 5: Count Set Bits (Popcount)

> **Core Idea:** Two approaches — loop over all bits, or loop only over set bits (faster in practice).

---

#### Method 1: Shift and check every bit

```cpp
int countBits(int n) {
    int count = 0;
    while (n) {
        count += (n & 1);
        n >>= 1;
    }
    return count;
}
```

**Complexity:** `O(log n)` — iterates over every bit position.

---

#### Method 2: Brian Kernighan's Algorithm ⭐

```cpp
int countBits(int n) {
    int count = 0;
    while (n) {
        count++;
        n = n & (n - 1);  // strip one set bit per iteration
    }
    return count;
}
```

**Complexity:** `O(k)` where `k` = number of set bits. Much faster when bits are sparse.

**Interview note:** If you say "Brian Kernighan" out loud during an interview, a senior engineer somewhere smiles. Use it.

---

### Pattern 6: XOR Cancellation — Single Number I

> **Core Idea:** `a ^ a = 0` and `a ^ 0 = a`. XOR-ing a number with itself cancels it out. XOR-ing all elements leaves only the unique one.

**Problem:** Every element appears twice except one. Find it.

```cpp
int singleNumber(vector<int>& nums) {
    int result = 0;
    for (int x : nums) result ^= x;
    return result;
}
```

**Walkthrough:**
```
[2, 1, 2, 3, 1]

2 ^ 1 ^ 2 ^ 3 ^ 1
= (2^2) ^ (1^1) ^ 3
=   0   ^   0   ^ 3
= 3  ✓
```

**Complexity:** `O(n)` time, `O(1)` space.

---

### Pattern 7: XOR Partition — Two Unique Numbers

> **Core Idea:** XOR all → get `a ^ b`. Find a bit where `a` and `b` differ (rightmost set bit of `a^b`). Use it to partition the array into two groups, each containing exactly one unique number.

**Problem:** Two elements appear once, rest appear twice. Find both.

```cpp
vector<int> singleNumberIII(vector<int>& nums) {
    int xorAll = 0;
    for (int x : nums) xorAll ^= x;

    // Rightmost bit where a and b differ
    int mask = xorAll & (-xorAll);

    int a = 0, b = 0;
    for (int x : nums) {
        if (x & mask) a ^= x;  // group 1
        else          b ^= x;  // group 2
    }
    return {a, b};
}
```

**Why `xorAll & (-xorAll)`?** Negative in two's complement flips all bits and adds 1, which isolates the rightmost set bit.

**Complexity:** `O(n)` time, `O(1)` space.

---

### Pattern 8: Minimum Bit Flips

> **Core Idea:** XOR produces a 1 at every position where two numbers differ. Count those 1s to get the number of flips needed.

**Problem:** Minimum bit flips to convert A to B.

```cpp
int minBitFlips(int a, int b) {
    return __builtin_popcount(a ^ b);  // or count manually
}
```

**Walkthrough:**
```
A = 10 = 1010
B =  7 = 0111

A ^ B  = 1101   →  3 set bits  →  3 flips needed
```

**Complexity:** `O(log n)` or `O(set bits)` with Kernighan.

---

### Pattern 9: Power Set via Bitmask

> **Core Idea:** An array of `n` elements has `2^n` subsets. Each subset maps to a unique bitmask from `0` to `2^n - 1`. If bit `i` is set in the mask → include `arr[i]`.

```cpp
vector<vector<int>> powerSet(vector<int>& arr) {
    int n = arr.size();
    vector<vector<int>> result;

    for (int mask = 0; mask < (1 << n); mask++) {
        vector<int> subset;
        for (int i = 0; i < n; i++) {
            if (mask & (1 << i))
                subset.push_back(arr[i]);
        }
        result.push_back(subset);
    }
    return result;
}
```

**Mask → Subset mapping:**
```
arr = [1, 2, 3]

mask 000 → {}
mask 001 → {1}
mask 010 → {2}
mask 011 → {1,2}
mask 100 → {3}
mask 101 → {1,3}
mask 110 → {2,3}
mask 111 → {1,2,3}
```

**Complexity:** `O(n · 2^n)` — `2^n` masks, each requiring `n` bit checks.

---

### Pattern 10: XOR from 1 to N (Range XOR)

> **Core Idea:** XOR from 1 to N follows a cycle of 4. Once you know this, any range XOR becomes `O(1)`.

**XOR(1 to n):**
```
n % 4 == 0  →  n
n % 4 == 1  →  1
n % 4 == 2  →  n + 1
n % 4 == 3  →  0
```

```cpp
int xorUpTo(int n) {
    switch (n % 4) {
        case 0: return n;
        case 1: return 1;
        case 2: return n + 1;
        case 3: return 0;
    }
    return 0;
}

// XOR from L to R
int xorRange(int l, int r) {
    return xorUpTo(r) ^ xorUpTo(l - 1);
}
```

**Complexity:** `O(1)`.

---

### Pattern 11: Division Without / or *

> **Core Idea:** Division is repeated subtraction, which is too slow. Instead, subtract the largest possible power-of-two multiples of the divisor using left shifts.

```cpp
int divide(int dividend, int divisor) {
    long long a = abs((long long)dividend);
    long long b = abs((long long)divisor);
    long long result = 0;

    while (a >= b) {
        long long temp = b, multiple = 1;
        while (a >= (temp << 1)) {
            temp <<= 1;
            multiple <<= 1;
        }
        a -= temp;
        result += multiple;
    }

    bool negative = (dividend < 0) ^ (divisor < 0);
    return negative ? -result : result;
}
```

**Example:** `43 / 5`
```
5 << 3 = 40  →  subtract 40, add 2^3 = 8 to quotient
43 - 40 = 3  (less than 5, stop)
Result = 8
```

**Complexity:** `O(log² n)`.

---

### Pattern 12: Fast Exponentiation (Binary Exponentiation)

> **Core Idea:** Express the exponent in binary. `x^13 = x^(8+4+1) = x^8 · x^4 · x^1`. Each squaring step doubles the exponent covered, reducing O(n) to O(log n).

```cpp
double fastPow(double x, long long n) {
    if (n < 0) { x = 1.0 / x; n = -n; }
    double ans = 1.0;

    while (n) {
        if (n & 1) ans *= x;  // current bit is set → multiply in x
        x *= x;               // square x for the next bit
        n >>= 1;
    }
    return ans;
}
```

**Walkthrough for x^13:**
```
13 = 1101

bit 0 set  →  ans *= x^1
bit 1 unset
bit 2 set  →  ans *= x^4
bit 3 set  →  ans *= x^8

ans = x^1 * x^4 * x^8 = x^13  ✓
```

**Complexity:** `O(log n)` instead of `O(n)`.

---

### Pattern 13: Set Rightmost Unset Bit

> **Core Idea:** `n | (n+1)` sets the rightmost 0-bit to 1.

```cpp
int setRightmostUnsetBit(int n) {
    return n | (n + 1);
}
```

**Walkthrough:**
```
10 = 1010
11 = 1011

  1010
| 1011
------
  1011   ←  rightmost 0 is now 1
```

**Complexity:** `O(1)`.

---

### Pattern 14: Swap Two Numbers (XOR Swap)

> **Core Idea:** `a ^ b ^ b = a` and `a ^ a ^ b = b`. XOR three times to swap without a temp variable.

```cpp
void xorSwap(int &a, int &b) {
    a = a ^ b;
    b = a ^ b;  // b = (a^b)^b = a
    a = a ^ b;  // a = (a^b)^a = b
}
```

**Note:** In production code, prefer `std::swap(a, b)`. The XOR trick is an interview classic, not a best practice — a compiler generates identical or better code from `swap()`.

**Complexity:** `O(1)`, no extra space.

---

### Pattern 15: Bitmask DP Foundation

> **Core Idea:** Use an integer as a compact set representation. When a problem involves subsets, visited states, or selected items — a bitmask is the state.

**Common operations:**
```cpp
mask & (1 << i)     // is element i in the set?
mask | (1 << i)     // add element i
mask ^ (1 << i)     // toggle element i
mask & ~(1 << i)    // remove element i
__builtin_popcount(mask)  // count elements in set
```

**Example — Traveling Salesman (state = visited cities):**
```cpp
// dp[mask][i] = min cost to visit cities in 'mask', ending at city i
int dp[1 << n][n];

for (int mask = 1; mask < (1 << n); mask++) {
    for (int u = 0; u < n; u++) {
        if (!(mask & (1 << u))) continue;
        for (int v = 0; v < n; v++) {
            if (mask & (1 << v)) continue;
            int newMask = mask | (1 << v);
            dp[newMask][v] = min(dp[newMask][v], dp[mask][u] + dist[u][v]);
        }
    }
}
```

**When to think bitmask:** subsets, visited cities, selected items, state compression, assignment problems.

---

## Basic Maths Patterns

---

### Pattern M1: Prime Checking

> **Core Idea:** A number `n` is prime if it has no divisors from `2` to `√n`. Checking beyond `√n` is redundant because divisors come in pairs.

```cpp
bool isPrime(int n) {
    if (n < 2) return false;
    if (n == 2) return true;
    if (n % 2 == 0) return false;

    for (int i = 3; i * i <= n; i += 2) {
        if (n % i == 0) return false;
    }
    return true;
}
```

**Complexity:** `O(√n)`.

**Why `√n`?** If `n = a * b` and `a ≤ b`, then `a ≤ √n`. So any factor pair has at least one element ≤ √n.

---

### Pattern M2: Sieve of Eratosthenes

> **Core Idea:** Mark all multiples of every prime as composite. Efficiently finds all primes up to `N` in one pass.

```cpp
vector<bool> sieve(int n) {
    vector<bool> isPrime(n + 1, true);
    isPrime[0] = isPrime[1] = false;

    for (int i = 2; i * i <= n; i++) {
        if (isPrime[i]) {
            for (int j = i * i; j <= n; j += i)
                isPrime[j] = false;
        }
    }
    return isPrime;
}
```

**Complexity:** `O(n log log n)` time, `O(n)` space.

**Use when:** You need primes in a range, not just a single check.

---

### Pattern M3: GCD & LCM (Euclidean Algorithm)

> **Core Idea:** `gcd(a, b) = gcd(b, a % b)`. Repeated modulo reduces both numbers quickly.

```cpp
int gcd(int a, int b) {
    return b == 0 ? a : gcd(b, a % b);
}

int lcm(int a, int b) {
    return (a / gcd(a, b)) * b;  // divide first to avoid overflow
}
```

**Walkthrough:**
```
gcd(48, 18)
= gcd(18, 48%18) = gcd(18, 12)
= gcd(12, 18%12) = gcd(12, 6)
= gcd(6,  12%6)  = gcd(6, 0)
= 6
```

**Complexity:** `O(log(min(a, b)))`.

---

### Pattern M4: Count Digits

> **Core Idea:** Extract digits one by one by dividing by 10. Count until `n` becomes 0.

```cpp
int countDigits(int n) {
    if (n == 0) return 1;
    int count = 0;
    n = abs(n);
    while (n > 0) {
        count++;
        n /= 10;
    }
    return count;
}

// O(1) version
int countDigitsLog(int n) {
    return (int)floor(log10(abs(n))) + 1;
}
```

**Complexity:** `O(d)` where `d` = number of digits = `O(log n)`.

---

### Pattern M5: Digit Sum & Digital Root

> **Core Idea:** Sum digits by repeatedly taking `n % 10`. Digital root reduces to a single digit using the mod-9 property.

```cpp
int digitSum(int n) {
    int sum = 0;
    while (n > 0) {
        sum += n % 10;
        n /= 10;
    }
    return sum;
}

// Digital root O(1)
int digitalRoot(int n) {
    if (n == 0) return 0;
    return 1 + (n - 1) % 9;
}
```

**Complexity:** `O(log n)` for digit sum, `O(1)` for digital root.

---

### Pattern M6: Reverse a Number

> **Core Idea:** Extract the last digit with `% 10`, build the reversed number by multiplying the result by 10 and adding the digit.

```cpp
int reverse(int n) {
    bool negative = n < 0;
    long long rev = 0;
    n = abs(n);

    while (n > 0) {
        rev = rev * 10 + (n % 10);
        n /= 10;
    }
    return negative ? -rev : rev;
}
```

**Complexity:** `O(d)` = `O(log n)`.

---

### Pattern M7: Palindrome Number

> **Core Idea:** Reverse the number and check equality. Or: compare first and last digits symmetrically.

```cpp
bool isPalindrome(int n) {
    if (n < 0) return false;
    return n == reverse(n);   // reuse Pattern M6
}
```

**Complexity:** `O(log n)`.

---

### Pattern M8: Armstrong Number

> **Core Idea:** An Armstrong number equals the sum of its digits each raised to the power of the number of digits. `153 = 1³ + 5³ + 3³`.

```cpp
bool isArmstrong(int n) {
    int digits = countDigits(n);   // reuse Pattern M4
    int temp = n, sum = 0;

    while (temp > 0) {
        int d = temp % 10;
        sum += pow(d, digits);
        temp /= 10;
    }
    return sum == n;
}
```

**Complexity:** `O(log n)`.

---

### Pattern M9: Modular Arithmetic

> **Core Idea:** Use `(a + b) % mod`, `(a * b) % mod`, and Fermat's little theorem for modular inverse. Essential for large number problems.

```cpp
const int MOD = 1e9 + 7;

long long modAdd(long long a, long long b) {
    return (a % MOD + b % MOD) % MOD;
}

long long modMul(long long a, long long b) {
    return (a % MOD) * (b % MOD) % MOD;
}

// Modular inverse using fast exponentiation (MOD must be prime)
long long modInverse(long long a) {
    return fastPow(a, MOD - 2, MOD);   // Fermat's little theorem
}
```

**Key rules:**
```
(a + b) % m = ((a % m) + (b % m)) % m
(a * b) % m = ((a % m) * (b % m)) % m
(a - b) % m = ((a % m) - (b % m) + m) % m   ← +m avoids negatives
```

**Complexity:** `O(1)` per operation, `O(log m)` for inverse.

---

### Pattern M10: Count Trailing Zeros in N!

> **Core Idea:** Trailing zeros come from factors of 10 = 2 × 5. Factors of 5 are rarer, so count multiples of 5, 25, 125... in `[1, N]`.

```cpp
int trailingZeros(int n) {
    int count = 0;
    while (n >= 5) {
        n /= 5;
        count += n;
    }
    return count;
}
```

**Example for N = 100:**
```
100/5  = 20
20/5   = 4
4/5    = 0

Total = 24 trailing zeros
```

**Complexity:** `O(log₅ n)`.

---

## Interview Recognition Guide

| Problem Clue | Pattern |
|---|---|
| Check / set / clear a specific bit | Bit masking (Pattern 1) |
| Is number odd or even? | `n & 1` (Pattern 2) |
| Is number a power of 2? | `n & (n-1) == 0` (Pattern 3) |
| Strip the lowest set bit | `n & (n-1)` (Pattern 4) |
| Count 1-bits in a number | Brian Kernighan (Pattern 5) |
| One unique number, rest appear twice | XOR cancellation (Pattern 6) |
| Two unique numbers, rest appear twice | XOR partition (Pattern 7) |
| Bit positions where two numbers differ | `A ^ B` + count bits (Pattern 8) |
| Generate all subsets | Bitmask enumeration (Pattern 9) |
| XOR of a range | Mod-4 formula (Pattern 10) |
| Divide without `/` or `*` | Bit shifting (Pattern 11) |
| Fast power `x^n` | Binary exponentiation (Pattern 12) |
| Set rightmost zero bit | `n \| (n+1)` (Pattern 13) |
| Swap without temp variable | XOR swap (Pattern 14) |
| Visited/selected states as a set | Bitmask DP (Pattern 15) |
| Is N prime? | Trial division to √n (Pattern M1) |
| All primes up to N | Sieve of Eratosthenes (Pattern M2) |
| Common factor / multiple | Euclidean GCD (Pattern M3) |
| Number of digits in N | Divide by 10 / log10 (Pattern M4) |
| Sum of digits | Repeated `% 10` (Pattern M5) |
| Reverse a number | Digit extraction (Pattern M6) |
| Is number a palindrome? | Reverse + compare (Pattern M7) |
| Armstrong / Narcissistic number | Digit power sum (Pattern M8) |
| Large number computations | Modular arithmetic (Pattern M9) |
| Trailing zeros in factorial | Count factors of 5 (Pattern M10) |

---

## Quick Reference Cheatsheet

```
┌─────────────────────────────────────────────────────────────┐
│                   BIT OPERATION CHEATSHEET                  │
├───────────────────────────┬─────────────────────────────────┤
│ Check i-th bit            │ n & (1 << i)                    │
│ Set i-th bit              │ n | (1 << i)                    │
│ Unset i-th bit            │ n & ~(1 << i)                   │
│ Toggle i-th bit           │ n ^ (1 << i)                    │
│ Is odd?                   │ n & 1                           │
│ Power of two?             │ n > 0 && (n & (n-1)) == 0       │
│ Remove rightmost set bit  │ n & (n-1)                       │
│ Isolate rightmost set bit │ n & (-n)                        │
│ Set rightmost unset bit   │ n | (n+1)                       │
│ XOR all → find unique     │ result ^= each element          │
│ Fast power                │ square + check LSB of exponent  │
├───────────────────────────┼─────────────────────────────────┤
│                   MATHS CHEATSHEET                          │
├───────────────────────────┬─────────────────────────────────┤
│ Is prime?                 │ trial division up to √n         │
│ GCD                       │ gcd(a,b) = gcd(b, a%b)          │
│ LCM                       │ (a / gcd(a,b)) * b              │
│ Digit count               │ floor(log10(n)) + 1             │
│ Digital root              │ 1 + (n-1) % 9                   │
│ Trailing zeros in n!      │ n/5 + n/25 + n/125 + ...        │
│ Modular inverse           │ pow(a, MOD-2, MOD)              │
└───────────────────────────┴─────────────────────────────────┘
```

---

> **Final thought:** 90% of bit manipulation questions in coding rounds are these patterns in disguise. The remaining 10% are the same patterns wearing fake moustaches. You'll recognize them.

---

*Happy Hacking! 🚀*