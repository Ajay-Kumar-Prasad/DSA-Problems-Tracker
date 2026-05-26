# 🔁 DSA Patterns in Recursion

A comprehensive reference mapping every problem in this recursion problem set to the underlying recursive pattern it demonstrates.

---

## 📚 Table of Contents

1. [Pattern Overview](#pattern-overview)
2. [Problems by Pattern](#problems-by-pattern)
3. [Pattern Deep Dives](#pattern-deep-dives)
4. [Problem → Pattern Quick Reference](#problem--pattern-quick-reference)

---

## Pattern Overview

| # | Pattern | Core Idea |
|---|---------|-----------|
| 1 | **Reduce & Delegate** | Shrink the input by 1, delegate rest to recursion |
| 2 | **Divide & Conquer** | Split input into halves; combine results |
| 3 | **Digit / Math Recursion** | Recurse on individual digits or math operations |
| 4 | **Pick / Not-Pick (Subsequences)** | At each index, choose to include or exclude |
| 5 | **Permutation via Swap** | Fix one element, recurse on remaining |
| 6 | **Combination / Subset Building** | Add candidates one at a time; backtrack |
| 7 | **Backtracking on Grid** | Explore cells in 2D; backtrack on dead ends |
| 8 | **Constraint Satisfaction (CSP)** | Place elements under hard constraints; backtrack if violated |
| 9 | **String / Partition Recursion** | Try all splits of a string; validate each part |
| 10 | **Counting via Recursion** | Count valid paths / combinations using recursive summation |

---

## Problems by Pattern

---

### 1. 🔽 Reduce & Delegate

> **Idea:** Solve `f(n)` by doing a small amount of work and delegating `f(n-1)` to the recursive call. Classic linear recursion.

| Problem | How the Pattern Applies |
|---------|------------------------|
| **Recursive atoi()** | Convert last char to digit; recurse on the shorter string prefix |
| **Reverse a Stack** | Pop top element, reverse remaining stack recursively, insert top at bottom |
| **Sort a Stack using Recursion** | Pop top, sort remaining stack recursively, insert top in sorted position |

**Template:**
```python
def f(input):
    if base_case(input):
        return base_result
    small_work = process(input)
    return combine(small_work, f(shrink(input)))
```

---

### 2. ✂️ Divide & Conquer

> **Idea:** Split the problem into two (or more) roughly equal halves, solve each recursively, and merge.

| Problem | How the Pattern Applies |
|---------|------------------------|
| **Pow(x, n)** | `pow(x, n) = pow(x, n/2) * pow(x, n/2)`; handle odd `n` separately |

**Template:**
```python
def pow(x, n):
    if n == 0: return 1
    half = pow(x, n // 2)
    if n % 2 == 0: return half * half
    else: return x * half * half
```

---

### 3. 🔢 Digit / Math Recursion

> **Idea:** Recurse on the digit structure of a number (units, tens, hundreds…) to count or compute something.

| Problem | How the Pattern Applies |
|---------|------------------------|
| **Count Good Numbers** | Count valid digit choices at each position (even positions → evens, odd positions → primes); use fast exponentiation |
| **Generate Binary Strings Without Consecutive 1s** | Recurse character by character; last char placed constrains next choice |

**Template:**
```python
def count(position, last_char):
    if position == n: return 1
    total = 0
    for choice in valid_choices(position, last_char):
        total += count(position + 1, choice)
    return total
```

---

### 4. 🍃 Pick / Not-Pick (Subsequences)

> **Idea:** At each index `i`, make a binary decision — include `arr[i]` in the current subset/subsequence, or skip it. This generates all 2ⁿ subsets.

| Problem | How the Pattern Applies |
|---------|------------------------|
| **Count All Subsequences with Sum K** | At each index, pick or not-pick; count paths where running sum == K |
| **Check if Subsequence with Sum K Exists** | Same as above; return true on first valid path |
| **Power Set** | Pick/not-pick for every element; collect all resulting subsets |
| **Learn All Patterns of Subsequences (Theory)** | Foundation of this pattern |
| **Subsets I** | Pure pick/not-pick: generate all 2ⁿ subsets |
| **Subsets II** | Pick/not-pick with duplicate handling (sort + skip duplicates at same recursion level) |

**Template:**
```python
def solve(index, current, result):
    if index == len(arr):
        result.append(current[:])
        return
    # Pick
    current.append(arr[index])
    solve(index + 1, current, result)
    current.pop()
    # Not-pick
    solve(index + 1, current, result)
```

---

### 5. 🔀 Permutation via Swap / Used-Array

> **Idea:** At each position, try all unused elements; mark them used (or swap into position), recurse, then unmark (backtrack).

| Problem | How the Pattern Applies |
|---------|------------------------|
| **Letter Combinations of a Phone Number** | Each digit maps to 2–4 letters; try each letter at current position, recurse on next digit |
| **N Queen** | Place a queen in each row; try all columns; backtrack if unsafe |
| **Expression Add Operators** | At each position, try inserting `+`, `-`, `*`; recurse on remainder |

**Template:**
```python
def permute(index, path):
    if index == n:
        result.append(path[:])
        return
    for choice in choices_at(index):
        if not used[choice]:
            used[choice] = True
            path.append(choice)
            permute(index + 1, path)
            path.pop()
            used[choice] = False
```

---

### 6. 🧩 Combination / Subset Building (with constraints)

> **Idea:** Build combinations by iterating forward from a `start` index; decide how many of each candidate to include. Backtrack when a constraint is violated.

| Problem | How the Pattern Applies |
|---------|------------------------|
| **Combination Sum** | Candidates can be reused; recurse with same index until sum exceeded |
| **Combination Sum II** | Each candidate used once; skip duplicates at same depth level |
| **Combination Sum III** | Choose exactly `k` numbers from 1–9 that sum to `n` |
| **Generate Parentheses** | Track open/close counts; add `(` if open < n, add `)` if close < open |

**Template:**
```python
def backtrack(start, current, remaining):
    if remaining == 0:
        result.append(current[:])
        return
    for i in range(start, len(candidates)):
        if candidates[i] > remaining: break
        current.append(candidates[i])
        backtrack(i, current, remaining - candidates[i])  # i or i+1 depending on reuse
        current.pop()
```

---

### 7. 🗺️ Backtracking on Grid

> **Idea:** Explore a 2D grid cell by cell (typically 4 or 8 directions). Mark visited, recurse, unmark on backtrack.

| Problem | How the Pattern Applies |
|---------|------------------------|
| **Word Search** | DFS from each cell; match chars one by one; backtrack if mismatch |
| **Rat in a Maze** | Move in valid directions; mark path; backtrack if stuck |

**Template:**
```python
def dfs(row, col, index):
    if index == len(word): return True
    if out_of_bounds(row, col) or visited[row][col] or board[row][col] != word[index]:
        return False
    visited[row][col] = True
    for dr, dc in directions:
        if dfs(row + dr, col + dc, index + 1): return True
    visited[row][col] = False
    return False
```

---

### 8. 🎯 Constraint Satisfaction (CSP) / Placement Backtracking

> **Idea:** Place elements one at a time subject to hard constraints. If no valid placement exists at the current step, backtrack and try the next option.

| Problem | How the Pattern Applies |
|---------|------------------------|
| **N Queen** | One queen per row; check column + diagonal conflicts before placing |
| **Sudoku Solver** | Fill empty cells 1–9; validate row, col, and 3×3 box; backtrack on conflict |
| **M Coloring Problem** | Assign one of M colors to each node; no two adjacent nodes same color |
| **Rat in a Maze** | Valid cell + not visited = constraint; backtrack otherwise |

**Template:**
```python
def solve(position):
    if position == total: return True   # all placed
    for choice in all_choices:
        if is_valid(position, choice):
            place(position, choice)
            if solve(position + 1): return True
            remove(position, choice)    # backtrack
    return False
```

---

### 9. ✂️ String / Partition Recursion

> **Idea:** At each index, try all possible "cuts" of the remaining string. Recurse on the suffix if the current partition is valid.

| Problem | How the Pattern Applies |
|---------|------------------------|
| **Palindrome Partitioning** | At every position, try all substrings ending here; if palindrome, recurse on rest |
| **Word Break** | At every position, check if prefix is a valid word; recurse on remaining string |

**Template:**
```python
def partition(index, path):
    if index == len(s):
        result.append(path[:])
        return
    for end in range(index + 1, len(s) + 1):
        substring = s[index:end]
        if is_valid(substring):           # palindrome check, dictionary check, etc.
            path.append(substring)
            partition(end, path)
            path.pop()
```

---

### 10. 📊 Counting via Recursion

> **Idea:** Instead of collecting results, just count valid paths or outcomes. Often overlaps with pick/not-pick or combination patterns.

| Problem | How the Pattern Applies |
|---------|------------------------|
| **Count Good Numbers** | Count valid digit assignments at each position recursively |
| **Count All Subsequences with Sum K** | Count leaves of the pick/not-pick tree where sum == K |
| **Generate Binary Strings Without Consecutive 1s** | Count valid strings of length n with no two consecutive 1s |

---

## Pattern Deep Dives

### 🌳 Recursion Tree Mental Model

Every recursion problem builds a **decision tree**:

```
                   f(arr, 0)
                  /          \
         pick arr[0]      not-pick arr[0]
            /    \              /    \
        pick     not         pick   not
       arr[1]   arr[1]      arr[1]  arr[1]
         ...      ...         ...    ...
       (leaf)   (leaf)      (leaf)  (leaf)
```

- **Depth** = number of decisions = `n`
- **Branching factor** = choices per step (2 for pick/not-pick, k for k-coloring, etc.)
- **Total leaves** = branching\_factor ^ depth in the worst case

### ⏪ Backtracking vs Pure Recursion

| | Pure Recursion | Backtracking |
|--|---------------|--------------|
| State | Usually immutable (return values) | Mutable (undo side effects) |
| Use case | Math computation, counting | Collection building, placement |
| Undo step | Not needed | **Required** (`pop`, `unmark`, `remove`) |
| Example | `pow(x,n)`, `atoi` | N-Queen, Sudoku, Subsets |

### 🔑 When to Use Each Pattern

```
Is the answer a single value (count, sum, bool)?
  ├─ Yes → Reduce & Delegate, Digit Recursion, or Counting Recursion
  └─ No → You need to collect results (backtracking)
        Is input a grid?
          ├─ Yes → Grid Backtracking
          └─ No → Is it about placing under hard rules?
                    ├─ Yes → CSP Backtracking
                    └─ No → Is it about splitting a string?
                              ├─ Yes → Partition Recursion
                              └─ No → Pick/Not-Pick or Combination Building
```

---

## Problem → Pattern Quick Reference

| Problem | Difficulty | Primary Pattern | Secondary Pattern |
|---------|-----------|-----------------|-------------------|
| Recursive atoi() | Easy | Reduce & Delegate | Digit Recursion |
| Pow(x, n) | Medium | Divide & Conquer | — |
| Count Good Numbers | Medium | Digit Recursion | Counting Recursion |
| Sort a Stack | Medium | Reduce & Delegate | — |
| Reverse a Stack | Medium | Reduce & Delegate | — |
| Generate Binary Strings | Medium | Digit Recursion | Pick/Not-Pick |
| Generate Parentheses | Medium | Combination Building | Constraint Satisfaction |
| Power Set | Medium | Pick / Not-Pick | — |
| Count Subsequences Sum K | Easy | Pick / Not-Pick | Counting Recursion |
| Check Subsequence Sum K | Easy | Pick / Not-Pick | — |
| Combination Sum | Medium | Combination Building | — |
| Combination Sum II | Medium | Combination Building | — |
| Subsets I | Medium | Pick / Not-Pick | — |
| Subsets II | Medium | Pick / Not-Pick | — |
| Combination Sum III | Medium | Combination Building | Constraint Satisfaction |
| Letter Combinations | Medium | Permutation / Mapping | — |
| Palindrome Partitioning | Hard | Partition Recursion | — |
| Word Search | Hard | Grid Backtracking | — |
| N Queen | Hard | CSP Backtracking | Permutation |
| Rat in a Maze | Hard | Grid Backtracking | CSP Backtracking |
| Word Break | Medium | Partition Recursion | — |
| M Coloring Problem | Hard | CSP Backtracking | — |
| Sudoku Solver | Hard | CSP Backtracking | — |
| Expression Add Operators | Hard | Permutation / Try-All | Combination Building |

---

## 📝 Key Takeaways

1. **Master Pick/Not-Pick first** — it's the foundation of Subsets, Subsequences, and Combination Sum.
2. **Backtracking = Recursion + Undo** — always restore state after a recursive call when using mutable structures.
3. **CSP problems share a template** — `isValid() → place() → recurse() → remove()`.
4. **Grid problems need a visited array** — always mark before recursing, unmark after returning.
5. **Partition problems iterate over end indices** — the inner loop is what tries all possible cuts.
6. **Duplicate handling** in Subsets II / Combination Sum II: **sort first**, then skip `arr[i] == arr[i-1]` at the same recursion depth.

---

*Happy Recursing! 🚀*