# 📚 DSA Patterns: Stacks & Queues

> Almost every stack/queue interview problem comes from **5–6 recurring patterns**. Learn the pattern once — and 20 problems collapse into 5. This doc maps every problem to the pattern underneath it.

---

## 📖 Table of Contents

1. [Big Picture](#big-picture)
2. [Stack Patterns](#stack-patterns)
   - [Pattern 1: LIFO Simulation & Basic Implementation](#pattern-1-lifo-simulation--basic-implementation)
   - [Pattern 2: Data Structure Transformation](#pattern-2-data-structure-transformation)
   - [Pattern 3: Parentheses Matching](#pattern-3-parentheses-matching)
   - [Pattern 4: Expression Conversion](#pattern-4-expression-conversion)
   - [Pattern 5: Special Stack (Min / Max Stack)](#pattern-5-special-stack-min--max-stack)
   - [Pattern 6: Monotonic Stack](#pattern-6-monotonic-stack-)
   - [Pattern 7: Stock Span — Previous Greater Element](#pattern-7-stock-span--previous-greater-element)
   - [Pattern 8: Histogram — Nearest Smaller on Both Sides](#pattern-8-histogram--nearest-smaller-on-both-sides)
   - [Pattern 9: Contribution Technique](#pattern-9-contribution-technique)
   - [Pattern 10: Greedy Stack — Remove K Digits](#pattern-10-greedy-stack--remove-k-digits)
   - [Pattern 11: Collision Simulation — Asteroid Collision](#pattern-11-collision-simulation--asteroid-collision)
   - [Pattern 12: Trapping Rain Water (Stack Version)](#pattern-12-trapping-rain-water-stack-version)
3. [Queue Patterns](#queue-patterns)
   - [Pattern 13: FIFO Simulation & Circular Queue](#pattern-13-fifo-simulation--circular-queue)
   - [Pattern 14: Monotonic Queue — Sliding Window Maximum](#pattern-14-monotonic-queue--sliding-window-maximum)
   - [Pattern 15: Celebrity Problem — Elimination Stack/Queue](#pattern-15-celebrity-problem--elimination-stackqueue)
   - [Pattern 16: LRU Cache — HashMap + DLL](#pattern-16-lru-cache--hashmap--dll)
   - [Pattern 17: LFU Cache — HashMap + DLL + Frequency Map](#pattern-17-lfu-cache--hashmap--dll--frequency-map)
4. [Monotonic Stack Cheat Sheet](#monotonic-stack-cheat-sheet)
5. [Interview Recognition Guide](#interview-recognition-guide)
6. [Recommended Study Order](#recommended-study-order)

---

## Big Picture

```
STACK PATTERNS                          QUEUE PATTERNS
──────────────────────────────────      ──────────────────────────────
1. LIFO Simulation                      1. FIFO Simulation
   └─ Implement Stack                      └─ Implement Queue
   └─ Balanced Parentheses                 └─ Queue using Stack
   └─ Infix/Postfix/Prefix

2. Monotonic Stack                      2. Monotonic Queue
   └─ Next Greater / Smaller               └─ Sliding Window Maximum
   └─ Stock Span
   └─ Histogram
   └─ Sum of Subarray Minimums         3. HashMap + DLL + Queue Logic
   └─ Trapping Rain Water                  └─ LRU Cache
                                           └─ LFU Cache
3. Greedy Removal
   └─ Remove K Digits
   └─ Asteroid Collision

4. Special Stack
   └─ Min Stack
```

---

## Stack Patterns

---

### Pattern 1: LIFO Simulation & Basic Implementation

> **Core Idea:** The stack's Last-In-First-Out property directly models any problem where you need to "undo" the most recent action or match the latest open element.

**Use when you see:**
- Undo / redo operations
- Browser history navigation
- Parentheses / bracket matching
- Expression parsing

---

#### Stack using Array

```cpp
class Stack {
    int arr[1000], top = -1;
public:
    void push(int x) { arr[++top] = x; }
    void pop()       { if (!empty()) top--; }
    int  peek()      { return arr[top]; }
    bool empty()     { return top == -1; }
};
```

| Operation | Time |
|-----------|------|
| push      | O(1) |
| pop       | O(1) |
| top/peek  | O(1) |
| empty     | O(1) |

---

#### Stack using Linked List

```cpp
struct Node { int data; Node* next; };

class Stack {
    Node* head = nullptr;
public:
    void push(int x) {
        Node* node = new Node{x, head};
        head = node;
    }
    void pop() {
        if (head) { Node* tmp = head; head = head->next; delete tmp; }
    }
    int  top()   { return head->data; }
    bool empty() { return head == nullptr; }
};
```

**Complexity:** O(1) for all operations, O(n) space.

---

### Pattern 2: Data Structure Transformation

> **Core Idea:** Implement one data structure using another. The key insight is understanding how the ordering properties (LIFO vs FIFO) differ — then use the source structure's operations to simulate the target's order.

---

#### Stack using Two Queues

**Trick:** After each push, rotate the queue so the new element ends up at the front.

```cpp
class MyStack {
    queue<int> q;
public:
    void push(int x) {
        q.push(x);
        for (int i = 0; i < (int)q.size() - 1; i++) {
            q.push(q.front());
            q.pop();
        }
    }
    int  pop()   { int x = q.front(); q.pop(); return x; }
    int  top()   { return q.front(); }
    bool empty() { return q.empty(); }
};
```

| Operation | Time |
|-----------|------|
| push      | O(n) |
| pop       | O(1) |
| top       | O(1) |

---

#### Queue using Two Stacks

**Trick:** Use an input stack and an output stack. Transfer elements lazily — only when the output stack is empty.

```cpp
class MyQueue {
    stack<int> in, out;
    void transfer() {
        if (out.empty())
            while (!in.empty()) { out.push(in.top()); in.pop(); }
    }
public:
    void push(int x) { in.push(x); }
    int  pop()   { transfer(); int x = out.top(); out.pop(); return x; }
    int  peek()  { transfer(); return out.top(); }
    bool empty() { return in.empty() && out.empty(); }
};
```

**Why amortized O(1)?** Every element is moved at most once — from `in` to `out`. Total moves across all operations = O(n).

| Operation | Amortized Time |
|-----------|----------------|
| push      | O(1)           |
| pop       | O(1)           |
| peek      | O(1)           |

---

### Pattern 3: Parentheses Matching

> **Core Idea:** A closing bracket must match the **most recently seen** unmatched opening bracket. "Most recently seen" is exactly what a stack gives you — the top holds the latest unmatched opener.

```cpp
bool isValid(string s) {
    stack<char> st;
    for (char c : s) {
        if (c == '(' || c == '[' || c == '{') {
            st.push(c);
        } else {
            if (st.empty()) return false;
            char top = st.top(); st.pop();
            if (c == ')' && top != '(') return false;
            if (c == ']' && top != '[') return false;
            if (c == '}' && top != '{') return false;
        }
    }
    return st.empty();
}
```

**Complexity:** O(n) time, O(n) space.

**Extension problems using this pattern:**
- Minimum additions to make brackets valid
- Longest valid parentheses substring
- Score of parentheses

---

### Pattern 4: Expression Conversion

> **Core Idea:** Operators go into the stack (for precedence resolution); operands go directly to the output. When a lower-precedence operator arrives, pop higher-precedence operators off the stack first.

**Three forms of expressions:**

| Form    | Example     | Notes |
|---------|-------------|-------|
| Infix   | `a + b * c` | Human-readable; needs precedence rules |
| Postfix | `a b c * +` | No parentheses needed; easy to evaluate |
| Prefix  | `+ a * b c` | Evaluated right to left |

---

#### Operator Precedence

```
^            →   3   (highest, right-associative)
* /          →   2
+ -          →   1   (lowest)
```

---

#### Infix → Postfix

```cpp
string infixToPostfix(string s) {
    stack<char> st;
    string result;
    auto prec = [](char c) {
        if (c == '^') return 3;
        if (c == '*' || c == '/') return 2;
        if (c == '+' || c == '-') return 1;
        return 0;
    };

    for (char c : s) {
        if (isalnum(c)) {
            result += c;
        } else if (c == '(') {
            st.push(c);
        } else if (c == ')') {
            while (st.top() != '(') { result += st.top(); st.pop(); }
            st.pop();
        } else {
            while (!st.empty() && prec(st.top()) >= prec(c))
                { result += st.top(); st.pop(); }
            st.push(c);
        }
    }
    while (!st.empty()) { result += st.top(); st.pop(); }
    return result;
}
```

**Complexity:** O(n) for all conversion variants.

**Interview tip:** Prefix conversion = reverse input → Infix to Postfix → reverse output.

---

### Pattern 5: Special Stack (Min / Max Stack)

> **Core Idea:** Augment each stack entry with extra metadata — the minimum (or maximum) seen so far up to that position. The top always knows the current answer in O(1).

**Problem:** Design a stack that supports `getMin()` in O(1).

```cpp
class MinStack {
    stack<pair<int,int>> st;  // {value, min_so_far}
public:
    void push(int x) {
        int curMin = st.empty() ? x : min(x, st.top().second);
        st.push({x, curMin});
    }
    void pop()    { st.pop(); }
    int  top()    { return st.top().first; }
    int  getMin() { return st.top().second; }
};
```

**Trace:**
```
push(5)  →  stack: [(5,5)]          min=5
push(2)  →  stack: [(5,5),(2,2)]    min=2
push(8)  →  stack: [(5,5),(2,2),(8,2)]  min=2
push(1)  →  stack: [...,(1,1)]      min=1
pop()    →  stack: [...,(8,2)]      min=2  ← restored automatically
```

| Operation | Time |
|-----------|------|
| push      | O(1) |
| pop       | O(1) |
| getMin    | O(1) |

---

### Pattern 6: Monotonic Stack ⭐

> **Core Idea:** Maintain a stack that is always sorted (increasing or decreasing). When a new element violates the order, pop elements — each popped element has found its "answer" (the current element is its next greater/smaller). Every element is pushed and popped at most once → O(n).

**This is the most important stack pattern. Recognize it by these keywords:**

```
Next Greater Element       →  Decreasing Stack
Next Smaller Element       →  Increasing Stack
Previous Greater Element   →  Decreasing Stack (traverse left→right)
Previous Smaller Element   →  Increasing Stack (traverse left→right)
```

---

#### Next Greater Element (NGE)

```
Input:   2  1  5  3  6
NGE:     5  5  6  6  -1
```

```cpp
vector<int> nextGreater(vector<int>& arr) {
    int n = arr.size();
    vector<int> result(n, -1);
    stack<int> st;  // stores indices

    for (int i = 0; i < n; i++) {
        // Pop all elements smaller than arr[i] — arr[i] is their NGE
        while (!st.empty() && arr[st.top()] < arr[i]) {
            result[st.top()] = arr[i];
            st.pop();
        }
        st.push(i);
    }
    return result;
}
```

**Why O(n)?** Each element enters the stack once and leaves once. Total operations = 2n = O(n).

---

#### Next Smaller Element (NSE)

```cpp
// Flip the comparison: pop when arr[st.top()] > arr[i]
while (!st.empty() && arr[st.top()] > arr[i]) { ... }
```

---

#### Previous Greater / Smaller Element

Traverse right → left (or maintain the answer while pushing left → right):

```cpp
// Previous Greater: traverse right to left, decreasing stack
for (int i = n - 1; i >= 0; i--) {
    while (!st.empty() && arr[st.top()] <= arr[i]) st.pop();
    result[i] = st.empty() ? -1 : arr[st.top()];
    st.push(i);
}
```

---

### Pattern 7: Stock Span — Previous Greater Element

> **Core Idea:** The span of a stock price on day `i` = number of consecutive days (including today) where the price was ≤ today's price. This is exactly the distance to the **Previous Greater Element**.

```
Prices:  100  80  60  70  60  75  85
Span:      1   1   1   2   1   4   6
```

```cpp
vector<int> stockSpan(vector<int>& prices) {
    int n = prices.size();
    vector<int> span(n);
    stack<int> st;  // stores indices

    for (int i = 0; i < n; i++) {
        while (!st.empty() && prices[st.top()] <= prices[i])
            st.pop();
        span[i] = st.empty() ? i + 1 : i - st.top();
        st.push(i);
    }
    return span;
}
```

**Key insight:** `span[i] = i - index_of_previous_greater`. If no previous greater exists, span = `i + 1` (the entire prefix).

**Complexity:** O(n) time, O(n) space.

---

### Pattern 8: Histogram — Nearest Smaller on Both Sides

> **Core Idea:** For each bar, find how far left and right it can extend (until a shorter bar stops it). The nearest smaller bar on each side defines the width. Use two monotonic stack passes (or one combined pass) to find both boundaries.

**Problem:** Largest Rectangle in Histogram.

```
Heights: [2, 1, 5, 6, 2, 3]
Answer:  10  (bars at index 2 and 3, height 5, width 2)
```

```cpp
int largestRectangle(vector<int>& heights) {
    int n = heights.size();
    vector<int> left(n), right(n);
    stack<int> st;

    // Nearest Smaller to the Left
    for (int i = 0; i < n; i++) {
        while (!st.empty() && heights[st.top()] >= heights[i]) st.pop();
        left[i] = st.empty() ? -1 : st.top();
        st.push(i);
    }

    while (!st.empty()) st.pop();

    // Nearest Smaller to the Right
    for (int i = n - 1; i >= 0; i--) {
        while (!st.empty() && heights[st.top()] >= heights[i]) st.pop();
        right[i] = st.empty() ? n : st.top();
        st.push(i);
    }

    int maxArea = 0;
    for (int i = 0; i < n; i++) {
        int width  = right[i] - left[i] - 1;
        int area   = heights[i] * width;
        maxArea    = max(maxArea, area);
    }
    return maxArea;
}
```

**Formula:**
```
width[i] = right_smaller[i] - left_smaller[i] - 1
area[i]  = heights[i] × width[i]
```

**Extension:** Maximal Rectangle in Binary Matrix → convert each row into a histogram → apply this pattern per row.

**Complexity:** O(n) time, O(n) space.

---

### Pattern 9: Contribution Technique

> **Core Idea:** Instead of iterating over all subarrays, ask: *"For how many subarrays is element `arr[i]` the minimum (or maximum)?"*. The count = `left_choices × right_choices`. Monotonic stack finds both boundaries in O(n).

---

#### Sum of Subarray Minimums

```cpp
long long sumSubarrayMins(vector<int>& arr) {
    int n = arr.size();
    const int MOD = 1e9 + 7;
    vector<long long> left(n), right(n);
    stack<int> st;

    // Previous Smaller or Equal (left boundary)
    for (int i = 0; i < n; i++) {
        while (!st.empty() && arr[st.top()] >= arr[i]) st.pop();
        left[i] = st.empty() ? i + 1 : i - st.top();
        st.push(i);
    }

    while (!st.empty()) st.pop();

    // Next Smaller (right boundary)
    for (int i = n - 1; i >= 0; i--) {
        while (!st.empty() && arr[st.top()] > arr[i]) st.pop();
        right[i] = st.empty() ? n - i : st.top() - i;
        st.push(i);
    }

    long long ans = 0;
    for (int i = 0; i < n; i++)
        ans = (ans + arr[i] * left[i] % MOD * right[i] % MOD) % MOD;

    return ans;
}
```

**Intuition:**
```
For arr[i] to be the minimum of a subarray:
  - Left boundary can be any of `left[i]` positions
  - Right boundary can be any of `right[i]` positions
  - Total subarrays where arr[i] is min = left[i] × right[i]
  - Contribution = arr[i] × left[i] × right[i]
```

---

#### Sum of Subarray Ranges

```
Range of subarray = max - min
Sum of all ranges = Σ(max of each subarray) - Σ(min of each subarray)
```

Apply the contribution technique twice — once with a decreasing stack (for max), once with an increasing stack (for min).

**Complexity:** O(n) for both problems.

---

### Pattern 10: Greedy Stack — Remove K Digits

> **Core Idea:** To make the smallest number, greedily remove digits that are larger than the digit after them. A monotonic increasing stack maintains only digits worth keeping.

```
Input:  1 4 3 2 2 1 9,  k = 3
Output: 1 2 2 1 9  →  "12219"
```

```cpp
string removeKDigits(string num, int k) {
    stack<char> st;

    for (char c : num) {
        while (k > 0 && !st.empty() && st.top() > c) {
            st.pop();
            k--;
        }
        st.push(c);
    }

    // Remove remaining from the back
    while (k-- > 0) st.pop();

    // Build result
    string result;
    while (!st.empty()) { result += st.top(); st.pop(); }
    reverse(result.begin(), result.end());

    // Remove leading zeros
    int start = result.find_first_not_of('0');
    return (start == string::npos) ? "0" : result.substr(start);
}
```

**Why greedy works:** A larger digit in a higher position always makes the number bigger. Removing it in favor of a smaller digit behind it is always optimal.

**Complexity:** O(n) time, O(n) space.

---

### Pattern 11: Collision Simulation — Asteroid Collision

> **Core Idea:** The stack represents asteroids currently "alive" and moving. Collisions only happen between a positive (right-moving) asteroid on the stack top and an incoming negative (left-moving) asteroid. Simulate each interaction directly.

```
Input:  [5, 10, -5]
Output: [5, 10]   ← -5 destroyed by 10
```

```cpp
vector<int> asteroidCollision(vector<int>& asteroids) {
    stack<int> st;

    for (int a : asteroids) {
        bool alive = true;
        while (alive && a < 0 && !st.empty() && st.top() > 0) {
            if      (st.top() < -a)  { st.pop(); }           // stack asteroid destroyed
            else if (st.top() == -a) { st.pop(); alive = false; }  // both destroyed
            else                     { alive = false; }       // incoming destroyed
        }
        if (alive) st.push(a);
    }

    vector<int> result;
    while (!st.empty()) { result.push_back(st.top()); st.pop(); }
    reverse(result.begin(), result.end());
    return result;
}
```

**Collision rules summary:**
```
(+, +)  →  no collision, both move right
(-, -)  →  no collision, both move left
(-, +)  →  no collision, moving away from each other
(+, -)  →  COLLISION → larger absolute value survives
```

**Complexity:** O(n) — each asteroid is pushed and popped at most once.

---

### Pattern 12: Trapping Rain Water (Stack Version)

> **Core Idea:** Use the stack to find left and right boundaries for each "valley". When a taller bar arrives, the bar at the stack top is a valley floor — compute water trapped between the left boundary (new stack top after popping) and the current bar.

```
Heights: [0,1,0,2,1,0,1,3,2,1,2,1]
Water:   6 units
```

```cpp
int trapRainWater(vector<int>& height) {
    stack<int> st;
    int water = 0;

    for (int i = 0; i < height.size(); i++) {
        while (!st.empty() && height[i] > height[st.top()]) {
            int bottom = st.top(); st.pop();
            if (st.empty()) break;

            int width      = i - st.top() - 1;
            int boundedH   = min(height[i], height[st.top()]) - height[bottom];
            water         += width * boundedH;
        }
        st.push(i);
    }
    return water;
}
```

**Other approaches:** Two-pointer O(1) space is often preferred in interviews, but the stack version is a natural application of the boundary-finding pattern.

**Complexity:** O(n) time, O(n) space.

---

## Queue Patterns

---

### Pattern 13: FIFO Simulation & Circular Queue

> **Core Idea:** A naive array-based queue wastes space as the front pointer advances. A circular queue reuses freed space by wrapping the rear pointer using modulo arithmetic.

#### Circular Queue

```cpp
class CircularQueue {
    int arr[1000], front = 0, rear = 0, size = 0, capacity;
public:
    CircularQueue(int k) : capacity(k) {}

    bool enqueue(int x) {
        if (size == capacity) return false;
        arr[rear] = x;
        rear = (rear + 1) % capacity;   // ← wrap around
        size++;
        return true;
    }
    bool dequeue() {
        if (size == 0) return false;
        front = (front + 1) % capacity;
        size--;
        return true;
    }
    int Front() { return size ? arr[front] : -1; }
    int Rear()  { return size ? arr[(rear - 1 + capacity) % capacity] : -1; }
};
```

**Without circular behavior:** `dequeue()` leaves dead space at the front, eventually causing overflow even when capacity is available. The `% capacity` trick eliminates that.

| Operation | Time |
|-----------|------|
| enqueue   | O(1) |
| dequeue   | O(1) |
| front     | O(1) |
| rear      | O(1) |

---

### Pattern 14: Monotonic Queue — Sliding Window Maximum

> **Core Idea:** Maintain a deque (double-ended queue) that is always decreasing. The front holds the maximum of the current window. Before inserting a new element, pop all smaller elements from the back — they can never be the maximum for any future window.

```
Input:  [1, 3, -1, -3, 5, 3, 6, 7],  k = 3
Output: [3, 3, 5, 5, 6, 7]
```

```cpp
vector<int> maxSlidingWindow(vector<int>& nums, int k) {
    deque<int> dq;  // stores indices, front = max index
    vector<int> result;

    for (int i = 0; i < nums.size(); i++) {
        // Remove out-of-window index from front
        if (!dq.empty() && dq.front() <= i - k)
            dq.pop_front();

        // Remove all smaller elements from back (they're useless)
        while (!dq.empty() && nums[dq.back()] < nums[i])
            dq.pop_back();

        dq.push_back(i);

        // Window fully formed
        if (i >= k - 1)
            result.push_back(nums[dq.front()]);
    }
    return result;
}
```

**Why O(n)?** Each element enters and leaves the deque at most once.

**Deque state trace for [1,3,-1,-3,5,3,6,7], k=3:**
```
i=0: dq=[0]          → window not full yet
i=1: dq=[1]          → 3 evicts 1 (smaller)
i=2: dq=[1,2]        → max=nums[1]=3  ✓
i=3: dq=[1,2,3]      → max=nums[1]=3  ✓
i=4: dq=[4]          → 5 evicts all   max=5 ✓
...
```

| Operation | Time |
|-----------|------|
| Total     | O(n) |
| Space     | O(k) |

---

### Pattern 15: Celebrity Problem — Elimination Stack/Queue

> **Core Idea:** If `A knows B`, then `A` cannot be the celebrity. Use this to eliminate candidates one by one until one remains, then verify.

```
A celebrity is known by everyone and knows nobody.
```

```cpp
int findCelebrity(int n) {
    stack<int> st;
    for (int i = 0; i < n; i++) st.push(i);

    // Eliminate non-celebrities
    while (st.size() > 1) {
        int a = st.top(); st.pop();
        int b = st.top(); st.pop();
        if (knows(a, b))
            st.push(b);   // a is eliminated
        else
            st.push(a);   // b is eliminated
    }

    // Verify the last remaining candidate
    int candidate = st.top();
    for (int i = 0; i < n; i++) {
        if (i != candidate && (knows(candidate, i) || !knows(i, candidate)))
            return -1;
    }
    return candidate;
}
```

**Why it works:** Each `knows()` call eliminates exactly one candidate. After `n-1` calls we have one candidate left. One final O(n) pass verifies them.

**Complexity:** O(n) time, O(n) space.

---

### Pattern 16: LRU Cache — HashMap + Doubly Linked List

> **Core Idea:** Combine a HashMap (O(1) lookup by key) with a Doubly Linked List (O(1) insertion/deletion at any position). Most Recently Used goes to the front; Least Recently Used sits at the back and is evicted first.

```
get(key)  → O(1) lookup via HashMap; move node to front
put(key)  → O(1) insert at front; evict from back if over capacity
```

```cpp
struct Node { int key, val; Node *prev, *next; };

class LRUCache {
    int cap;
    unordered_map<int, Node*> mp;
    Node *head, *tail;   // dummy head and tail

    void remove(Node* node) {
        node->prev->next = node->next;
        node->next->prev = node->prev;
    }
    void insertFront(Node* node) {
        node->next = head->next;
        node->prev = head;
        head->next->prev = node;
        head->next = node;
    }

public:
    LRUCache(int capacity) : cap(capacity) {
        head = new Node(); tail = new Node();
        head->next = tail; tail->prev = head;
    }

    int get(int key) {
        if (!mp.count(key)) return -1;
        Node* node = mp[key];
        remove(node);
        insertFront(node);
        return node->val;
    }

    void put(int key, int val) {
        if (mp.count(key)) {
            mp[key]->val = val;
            remove(mp[key]);
            insertFront(mp[key]);
        } else {
            Node* node = new Node{key, val};
            mp[key] = node;
            insertFront(node);
            if (mp.size() > cap) {
                Node* lru = tail->prev;
                mp.erase(lru->key);
                remove(lru);
                delete lru;
            }
        }
    }
};
```

**Data structure roles:**
```
HashMap      →  O(1) key → node lookup
DLL front    →  Most Recently Used
DLL back     →  Least Recently Used (eviction target)
```

| Operation | Time |
|-----------|------|
| get       | O(1) |
| put       | O(1) |

---

### Pattern 17: LFU Cache — HashMap + DLL + Frequency Map

> **Core Idea:** Evict the Least Frequently Used entry. Track usage frequency per key. Maintain a separate DLL per frequency level and track the current minimum frequency. All operations remain O(1).

```
get(key)   → increment frequency, move to correct freq-list
put(key)   → add with freq=1; if capacity exceeded, evict from min-freq list
```

```cpp
class LFUCache {
    int cap, minFreq = 0;
    unordered_map<int, pair<int,int>> keyMap;     // key → {val, freq}
    unordered_map<int, list<int>> freqMap;         // freq → list of keys
    unordered_map<int, list<int>::iterator> iter;  // key → iterator in freqMap

    void updateFreq(int key) {
        int freq = keyMap[key].second;
        freqMap[freq].erase(iter[key]);
        if (freqMap[minFreq].empty()) minFreq++;
        keyMap[key].second++;
        freqMap[freq + 1].push_front(key);
        iter[key] = freqMap[freq + 1].begin();
    }

public:
    LFUCache(int capacity) : cap(capacity) {}

    int get(int key) {
        if (!keyMap.count(key)) return -1;
        updateFreq(key);
        return keyMap[key].first;
    }

    void put(int key, int val) {
        if (!cap) return;
        if (keyMap.count(key)) {
            keyMap[key].first = val;
            updateFreq(key);
        } else {
            if (keyMap.size() == cap) {
                int evict = freqMap[minFreq].back();
                freqMap[minFreq].pop_back();
                iter.erase(evict);
                keyMap.erase(evict);
            }
            keyMap[key] = {val, 1};
            freqMap[1].push_front(key);
            iter[key] = freqMap[1].begin();
            minFreq = 1;
        }
    }
};
```

**Three maps and their roles:**
```
keyMap   →  key → (value, frequency)
freqMap  →  frequency → ordered list of keys (LRU within same freq)
iter     →  key → iterator in its frequency list (for O(1) removal)
minFreq  →  tracks which frequency list to evict from
```

| Operation | Time |
|-----------|------|
| get       | O(1) |
| put       | O(1) |

---

## Monotonic Stack Cheat Sheet

```
┌────────────────────┬─────────────────┬──────────────────────────────────────┐
│ Query              │ Stack Type      │ Pop Condition                        │
├────────────────────┼─────────────────┼──────────────────────────────────────┤
│ Next Greater       │ Decreasing      │ pop while top < current              │
│ Next Smaller       │ Increasing      │ pop while top > current              │
│ Previous Greater   │ Decreasing      │ pop while top <= current (R→L scan)  │
│ Previous Smaller   │ Increasing      │ pop while top >= current (R→L scan)  │
└────────────────────┴─────────────────┴──────────────────────────────────────┘
```

**Always store indices, not values.** You need the position to compute spans and widths.

---

## Interview Recognition Guide

| Problem Clue | Pattern |
|---|---|
| Undo / redo / browser history | LIFO Simulation (Pattern 1) |
| Implement stack using queue | Data Structure Transformation (Pattern 2) |
| Implement queue using stack | Data Structure Transformation (Pattern 2) |
| Balanced / valid brackets | Parentheses Matching (Pattern 3) |
| Evaluate / convert expression | Expression Conversion (Pattern 4) |
| getMin() / getMax() in O(1) | Special Stack (Pattern 5) |
| Next Greater / Smaller Element | Monotonic Stack (Pattern 6) |
| Stock span / consecutive days | Previous Greater → Monotonic Stack (Pattern 7) |
| Largest rectangle in histogram | Nearest Smaller Both Sides (Pattern 8) |
| Sum of subarray minimums | Contribution Technique (Pattern 9) |
| Sum of subarray ranges | Contribution Technique (Pattern 9) |
| Remove k digits / smallest number | Greedy Stack (Pattern 10) |
| Asteroid collision | Collision Simulation (Pattern 11) |
| Trapping rain water | Boundary Stack (Pattern 12) |
| Circular / bounded queue | Circular Queue (Pattern 13) |
| Sliding window maximum | Monotonic Queue (Pattern 14) |
| Find celebrity in a group | Elimination Stack (Pattern 15) |
| get/put with O(1), evict oldest used | LRU Cache (Pattern 16) |
| get/put with O(1), evict least used | LFU Cache (Pattern 17) |

---

## Recommended Study Order

Master these in sequence — each level builds on the previous:

```
Level 1 — Foundation
  1. Stack Implementation (array + linked list)
  2. Queue Implementation (circular)
  3. Stack ↔ Queue transformations

Level 2 — Core Stack Patterns
  4. Balanced Parentheses
  5. Infix → Postfix conversion
  6. Min Stack

Level 3 — Monotonic Stack (Most Important)
  7. Next Greater Element
  8. Next Smaller Element
  9. Stock Span

Level 4 — Applied Monotonic Stack
  10. Asteroid Collision
  11. Remove K Digits
  12. Largest Rectangle in Histogram
  13. Trapping Rain Water

Level 5 — Advanced
  14. Sum of Subarray Minimums (Contribution Technique)
  15. Sliding Window Maximum (Monotonic Queue)
  16. LRU Cache
  17. LFU Cache
```

**80/20 rule:** Master these four patterns and you can solve ~80% of stack/queue interview problems:

1. **Monotonic Stack** — NGE, NSE, Histogram, Stock Span, Rain Water
2. **Greedy Stack** — Remove K Digits, Asteroid Collision
3. **Expression/Parentheses Stack** — Balanced brackets, Infix/Postfix
4. **Monotonic Queue** — Sliding Window Maximum

> Almost everything after NGE is monotonic stack wearing different clothes. Interview questions love disguises. The underlying stack doesn't care about the costume.

---

*Happy Stacking! 🚀*