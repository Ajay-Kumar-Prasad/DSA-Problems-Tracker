# 🎯 DSA Patterns: Greedy Algorithms

> Greedy isn't one technique — it's a mindset applied to **6 recurring structures**: sorting + picking, interval scheduling, exchange arguments, two-pointer greedy, simulation, and ratio-based selection. The hard part of greedy is never the code. It's proving the greedy choice is actually safe.

---

## 📖 Table of Contents

1. [What Makes a Problem "Greedy"](#what-makes-a-problem-greedy)
2. [Pattern 1: Sort + Two-Pointer Matching](#pattern-1-sort--two-pointer-matching)
3. [Pattern 2: Ratio-Based Selection (Fractional Knapsack)](#pattern-2-ratio-based-selection-fractional-knapsack)
4. [Pattern 3: Simulation Greedy](#pattern-3-simulation-greedy)
5. [Pattern 4: Interval Scheduling](#pattern-4-interval-scheduling)
6. [Pattern 5: Jump / Reachability Greedy](#pattern-5-jump--reachability-greedy)
7. [Pattern 6: Two-Pass Exchange Argument](#pattern-6-two-pass-exchange-argument)
8. [Pattern 7: Deadline Scheduling (Job Sequencing)](#pattern-7-deadline-scheduling-job-sequencing)
9. [Pattern 8: Stack-Based Validity Greedy](#pattern-8-stack-based-validity-greedy)
10. [Pattern 9: Sweep Line / Counting Greedy](#pattern-9-sweep-line--counting-greedy)
11. [Special Case: LRU Page Replacement](#special-case-lru-page-replacement)
12. [Master Cheat Sheet](#master-cheat-sheet)
13. [Interview Recognition Guide](#interview-recognition-guide)
14. [Edge Cases Reference](#edge-cases-reference)

---

## What Makes a Problem "Greedy"

A problem is greedy-solvable when a **locally optimal choice at each step leads to a globally optimal solution** — and crucially, that choice never needs to be undone later. The two properties to check before trusting a greedy approach:

```
1. Greedy Choice Property
   → A globally optimal solution can be built by choosing
     the locally best option at each step.

2. Optimal Substructure
   → An optimal solution to the problem contains
     optimal solutions to its subproblems.
```

**Hint:** If you ever feel the urge to "undo" a greedy choice, the problem is probably DP, not greedy.

---

## Pattern 1: Sort + Two-Pointer Matching

> **Core Idea:** Sort both sides of the matching problem. Use two pointers to greedily satisfy the smallest demand with the smallest sufficient supply (or vice versa). This guarantees you never "waste" a large resource on a small need.

### 🔍 Recognition Hint

```
"Maximize the number of X satisfied with Y"
"Each X needs at least some amount of Y"
"Match greedily by size"
```

---

### Assign Cookies

**Problem:** Each child has a greed factor; each cookie has a size. A child is content if the cookie size ≥ their greed factor. Maximize content children.

```cpp
int findContentChildren(vector<int>& g, vector<int>& s) {
    sort(g.begin(), g.end());   // greed factors
    sort(s.begin(), s.end());   // cookie sizes

    int i = 0, j = 0;   // i -> children, j -> cookies
    int count = 0;

    while (i < g.size() && j < s.size()) {
        if (s[j] >= g[i]) {     // cookie satisfies this child
            count++;
            i++;
        }
        j++;   // move to next cookie regardless
    }
    return count;
}
```

**Why greedy works:** Giving the smallest sufficient cookie to the least greedy child first never hurts — a bigger cookie saved for later can only help satisfy a greedier child. This is a classic **exchange argument**: if an optimal solution gave a larger cookie to a less greedy child, swapping it with our greedy choice never makes things worse.

**Complexity:** O(n log n + m log m) for sorting, O(n + m) for the two-pointer scan. Overall **O(n log n)**.

**Edge Cases:**
- No cookies (`s` empty) → answer = 0
- No children (`g` empty) → answer = 0
- All cookies too small → answer = 0
- All cookies oversized → answer = min(n, m)
- Duplicate greed factors / cookie sizes → handled naturally by sorting

---

## Pattern 2: Ratio-Based Selection (Fractional Knapsack)

> **Core Idea:** When items can be split (fractionally taken), always sort by **value-to-weight ratio** and greedily fill capacity with the highest-ratio items first. Unlike 0/1 Knapsack, this is provably greedy-optimal because fractions let you always use 100% of remaining capacity efficiently.

### 🔍 Recognition Hint

```
"Items can be split / taken fractionally"
"Maximize value within a weight/capacity limit"
"Value per unit weight matters"
```

```cpp
struct Item { int value, weight; };

double fractionalKnapsack(int capacity, vector<Item>& items) {
    sort(items.begin(), items.end(), [](Item& a, Item& b) {
        return (double)a.value / a.weight > (double)b.value / b.weight;
    });

    double totalValue = 0.0;
    int remaining = capacity;

    for (auto& item : items) {
        if (item.weight <= remaining) {
            totalValue += item.value;
            remaining  -= item.weight;
        } else {
            // Take the fraction that fits
            totalValue += item.value * ((double)remaining / item.weight);
            break;   // capacity exhausted
        }
    }
    return totalValue;
}
```

**Why greedy works (and 0/1 Knapsack isn't greedy):** Because fractions are allowed, you can always consume exactly the remaining capacity with the best available ratio — there's no "wasted" leftover space that could've been better used by a different combination. In 0/1 Knapsack, items are atomic, so a greedy ratio choice can leave unusable leftover capacity — that's why 0/1 needs DP.

**Complexity:** O(n log n) for sorting, O(n) for the greedy fill. Overall **O(n log n)**.

**Edge Cases:**
- `capacity = 0` → answer = 0
- An item's weight = 0 with positive value → place it first (infinite ratio); guard against division by zero
- `capacity >= total weight of all items` → take everything, no fractions needed
- All items have identical ratio → order among them doesn't matter
- Single item larger than capacity → take the fraction and stop

---

## Pattern 3: Simulation Greedy

> **Core Idea:** Some problems don't need sorting or clever math — they need you to simulate the process directly, making the locally obvious correct choice at each step (e.g., always give change using the largest denomination first).

### 🔍 Recognition Hint

```
"Process transactions/customers in order"
"Track running state, can you reach the end successfully?"
"Make change / give back resources"
```

---

### Lemonade Change

**Problem:** Customers pay with $5, $10, or $20 bills for a $5 lemonade. You start with no change. Determine if you can give correct change to everyone.

```cpp
bool lemonadeChange(vector<int>& bills) {
    int five = 0, ten = 0;

    for (int bill : bills) {
        if (bill == 5) {
            five++;
        } else if (bill == 10) {
            if (five == 0) return false;
            five--;
            ten++;
        } else {   // bill == 20
            // Prefer giving a $10 + $5 over three $5s — saves $5 bills for flexibility
            if (ten > 0 && five > 0) {
                ten--;
                five--;
            } else if (five >= 3) {
                five -= 3;
            } else {
                return false;
            }
        }
    }
    return true;
}
```

**Why greedy works:** $5 bills are strictly more flexible than $10 bills (a $10 can only be used for $20 change, while a $5 can be used for either). The greedy choice — preserve $5s by spending $10s first when paying $20 change — maximizes future flexibility.

**Complexity:** O(n) time, O(1) space.

**Edge Cases:**
- First customer pays with $10 or $20 → immediately false (no change available)
- All customers pay with $5 → always true
- Exactly enough change at some point, then it runs out → must check at every step, not just the end

---

## Pattern 4: Interval Scheduling

> **Core Idea:** Sort intervals by **end time** (not start time!) and greedily select the interval that finishes earliest among remaining valid choices. Finishing earlier always leaves more room for future intervals — this is the single most important greedy insight in interval problems.

### 🔍 Recognition Hint

```
"Maximize number of non-overlapping meetings/intervals"
"Minimum removals to make intervals non-overlapping"
"Schedule the most events in one room"
```

---

### N Meetings in One Room

**Problem:** Given start and end times of N meetings in one room, find the maximum number of meetings that can be held.

```cpp
int maxMeetings(vector<int>& start, vector<int>& end) {
    int n = start.size();
    vector<pair<int,int>> meetings;   // {end, start}

    for (int i = 0; i < n; i++)
        meetings.push_back({end[i], start[i]});

    sort(meetings.begin(), meetings.end());   // sort by end time

    int count = 1;
    int lastEnd = meetings[0].first;

    for (int i = 1; i < n; i++) {
        if (meetings[i].second > lastEnd) {   // starts after previous ends
            count++;
            lastEnd = meetings[i].first;
        }
    }
    return count;
}
```

**Why sort by end time, not start time?** A meeting that ends early leaves the most room for future meetings, regardless of when it started. Sorting by start time can trap you into picking a long meeting that blocks many short ones later.

**Complexity:** O(n log n) for sorting, O(n) for the greedy scan. Overall **O(n log n)**.

**Edge Cases:**
- Meetings with identical end times → tie-break doesn't matter for correctness
- Back-to-back meetings (`end[i] == start[i+1]`) → typically counted as non-overlapping (check problem's boundary convention)
- Single meeting → answer = 1
- All meetings overlap each other → answer = 1

---

### Merge Intervals

**Problem:** Merge all overlapping intervals.

```cpp
vector<vector<int>> mergeIntervals(vector<vector<int>>& intervals) {
    if (intervals.empty()) return {};

    sort(intervals.begin(), intervals.end());   // sort by start time
    vector<vector<int>> merged;
    merged.push_back(intervals[0]);

    for (int i = 1; i < intervals.size(); i++) {
        if (intervals[i][0] <= merged.back()[1]) {
            // Overlapping — extend the last merged interval
            merged.back()[1] = max(merged.back()[1], intervals[i][1]);
        } else {
            merged.push_back(intervals[i]);
        }
    }
    return merged;
}
```

**Complexity:** O(n log n) for sorting, O(n) for the merge pass. Overall **O(n log n)**.

**Edge Cases:**
- Already non-overlapping intervals → output equals input (still sorted)
- Fully nested intervals (`[1,10]`, `[2,3]`) → must use `max()`, not just the later interval's end
- Single interval → return as-is
- Empty input → return empty

---

### Insert Interval

**Problem:** Insert a new interval into a sorted list of non-overlapping intervals, merging as needed.

```cpp
vector<vector<int>> insertInterval(vector<vector<int>>& intervals, vector<int>& newInterval) {
    vector<vector<int>> result;
    int i = 0, n = intervals.size();

    // Add all intervals ending before newInterval starts
    while (i < n && intervals[i][1] < newInterval[0]) {
        result.push_back(intervals[i++]);
    }

    // Merge all overlapping intervals into newInterval
    while (i < n && intervals[i][0] <= newInterval[1]) {
        newInterval[0] = min(newInterval[0], intervals[i][0]);
        newInterval[1] = max(newInterval[1], intervals[i][1]);
        i++;
    }
    result.push_back(newInterval);

    // Add remaining intervals
    while (i < n) result.push_back(intervals[i++]);

    return result;
}
```

**Complexity:** O(n) time (input already sorted, no extra sort needed), O(n) space.

**Edge Cases:**
- New interval doesn't overlap anything → simple insertion at correct position
- New interval swallows all existing intervals → result = single merged interval
- Empty input list → result = `[newInterval]`
- New interval at the very start or very end → boundary while-loops must not over/under-shoot

---

### Non-overlapping Intervals

**Problem:** Find the minimum number of intervals to remove to make the rest non-overlapping.

```cpp
int eraseOverlapIntervals(vector<vector<int>>& intervals) {
    if (intervals.empty()) return 0;

    sort(intervals.begin(), intervals.end(), [](vector<int>& a, vector<int>& b) {
        return a[1] < b[1];   // sort by end time
    });

    int count = 0;
    int lastEnd = intervals[0][1];

    for (int i = 1; i < intervals.size(); i++) {
        if (intervals[i][0] < lastEnd) {
            count++;   // must remove this interval — it overlaps
        } else {
            lastEnd = intervals[i][1];   // keep this one, update boundary
        }
    }
    return count;
}
```

**Key insight:** This is the **same pattern as N Meetings** — maximize kept intervals by sorting on end time, then `removals = total - kept`.

**Complexity:** O(n log n) for sorting, O(n) for the scan. Overall **O(n log n)**.

**Edge Cases:**
- No overlaps at all → answer = 0
- All intervals identical → keep 1, remove the rest
- Single interval → answer = 0

---

## Pattern 5: Jump / Reachability Greedy

> **Core Idea:** Track the **farthest index reachable** at each step. The greedy insight is that you don't need to decide which specific jump to take in advance — you just need to know the maximum frontier you can reach, and greedily extend it.

### 🔍 Recognition Hint

```
"Can you reach the last index?"
"Minimum number of jumps to reach the end"
"Each element tells you the max jump length from there"
```

---

### Jump Game I (Can you reach the end?)

```cpp
bool canJump(vector<int>& nums) {
    int maxReach = 0;

    for (int i = 0; i < nums.size(); i++) {
        if (i > maxReach) return false;   // stuck — can't even reach index i
        maxReach = max(maxReach, i + nums[i]);
    }
    return true;
}
```

**Why greedy works:** At every index, you only care about the single number that matters: the farthest point reachable so far. You never need to track *which* jump sequence got you there.

**Complexity:** O(n) time, O(1) space.

**Edge Cases:**
- Single element array → always true (already at the end)
- First element is 0 and array length > 1 → false immediately
- A 0 in the middle that's already "jumped over" by maxReach → still fine, no issue

---

### Jump Game II (Minimum jumps to reach the end)

```cpp
int jump(vector<int>& nums) {
    int jumps = 0, currentEnd = 0, farthest = 0;

    for (int i = 0; i < nums.size() - 1; i++) {
        farthest = max(farthest, i + nums[i]);

        if (i == currentEnd) {        // exhausted current jump's range
            jumps++;
            currentEnd = farthest;    // commit to the best next jump
        }
    }
    return jumps;
}
```

**Why greedy works (BFS-like levels):** Think of it as BFS where each "level" is one jump. `currentEnd` marks the boundary of the current jump's reach; `farthest` tracks the best possible next boundary. When you exhaust the current level, you commit to a jump — this is provably optimal because delaying the jump can never help reach farther sooner.

**Complexity:** O(n) time, O(1) space.

**Edge Cases:**
- Array of size 1 → answer = 0 (already there)
- `nums[0] = 0` with array length > 1 → unreachable (problem usually guarantees reachability — verify constraints)
- Last index reachable exactly at `currentEnd` → loop condition `i < n-1` avoids an unnecessary final jump count

---

## Pattern 6: Two-Pass Exchange Argument

> **Core Idea:** Some constraints are easier to satisfy in two separate directions. Solve the left-to-right constraint first, then the right-to-left constraint, and combine with `max()` at each position. Trying to satisfy both directions in a single pass usually fails.

### 🔍 Recognition Hint

```
"Each element must satisfy a comparison with BOTH neighbors"
"Higher rating than X gets more than X"
```

---

### Candy

**Problem:** Children with ratings stand in a line. Each child gets at least 1 candy. A child with a higher rating than a neighbor must get more candy than that neighbor. Minimize total candies.

```cpp
int candy(vector<int>& ratings) {
    int n = ratings.size();
    vector<int> candies(n, 1);

    // Left to right pass: satisfy "higher than left neighbor"
    for (int i = 1; i < n; i++) {
        if (ratings[i] > ratings[i - 1])
            candies[i] = candies[i - 1] + 1;
    }

    // Right to left pass: satisfy "higher than right neighbor"
    for (int i = n - 2; i >= 0; i--) {
        if (ratings[i] > ratings[i + 1])
            candies[i] = max(candies[i], candies[i + 1] + 1);
    }

    int total = 0;
    for (int c : candies) total += c;
    return total;
}
```

**Why two passes (and why `max`, not `+=`)?** A single pass can't satisfy both neighbor constraints simultaneously — fixing the right-side constraint might violate what the left pass already established. Using `max()` in the second pass ensures we never undo progress made by the first pass; we only increase a candy count when strictly necessary.

**Complexity:** O(n) time, O(n) space for the candies array (can be optimized to O(1) with a more complex single-pass slope-tracking trick, but the two-pass version is the standard, interview-safe approach).

**Edge Cases:**
- All ratings equal → everyone gets 1 candy
- Strictly increasing ratings → candies = 1, 2, 3, ..., n
- Strictly decreasing ratings → candies = n, n-1, ..., 1
- Single child → answer = 1
- Up-down-up pattern (peak in the middle) → verify the `max()` correctly captures both constraints at the peak

---

## Pattern 7: Deadline Scheduling (Job Sequencing)

> **Core Idea:** Sort jobs by profit (descending). For each job, greedily place it in the **latest available time slot** before its deadline. This maximizes the number of high-profit jobs that fit, since reserving early slots for later jobs preserves the most flexibility.

### 🔍 Recognition Hint

```
"Each job has a deadline and a profit"
"Each job takes 1 unit of time"
"Maximize total profit, only one job per time slot"
```

```cpp
struct Job { int id, deadline, profit; };

pair<int,int> jobSequencing(vector<Job>& jobs) {
    sort(jobs.begin(), jobs.end(), [](Job& a, Job& b) {
        return a.profit > b.profit;   // sort by profit descending
    });

    int maxDeadline = 0;
    for (auto& job : jobs) maxDeadline = max(maxDeadline, job.deadline);

    vector<int> slot(maxDeadline + 1, -1);   // slot[i] = job id scheduled at time i
    int countJobs = 0, totalProfit = 0;

    for (auto& job : jobs) {
        // Try to place this job in the latest available slot before its deadline
        for (int t = job.deadline; t > 0; t--) {
            if (slot[t] == -1) {
                slot[t] = job.id;
                countJobs++;
                totalProfit += job.profit;
                break;
            }
        }
    }
    return {countJobs, totalProfit};
}
```

**Why the latest available slot (not the earliest)?** Reserving the latest possible slot for the current (high-profit) job leaves earlier slots open for jobs with tighter (smaller) deadlines that might come later in the sorted order. Taking an early slot needlessly could block a future job that has no other option.

**Complexity:** O(n log n) for sorting + O(n × maxDeadline) for slot search = **O(n²)** in the worst case. Can be optimized to **O(n log n)** using a Disjoint Set Union (DSU) to jump directly to the nearest free slot.

**Edge Cases:**
- All jobs have the same deadline → only 1 job (highest profit) can be scheduled
- Deadlines larger than the number of jobs → no slot contention, all jobs may fit
- Duplicate profits → tie-break order doesn't affect total profit
- A job with deadline = 0 → cannot be scheduled (no valid slot ≥ 1)

---

## Pattern 8: Stack-Based Validity Greedy

> **Core Idea:** Some greedy problems are validity checks, not optimization. Use a stack to track open structure (brackets, nested state) and greedily close/match as soon as possible. The decision at each character is immediate and never revisited.

### 🔍 Recognition Hint

```
"Valid sequence of brackets/parentheses"
"Matching nested structure"
```

---

### Valid Parenthesis Checker

```cpp
bool isValid(string s) {
    stack<char> st;
    unordered_map<char,char> match = {{')','('}, {']','['}, {'}','{'}};

    for (char c : s) {
        if (c == '(' || c == '[' || c == '{') {
            st.push(c);
        } else {
            if (st.empty() || st.top() != match[c])
                return false;
            st.pop();
        }
    }
    return st.empty();
}
```

**Why this is greedy:** Each closing bracket is matched against the most recent unmatched opener immediately — there's no benefit to delaying the match or trying alternative pairings. The locally correct choice (match with the top of stack) is always part of the globally correct answer.

**Complexity:** O(n) time, O(n) space.

**Edge Cases:**
- Empty string → valid (trivially balanced)
- Only opening brackets → invalid (stack non-empty at the end)
- Only closing brackets → invalid immediately (empty stack on first close)
- Mismatched types (e.g., `"(]"`) → invalid
- Long, fully nested string (`"((((...))))"`) → still O(n), no issue

---

## Pattern 9: Sweep Line / Counting Greedy

> **Core Idea:** Convert events into a timeline of "+1" (start) and "-1" (end) signals, sort them, and sweep through while tracking a running counter. The maximum value of the counter during the sweep is the answer. This avoids comparing every pair of intervals directly.

### 🔍 Recognition Hint

```
"Minimum resources needed to handle overlapping events"
"Maximum number of overlapping intervals at any point in time"
```

---

### Minimum Number of Platforms Required for a Railway

**Problem:** Given arrival and departure times of trains, find the minimum number of platforms needed so no train waits.

```cpp
int findPlatform(vector<int>& arr, vector<int>& dep) {
    sort(arr.begin(), arr.end());
    sort(dep.begin(), dep.end());

    int platforms = 0, maxPlatforms = 0;
    int i = 0, j = 0, n = arr.size();

    while (i < n && j < n) {
        if (arr[i] <= dep[j]) {     // a train arrives before another departs
            platforms++;
            maxPlatforms = max(maxPlatforms, platforms);
            i++;
        } else {                     // a train departs, freeing a platform
            platforms--;
            j++;
        }
    }
    return maxPlatforms;
}
```

**Why sort arrivals and departures separately?** You don't need to track *which* train is on *which* platform — only how many are simultaneously present. Sorting both arrays independently and sweeping with two pointers correctly counts overlaps without pairing trains explicitly.

**Complexity:** O(n log n) for sorting both arrays, O(n) for the sweep. Overall **O(n log n)**.

**Edge Cases:**
- All trains arrive and depart at the same time → all need separate platforms simultaneously
- No overlapping trains → answer = 1
- A train's arrival equals another's departure (`arr[i] == dep[j]`) → convention matters; check whether simultaneous arrival/departure needs 1 or 2 platforms based on problem statement
- Single train → answer = 1

---

## Special Case: LRU Page Replacement

> Not a classic "sort and pick" greedy — it's a **greedy eviction policy**: at each page fault, evict the page that hasn't been used for the longest time, betting that recently used pages are more likely to be reused soon (temporal locality).

### 🔍 Recognition Hint

```
"Page replacement"
"Evict least recently used"
"Cache with limited capacity"
```

```cpp
int lruPageFaults(vector<int>& pages, int capacity) {
    list<int> cache;                              // front = most recent
    unordered_map<int, list<int>::iterator> pos;
    int faults = 0;

    for (int page : pages) {
        if (pos.find(page) == pos.end()) {        // page fault
            faults++;
            if (cache.size() == capacity) {
                int lru = cache.back();
                cache.pop_back();
                pos.erase(lru);
            }
        } else {
            cache.erase(pos[page]);               // remove old position
        }
        cache.push_front(page);                   // move to front (most recent)
        pos[page] = cache.begin();
    }
    return faults;
}
```

**Why this is "greedy":** At every eviction decision, LRU makes the locally reasonable choice — discard whatever was used longest ago — without any lookahead into future access patterns. (Note: the *optimal* offline page replacement strategy, Belady's algorithm, requires future knowledge and isn't greedy in the same online sense.)

**Complexity:** O(1) per access using HashMap + Doubly Linked List, O(n) total for n page accesses.

**Edge Cases:**
- Capacity = 0 → every access is a fault
- All accesses are the same page → only 1 fault total (first access)
- Capacity ≥ number of distinct pages → faults = number of distinct pages (each loaded once)
- Empty access sequence → 0 faults

---

## Master Cheat Sheet

| Pattern | Problems | Sort Key | Core Trick |
|---------|----------|----------|-------------|
| Sort + Two-Pointer | Assign Cookies | Both arrays ascending | Match smallest sufficient resource |
| Ratio-Based Selection | Fractional Knapsack | value/weight descending | Take fractions of best ratio first |
| Simulation Greedy | Lemonade Change | — (process in order) | Preserve flexible resources |
| Interval Scheduling | N Meetings, Merge Intervals, Insert Interval, Non-overlapping Intervals | End time ascending | Earliest finish leaves most room |
| Jump/Reachability | Jump Game I & II | — (single pass) | Track farthest reachable index |
| Two-Pass Exchange | Candy | — (L→R then R→L) | Combine both passes with `max()` |
| Deadline Scheduling | Job Sequencing | Profit descending | Place in latest available slot |
| Stack Validity | Valid Parenthesis Checker | — (stack-based) | Match immediately, never delay |
| Sweep Line | Minimum Platforms | Arrivals & departures separately | Track running overlap counter |
| Greedy Eviction | LRU Page Replacement | — (recency-based) | Evict the least recently used |

---

## Interview Recognition Guide

| Problem Clue | Pattern |
|---|---|
| "Maximize satisfied children/people with limited resources" | Sort + Two-Pointer (Pattern 1) |
| "Items can be split, maximize value within capacity" | Ratio-Based Selection (Pattern 2) |
| "Process customers/transactions in order, can you succeed?" | Simulation Greedy (Pattern 3) |
| "Maximum non-overlapping intervals / meetings" | Interval Scheduling (Pattern 4) |
| "Merge / insert intervals" | Interval Scheduling (Pattern 4) |
| "Minimum removals to eliminate overlaps" | Interval Scheduling (Pattern 4) |
| "Can you reach the end? / Minimum jumps" | Jump/Reachability Greedy (Pattern 5) |
| "Must be greater than BOTH neighbors" | Two-Pass Exchange Argument (Pattern 6) |
| "Jobs with deadlines and profits, maximize profit" | Deadline Scheduling (Pattern 7) |
| "Balanced brackets / valid nested structure" | Stack-Based Validity (Pattern 8) |
| "Minimum platforms/rooms for overlapping events" | Sweep Line / Counting (Pattern 9) |
| "Cache eviction, limited capacity" | Greedy Eviction (LRU) |

### Decision Tree

```
Does the problem involve scheduling/intervals?
  ├─ YES → Sort by END time → Interval Scheduling (Pattern 4)
  └─ NO  → Does it involve splitting items / ratios?
              ├─ YES → Ratio-Based Selection (Pattern 2)
              └─ NO  → Does each element depend on BOTH neighbors?
                          ├─ YES → Two-Pass Exchange Argument (Pattern 6)
                          └─ NO  → Is it about reaching an end index?
                                      ├─ YES → Jump/Reachability Greedy (Pattern 5)
                                      └─ NO  → Does it involve deadlines + profit?
                                                  ├─ YES → Deadline Scheduling (Pattern 7)
                                                  └─ NO  → Is it bracket/structure validity?
                                                              ├─ YES → Stack-Based Validity (Pattern 8)
                                                              └─ NO  → Simulate the process directly (Pattern 3)
```

---

## Edge Cases Reference

Cross-cutting edge cases worth memorizing across greedy problems:

| Situation | What to check |
|---|---|
| Empty input array | Return the identity result (0, true, empty list) before any processing |
| Single element | Many greedy problems trivially return a base case — verify your loop handles `n=1` without index errors |
| All elements identical | Greedy comparisons (`>`, `<`) should degrade gracefully to "no action needed" |
| Sorting key choice | Double-check whether you need ascending or descending, and whether you're sorting by start, end, ratio, or profit — this is the #1 source of greedy bugs |
| Boundary equality (`==` vs `<` / `<=`) | Interval problems are especially sensitive to whether touching endpoints count as overlapping |
| Integer overflow | Profit/cost accumulation problems (Job Sequencing, Connect Sticks-style) should use `long long` if values are large |
| Greedy choice requiring proof | If you can't articulate *why* the locally optimal choice is globally safe (exchange argument), the problem may actually need DP |
| Two-pass problems collapsed into one pass | Don't try to force Candy-style two-directional constraints into a single pass — it will silently produce wrong answers |
| Negative numbers / zero values | Jump Game and Fractional Knapsack must handle 0-step jumps and 0-weight items without crashing |

---

> **Final thought:** Greedy problems fail silently — wrong greedy choices often still produce *a* valid-looking answer, just not the optimal one. Always verify your greedy choice with a quick exchange-argument sanity check before trusting it on an interview whiteboard.

---

*Happy Greedy-ing! 🚀*