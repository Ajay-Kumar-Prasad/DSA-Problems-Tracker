# 🗃️ Array DSA Patterns

> A complete reference for mastering Array problems through reusable patterns and intuition.

---

## 📚 Table of Contents

| # | Pattern | Key Use Cases |
|---|---------|--------------|
| 1 | [Two Pointer](#1-two-pointer-pattern) | Pair sum, Remove duplicates, Reverse |
| 2 | [Sliding Window](#2-sliding-window-pattern) | Subarray sum/length, Longest substring |
| 3 | [Prefix Sum](#3-prefix-sum-pattern) | Range sum queries, Subarray sum equals K |
| 4 | [Binary Search](#4-binary-search-pattern) | Search sorted array, Find boundary |
| 5 | [Kadane's Algorithm](#5-kadanes-algorithm) | Maximum subarray sum |
| 6 | [Dutch National Flag](#6-dutch-national-flag-pattern) | Sort 0s 1s 2s, 3-way partition |
| 7 | [Cyclic Sort](#7-cyclic-sort-pattern) | Missing number, Duplicate number |
| 8 | [Merge Intervals](#8-merge-intervals-pattern) | Overlapping intervals, Insert interval |
| 9 | [Matrix Traversal](#9-matrix-traversal-pattern) | Spiral order, Diagonal, BFS/DFS on grid |
| 10 | [Frequency / Hashing](#10-frequency--hashing-pattern) | Count, Anagram, Two Sum |
| 11 | [Monotonic Stack](#11-monotonic-stack-pattern) | Next greater, Largest rectangle |
| 12 | [Backtracking on Arrays](#12-backtracking-on-arrays-pattern) | Subsets, Permutations, Combinations |
| 13 | [Modified Binary Search](#13-modified-binary-search-pattern) | Rotated array, First/last position |
| 14 | [Greedy on Arrays](#14-greedy-on-arrays-pattern) | Jump game, Gas station, Activity selection |
| 15 | [Divide & Conquer](#15-divide--conquer-pattern) | Merge sort, Quick sort, Count inversions |

---

## 1. Two Pointer Pattern

### Core Idea
Place two pointers — usually at opposite ends or both at the start — and move them toward/away from each other based on conditions. Eliminates the need for nested loops.

### Templates

**Opposite ends (sorted array):**
```cpp
int left = 0, right = n - 1;

while (left < right) {
    int sum = arr[left] + arr[right];
    if (sum == target) {
        // found
    } else if (sum < target) {
        left++;
    } else {
        right--;
    }
}
```

**Same direction (fast/slow read-write):**
```cpp
int slow = 0;

for (int fast = 0; fast < n; fast++) {
    if (arr[fast] != val) {         // condition to keep
        arr[slow++] = arr[fast];
    }
}
// slow = new length
```

### Use Cases
- Two Sum (sorted input)
- Three Sum / Four Sum
- Remove Duplicates from Sorted Array
- Reverse Array / String
- Container With Most Water
- Trapping Rain Water

### Complexity
| Time | Space |
|------|-------|
| O(n) | O(1) |

---

## 2. Sliding Window Pattern

### Core Idea
Maintain a window `[left, right]` over the array. Expand right to grow the window; shrink left when a condition is violated. Avoids recomputing from scratch each step.

### Templates

**Fixed-size window:**
```cpp
int windowSum = 0;

// Build first window
for (int i = 0; i < k; i++)
    windowSum += arr[i];

int maxSum = windowSum;

for (int i = k; i < n; i++) {
    windowSum += arr[i] - arr[i - k];  // slide: add new, remove old
    maxSum = max(maxSum, windowSum);
}
```

**Variable-size window:**
```cpp
int left = 0, result = 0;
// (some running state: sum, count, map)

for (int right = 0; right < n; right++) {
    // expand: include arr[right] in window state

    while (/* window violates condition */) {
        // shrink: remove arr[left] from state
        left++;
    }

    result = max(result, right - left + 1);
}
```

### Use Cases
- Maximum Sum Subarray of Size K
- Longest Substring Without Repeating Characters
- Minimum Size Subarray Sum ≥ K
- Longest Subarray with Ones after Replacement
- Fruit Into Baskets (at most 2 distinct)
- Permutation in String

### Key Decision: Fixed vs Variable
| Fixed Window | Variable Window |
|-------------|----------------|
| Size K given explicitly | Optimize length under a constraint |
| Slide by 1 each step | Shrink when constraint violated |

### Complexity
| Time | Space |
|------|-------|
| O(n) | O(1) or O(k) for frequency map |

---

## 3. Prefix Sum Pattern

### Core Idea
Precompute a running sum array so any subarray sum `[l, r]` can be answered in O(1).

### Template
```cpp
vector<int> prefix(n + 1, 0);

for (int i = 0; i < n; i++)
    prefix[i + 1] = prefix[i] + arr[i];

// Sum of arr[l..r] (0-indexed, inclusive)
int rangeSum = prefix[r + 1] - prefix[l];
```

### Subarray Sum Equals K (HashMap Variant)
```cpp
unordered_map<int, int> freq;
freq[0] = 1;
int sum = 0, count = 0;

for (int x : arr) {
    sum += x;
    count += freq[sum - k];  // how many prefixes sum to (sum - k)
    freq[sum]++;
}
```

### Use Cases
- Range Sum Query (immutable)
- Subarray Sum Equals K
- Number of Subarrays with Sum Divisible by K
- Count of Subarrays with Given XOR
- 2D Matrix Range Sum (2D prefix sum)

### Complexity
| Time | Space |
|------|-------|
| O(n) build, O(1) query | O(n) |

---

## 4. Binary Search Pattern

### Core Idea
Repeatedly halve the search space on a **sorted** (or monotonic) structure. The key is correctly identifying the invariant: what does `left` always point to? What does `right` exclude?

### Template (Standard)
```cpp
int left = 0, right = n - 1;

while (left <= right) {
    int mid = left + (right - left) / 2;  // avoid overflow

    if (arr[mid] == target) return mid;
    else if (arr[mid] < target) left = mid + 1;
    else right = mid - 1;
}

return -1;  // not found
```

### Template (Find First True / Lower Bound)
```cpp
int left = 0, right = n;  // right = n (exclusive upper bound)

while (left < right) {
    int mid = left + (right - left) / 2;

    if (condition(mid)) right = mid;  // mid could be answer; shrink right
    else left = mid + 1;
}

return left;  // first index where condition is true
```

### Use Cases
- Binary Search (classic)
- Search Insert Position
- First and Last Position of Element
- Search in Rotated Sorted Array
- Find Minimum in Rotated Array
- Koko Eating Bananas (binary search on answer)
- Capacity To Ship Packages

### ⚠️ Classic Pitfall
```cpp
// ❌ Can overflow when n is large
int mid = (left + right) / 2;

// ✅ Always use this
int mid = left + (right - left) / 2;
```

### Complexity
| Time | Space |
|------|-------|
| O(log n) | O(1) |

---

## 5. Kadane's Algorithm

### Core Idea
Track the maximum subarray sum ending at each index. At every position, decide: extend the previous subarray or start fresh from the current element.

### Template
```cpp
int maxSum = arr[0];
int currSum = arr[0];

for (int i = 1; i < n; i++) {
    currSum = max(arr[i], currSum + arr[i]);  // extend or restart
    maxSum  = max(maxSum, currSum);
}

return maxSum;
```

### Variant — Also Track Indices
```cpp
int maxSum = arr[0], currSum = arr[0];
int start = 0, end = 0, tempStart = 0;

for (int i = 1; i < n; i++) {
    if (arr[i] > currSum + arr[i]) {
        currSum = arr[i];
        tempStart = i;
    } else {
        currSum += arr[i];
    }

    if (currSum > maxSum) {
        maxSum = currSum;
        start = tempStart;
        end = i;
    }
}
```

### Use Cases
- Maximum Subarray Sum
- Maximum Product Subarray (track both max and min)
- Maximum Sum Circular Subarray

### Key Insight
`currSum = max(arr[i], currSum + arr[i])` is equivalent to: "if the running sum went negative, reset — it's a drag on future sums."

### Complexity
| Time | Space |
|------|-------|
| O(n) | O(1) |

---

## 6. Dutch National Flag Pattern

### Core Idea
Three-way partition using three pointers: `low`, `mid`, `high`. Elements are classified into three buckets and swapped into place in a single pass.

### Template
```cpp
int low = 0, mid = 0, high = n - 1;

while (mid <= high) {
    if (arr[mid] == 0) {
        swap(arr[low++], arr[mid++]);
    } else if (arr[mid] == 1) {
        mid++;
    } else {
        swap(arr[mid], arr[high--]);
        // don't increment mid — recheck swapped element
    }
}
```

### Invariant at Every Step
```
arr[0..low-1]   → all 0s
arr[low..mid-1] → all 1s
arr[mid..high]  → unknown
arr[high+1..n-1]→ all 2s
```

### Use Cases
- Sort Colors (0s, 1s, 2s)
- Three-way partition around a pivot (QuickSort variant)
- Segregate negative and positive numbers

### Complexity
| Time | Space |
|------|-------|
| O(n) | O(1) |

---

## 7. Cyclic Sort Pattern

### Core Idea
For arrays containing numbers in range `[1, n]` (or `[0, n-1]`), each number belongs at a specific index. Place each element at its correct index by swapping. Then a single scan reveals missing/duplicate numbers.

### Template
```cpp
int i = 0;

while (i < n) {
    int correct = arr[i] - 1;  // where arr[i] should go (for range 1..n)

    if (arr[i] != arr[correct]) {
        swap(arr[i], arr[correct]);  // place at correct index
    } else {
        i++;
    }
}

// Now scan for anomalies
for (int i = 0; i < n; i++) {
    if (arr[i] != i + 1)
        // i+1 is missing; arr[i] is a duplicate
}
```

### Use Cases
- Find the Missing Number
- Find All Missing Numbers
- Find the Duplicate Number
- Find All Duplicates
- Find the Corrupt Pair (missing + duplicate)

### Key Insight
Works only when elements are in a **known, bounded range** that maps directly to indices.

### Complexity
| Time | Space |
|------|-------|
| O(n) | O(1) |

---

## 8. Merge Intervals Pattern

### Core Idea
Sort intervals by start time. Then iterate and either merge the current interval into the last one (if overlapping) or append it as a new interval.

### Template
```cpp
sort(intervals.begin(), intervals.end());  // sort by start

vector<vector<int>> result;
result.push_back(intervals[0]);

for (int i = 1; i < intervals.size(); i++) {
    auto& last = result.back();

    if (intervals[i][0] <= last[1]) {          // overlapping
        last[1] = max(last[1], intervals[i][1]); // merge
    } else {
        result.push_back(intervals[i]);          // non-overlapping
    }
}

return result;
```

### Overlap Condition
Two intervals `[a, b]` and `[c, d]` overlap when `c <= b` (after sorting by start).

### Use Cases
- Merge Intervals
- Insert Interval
- Meeting Rooms I & II
- Minimum Number of Arrows to Burst Balloons
- Employee Free Time

### Complexity
| Time | Space |
|------|-------|
| O(n log n) | O(n) |

---

## 9. Matrix Traversal Pattern

### Core Idea
Treat a 2D array as a graph. Use direction vectors for BFS/DFS. Handle boundaries and visited state carefully.

### Direction Vectors
```cpp
// 4-directional (up, down, left, right)
int dr[] = {-1, 1, 0, 0};
int dc[] = {0, 0, -1, 1};

// 8-directional (include diagonals)
int dr[] = {-1,-1,-1, 0, 0, 1, 1, 1};
int dc[] = {-1, 0, 1,-1, 1,-1, 0, 1};
```

### BFS on Grid
```cpp
queue<pair<int,int>> q;
q.push({startR, startC});
visited[startR][startC] = true;

while (!q.empty()) {
    auto [r, c] = q.front(); q.pop();

    for (int d = 0; d < 4; d++) {
        int nr = r + dr[d], nc = c + dc[d];

        if (nr >= 0 && nr < rows && nc >= 0 && nc < cols && !visited[nr][nc]) {
            visited[nr][nc] = true;
            q.push({nr, nc});
        }
    }
}
```

### Spiral Order Traversal
```cpp
int top = 0, bottom = rows-1, left = 0, right = cols-1;

while (top <= bottom && left <= right) {
    for (int c = left; c <= right; c++)  result.push_back(mat[top][c]);
    top++;
    for (int r = top; r <= bottom; r++)  result.push_back(mat[r][right]);
    right--;
    if (top <= bottom)
        for (int c = right; c >= left; c--) result.push_back(mat[bottom][c]);
    bottom--;
    if (left <= right)
        for (int r = bottom; r >= top; r--) result.push_back(mat[r][left]);
    left++;
}
```

### Use Cases
- Number of Islands (BFS/DFS)
- Rotting Oranges (multi-source BFS)
- Spiral Matrix
- Set Matrix Zeroes
- Word Search (DFS + backtrack)
- Shortest Path in Binary Matrix

### Complexity
| Time | Space |
|------|-------|
| O(rows × cols) | O(rows × cols) for visited |

---

## 10. Frequency / Hashing Pattern

### Core Idea
Use a hashmap or frequency array to count occurrences, enabling O(1) lookups instead of O(n) scans.

### Templates

**Frequency map:**
```cpp
unordered_map<int, int> freq;
for (int x : arr) freq[x]++;
```

**Two Sum using hashmap:**
```cpp
unordered_map<int, int> seen;  // value → index

for (int i = 0; i < n; i++) {
    int complement = target - arr[i];
    if (seen.count(complement))
        return {seen[complement], i};
    seen[arr[i]] = i;
}
```

### Use Cases
- Two Sum
- Group Anagrams
- Top K Frequent Elements
- Longest Consecutive Sequence
- Subarray Sum Equals K
- Valid Anagram
- Find All Duplicates

### When to Use Array vs HashMap
| Use `int freq[26]`  | Use `unordered_map` |
|--------------------|---------------------|
| Keys are small integers or chars | Arbitrary keys |
| Fixed known range | Unknown range |
| Slightly faster (cache) | More flexible |

### Complexity
| Time | Space |
|------|-------|
| O(n) average | O(n) |

---

## 11. Monotonic Stack Pattern

### Core Idea
Maintain a stack that is always sorted (increasing or decreasing). When a new element breaks the monotonicity, pop and process — those popped elements have found their "next greater/smaller" answer.

### Template — Next Greater Element
```cpp
vector<int> result(n, -1);
stack<int> st;  // stores indices

for (int i = 0; i < n; i++) {
    while (!st.empty() && arr[st.top()] < arr[i]) {
        result[st.top()] = arr[i];  // arr[i] is the next greater
        st.pop();
    }
    st.push(i);
}
```

### Monotonic Stack Types
| Stack Order | Finds |
|-------------|-------|
| Decreasing (top is smallest) | Next Greater Element |
| Increasing (top is largest) | Next Smaller Element |

### Use Cases
- Next Greater Element I & II
- Daily Temperatures
- Largest Rectangle in Histogram
- Trapping Rain Water
- Remove K Digits
- 132 Pattern

### Complexity
| Time | Space |
|------|-------|
| O(n) — each element pushed/popped once | O(n) |

---

## 12. Backtracking on Arrays Pattern

### Core Idea
Explore all possible choices recursively. At each step, make a choice, recurse, then undo (backtrack). Build the solution incrementally and abandon branches that can't succeed.

### Template
```cpp
void backtrack(vector<int>& arr, int start,
               vector<int>& current, vector<vector<int>>& result) {

    result.push_back(current);  // record current state (for subsets)

    for (int i = start; i < arr.size(); i++) {
        // skip duplicates (if needed)
        if (i > start && arr[i] == arr[i-1]) continue;

        current.push_back(arr[i]);        // choose
        backtrack(arr, i + 1, current, result);  // explore
        current.pop_back();               // undo
    }
}
```

### Use Cases
- Subsets / Subsets II
- Permutations / Permutations II
- Combinations / Combination Sum
- Palindrome Partitioning
- Word Search

### Three Key Decisions
1. **Base case** — when to record or stop
2. **Loop range** — `start` (subsets) vs `0` (permutations)
3. **Skip duplicates** — sort first, then `if (i > start && arr[i] == arr[i-1]) continue`

### Complexity
| Problem | Time |
|---------|------|
| Subsets | O(2ⁿ) |
| Permutations | O(n!) |
| Combination Sum | O(2ⁿ) worst case |

---

## 13. Modified Binary Search Pattern

### Core Idea
Extend classic binary search to handle rotated arrays, finding boundaries, or "binary searching on the answer" when the search space is not an explicit array but a value range.

### Search in Rotated Sorted Array
```cpp
int left = 0, right = n - 1;

while (left <= right) {
    int mid = left + (right - left) / 2;

    if (arr[mid] == target) return mid;

    if (arr[left] <= arr[mid]) {          // left half is sorted
        if (arr[left] <= target && target < arr[mid])
            right = mid - 1;
        else
            left = mid + 1;
    } else {                               // right half is sorted
        if (arr[mid] < target && target <= arr[right])
            left = mid + 1;
        else
            right = mid - 1;
    }
}
```

### Binary Search on Answer
```cpp
// "Is it possible to achieve X with mid value?"
int left = minPossible, right = maxPossible;

while (left < right) {
    int mid = left + (right - left) / 2;

    if (feasible(mid))  right = mid;    // mid works; try smaller
    else                left = mid + 1; // mid too small
}

return left;
```

### Use Cases
- Search in Rotated Sorted Array
- Find Minimum in Rotated Sorted Array
- First and Last Position of Element
- Koko Eating Bananas
- Minimum Days to Make Bouquets
- Split Array Largest Sum

### Complexity
| Time | Space |
|------|-------|
| O(log n) | O(1) |

---

## 14. Greedy on Arrays Pattern

### Core Idea
Make the locally optimal choice at each step with the hope of reaching the global optimum. Greedy works when a problem has the **greedy choice property** and **optimal substructure**.

### Template — Jump Game
```cpp
int maxReach = 0;

for (int i = 0; i < n; i++) {
    if (i > maxReach) return false;     // can't reach here
    maxReach = max(maxReach, i + arr[i]);
}

return true;
```

### Template — Activity Selection (Interval Scheduling)
```cpp
// Sort by end time
sort(intervals.begin(), intervals.end(),
     [](auto& a, auto& b){ return a[1] < b[1]; });

int count = 1, lastEnd = intervals[0][1];

for (int i = 1; i < intervals.size(); i++) {
    if (intervals[i][0] >= lastEnd) {   // no overlap
        count++;
        lastEnd = intervals[i][1];
    }
}
```

### Use Cases
- Jump Game I & II
- Gas Station
- Candy Distribution
- Non-overlapping Intervals
- Minimum Number of Platforms
- Assign Cookies
- Partition Labels

### ⚠️ Greedy vs DP
> When greedy fails — usually when past choices affect future options — switch to Dynamic Programming.

### Complexity
Typically O(n) or O(n log n) with sorting.

---

## 15. Divide & Conquer Pattern

### Core Idea
Split the array into halves, solve each half recursively, then combine the results. Naturally maps to sorting and counting problems.

### Merge Sort Template
```cpp
void mergeSort(vector<int>& arr, int left, int right) {
    if (left >= right) return;

    int mid = left + (right - left) / 2;
    mergeSort(arr, left, mid);
    mergeSort(arr, mid + 1, right);
    merge(arr, left, mid, right);
}

void merge(vector<int>& arr, int left, int mid, int right) {
    vector<int> temp;
    int i = left, j = mid + 1;

    while (i <= mid && j <= right) {
        if (arr[i] <= arr[j]) temp.push_back(arr[i++]);
        else                  temp.push_back(arr[j++]);
    }

    while (i <= mid)  temp.push_back(arr[i++]);
    while (j <= right) temp.push_back(arr[j++]);

    for (int k = left; k <= right; k++)
        arr[k] = temp[k - left];
}
```

### Use Cases
- Merge Sort
- Quick Sort
- Count Inversions (modified merge sort)
- Find Median of Two Sorted Arrays
- Maximum Subarray (D&C approach)
- Closest Pair of Points

### Complexity
| Time | Space |
|------|-------|
| O(n log n) | O(n) |

---

## 📋 Common Interview Problems Cheatsheet

| Problem | Pattern(s) |
|---------|-----------|
| Two Sum | Hashing |
| Three Sum | Two Pointer |
| Container With Most Water | Two Pointer |
| Maximum Subarray | Kadane's Algorithm |
| Best Time to Buy and Sell Stock | Kadane's / Greedy |
| Product of Array Except Self | Prefix Sum (prefix × suffix) |
| Maximum Product Subarray | Kadane's (track min too) |
| Find Minimum in Rotated Array | Modified Binary Search |
| Search in Rotated Array | Modified Binary Search |
| Merge Intervals | Merge Intervals |
| Sort Colors | Dutch National Flag |
| Subarray Sum Equals K | Prefix Sum + Hashing |
| Longest Subarray / Substring | Sliding Window |
| Trapping Rain Water | Two Pointer / Monotonic Stack |
| Largest Rectangle in Histogram | Monotonic Stack |
| Number of Islands | Matrix BFS/DFS |
| Spiral Matrix | Matrix Traversal |
| Permutations | Backtracking |
| Subsets | Backtracking |
| Combination Sum | Backtracking |
| Missing Number | Cyclic Sort / XOR |
| Find Duplicate | Cyclic Sort / Fast-Slow |
| Jump Game | Greedy |
| Merge Sort | Divide & Conquer |
| Count Inversions | Divide & Conquer |

---

## 🐛 Debugging Guide

### 1. Always Verify Index Bounds
```cpp
// ❌ Off-by-one: reads arr[n]
for (int i = 0; i <= n; i++) ...

// ✅ Correct
for (int i = 0; i < n; i++) ...
```

### 2. Avoid Integer Overflow
```cpp
// ❌ Overflows when left + right > INT_MAX
int mid = (left + right) / 2;

// ✅ Safe
int mid = left + (right - left) / 2;
```

### 3. Sliding Window — Don't Forget to Shrink
When the window violates a condition, shrink from the left **before** updating the result.

### 4. Backtracking — Sort First for Duplicate Skipping
```cpp
sort(arr.begin(), arr.end());   // required before skipping duplicates
if (i > start && arr[i] == arr[i-1]) continue;
```

### 5. Think in Invariants
Ask after every iteration:
- Is the window still valid?
- Is the two-pointer condition still satisfied?
- Is the monotonic stack still monotonic?

### 6. Always Test Edge Cases

| Edge Case | Why It Matters |
|-----------|---------------|
| Empty array | Immediate return, no access |
| Single element | Many patterns assume n ≥ 2 |
| All same elements | Duplicates in sorting/searching |
| All negatives | Kadane's, product subarray |
| Already sorted | Quicksort worst case |
| Reverse sorted | Two pointer, merge |
| Target not present | Binary search return value |
| Overflow values | Product subarray, prefix sum |

---

## 🏆 Pattern Selection Guide

```
Input is sorted?
├── Yes → Binary Search (O(log n)) or Two Pointer (O(n))
└── No  → Consider sorting first (O(n log n))

Need subarray result?
├── Fixed length     → Fixed Sliding Window
├── Optimize length  → Variable Sliding Window
├── Sum equals K     → Prefix Sum + HashMap
└── Maximum sum      → Kadane's Algorithm

Need all combinations?
└── Backtracking (subsets / permutations / combinations)

Intervals given?
└── Sort + Merge Intervals

Elements in range [1..n]?
└── Cyclic Sort (find missing / duplicate in O(n) O(1))

"Next greater / smaller" element?
└── Monotonic Stack

2D grid problem?
└── Matrix BFS/DFS with direction vectors

Greedy or DP?
├── Past choices don't affect future → Greedy
└── Past choices DO affect future    → Dynamic Programming
```

---

## 🎯 Final Advice

**Don't memorize problems — recognize patterns.**

Most array interview questions are combinations of just a few building blocks:

```
Two Pointer  +  Sliding Window  +  Binary Search  +  Hashing  +  Sorting
```

When you see a new problem, ask:
1. Is the input sorted (or can it be)? → Binary Search
2. Am I looking for a subarray/substring? → Sliding Window or Prefix Sum
3. Am I looking for pairs/triplets? → Two Pointer
4. Do I need counts or lookups? → Hashing
5. Do I need all solutions? → Backtracking
6. Is there an obvious greedy choice? → Greedy
7. Does it involve "next greater/smaller"? → Monotonic Stack

Once patterns click, arrays stop feeling like puzzles and start feeling like templates.

---

*Generated for DSA Interview Preparation · Array Patterns Reference*