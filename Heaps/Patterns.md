# 🏔️ DSA Patterns: Heaps / Priority Queue

> The trick isn't memorizing 15 heap solutions. Almost every heap problem falls into **6 core patterns**. Interviewers keep repainting the same heap and giving it a new job title.

---

## 📖 Table of Contents

1. [Heap Fundamentals](#heap-fundamentals)
2. [Pattern 1: Top-K Elements](#pattern-1-top-k-elements)
3. [Pattern 2: K-way Merge](#pattern-2-k-way-merge)
4. [Pattern 3: Dynamic Median (Two Heaps)](#pattern-3-dynamic-median-two-heaps)
5. [Pattern 4: Greedy Merge](#pattern-4-greedy-merge)
6. [Pattern 5: Best-First Search on Sorted Matrix/Grid](#pattern-5-best-first-search-on-sorted-matrixgrid)
7. [Pattern 6: Frequency Heap](#pattern-6-frequency-heap)
8. [Special Case: Task Scheduler](#special-case-task-scheduler)
9. [Special Case: Design Twitter](#special-case-design-twitter)
10. [Special Case: Hand of Straights](#special-case-hand-of-straights-not-really-a-heap-problem)
11. [Master Cheat Sheet](#master-cheat-sheet)
12. [Interview Recognition Guide](#interview-recognition-guide)
13. [Edge Cases Reference](#edge-cases-reference)

---

## Heap Fundamentals

A heap is a tree-based structure that keeps the smallest (or largest) element accessible in O(1), with O(log n) insertion and removal. In C++, `priority_queue` is a binary heap under the hood.

### Min Heap

```cpp
priority_queue<int, vector<int>, greater<int>> pq;
```

Top = smallest element. Use when you repeatedly need the smallest remaining value.

### Max Heap

```cpp
priority_queue<int> pq;   // default behavior
```

Top = largest element. Use when you repeatedly need the largest remaining value.

### Custom Comparator (for pairs/structs)

```cpp
// Min heap of pairs, ordered by first element
priority_queue<pair<int,int>, vector<pair<int,int>>, greater<pair<int,int>>> pq;

// Custom comparator lambda
auto cmp = [](pair<int,int>& a, pair<int,int>& b) {
    return a.first > b.first;   // smaller first = higher priority
};
priority_queue<pair<int,int>, vector<pair<int,int>>, decltype(cmp)> pq(cmp);
```

### Complexities

| Operation     | Complexity |
|---------------|------------|
| push          | O(log n)   |
| pop           | O(log n)   |
| top           | O(1)       |
| build heap    | O(n)       |
| heapify (one element) | O(log n) |

### The One Question That Tells You It's a Heap Problem

> **"Do I repeatedly need the smallest element, the largest element, or the next best candidate while the data keeps changing?"**

If yes — a heap is lurking nearby.

---

## Pattern 1: Top-K Elements

> **Core Idea:** Maintain a heap of size exactly `K`. To find the K largest elements, paradoxically use a **Min Heap** of size K — the smallest of the K largest sits at the top, ready to be evicted the moment a bigger element arrives. The reverse applies for K smallest.

### Recognition

The question asks for:
```
Largest K
Smallest K
Kth largest
Kth smallest
Top K
```

---

### Kth Largest Element

**Use a Min Heap of size K.** The heap holds the K largest elements seen so far; its top is the smallest among them — which is exactly the Kth largest overall.

```cpp
int findKthLargest(vector<int>& nums, int k) {
    priority_queue<int, vector<int>, greater<int>> minHeap;

    for (int x : nums) {
        minHeap.push(x);
        if (minHeap.size() > k)
            minHeap.pop();   // evict the smallest, keeping only top K
    }
    return minHeap.top();
}
```

**Why this works:** After processing all elements, the heap contains exactly the K largest values. The smallest among them (`top()`) is the Kth largest.

**Complexity:** O(n log k) time, O(k) space.

---

### Kth Smallest Element

**Use a Max Heap of size K.** Symmetric logic — the heap holds the K smallest elements, and its top (the largest among them) is the Kth smallest.

```cpp
int findKthSmallest(vector<int>& nums, int k) {
    priority_queue<int> maxHeap;   // default max heap

    for (int x : nums) {
        maxHeap.push(x);
        if (maxHeap.size() > k)
            maxHeap.pop();
    }
    return maxHeap.top();
}
```

**Complexity:** O(n log k) time, O(k) space.

---

### Kth Largest Element in a Stream (Design Variant)

```cpp
class KthLargest {
    priority_queue<int, vector<int>, greater<int>> minHeap;
    int k;
public:
    KthLargest(int k, vector<int>& nums) : k(k) {
        for (int x : nums) add(x);
    }

    int add(int val) {
        minHeap.push(val);
        if (minHeap.size() > k)
            minHeap.pop();
        return minHeap.top();
    }
};
```

**Complexity:** O(log k) per insertion.

---

### Top K Frequent Elements (Top-K + Frequency hybrid)

```cpp
vector<int> topKFrequent(vector<int>& nums, int k) {
    unordered_map<int, int> freq;
    for (int x : nums) freq[x]++;

    priority_queue<pair<int,int>, vector<pair<int,int>>, greater<>> minHeap;
    for (auto& [val, count] : freq) {
        minHeap.push({count, val});
        if (minHeap.size() > k) minHeap.pop();
    }

    vector<int> result;
    while (!minHeap.empty()) {
        result.push_back(minHeap.top().second);
        minHeap.pop();
    }
    return result;
}
```

**Complexity:** O(n log k) time, O(n) space for the frequency map.

---

### Edge Cases

| Case | Behavior to verify |
|---|---|
| `k = 1` | Should just return the max (or min) of the array |
| `k = n` | Heap grows to hold the entire array |
| Duplicate values | Heap must still pop/push correctly — duplicates are independent entries |
| Negative values | Comparator must handle negatives normally (no special casing needed) |
| `k > n` | Invalid input — should be validated by caller or guarded explicitly |

---

## Pattern 2: K-way Merge

> **Core Idea:** When merging K sorted structures (lists, arrays), put the "current smallest candidate" from each structure into a heap. Pop the global smallest, then push the next element from whichever structure it came from. This always produces the next smallest in sorted order.

### Recognition

```
Multiple sorted structures
Merge them into one
Find global minimum/maximum across structures
```

---

### Merge K Sorted Lists

```cpp
struct ListNode { int val; ListNode* next; };

struct Compare {
    bool operator()(ListNode* a, ListNode* b) {
        return a->val > b->val;   // min heap by node value
    }
};

ListNode* mergeKLists(vector<ListNode*>& lists) {
    priority_queue<ListNode*, vector<ListNode*>, Compare> minHeap;

    for (ListNode* node : lists)
        if (node) minHeap.push(node);

    ListNode dummy;
    ListNode* tail = &dummy;

    while (!minHeap.empty()) {
        ListNode* smallest = minHeap.top();
        minHeap.pop();

        tail->next = smallest;
        tail = tail->next;

        if (smallest->next)
            minHeap.push(smallest->next);   // push the list's next element
    }
    return dummy.next;
}
```

**Complexity:** For k lists totaling n elements → O(n log k) time, O(k) space.

---

### Sort a K-Sorted (Nearly Sorted) Array

Each element is at most `k` positions away from its sorted position.

```cpp
vector<int> sortKSortedArray(vector<int>& arr, int k) {
    priority_queue<int, vector<int>, greater<int>> minHeap;
    vector<int> result;

    for (int i = 0; i < arr.size(); i++) {
        minHeap.push(arr[i]);
        if (minHeap.size() > k + 1) {
            result.push_back(minHeap.top());
            minHeap.pop();
        }
    }
    while (!minHeap.empty()) {
        result.push_back(minHeap.top());
        minHeap.pop();
    }
    return result;
}
```

**Why heap size = k+1?** At any point, the correct next sorted element is guaranteed to be among the last `k+1` elements seen.

**Complexity:** O(n log k) time, O(k) space.

---

### Edge Cases

| Case | Behavior to verify |
|---|---|
| Empty lists in the input | Skip nulls before pushing to the heap |
| Only one list | Heap degenerates to a simple pass-through; still correct |
| Lists of very different lengths | Shorter lists empty out and stop contributing — no special handling needed |
| All lists empty | Return null / empty result immediately |

---

## Pattern 3: Dynamic Median (Two Heaps)

> **Core Idea:** Split the data stream into two halves using two heaps — a **Max Heap** for the smaller half and a **Min Heap** for the larger half. Keep both heaps balanced in size (difference ≤ 1). The median is then accessible in O(1) from the tops.

### Recognition

```
Stream of numbers
Need the median after every insertion
```

### Invariants to Maintain

```
every element in maxHeap (smaller half) <= every element in minHeap (larger half)
|size(maxHeap) - size(minHeap)| <= 1
```

```cpp
class MedianFinder {
    priority_queue<int> maxHeap;                              // smaller half
    priority_queue<int, vector<int>, greater<int>> minHeap;   // larger half

public:
    void addNum(int num) {
        // Step 1: Add to the correct heap
        if (maxHeap.empty() || num <= maxHeap.top())
            maxHeap.push(num);
        else
            minHeap.push(num);

        // Step 2: Rebalance — maxHeap can have at most 1 more than minHeap
        if (maxHeap.size() > minHeap.size() + 1) {
            minHeap.push(maxHeap.top());
            maxHeap.pop();
        } else if (minHeap.size() > maxHeap.size()) {
            maxHeap.push(minHeap.top());
            minHeap.pop();
        }
    }

    double findMedian() {
        if (maxHeap.size() > minHeap.size())
            return maxHeap.top();
        return (maxHeap.top() + minHeap.top()) / 2.0;
    }
};
```

**Visual model:**
```
[ ... smaller half ... ]  |  [ ... larger half ... ]
       maxHeap                    minHeap
      (top = largest               (top = smallest
       of small half)               of large half)
            ↑                           ↑
       these two values straddle the median
```

**Complexity:** `addNum` → O(log n), `findMedian` → O(1).

---

### Edge Cases

| Case | Behavior to verify |
|---|---|
| Single element | `maxHeap` has it, `minHeap` empty — median = `maxHeap.top()` |
| Duplicate values | Heaps handle duplicates naturally; no dedup needed |
| Negative numbers | No special handling — comparators work as-is |
| Even vs odd total count | Even → average of two tops; odd → top of the larger heap |

---

## Pattern 4: Greedy Merge

> **Core Idea:** Repeatedly combine the **two smallest** elements, accumulate the cost, and push the combined result back. A Min Heap always gives you the next two smallest in O(log n) each.

### Recognition

```
Combine things pairwise
Cost depends on the combined value
Minimize total cost
```

---

### Minimum Cost to Connect Sticks

```cpp
int connectSticks(vector<int>& sticks) {
    priority_queue<int, vector<int>, greater<int>> minHeap(sticks.begin(), sticks.end());
    int totalCost = 0;

    while (minHeap.size() > 1) {
        int a = minHeap.top(); minHeap.pop();
        int b = minHeap.top(); minHeap.pop();

        int combined = a + b;
        totalCost += combined;
        minHeap.push(combined);
    }
    return totalCost;
}
```

**Why greedy works:** Combining the two smallest first minimizes how many times large values get "re-added" into future combination costs (this mirrors Huffman's optimality proof).

**Complexity:** O(n log n) time, O(n) space.

---

### Huffman Coding

```cpp
struct Node {
    int freq;
    char ch;
    Node *left = nullptr, *right = nullptr;
};

struct Compare {
    bool operator()(Node* a, Node* b) { return a->freq > b->freq; }
};

Node* buildHuffmanTree(vector<pair<char,int>>& freqs) {
    priority_queue<Node*, vector<Node*>, Compare> minHeap;

    for (auto& [ch, freq] : freqs)
        minHeap.push(new Node{freq, ch});

    while (minHeap.size() > 1) {
        Node* left = minHeap.top(); minHeap.pop();
        Node* right = minHeap.top(); minHeap.pop();

        Node* parent = new Node{left->freq + right->freq, '\0'};
        parent->left = left;
        parent->right = right;
        minHeap.push(parent);
    }
    return minHeap.top();   // root of Huffman tree
}
```

**Complexity:** O(n log n) time, O(n) space.

---

### Edge Cases

| Case | Behavior to verify |
|---|---|
| Only 1 stick/character | No combination needed — cost = 0 |
| All values equal | Greedy still works; cost = sum of intermediate combinations |
| Very large values | Watch for integer overflow — use `long long` if values are large |

---

## Pattern 5: Best-First Search on Sorted Matrix/Grid

> **Core Idea:** When searching for top-K combinations across two (or more) **sorted** arrays/rows, a heap lets you explore only the most promising candidates instead of generating all combinations. Push the starting candidate, then push its "neighbors" (next index in each dimension) only after popping it — this prunes the search to O(k log k).

### Recognition

```
Top K combinations / pair sums
Sorted rows and columns
"K smallest pairs", "K largest sums"
```

---

### Visualizing the Matrix of Combinations

For `K Smallest Pairs` with `a = [1,7,11]`, `b = [2,4,6]`, sums form an implicit matrix:

```
     b=2   b=4   b=6
a=1   3     5     7
a=7   9    11    13
a=11 13    15    17
```

You never build this matrix — the heap explores it lazily.

---

### K Smallest Pairs

```cpp
vector<vector<int>> kSmallestPairs(vector<int>& nums1, vector<int>& nums2, int k) {
    auto cmp = [&](pair<int,int>& a, pair<int,int>& b) {
        return nums1[a.first] + nums2[a.second] > nums1[b.first] + nums2[b.second];
    };
    priority_queue<pair<int,int>, vector<pair<int,int>>, decltype(cmp)> minHeap(cmp);

    set<pair<int,int>> visited;

    minHeap.push({0, 0});
    visited.insert({0, 0});

    vector<vector<int>> result;

    while (k-- > 0 && !minHeap.empty()) {
        auto [i, j] = minHeap.top();
        minHeap.pop();
        result.push_back({nums1[i], nums2[j]});

        if (i + 1 < nums1.size() && visited.insert({i + 1, j}).second)
            minHeap.push({i + 1, j});

        if (j + 1 < nums2.size() && visited.insert({i, j + 1}).second)
            minHeap.push({i, j + 1});
    }
    return result;
}
```

**Why this is correct:** Starting from `(0,0)` (smallest possible sum), every pop guarantees the next smallest unexplored sum, because all unexplored candidates are reachable only by incrementing `i` or `j` from an already-popped pair.

**Complexity:** O(k log k) time (each of k pops can push at most 2 new candidates), O(k) space.

---

### Maximum Sum Combination

Same pattern, flipped to a **Max Heap**, starting from the largest elements of each sorted array.

**Complexity:** O(k log k) time, O(k) space.

---

### Edge Cases

| Case | Behavior to verify |
|---|---|
| Duplicate sums | `visited` set must use pair `(i,j)`, not the sum value, to avoid incorrectly skipping valid index pairs |
| `k > total possible pairs` | Loop naturally terminates when heap empties — cap k at `min(k, n*m)` |
| Different array sizes | Bounds checks (`i+1 < size`, `j+1 < size`) handle this automatically |
| One array is empty | Return empty result immediately |

---

## Pattern 6: Frequency Heap

> **Core Idea:** Build a frequency map first, then heapify based on frequency rather than value. The heap answers "what's most/least frequent right now" rather than "what's biggest/smallest."

### Recognition

```
Frequency
Most frequent
Least frequent
```

---

### Step 1: Build Frequency Map

```cpp
unordered_map<int, int> freq;
for (int x : nums) freq[x]++;
```

### Step 2: Heap Stores {frequency, value}

```cpp
priority_queue<pair<int,int>> maxHeap;   // ordered by frequency first
for (auto& [val, count] : freq)
    maxHeap.push({count, val});
```

---

### Top K Frequent Elements (Heap Approach)

```cpp
vector<int> topKFrequent(vector<int>& nums, int k) {
    unordered_map<int, int> freq;
    for (int x : nums) freq[x]++;

    priority_queue<pair<int,int>> maxHeap;   // {frequency, value}
    for (auto& [val, count] : freq)
        maxHeap.push({count, val});

    vector<int> result;
    while (k-- > 0) {
        result.push_back(maxHeap.top().second);
        maxHeap.pop();
    }
    return result;
}
```

**Complexity:** O(n log n) for building + popping the full heap, or O(n log k) if you cap the heap at size k (see Pattern 1's hybrid version).

---

### Edge Cases

| Case | Behavior to verify |
|---|---|
| All elements equally frequent | Any K of them is a valid answer — order may be implementation-defined |
| `k` equals number of distinct elements | Pop until heap is empty |
| Single repeated element | Frequency map has just 1 entry |

---

## Special Case: Task Scheduler

> Uses a Max Heap of frequencies, but the cooldown constraint needs a secondary queue to track "when can this task be reinserted."

### Recognition

```
Cooldown period
Schedule tasks
Most frequent task matters
```

### Idea

```cpp
int leastInterval(vector<char>& tasks, int n) {
    unordered_map<char, int> freq;
    for (char t : tasks) freq[t]++;

    priority_queue<int> maxHeap;
    for (auto& [ch, count] : freq) maxHeap.push(count);

    int time = 0;
    queue<pair<int,int>> cooldown;   // {count, available_time}

    while (!maxHeap.empty() || !cooldown.empty()) {
        time++;

        if (!maxHeap.empty()) {
            int count = maxHeap.top() - 1;
            maxHeap.pop();
            if (count > 0)
                cooldown.push({count, time + n});
        }

        if (!cooldown.empty() && cooldown.front().second == time) {
            maxHeap.push(cooldown.front().first);
            cooldown.pop();
        }
    }
    return time;
}
```

**Complexity:** O(n log 26) — effectively O(n) since the heap never exceeds 26 distinct task types.

### Edge Cases

| Case | Behavior to verify |
|---|---|
| `n = 0` | No cooldown needed — answer = total task count |
| Only one task type | Answer = `count + (count-1) * n` |
| More distinct tasks than the cooldown window | No idle cycles needed |

---

## Special Case: Design Twitter

> A disguised **K-way merge**: each user's tweet list is already sorted by recency; merging followee timelines to get the most recent tweets is structurally identical to Pattern 2.

### Recognition

```
Merge timelines
Most recent tweets across multiple users
```

### Idea

```cpp
class Twitter {
    int timestamp = 0;
    unordered_map<int, vector<pair<int,int>>> tweets;     // userId -> {time, tweetId}
    unordered_map<int, set<int>> following;               // userId -> followee set

public:
    void postTweet(int userId, int tweetId) {
        tweets[userId].push_back({timestamp++, tweetId});
    }

    vector<int> getNewsFeed(int userId) {
        priority_queue<tuple<int,int,int>> maxHeap;   // {time, tweetId, userId} — implicit pointer via index

        auto consider = [&](int uid) {
            if (!tweets[uid].empty())
                maxHeap.push({tweets[uid].back().first, tweets[uid].back().second, uid});
        };

        consider(userId);
        for (int followee : following[userId]) consider(followee);

        vector<int> result;
        unordered_map<int, int> idx;   // track position per user

        while (!maxHeap.empty() && result.size() < 10) {
            auto [time, tweetId, uid] = maxHeap.top();
            maxHeap.pop();
            result.push_back(tweetId);

            int nextIdx = tweets[uid].size() - 2 - idx[uid];
            idx[uid]++;
            if (nextIdx >= 0)
                maxHeap.push({tweets[uid][nextIdx].first, tweets[uid][nextIdx].second, uid});
        }
        return result;
    }

    void follow(int followerId, int followeeId) { following[followerId].insert(followeeId); }
    void unfollow(int followerId, int followeeId) { following[followerId].erase(followeeId); }
};
```

**Complexity:** O((F + 10) log F) per feed request, where F = number of followees.

### Edge Cases

| Case | Behavior to verify |
|---|---|
| User follows nobody | Feed = only their own tweets |
| Fewer than 10 total tweets | Return all available tweets |
| Unfollow then refetch feed | Followee's tweets must disappear from future feeds |

---

## Special Case: Hand of Straights (Not Really a Heap Problem)

> Many people force a heap here, but the real solution is frequency counting + greedy consumption. A heap (or sorted map) helps find the smallest remaining card quickly, but it isn't the core insight.

### Recognition

```
Group cards into consecutive sequences
```

### Idea

```cpp
bool isNStraightHand(vector<int>& hand, int groupSize) {
    map<int, int> freq;   // sorted map gives smallest-first iteration
    for (int card : hand) freq[card]++;

    for (auto it = freq.begin(); it != freq.end(); it = freq.begin()) {
        if (it->second == 0) { freq.erase(it); continue; }

        int start = it->first;
        int count = it->second;

        for (int i = 0; i < groupSize; i++) {
            if (freq[start + i] < count) return false;
            freq[start + i] -= count;
            if (freq[start + i] == 0) freq.erase(start + i);
        }
    }
    return true;
}
```

**Why a heap isn't the real pattern:** The core operation is "find smallest remaining card and consume a run starting there" — a sorted map (`std::map`) already gives ordered iteration; a heap only adds the same benefit with more bookkeeping for decrement-and-reinsert.

### Edge Cases

| Case | Behavior to verify |
|---|---|
| `hand.size() % groupSize != 0` | Immediately return false |
| `groupSize = 1` | Always true (every card is its own group) |
| Duplicate cards | Frequency map naturally handles this |

---

## Master Cheat Sheet

| Pattern | Problems | Heap Type |
|---------|----------|-----------|
| Top-K Elements | Kth Largest, Kth Smallest, Kth Largest in Stream | Min Heap (for largest) / Max Heap (for smallest) |
| Frequency Heap | Top K Frequent Elements | Min or Max Heap on `{freq, value}` |
| K-way Merge | Merge K Sorted Lists, Sort K-Sorted Array | Min Heap |
| Dynamic Median | Find Median From Data Stream | Two Heaps (Max + Min) |
| Greedy Merge | Connect Sticks, Huffman Coding | Min Heap |
| Best-First Search | K Smallest Pairs, Maximum Sum Combination | Min Heap (smallest) / Max Heap (largest) + visited set |
| Task Scheduler (special) | Task Scheduler | Max Heap + cooldown queue |
| Design Twitter (K-way merge) | Design Twitter | Max Heap + K-way merge |
| Hand of Straights (not a heap) | Hand of Straights | Sorted Map + greedy |

---

## Interview Recognition Guide

| Problem Clue | Pattern |
|---|---|
| "Kth largest / smallest" | Top-K Elements (Pattern 1) |
| "Top K frequent" | Frequency Heap (Pattern 6) |
| "Merge K sorted lists/arrays" | K-way Merge (Pattern 2) |
| "Median of a stream" | Dynamic Median (Pattern 3) |
| "Minimum cost to combine/connect" | Greedy Merge (Pattern 4) |
| "Huffman / optimal merge cost" | Greedy Merge (Pattern 4) |
| "K smallest/largest pair sums from sorted arrays" | Best-First Search (Pattern 5) |
| "Cooldown between repeated tasks" | Task Scheduler (Special Case) |
| "Most recent posts across followees" | Design Twitter (K-way Merge variant) |
| "Group into consecutive runs" | NOT a heap — use sorted map + greedy |

### Decision Tree

```
Do you need a single Kth value?
  ├─ YES → Top-K Elements (Pattern 1)
  └─ NO  → Are you merging multiple sorted sources?
              ├─ YES → K-way Merge (Pattern 2)
              └─ NO  → Do you need a running median?
                          ├─ YES → Two Heaps (Pattern 3)
                          └─ NO  → Are you combining pairs to minimize cost?
                                      ├─ YES → Greedy Merge (Pattern 4)
                                      └─ NO  → Are you searching sorted rows/cols for top-K combos?
                                                  ├─ YES → Best-First Search (Pattern 5)
                                                  └─ NO  → Is it about frequency ranking?
                                                              ├─ YES → Frequency Heap (Pattern 6)
                                                              └─ NO  → Probably not a heap problem
```

---

## Edge Cases Reference

Cross-cutting edge cases that apply across multiple heap patterns:

| Situation | What to check |
|---|---|
| `k = 1` | Should degrade to a simple max/min computation |
| `k = n` or `k > n` | Validate bounds; heap may grow to cover the entire input |
| Duplicate values | Heaps handle duplicates as independent entries — no dedup logic needed unless the problem says otherwise |
| Negative numbers | No special handling required; comparators work uniformly |
| Empty input | Return early before any heap operations |
| Overflow on combined sums (Greedy Merge, Huffman) | Use `long long` for cumulative cost |
| Visited set in grid/matrix search | Must key on **index pairs**, not values, to correctly avoid duplicate pushes when sums repeat |
| Heap comparator direction | Double-check `greater<>` vs default `less<>` — this is the #1 source of "my heap returns the wrong order" bugs |
| Two-heap balancing (Median) | Always rebalance immediately after insertion, every single time, or the invariant breaks silently |

---

> **Final thought:** Once you internalize these 6 patterns, almost every heap problem becomes "which of these 6 templates am I filling in" rather than a fresh algorithm. The special cases (Task Scheduler, Twitter) are usually just Pattern 1 or Pattern 2 wearing a disguise — and Hand of Straights isn't even really a heap problem, no matter how many people insist otherwise.

---

*Happy Heaping! 🚀*