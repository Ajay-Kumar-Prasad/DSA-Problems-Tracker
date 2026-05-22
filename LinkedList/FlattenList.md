# Flatten a Multilevel Linked List

**GFG / Interview Classic — Medium-Hard**

---

## Problem

A special linked list has two pointers per node:
- **`next`** — points to the next node in the horizontal (main) chain
- **`child`** — points to the head of a vertical sub-list hanging below the node

Every sub-list is **sorted in ascending order**, and the main chain is also sorted.

**Task:** Flatten the entire structure into a single sorted linked list using only the `child` pointer. Set all `next` pointers to `NULL`.

```
Input:
5 → 10 → 19 → 28
↓        ↓     ↓
7       22    35
↓        ↓
8       50
↓
30

Output (child chain):
5 → 7 → 8 → 10 → 19 → 22 → 28 → 30 → 35 → 50
(using child pointers, all next = NULL)
```

---

## Approach 1 — Brute Force (Collect + Sort)

### Intuition

Don't fight the structure. Just collect every value, sort them, and rebuild a fresh child chain.

### Steps

1. Traverse every node — follow both `next` and `child` pointers — and collect all values into a vector.
2. Sort the vector.
3. Build a new linked list using `child` pointers.

### Code

```cpp
ListNode* flattenLinkedList(ListNode* head) {
    vector<int> vals;
    ListNode* curr = head;

    // Collect all values
    while (curr) {
        ListNode* col = curr;
        while (col) {
            vals.push_back(col->val);
            col = col->child;
        }
        curr = curr->next;
    }

    sort(vals.begin(), vals.end());

    // Rebuild as child chain
    ListNode* newHead = new ListNode(vals[0]);
    ListNode* temp = newHead;
    for (int i = 1; i < vals.size(); i++) {
        temp->child = new ListNode(vals[i]);
        temp = temp->child;
    }
    return newHead;
}
```

### Complexity

| | |
|---|---|
| **Time** | O(n log n) — collecting all n nodes + sorting |
| **Space** | O(n) — vector storing all values + new nodes |

### Drawback

Creates entirely new nodes. Doesn't reuse the existing list structure. Not in-place. The sort step is unnecessary overhead given the sub-lists are already sorted.

---

## Approach 2 — Better (Iterative Merge, Left to Right)

### Intuition

Since every vertical list is already sorted, the problem reduces to **merging k sorted lists**. A natural iterative approach: merge the first two lists, then merge the result with the third, and so on — like a running merge.

### Steps

1. Start with the first vertical list.
2. For each subsequent list (via `next` pointers), merge it with the accumulated result.
3. Merging two sorted child-chains is identical to merging two sorted linked lists.

### Code

```cpp
ListNode* mergeTwoLists(ListNode* h1, ListNode* h2) {
    ListNode dummy(-1);
    ListNode* temp = &dummy;
    while (h1 && h2) {
        if (h1->val <= h2->val) {
            temp->child = h1;
            h1 = h1->child;
        } else {
            temp->child = h2;
            h2 = h2->child;
        }
        temp = temp->child;
        temp->next = nullptr;
    }
    temp->child = h1 ? h1 : h2;
    return dummy.child;
}

ListNode* flattenLinkedList(ListNode* head) {
    if (!head) return head;

    ListNode* result = head;
    ListNode* curr = head->next;

    while (curr) {
        result = mergeTwoLists(result, curr);
        curr = curr->next;
    }
    return result;
}
```

### Complexity

| | |
|---|---|
| **Time** | O(n × k) — each merge pass touches all accumulated nodes; k = number of columns |
| **Space** | O(1) — in-place relinking, no extra storage |

### Drawback

The accumulated list grows with each merge. Early elements are re-scanned in every subsequent merge — wasteful when k is large and lists are long.

---

## Approach 3 — Optimal (Recursive Merge from Right) ✓

### Intuition

Instead of merging left to right (where early nodes keep getting re-scanned), recurse to the **rightmost list first**, then merge back toward the left.

> _Flatten everything to the right of `head`, then merge that result with `head`'s own child list._

This is the classic **divide & conquer** framing: reduce to a smaller problem, solve it, combine.

Since every sub-problem returns a single sorted child chain, each merge step is clean and non-redundant. Every node is touched at most twice — once during recursion descent, once during merge.

### Recursive Pattern

```
flatten(head):
    if no next → return head (already a single sorted list)

    newHead = flatten(head->next)   ← flatten everything to the right
    return merge(head, newHead)     ← merge current column with flattened rest
```

### Visual Walkthrough

```
List:
5 → 10 → 19 → 28
↓         ↓    ↓
7        22   35
↓         ↓
8        50
↓
30
```

**Recursion unwinds right-to-left:**

```
flatten(28):   next=null → return [28→35]
flatten(19):   merge([19→22→50], [28→35])  → [19→22→28→35→50]
flatten(10):   merge([10],       [19→22→28→35→50]) → [10→19→22→28→35→50]
flatten(5):    merge([5→7→8→30], [10→19→22→28→35→50])
               → [5→7→8→10→19→22→28→30→35→50]  ✓
```

Each merge step is cheap because neither input has been repeatedly scanned.

### Code

```cpp
class Solution {
public:
    // Merge two sorted child-chains into one sorted child-chain
    ListNode* merge(ListNode* h1, ListNode* h2) {
        ListNode* dummy = new ListNode(-1);
        ListNode* temp = dummy;
        ListNode* t1 = h1;
        ListNode* t2 = h2;

        while (t1 && t2) {
            if (t1->val <= t2->val) {
                temp->child = t1;
                t1 = t1->child;
            } else {
                temp->child = t2;
                t2 = t2->child;
            }
            temp = temp->child;
            temp->next = nullptr;  // clear next pointer as we build
        }

        // Attach remaining nodes
        if (t1) temp->child = t1;
        if (t2) temp->child = t2;

        return dummy->child;
    }

    ListNode* flattenLinkedList(ListNode* &head) {
        // Base case: single column, already flat
        if (!head || head->next == nullptr) return head;

        // Recursively flatten everything to the right
        ListNode* newHead = flattenLinkedList(head->next);

        // Merge current column with the flattened right portion
        return merge(head, newHead);
    }
};
```

### Detailed Dry Run

**Input:** `5 → 10 → 19 → 28` (with children as shown above)

| Recursion Depth | Call | Merges | Result |
|---|---|---|---|
| 4 (base) | `flatten(28)` | — | `28→35` |
| 3 | `flatten(19)` | merge(`19→22→50`, `28→35`) | `19→22→28→35→50` |
| 2 | `flatten(10)` | merge(`10`, `19→22→28→35→50`) | `10→19→22→28→35→50` |
| 1 | `flatten(5)` | merge(`5→7→8→30`, `10→19→…→50`) | `5→7→8→10→19→22→28→30→35→50` |

**Merge trace for the final step** (`5→7→8→30` vs `10→19→22→28→35→50`):

```
t1=5,  t2=10 → pick 5  → dummy→5
t1=7,  t2=10 → pick 7  → 5→7
t1=8,  t2=10 → pick 8  → 7→8
t1=30, t2=10 → pick 10 → 8→10
t1=30, t2=19 → pick 19 → 10→19
t1=30, t2=22 → pick 22 → 19→22
t1=30, t2=28 → pick 28 → 22→28
t1=30, t2=35 → pick 30 → 28→30
t1=null      → attach remaining 35→50
Final: 5→7→8→10→19→22→28→30→35→50 ✓
```

### Complexity

| | |
|---|---|
| **Time** | O(n) — each node is merged exactly once across all recursive calls |
| **Space** | O(k) — recursion depth equals number of columns (k), not total nodes |

> **Why O(n) time?** In the left-to-right iterative approach, a node in the first column gets re-scanned in every merge. Here, each node participates in exactly one merge — the merge at the recursion level where its column is introduced. Total comparisons = total nodes = O(n).

---

## Approach Comparison

| Approach | Time | Space | Notes |
|---|---|---|---|
| Brute (Collect + Sort) | O(n log n) | O(n) | Ignores sorted property; creates new nodes |
| Better (Iterative L→R) | O(n × k) | O(1) | Re-scans early nodes in each merge pass |
| **Optimal (Recursive R→L)** | **O(n)** | **O(k)** | Each node merged once; clean recursive structure |

---

## Edge Cases

| Case | Behavior |
|---|---|
| Single node, no `next`, no `child` | Returns head unchanged (base case) |
| All nodes in one vertical column | `head->next == null` → return head directly |
| All nodes in horizontal chain (no children) | Each column is length 1; merge works trivially |
| Duplicate values | `t1->val <= t2->val` handles ties; stable ordering maintained |

---

## Key Implementation Details

### Why `temp->next = nullptr` inside merge?

Nodes in the horizontal chain still have their `next` pointers set from the original structure. After stitching them into the `child` chain, those stale `next` pointers must be cleared — otherwise the output list has dangling `next` references.

```cpp
temp = temp->child;
temp->next = nullptr;  // ← essential cleanup
```

### Why does `temp` become the old group head after reversal?

In `flattenLinkedList`, after `flatten(head->next)` returns, `head->next` now points into the middle of a flattened list. That stale `next` is never cleaned up in the recursive function itself — but the `merge` call's `temp->next = nullptr` inside the merge loop handles it for every node that gets relinked.

### The Dummy Node Pattern

```cpp
ListNode* dummy = new ListNode(-1);
ListNode* temp = dummy;
// ... build via temp->child ...
return dummy->child;
```

Avoids special-casing the first node. `dummy->child` always points to the true head of the merged result regardless of which list wins the first comparison.