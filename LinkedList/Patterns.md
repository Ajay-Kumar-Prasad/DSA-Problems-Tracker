# 🔗 Linked List DSA Patterns

> A complete reference for mastering Linked List problems through reusable patterns and intuition.

---

## 📚 Table of Contents

| # | Pattern | Key Use Cases |
|---|---------|--------------|
| 1 | [Traversal](#1-traversal-pattern) | Find length, Search, Sum, Min/Max |
| 2 | [Fast & Slow Pointer](#2-fast--slow-pointer-pattern) | Middle node, Cycle detection, Palindrome |
| 3 | [Reverse Linked List](#3-reverse-linked-list-pattern) | Reverse, Palindrome, Reorder |
| 4 | [Dummy Node](#4-dummy-node-pattern) | Remove node, Merge, Partition |
| 5 | [Two Pointer Gap](#5-two-pointer-gap-pattern) | Remove Nth from end, Kth from end |
| 6 | [Merge Pattern](#6-merge-pattern) | Merge sorted lists, Flatten, Sort |
| 7 | [Cycle Detection (Floyd's)](#7-cycle-detection-pattern) | Detect cycle, Find cycle start |
| 8 | [In-place Modification](#8-in-place-modification-pattern) | Odd-Even, Reorder, Rotate |
| 9 | [Recursion](#9-recursion-pattern) | Recursive reverse, Flatten |
| 10 | [Divide & Conquer](#10-divide--conquer-pattern) | Merge Sort, Merge K lists |
| 11 | [Random Pointer / Mapping](#11-random-pointer--mapping-pattern) | Clone list with random pointers |
| 12 | [Circular Linked List](#12-circular-linked-list-pattern) | Rotate list |
| 13 | [Multi-Level Linked List](#13-multi-level-linked-list-pattern) | Flatten multi-level list |

---

## 1. Traversal Pattern

### Core Idea
Traverse node-by-node from `head` until `NULL`.

### Template
```cpp
ListNode* temp = head;

while (temp) {
    // process node
    temp = temp->next;
}
```

### Use Cases
- Find length of list
- Search for an element
- Print all nodes
- Sum / Maximum / Minimum of nodes

### Complexity
| Time | Space |
|------|-------|
| O(n) | O(1) |

---

## 2. Fast & Slow Pointer Pattern

### Core Idea
Two pointers move at different speeds — slow moves 1 step, fast moves 2 steps.

### Template
```cpp
ListNode* slow = head;
ListNode* fast = head;

while (fast && fast->next) {
    slow = slow->next;
    fast = fast->next->next;
}
// slow is now at the middle
```

### Use Cases
- Middle of Linked List
- Detect Cycle
- Find Cycle Start
- Palindrome Linked List
- Happy Number

### Key Intuition
| Scenario | Outcome |
|----------|---------|
| Cycle exists | `fast` and `slow` eventually meet |
| No cycle | `fast` reaches `NULL` |

### Complexity
| Time | Space |
|------|-------|
| O(n) | O(1) |

---

## 3. Reverse Linked List Pattern

### Core Idea
Reverse pointers one-by-one using three pointers: `prev`, `curr`, and `nextNode`.

### Template
```cpp
ListNode* prev = NULL;
ListNode* curr = head;

while (curr) {
    ListNode* nextNode = curr->next;  // save before breaking link
    curr->next = prev;
    prev = curr;
    curr = nextNode;
}

return prev;  // new head
```

### Use Cases
- Reverse Linked List
- Reverse K Group
- Palindrome LL
- Reorder List

### ⚠️ Critical Rule
Always save `nextNode = curr->next` **before** changing any links.

### Complexity
| Time | Space |
|------|-------|
| O(n) | O(1) |

---

## 4. Dummy Node Pattern

### Core Idea
Prepend a fake/sentinel node before `head` to uniformly handle edge cases (deleting head, inserting at head).

### Template
```cpp
ListNode* dummy = new ListNode(-1);
dummy->next = head;

// ... operate on list ...

return dummy->next;  // actual head
```

### Use Cases
- Remove Nth Node From End
- Merge Two Lists
- Partition List
- Swap Nodes in Pairs

### Why It Helps
| Without Dummy | With Dummy |
|--------------|------------|
| Special-case deleting head | Uniform deletion logic |
| Special-case inserting at head | Uniform insertion logic |

---

## 5. Two Pointer Gap Pattern

### Core Idea
Maintain a fixed distance `n` between two pointers. When `fast` hits `NULL`, `slow` is at the target node.

### Template
```cpp
ListNode* slow = head;
ListNode* fast = head;

// advance fast by n steps
for (int i = 0; i < n; i++) {
    fast = fast->next;
}

while (fast) {
    slow = slow->next;
    fast = fast->next;
}
// slow is now n steps from the end
```

### Use Cases
- Remove Nth Node From End
- Find Kth Node From End

### Complexity
| Time | Space |
|------|-------|
| O(n) | O(1) |

---

## 6. Merge Pattern

### Core Idea
Merge two sorted linked lists by comparing values and linking nodes in order.

### Template
```cpp
ListNode* dummy = new ListNode(-1);
ListNode* temp = dummy;

while (l1 && l2) {
    if (l1->val <= l2->val) {
        temp->next = l1;
        l1 = l1->next;
    } else {
        temp->next = l2;
        l2 = l2->next;
    }
    temp = temp->next;
}

if (l1) temp->next = l1;
if (l2) temp->next = l2;

return dummy->next;
```

### Use Cases
- Merge Two Sorted Lists
- Merge K Sorted Lists
- Sort Linked List (Merge Sort)
- Flatten Multi-level Linked List

### Complexity
| Time | Space |
|------|-------|
| O(n + m) | O(1) |

---

## 7. Cycle Detection Pattern

### Core Idea — Floyd's Tortoise & Hare Algorithm

**Step 1:** Move `slow` by 1, `fast` by 2 until they collide (cycle exists) or `fast` reaches NULL (no cycle).

**Step 2:** If collision detected, move one pointer back to `head`.

**Step 3:** Move both pointers one step at a time.

**Step 4:** Their meeting point is the **cycle start**.

### Template
```cpp
ListNode* slow = head;
ListNode* fast = head;

// Phase 1: Detect cycle
while (fast && fast->next) {
    slow = slow->next;
    fast = fast->next->next;
    if (slow == fast) break;
}

// No cycle
if (!fast || !fast->next) return NULL;

// Phase 2: Find cycle start
slow = head;
while (slow != fast) {
    slow = slow->next;
    fast = fast->next;
}

return slow;  // cycle start node
```

### Complexity
| Time | Space |
|------|-------|
| O(n) | O(1) |

---

## 8. In-place Modification Pattern

### Core Idea
Rearrange node links directly without using extra memory.

### Use Cases
- Odd Even Linked List
- Reorder List
- Rotate List
- Reverse Nodes in K-Group

### Key Skill: Pointer Bookkeeping
Always track multiple boundary pointers:
```
current → next → previous boundaries
```

### Complexity
| Time | Space |
|------|-------|
| O(n) | O(1) |

---

## 9. Recursion Pattern

### Core Idea
Break the problem into a smaller subproblem on `head->next`, solve it recursively, then fix the link for `head`.

### Template (Recursive Reverse)
```cpp
ListNode* reverse(ListNode* head) {
    if (!head || !head->next)
        return head;

    ListNode* newHead = reverse(head->next);  // recursively reverse rest

    head->next->next = head;  // make next node point back to current
    head->next = NULL;        // break old link

    return newHead;
}
```

### Use Cases
- Recursive Reverse Linked List
- Flatten Multi-level List
- Merge Two Sorted Lists (recursive)

### Complexity
| Time | Space |
|------|-------|
| O(n) | O(n) call stack |

---

## 10. Divide & Conquer Pattern

### Core Idea
Split the linked list into halves, sort/process each half recursively, then merge results.

### Use Cases
- Merge Sort on Linked List
- Merge K Sorted Lists

### Why Linked Lists Love Merge Sort
- No random access needed
- Splitting uses the Fast & Slow pointer pattern
- Merging is O(n) in-place

### Template Sketch
```
mergeSort(head):
    mid  = findMiddle(head)         // Fast & Slow pointer
    left = mergeSort(head)
    right = mergeSort(mid)
    return merge(left, right)       // Merge Pattern
```

### Complexity
| Time | Space |
|------|-------|
| O(n log n) | O(log n) call stack |

---

## 11. Random Pointer / Mapping Pattern

### Core Idea
When cloning a list with arbitrary/random pointers, use a hashmap to map each original node to its copy.

### Hashmap Approach — O(n) Space
```cpp
unordered_map<Node*, Node*> mp;

// Pass 1: create all cloned nodes
Node* temp = head;
while (temp) {
    mp[temp] = new Node(temp->val);
    temp = temp->next;
}

// Pass 2: assign next and random pointers
temp = head;
while (temp) {
    mp[temp]->next   = mp[temp->next];
    mp[temp]->random = mp[temp->random];
    temp = temp->next;
}

return mp[head];
```

### Optimal Interleaving Trick — O(1) Space
Interleave copied nodes into the original list:
```
A → A' → B → B' → C → C'
```
Then:
```cpp
copy->random = original->random->next;
```
Finally, separate the two lists.

### Use Cases
- Clone Linked List with Random Pointer

---

## 12. Circular Linked List Pattern

### Core Idea
Temporarily convert the list into a cycle to simplify rotation or circular traversal.

### Template
```cpp
// Step 1: form a cycle
tail->next = head;

// Step 2: find the new tail
// (walk to the correct position)

// Step 3: break the cycle
newTail->next = NULL;
```

### Use Cases
- Rotate Linked List by K

### Complexity
| Time | Space |
|------|-------|
| O(n) | O(1) |

---

## 13. Multi-Level Linked List Pattern

### Core Idea
Recursively flatten a list with child/next pointers by merging each level.

### Recursive Insight
```
flatten(head) = merge(
    current level list,
    flatten(next level list)
)
```

### Template Sketch
```cpp
Node* flatten(Node* head) {
    if (!head) return NULL;
    Node* flatNext = flatten(head->next);
    return merge(head, flatNext);
}
```

### Use Cases
- Flatten a Multi-level Doubly Linked List
- Flatten Sorted Linked Lists

---

## 📋 Common Interview Problems Cheatsheet

| Problem | Pattern(s) |
|---------|-----------|
| Reverse Linked List | Reverse Pattern |
| Middle of Linked List | Fast & Slow Pointer |
| Linked List Cycle | Fast & Slow Pointer |
| Find Cycle Start | Floyd's Algorithm |
| Remove Nth Node From End | Two Pointer Gap + Dummy Node |
| Merge Two Sorted Lists | Merge Pattern + Dummy Node |
| Palindrome Linked List | Fast & Slow + Reverse |
| Reverse Nodes in K-Group | In-place + Local Reversal |
| Rotate Linked List | Circular Pattern |
| Flatten Linked List | Recursive Merge |
| Clone List with Random Pointer | Random Pointer / Mapping |
| Reorder List | Reverse + Merge |
| Sort Linked List | Divide & Conquer (Merge Sort) |
| Odd Even Linked List | In-place Modification |

---

## 🐛 Debugging Guide

### 1. Draw Everything
Always track these three pointers on paper:
```
prev → curr → next
```

### 2. Save Next Before Breaking Links
```cpp
// ✅ Always do this FIRST
ListNode* nextNode = curr->next;

// Then break/reassign
curr->next = prev;
```

### 3. Think in Invariants
Ask yourself after every iteration:
- Is the merged list still sorted?
- Is the gap between pointers still fixed?
- Is the reversed portion still valid?

### 4. Always Test Edge Cases

| Edge Case | Why It Matters |
|-----------|---------------|
| `NULL` / empty list | Head pointer is null |
| Single node | No `next` to traverse |
| Two nodes | Minimal reversal/swap scenario |
| All duplicates | Equality comparisons in merge |
| No cycle | Fast pointer reaches NULL |
| Cycle at head | Cycle detection begins at node 0 |

---

## 🏆 Golden Rule

> Most linked list bugs are **not algorithmic** — they are pointer mistakes.

The four most common culprits:
1. **Null pointer dereference** — accessing `.next` on NULL
2. **Broken connections** — forgetting to re-link a node
3. **Lost nodes** — overwriting a pointer without saving it first
4. **Wrong traversal order** — changing a link before reading it

The real skill is maintaining **pointer invariants** carefully.

---

## 🎯 Final Advice

**Don't memorize problems — recognize patterns.**

Most linked list interview questions are combinations of just four fundamentals:

```
Reversal  +  Fast/Slow Pointer  +  Merging  +  Pointer Reconnection
```

Master these patterns and linked lists stop feeling like guesswork — they become mechanical.

---

*Generated for DSA Interview Preparation · Linked List Patterns Reference*