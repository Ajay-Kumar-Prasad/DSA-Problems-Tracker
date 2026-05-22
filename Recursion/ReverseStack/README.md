# Reverse Stack Using Recursion

## Problem

Reverse a stack using recursion, **without using any extra data structure**.

---

## Intuition

Think of it this way:

> _"If I could magically reverse everything below the top element, where should the top element go?"_

It should go to the **bottom**. That's the whole idea.

1. **Pop** the top element
2. **Recursively reverse** the remaining stack
3. **Insert** the popped element at the bottom

Repeating this for every element flips the entire stack.

---

## Recursive Pattern

```
reverse(stack):
    if stack is empty → return

    top = pop from stack
    reverse(stack)               ← reverse the rest
    insertAtBottom(stack, top)   ← send top to the bottom
```

```
insertAtBottom(stack, x):
    if stack is empty → push x and return

    top = pop from stack
    insertAtBottom(stack, x)     ← recurse until empty
    push top back                ← restore everything above x
```

`insertAtBottom` empties the stack down to nothing, pushes `x`, then rebuilds the stack on top — effectively placing `x` at the bottom without touching any extra structure.

---

## Dry Run

### Initial Stack
```
Top → [ 4, 3, 2, 1 ]   (4 is on top)
```

### Phase 1 — Unwinding (popping & recursing)

| Call Depth | Action              | Stack State     | Saved (call stack) |
|------------|---------------------|-----------------|--------------------|
| 1          | pop 4, recurse      | [ 3, 2, 1 ]     | holds 4            |
| 2          | pop 3, recurse      | [ 2, 1 ]        | holds 3            |
| 3          | pop 2, recurse      | [ 1 ]           | holds 2            |
| 4          | pop 1, recurse      | [ ]             | holds 1            |
| 5          | base case (empty)   | [ ]             | —                  |

### Phase 2 — Rewinding (inserting each element at the bottom)

**insertAtBottom([], 1)**
- Stack is empty → push 1
- Stack: `[ 1 ]`

**insertAtBottom([1], 2)**
- pop 1, recurse insertAtBottom([], 2)
  - empty → push 2
- push 1 back
- Stack: `[ 1, 2 ]`  ← 1 on top, 2 at bottom

**insertAtBottom([1,2], 3)**
- pop 1, recurse insertAtBottom([2], 3)
  - pop 2, recurse insertAtBottom([], 3)
    - empty → push 3
  - push 2 back
- push 1 back
- Stack: `[ 1, 2, 3 ]`

**insertAtBottom([1,2,3], 4)**
- pop 1 → pop 2 → pop 3 → insertAtBottom([], 4) → push 4
- push 3, push 2, push 1
- Stack: `[ 1, 2, 3, 4 ]`

### Final Stack
```
Top → [ 1, 2, 3, 4 ]   ✓ Reversed
```

---

## C++ Code

```cpp
#include <iostream>
#include <stack>
using namespace std;

// Insert x at the bottom of the stack
void insertAtBottom(stack<int>& st, int x) {
    if (st.empty()) {          // base case: stack empty, x goes here
        st.push(x);
        return;
    }

    int topElement = st.top();
    st.pop();

    insertAtBottom(st, x);    // recurse until empty

    st.push(topElement);      // rebuild stack on top of x
}

// Reverse stack using recursion
void reverseStack(stack<int>& st) {
    if (st.empty()) return;   // base case

    int topElement = st.top();
    st.pop();

    reverseStack(st);         // reverse remaining stack

    insertAtBottom(st, topElement); // send current top to bottom
}

int main() {
    stack<int> st;
    st.push(1);
    st.push(2);
    st.push(3);
    st.push(4);

    reverseStack(st);

    while (!st.empty()) {
        cout << st.top() << " ";
        st.pop();
    }
    // Output: 1 2 3 4

    return 0;
}
```

---

## Complexity Analysis

### Time Complexity — O(n²)

- `reverseStack` makes **n** recursive calls (one per element).
- For each call, `insertAtBottom` traverses the entire current stack to reach the bottom.
- For element at position `k` (0-indexed), `insertAtBottom` makes `k` recursive calls.

```
Total work = 0 + 1 + 2 + ... + (n-1) = n(n-1)/2 = O(n²)
```

### Space Complexity — O(n)

- `reverseStack` uses up to `n` frames on the call stack simultaneously (one per element, during full unwind).
- `insertAtBottom` adds up to `n` more frames sequentially on top of those during rewinding.
- Peak simultaneous depth is O(n) — the two don't fully overlap.
- Overall: **O(n) recursive stack space**.

---

## Key Insight

> The **recursive call stack stores the elements** while the stack underneath is being rebuilt in reverse.

When `reverseStack` recurses after popping an element, that element lives in a local variable on the system call stack. Once all elements are popped and the base case hits, the stack is empty. Now `insertAtBottom` places each element — as the call stack unwinds — at the bottom, rebuilding the stack in reversed order. No explicit auxiliary storage is declared anywhere.

---

## Comparison with Sort Stack

Both problems share the **same two-function structure**:

| Aspect             | Sort Stack                          | Reverse Stack                        |
|--------------------|-------------------------------------|--------------------------------------|
| Main function      | `sortStack`                         | `reverseStack`                       |
| Helper function    | `insertSorted`                      | `insertAtBottom`                     |
| Helper places x    | At its **sorted position**          | At the **bottom**                    |
| Helper condition   | `st.empty() \|\| st.top() <= x`     | `st.empty()`                         |
| Output order       | Descending (largest on top)         | Reversed input                       |
| Time complexity    | O(n²)                               | O(n²)                                |
| Space complexity   | O(n)                                | O(n)                                 |

The only meaningful difference is **where the helper inserts the element**.

---

## Visual Summary

```
reverseStack([4,3,2,1])
│
├── pop 4 → reverseStack([3,2,1])
│           │
│           ├── pop 3 → reverseStack([2,1])
│           │           │
│           │           ├── pop 2 → reverseStack([1])
│           │           │           │
│           │           │           ├── pop 1 → reverseStack([])  ← base
│           │           │           └── insertAtBottom([], 1)     → [1]
│           │           └── insertAtBottom([1], 2)                → [1,2]
│           └── insertAtBottom([1,2], 3)                          → [1,2,3]
└── insertAtBottom([1,2,3], 4)                                    → [1,2,3,4]
```