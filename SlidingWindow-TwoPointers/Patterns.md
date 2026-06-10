# 🪟 DSA Patterns: Sliding Window & Two Pointers

> Sliding Window is not 12 different problems. It's **5 core patterns** wearing different disguises. Interviewers keep changing the story — fruits, baskets, cards, characters, binary arrays. Same machinery underneath. This doc maps every problem to the pattern, with complexity analysis, recognition hints, and edge cases.

---

## 📖 Table of Contents

1. [Big Picture](#big-picture)
2. [Pattern 1: Longest Window Satisfying a Condition](#pattern-1-longest-window-satisfying-a-condition)
3. [Pattern 2: Count Subarrays Using the "At Most K" Trick](#pattern-2-count-subarrays-using-the-at-most-k-trick)
4. [Pattern 3: Count Contribution — Substrings Ending at j](#pattern-3-count-contribution--substrings-ending-at-j)
5. [Pattern 4: Fixed Size Window](#pattern-4-fixed-size-window)
6. [Pattern 5: Minimum Window Problems](#pattern-5-minimum-window-problems)
7. [Two Pointer Patterns](#two-pointer-patterns)
8. [Master Classification Table](#master-classification-table)
9. [Interview Recognition Cheat Sheet](#interview-recognition-cheat-sheet)
10. [Edge Cases Reference](#edge-cases-reference)

---

## Big Picture

```
SLIDING WINDOW PATTERNS
──────────────────────────────────────────────────────────────
Pattern 1: Longest Valid Window
  → Expand right, shrink from left when invalid
  → Answer = max(window size)

Pattern 2: Count Using AtMost(K) Trick
  → Exactly(K) = AtMost(K) - AtMost(K-1)
  → ans += (j - i + 1) per step

Pattern 3: Count Contribution
  → Shrink while valid; ans += i (not j-i+1)

Pattern 4: Fixed Size Window
  → Window size = k, slide without resizing

Pattern 5: Minimum Window
  → Expand until valid; THEN shrink while still valid
  → Flip of Pattern 1
──────────────────────────────────────────────────────────────
```

### Window Size vs Strategy

| Pattern | Window Size | Shrink When | Answer Update |
|---------|-------------|-------------|---------------|
| Longest Valid | Variable | Invalid | `max(j-i+1)` |
| AtMost Count | Variable | Invalid | `+= (j-i+1)` |
| Contribution | Variable | Valid | `+= i` |
| Fixed Size | Fixed = k | Size > k | per window |
| Minimum | Variable | Valid | `min(j-i+1)` |

---

## Pattern 1: Longest Window Satisfying a Condition

> **Core Idea:** Expand the right pointer freely. When the window becomes **invalid**, shrink from the left until it's valid again. The answer is the maximum window size seen at any point.

### Generic Template

```cpp
int i = 0, ans = 0;
unordered_map<char, int> freq;

for (int j = 0; j < n; j++) {
    // Step 1: Expand — add nums[j] to window state
    freq[s[j]]++;

    // Step 2: Shrink — restore validity
    while (/* window is invalid */) {
        freq[s[i]]--;
        if (freq[s[i]] == 0) freq.erase(s[i]);
        i++;
    }

    // Step 3: Update answer — window is now valid
    ans = max(ans, j - i + 1);
}
```

**Key mental model:**
```
window = [i ........... j]
          ↑               ↑
        left (shrinks)   right (always expands)
```

---

### Problem 1: Longest Substring Without Repeating Characters

**Condition:** All characters in the window must be unique.

```cpp
string longestUniqueSubstr(string s) {
    unordered_map<char, int> freq;
    int i = 0, maxLen = 0;

    for (int j = 0; j < s.size(); j++) {
        freq[s[j]]++;

        while (freq[s[j]] > 1) {   // duplicate found
            freq[s[i]]--;
            i++;
        }

        maxLen = max(maxLen, j - i + 1);
    }
    return maxLen;
}
```

**Complexity:** O(n) time, O(1) space (at most 26/128 keys in map).

**Edge Cases:**
- All characters identical: `"aaaa"` → answer = 1
- All characters unique: `"abcd"` → answer = n
- Empty string → answer = 0
- Single character → answer = 1

---

### Problem 2: Max Consecutive Ones III (At Most K Zeros)

**Condition:** Window may contain at most `k` zeros.

```cpp
int longestOnes(vector<int>& nums, int k) {
    int i = 0, zeros = 0, maxLen = 0;

    for (int j = 0; j < nums.size(); j++) {
        if (nums[j] == 0) zeros++;

        while (zeros > k) {
            if (nums[i] == 0) zeros--;
            i++;
        }

        maxLen = max(maxLen, j - i + 1);
    }
    return maxLen;
}
```

**Complexity:** O(n) time, O(1) space.

**Edge Cases:**
- `k = 0`: window can have no zeros — shrink on every 0 encountered
- `k >= total zeros`: entire array is valid — answer = n
- All zeros: answer = k
- All ones: answer = n regardless of k

---

### Problem 3: Fruit Into Baskets

**Condition:** Window contains at most 2 distinct fruit types.

```cpp
int totalFruit(vector<int>& fruits) {
    unordered_map<int, int> basket;
    int i = 0, maxLen = 0;

    for (int j = 0; j < fruits.size(); j++) {
        basket[fruits[j]]++;

        while (basket.size() > 2) {
            basket[fruits[i]]--;
            if (basket[fruits[i]] == 0) basket.erase(fruits[i]);
            i++;
        }

        maxLen = max(maxLen, j - i + 1);
    }
    return maxLen;
}
```

**Complexity:** O(n) time, O(1) space (map holds at most 3 keys at any time).

**Edge Cases:**
- Only 1 distinct fruit: entire array is valid
- `n = 1`: answer = 1
- Alternating distinct fruits past 2 types: window shrinks aggressively

**Recognition trick:** "At most 2 distinct" is a disguise for "at most K distinct" with K=2. The hashmap size check is always the validity condition.

---

### Problem 4: Longest Repeating Character Replacement

**Condition:** `windowSize - mostFreqCount <= k` (replacements needed ≤ k).

```cpp
int characterReplacement(string s, int k) {
    int freq[26] = {}, mostFreq = 0;
    int i = 0, maxLen = 0;

    for (int j = 0; j < s.size(); j++) {
        freq[s[j] - 'A']++;
        mostFreq = max(mostFreq, freq[s[j] - 'A']);

        // Replacements needed = windowSize - mostFreq
        while ((j - i + 1) - mostFreq > k) {
            freq[s[i] - 'A']--;
            i++;
            // Note: mostFreq is NOT updated here — see edge cases
        }

        maxLen = max(maxLen, j - i + 1);
    }
    return maxLen;
}
```

**Visual intuition:**
```
Window: A A B A C A   (size=6)
Most frequent char = A, count = 4
Replacements needed = 6 - 4 = 2

If k >= 2, this window is valid.
```

**Complexity:** O(n) time, O(1) space (fixed 26-char array).

**Edge Cases:**
- `k = 0`: only windows of identical characters are valid
- All same character: answer = n for any k
- `k >= n`: answer = n (replace everything)
- **The `mostFreq` is intentionally not decremented when shrinking.** This keeps the window size non-decreasing, which is valid because a smaller window with lower `mostFreq` can never beat our current best answer.

---

### Problem 5: Longest Substring With At Most K Distinct Characters

**Condition:** Distinct character count in window ≤ k.

```cpp
int lengthOfLongestSubstringKDistinct(string s, int k) {
    unordered_map<char, int> freq;
    int i = 0, maxLen = 0;

    for (int j = 0; j < s.size(); j++) {
        freq[s[j]]++;

        while ((int)freq.size() > k) {
            freq[s[i]]--;
            if (freq[s[i]] == 0) freq.erase(s[i]);
            i++;
        }

        maxLen = max(maxLen, j - i + 1);
    }
    return maxLen;
}
```

**Complexity:** O(n) time, O(k) space.

**Edge Cases:**
- `k = 0`: answer = 0 (no valid window possible)
- `k >= 26`: answer = n (all windows valid)
- Empty string → answer = 0

---

## Pattern 2: Count Subarrays Using the "At Most K" Trick

> **Core Idea:** Counting subarrays with **exactly K** of something is hard directly. Instead, count subarrays with **at most K** — then subtract **at most K-1**. The subtraction cancels everything except "exactly K."

### The Formula

```
countExactlyK = countAtMost(K) - countAtMost(K - 1)
```

### Why `ans += (j - i + 1)` counts valid subarrays

When the window `[i, j]` is valid, every subarray **ending at j** with a start in `[i, j]` is also valid:

```
Valid subarrays ending at j:
  [j]
  [j-1, j]
  [j-2, j]
  ...
  [i, j]

Count = j - i + 1
```

### Generic AtMost Template

```cpp
long long countAtMost(vector<int>& nums, int k) {
    int i = 0;
    long long ans = 0;
    // window state variables...

    for (int j = 0; j < nums.size(); j++) {
        // expand: add nums[j] to window

        while (/* window invalid */) {
            // shrink: remove nums[i] from window
            i++;
        }

        ans += (j - i + 1);   // count all valid subarrays ending at j
    }
    return ans;
}
```

---

### Problem 6: Binary Subarrays With Sum

**Goal:** Count subarrays with sum == goal (binary array).

```cpp
long long countAtMost(vector<int>& nums, int goal) {
    if (goal < 0) return 0;
    int i = 0, sum = 0;
    long long ans = 0;

    for (int j = 0; j < nums.size(); j++) {
        sum += nums[j];
        while (sum > goal) { sum -= nums[i++]; }
        ans += (j - i + 1);
    }
    return ans;
}

int numSubarraysWithSum(vector<int>& nums, int goal) {
    return countAtMost(nums, goal) - countAtMost(nums, goal - 1);
}
```

**Complexity:** O(n) time, O(1) space.

**Edge Cases:**
- `goal = 0`: only subarrays of all zeros count; `countAtMost(-1)` must return 0 (guard check)
- All zeros with `goal = 0`: answer = n*(n+1)/2

---

### Problem 7: Count Number of Nice Subarrays

**Goal:** Count subarrays with exactly k odd numbers.

```cpp
int countAtMostOdds(vector<int>& nums, int k) {
    int i = 0, odds = 0;
    long long ans = 0;

    for (int j = 0; j < nums.size(); j++) {
        if (nums[j] % 2 != 0) odds++;
        while (odds > k) { if (nums[i++] % 2 != 0) odds--; }
        ans += (j - i + 1);
    }
    return ans;
}

int numberOfSubarrays(vector<int>& nums, int k) {
    return countAtMostOdds(nums, k) - countAtMostOdds(nums, k - 1);
}
```

**Complexity:** O(n) time, O(1) space.

**Edge Cases:**
- `k = 0`: only all-even subarrays count
- All elements odd: answer = n - k + 1 (if k ≤ n, else 0)

---

### Problem 8: Subarrays With K Different Integers

**Goal:** Count subarrays with exactly K distinct integers. The canonical "At Most" problem.

```cpp
int countAtMostK(vector<int>& nums, int k) {
    unordered_map<int, int> freq;
    int i = 0;
    long long ans = 0;

    for (int j = 0; j < nums.size(); j++) {
        freq[nums[j]]++;
        while ((int)freq.size() > k) {
            freq[nums[i]]--;
            if (freq[nums[i]] == 0) freq.erase(nums[i]);
            i++;
        }
        ans += (j - i + 1);
    }
    return ans;
}

int subarraysWithKDistinct(vector<int>& nums, int k) {
    return countAtMostK(nums, k) - countAtMostK(nums, k - 1);
}
```

**Complexity:** O(n) time, O(k) space.

**Edge Cases:**
- `k > number of distinct elements in array`: answer = 0
- `k = 1`: only windows with a single repeated element count
- Single element array: answer = 1 if k == 1, else 0

---

## Pattern 3: Count Contribution — Substrings Ending at j

> **Core Idea:** Unlike Pattern 2 where the window is valid **after** shrinking, here you shrink **while** the window is valid — and the contribution per step is `i` (not `j-i+1`). Each value of `i` represents a new set of valid starting points.

### Visual Intuition

```
s = "a b c b a"
        i     j

When window [i..j] contains all of {a,b,c}:

  Substrings ending at j with valid starts:
    [0, j]
    [1, j]
    ...
    [i-1, j]       ← these all still contain a,b,c

  Count = i  (not j-i+1)
```

---

### Problem 9: Number of Substrings Containing All Three Characters

**Goal:** Count substrings containing at least one 'a', 'b', and 'c'.

```cpp
int numberOfSubstrings(string s) {
    int freq[3] = {}, i = 0;
    long long ans = 0;

    for (int j = 0; j < s.size(); j++) {
        freq[s[j] - 'a']++;

        // Shrink while all three characters are present
        while (freq[0] > 0 && freq[1] > 0 && freq[2] > 0) {
            freq[s[i] - 'a']--;
            i++;
        }

        // Every start index [0..i-1] produces a valid substring ending at j
        ans += i;
    }
    return ans;
}
```

**Complexity:** O(n) time, O(1) space.

**Edge Cases:**
- String contains only 2 distinct chars from {a,b,c}: answer = 0
- Minimum valid answer requires at least 3 characters
- `"abc"` → answer = 1; `"abcabc"` → answer = 10

---

## Pattern 4: Fixed Size Window

> **Core Idea:** The window size is exactly `k` and never changes. Add the new element on the right, remove the element that falls off the left. No while-loop for shrinking — just a size check.

### Generic Template

```cpp
int i = 0;
// window state...

for (int j = 0; j < n; j++) {
    add(nums[j]);                    // expand right

    if (j - i + 1 > k) {
        remove(nums[i]);             // drop left element
        i++;
    }

    if (j - i + 1 == k) {           // window fully formed
        // update answer
    }
}
```

---

### Problem 10: Maximum Points You Can Obtain from Cards

**Goal:** Take exactly `k` cards from the two ends of an array to maximize sum.

**Key observation:**
```
Take k cards from ends
= Remove n-k CONSECUTIVE cards from the middle
= Find the minimum sum subarray of size (n-k)
Answer = totalSum - minWindowSum
```

```cpp
int maxScore(vector<int>& cardPoints, int k) {
    int n = cardPoints.size();
    int windowSize = n - k;

    if (windowSize == 0) {
        return accumulate(cardPoints.begin(), cardPoints.end(), 0);
    }

    int totalSum = accumulate(cardPoints.begin(), cardPoints.end(), 0);
    int windowSum = accumulate(cardPoints.begin(), cardPoints.begin() + windowSize, 0);
    int minWindow = windowSum;

    for (int j = windowSize; j < n; j++) {
        windowSum += cardPoints[j] - cardPoints[j - windowSize];
        minWindow  = min(minWindow, windowSum);
    }

    return totalSum - minWindow;
}
```

**Complexity:** O(n) time, O(1) space.

**Edge Cases:**
- `k = n`: take all cards, answer = totalSum
- `k = 0`: answer = 0
- All cards have equal value: answer = k * cardValue

---

## Pattern 5: Minimum Window Problems

> **Core Idea:** The **reversal** of Pattern 1. Expand right until the window becomes **valid**, then shrink from the left **while it stays valid** — updating the answer at each valid shrink. The answer is the minimum valid window size seen.

### ⚠️ Key Difference from Pattern 1

| | Pattern 1 (Longest) | Pattern 5 (Minimum) |
|---|---|---|
| Shrink when | Window **invalid** | Window **valid** |
| Answer update | On every step | Only when valid |
| `while` condition | `while(invalid)` | `while(valid)` |

### Generic Template

```cpp
int i = 0;
int ans = INT_MAX;
// window state, required, formed...

for (int j = 0; j < n; j++) {
    // expand: add s[j] to window state

    while (/* window is valid */) {
        ans = min(ans, j - i + 1);   // update answer BEFORE shrinking
        // shrink: remove s[i] from window state
        i++;
    }
}
```

---

### Problem 11: Minimum Window Substring

**Goal:** Find the smallest substring of `s` containing all characters of `t`.

```cpp
string minWindow(string s, string t) {
    unordered_map<char, int> need, window;
    for (char c : t) need[c]++;

    int required = need.size();   // distinct chars needed
    int formed   = 0;             // distinct chars satisfied in window
    int i = 0, minLen = INT_MAX, start = 0;

    for (int j = 0; j < s.size(); j++) {
        // Expand
        window[s[j]]++;
        if (need.count(s[j]) && window[s[j]] == need[s[j]])
            formed++;

        // Shrink while valid
        while (formed == required) {
            if (j - i + 1 < minLen) {
                minLen = j - i + 1;
                start  = i;
            }
            window[s[i]]--;
            if (need.count(s[i]) && window[s[i]] < need[s[i]])
                formed--;
            i++;
        }
    }

    return minLen == INT_MAX ? "" : s.substr(start, minLen);
}
```

**`formed` vs `required` tracking:**
```
required = number of distinct chars in t
formed   = how many of those chars meet their count in the current window

Window is valid when formed == required
```

**Complexity:** O(|s| + |t|) time, O(|s| + |t|) space.

**Edge Cases:**
- `t` not present in `s`: return `""`
- `s == t`: return `s`
- Characters in `t` not present in `s` at all: return `""`
- Duplicate characters in `t`: both copies must appear in the window (frequency, not just presence)
- `|t| > |s|`: impossible, return `""`

---

### Problem 12: Minimum Window Subsequence

> ⚠️ **Not a standard sliding window.** Order matters — you need `t` as a subsequence (characters in order, not just present).

**Why the standard template fails:**
```
s = "abcdebdde",  t = "bde"

A valid window must have b...d...e in order.
The standard frequency check ignores character ordering.
```

**Two-pass approach:**
```cpp
string minWindowSubsequence(string s, string t) {
    int n = s.size(), m = t.size();
    int start = -1, minLen = INT_MAX;
    int i = 0;

    while (i < n) {
        // Forward pass: find end of a valid subsequence
        int ti = 0;
        while (i < n && ti < m) {
            if (s[i] == t[ti]) ti++;
            i++;
        }
        if (ti < m) break;   // t not found from this position

        // Backward pass: tighten the start
        int end = i - 1;
        ti = m - 1;
        while (ti >= 0) {
            if (s[end] == t[ti]) ti--;
            end--;
        }
        end++;

        if (i - end < minLen) {
            minLen = i - end;
            start  = end;
        }
        i = end + 1;   // restart search just after this window's start
    }
    return start == -1 ? "" : s.substr(start, minLen);
}
```

**Complexity:** O(n * m) worst case; O(n) average with the two-pass trick.

**Edge Cases:**
- `t` longer than `s`: return `""`
- `t` appears multiple times: find the shortest occurrence
- Characters in `t` not in `s`: return `""`

---

## Two Pointer Patterns

> **Note:** Two pointers and sliding window are closely related. Two pointers typically operate on a **sorted array** or work from **both ends** inward, while sliding window usually expands/contracts from one direction.

### Two Pointer — Opposite Ends Template

```cpp
int left = 0, right = n - 1;

while (left < right) {
    if (condition_met) {
        // record answer
        left++;  // or right--
    } else if (too_small) {
        left++;
    } else {
        right--;
    }
}
```

**Common problems:**
- Two Sum (sorted array)
- Container With Most Water
- Trapping Rain Water (O(1) space)
- 3Sum / 4Sum

### Two Pointer — Same Direction (Fast/Slow)

```cpp
int slow = 0;

for (int fast = 0; fast < n; fast++) {
    if (condition) {
        arr[slow++] = arr[fast];
    }
}
```

**Common problems:**
- Remove Duplicates from Sorted Array
- Move Zeroes
- Partition Array

---

## Master Classification Table

| Pattern | Problems | Key Condition | Answer Update |
|---------|----------|---------------|---------------|
| **1. Longest Valid Window** | Longest No-Repeat Substr, Max Consecutive Ones III, Fruit Into Baskets, Char Replacement, At Most K Distinct | Window becomes invalid | `ans = max(ans, j-i+1)` |
| **2. AtMost(K) Count** | Binary Subarrays With Sum, Nice Subarrays, K Different Integers | Window becomes invalid | `ans += (j-i+1)` |
| **3. Contribution Count** | Substrings With All Three Chars | Window becomes valid → shrink | `ans += i` |
| **4. Fixed Size Window** | Max Points From Cards | Window size > k | per window |
| **5. Minimum Window** | Minimum Window Substring | Window becomes valid → shrink | `ans = min(ans, j-i+1)` |
| **Not pure SW** | Minimum Window Subsequence | Order-dependent | two-pass / DP |

---

## Interview Recognition Cheat Sheet

### Keyword → Pattern Mapping

| You see in the problem... | Think immediately... |
|---|---|
| **"Longest"**, **"Maximum length"**, **"Largest subarray"** | Pattern 1: Expand + shrink when invalid |
| **"Exactly K"** (occurrences, sum, distinct) | Pattern 2: `AtMost(K) - AtMost(K-1)` |
| **"At most K"** | Pattern 2 directly: `ans += (j-i+1)` |
| **"Count subarrays/substrings"** with a condition | Pattern 2 or 3: `+= (j-i+1)` or `+= i` |
| **"Minimum window"**, **"Smallest substring"** | Pattern 5: shrink while valid |
| **"Window size = K"**, **"exactly K elements"** | Pattern 4: Fixed size |
| **"Sorted array"**, **"pair with sum"** | Two Pointers (opposite ends) |
| **"Contains all characters of t"** (unordered) | Pattern 5: `formed == required` |
| **"Subsequence"** of t | Not standard SW — two-pass or DP |

### Decision Tree

```
Is window size fixed?
  ├─ YES → Pattern 4 (Fixed Size)
  └─ NO  → Are you counting or finding an extremum?
              ├─ Finding LONGEST/MAXIMUM → Pattern 1
              ├─ Finding MINIMUM/SHORTEST → Pattern 5
              └─ COUNTING subarrays?
                    ├─ "Exactly K" → Pattern 2 (AtMost trick)
                    ├─ "At most K" → Pattern 2 directly
                    └─ "Contains all of {a,b,c}" → Pattern 3
```

---

## Edge Cases Reference

These edge cases appear across multiple sliding window problems and are worth memorising:

| Situation | What to check |
|---|---|
| `k = 0` or `goal = 0` | AtMost(-1) must return 0 — add `if (k < 0) return 0` guard |
| Empty string / empty array | Return 0 or `""` before entering the loop |
| All elements identical | Verify max/min window still works at boundaries |
| `k >= n` | Answer is often the full array/string |
| Duplicates in constraint string `t` | Track frequencies, not just presence |
| Window state after shrinking | Ensure map/counter cleanup when count drops to 0 |
| Integer overflow in sum | Use `long long` for subarray sum problems |
| Single element array | Verify `i = 0, j = 0` case produces `j - i + 1 = 1` correctly |
| Characters not in target | `minWindow` → return `""`, not a garbage substring |
| `mostFreq` not decremented | Intentional in Char Replacement — don't "fix" it |

---

> **Final thought:** 80% of sliding window questions in interviews are these five patterns wearing a fake moustache and introducing themselves as a new problem. The remaining 20% are usually two of these patterns duct-taped together.

---

*Happy Sliding! 🚀*