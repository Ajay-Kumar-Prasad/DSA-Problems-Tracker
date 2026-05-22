# Reverse Nodes in K-Group

**LeetCode 25 — Hard**

---

## Problem

Given the head of a linked list, reverse the nodes of the list `k` at a time and return the modified list.

- `k` is a positive integer ≤ length of the list.
- If the number of nodes is not a multiple of `k`, the **left-out nodes at the end are left as-is**.
- You may **not alter the values** in the list's nodes — only nodes themselves may be changed.

```
Input:  1 → 2 → 3 → 4 → 5,  k = 2
Output: 2 → 1 → 4 → 3 → 5

Input:  1 → 2 → 3 → 4 → 5,  k = 3
Output: 3 → 2 → 1 → 4 → 5
```

---

## Approach 1 — Brute Force (Stack)

### Intuition

Use a stack to reverse k elements at a time. A stack naturally reverses insertion order — push k nodes, then pop them to relink. Simple to reason about; trades space for simplicity.

### Steps

1. Traverse the list, pushing nodes onto a stack in groups of k.
2. If the stack reaches size k, pop all nodes and relink them in reversed order.
3. If fewer than k nodes remain, relink them in their original order (no reversal).

### Code

```cpp
ListNode* reverseKGroup(ListNode* head, int k) {
    if (!head || k == 1) return head;

    stack<ListNode*> st;
    ListNode dummy(0);
    ListNode* tail = &dummy;
    ListNode* curr = head;

    while (curr) {
        // Push k nodes onto stack
        int count = 0;
        ListNode* groupStart = curr;
        while (curr && count < k) {
            st.push(curr);
            curr = curr->next;
            count++;
        }

        if (count == k) {
            // Full group: pop and relink in reversed order
            while (!st.empty()) {
                tail->next = st.top();
                st.pop();
                tail = tail->next;
            }
        } else {
            // Partial group: attach as-is (original order)
            tail->next = groupStart;
            tail = curr ? tail : tail; // tail stays, no change needed
            break;
        }
    }
    tail->next = nullptr;
    return dummy.next;
}
```

### Complexity

| | |
|---|---|
| **Time** | O(n) — each node is pushed and popped once |
| **Space** | O(k) — stack holds at most k nodes at a time |

### Drawback

Extra O(k) space. Not truly in-place. In interviews this is usually the "I know what to do" answer but not the optimal one.

---

## Approach 2 — Better (Recursive)

### Intuition

Recursion maps cleanly to the problem structure:

> _Reverse the first k nodes, then recursively handle the rest, then connect them._

Each call handles one group of k and delegates the tail to the next call. The recursive call stack does the "bookkeeping" of where each group ends.

### Steps

1. Count k nodes from `head`. If fewer than k exist, return `head` (base case).
2. Reverse the first k nodes (standard iterative reversal).
3. Recursively call on the node after the k-th node.
4. Connect the original head (now the tail of the reversed group) to the result of the recursive call.

### Code

```cpp
ListNode* reverseKGroup(ListNode* head, int k) {
    // Check if k nodes exist
    ListNode* check = head;
    int count = 0;
    while (check && count < k) {
        check = check->next;
        count++;
    }
    // Base case: fewer than k nodes remain
    if (count < k) return head;

    // Reverse first k nodes
    ListNode* prev = nullptr;
    ListNode* curr = head;
    for (int i = 0; i < k; i++) {
        ListNode* next = curr->next;
        curr->next = prev;
        prev = curr;
        curr = next;
    }

    // head is now the tail of the reversed group
    // curr is the start of the next group
    head->next = reverseKGroup(curr, k);

    return prev; // prev is the new head of this group
}
```

### Dry Run (k=2, list: 1→2→3→4→5)

```
Call 1: head=1, k=2 nodes exist ✓
  Reverse [1,2] → prev=2, curr=3
  head(1)->next = reverseKGroup(3, 2)

  Call 2: head=3, k=2 nodes exist ✓
    Reverse [3,4] → prev=4, curr=5
    head(3)->next = reverseKGroup(5, 2)

    Call 3: head=5, only 1 node < k → return 5

  head(3)->next = 5  →  4→3→5
  return 4

head(1)->next = 4  →  2→1→4→3→5
return 2
```

### Complexity

| | |
|---|---|
| **Time** | O(n) — each node visited once per reversal pass |
| **Space** | O(n/k) — recursive call stack depth equals number of groups |

### Drawback

O(n/k) stack space. For large lists with small k, this approaches O(n). Not truly O(1) space.

---

## Approach 3 — Optimal (Iterative In-Place) ✓

### Intuition

Eliminate recursion entirely by managing three pointers:

- **`temp`** — start of the current group being processed
- **`kthNode`** — end of the current group (found by walking k-1 steps)
- **`prevGroupTail`** — tail of the previously processed group (used to stitch groups together)

For each group:
1. Find the k-th node. If it doesn't exist → remaining nodes < k, attach as-is and stop.
2. Save `nextGroup = kthNode->next`.
3. Cut the group (`kthNode->next = nullptr`) and reverse it.
4. Stitch: either update `head` (first group) or connect `prevGroupTail->next` to the new group head.
5. Connect the old group start (now tail after reversal) to `nextGroup`.
6. Advance `temp` to `nextGroup`.

### Visual Walkthrough (k=3, list: 1→2→3→4→5→6→7)

```
Initial:  1 → 2 → 3 → 4 → 5 → 6 → 7
          ^           ^
         temp      kthNode(3)

Step 1: nextGroup = 4, cut [1→2→3], reverse → [3→2→1]
        head = 3 (first group), prevGroupTail = 1, temp = 4
        3 → 2 → 1 → 4 → 5 → 6 → 7
                     ^           ^
                    temp      kthNode(6)

Step 2: nextGroup = 7, cut [4→5→6], reverse → [6→5→4]
        prevGroupTail(1)->next = 6, prevGroupTail = 4, temp = 7
        3 → 2 → 1 → 6 → 5 → 4 → 7
                                 ^
                                temp (only 1 node, < k)

Step 3: kthNode = nullptr, prevGroupTail(4)->next = 7, break

Final:  3 → 2 → 1 → 6 → 5 → 4 → 7  ✓
```

### Code

```cpp
class Solution {
public:
    // Walk k-1 steps from temp to find the kth node
    ListNode* getKthNode(ListNode* temp, int k) {
        k -= 1;
        while (temp && k > 0) {
            temp = temp->next;
            k--;
        }
        return temp;
    }

    // Standard iterative list reversal
    ListNode* reverseList(ListNode* head) {
        ListNode* prev = nullptr;
        ListNode* curr = head;
        while (curr) {
            ListNode* nextNode = curr->next;
            curr->next = prev;
            prev = curr;
            curr = nextNode;
        }
        return prev;
    }

    ListNode* reverseKGroup(ListNode* head, int k) {
        if (!head || k == 1) return head;

        ListNode* temp = head;
        ListNode* prevGroupTail = nullptr;

        while (temp) {
            // Find the kth node of current group
            ListNode* kthNode = getKthNode(temp, k);

            // Fewer than k nodes remain → leave them as-is
            if (kthNode == nullptr) {
                if (prevGroupTail) {
                    prevGroupTail->next = temp;
                }
                break;
            }

            ListNode* nextGroup = kthNode->next; // save next group start
            kthNode->next = nullptr;             // cut current group

            ListNode* newHead = reverseList(temp); // reverse current group

            // Stitch reversed group into the overall list
            if (temp == head) {
                head = newHead;                  // update overall head (first group only)
            } else {
                prevGroupTail->next = newHead;   // connect previous tail to new head
            }

            prevGroupTail = temp;                // temp is now the tail after reversal
            temp->next = nextGroup;              // connect tail to next group
            temp = nextGroup;                    // advance to next group
        }

        return head;
    }
};
```

### Pointer State Table (k=2, list: 1→2→3→4→5)

| Iteration | temp | kthNode | nextGroup | newHead | head | prevGroupTail |
|-----------|------|---------|-----------|---------|------|---------------|
| 1         | 1    | 2       | 3         | 2       | 2    | 1             |
| 2         | 3    | 4       | 5         | 4       | 2    | 3             |
| 3         | 5    | null    | —         | —       | 2    | 3 (→5, break) |

Final: `2 → 1 → 4 → 3 → 5` ✓

### Complexity

| | |
|---|---|
| **Time** | O(n) — `getKthNode` + `reverseList` together visit each node exactly twice |
| **Space** | O(1) — only a fixed number of pointers; no stack, no recursion |

---

## Approach Comparison

| Approach | Time | Space | Notes |
|---|---|---|---|
| Brute (Stack) | O(n) | O(k) | Easiest to implement; uses auxiliary stack |
| Better (Recursive) | O(n) | O(n/k) | Clean and readable; hidden stack cost |
| **Optimal (Iterative)** | **O(n)** | **O(1)** | In-place; preferred in interviews |

---

## Edge Cases

| Case | Behavior |
|---|---|
| `k == 1` | Return head unchanged (early exit) |
| `k == n` | Entire list reversed |
| `n` not divisible by `k` | Last `n % k` nodes left in original order |
| Single node | Return head unchanged |
| All nodes same value | Works correctly — structural, not value-based |

---

## Key Pointer Insight

After `reverseList(temp)`:
- `temp` (old group head) becomes the **tail** of the reversed group.
- `newHead` (old kth node) becomes the **head** of the reversed group.
- This is why `prevGroupTail = temp` works — `temp` is always the tail post-reversal.

```
Before reversal:  temp → ... → kthNode  |  nextGroup
                  (head)       (tail)

After reversal:   newHead → ... → temp  |  nextGroup
                  (kthNode)      (old head = new tail)
```

This invariant is what makes the iterative approach tick.