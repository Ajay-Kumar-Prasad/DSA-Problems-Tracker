# Sort Stack Using Recursion

## Problem

Sort a stack in **descending order** (largest on top) using recursion, **without using any extra data structure**.

---

## Intuition

Think of it as solving a smaller version of the same problem:

> _"If I could magically sort everything below the top element, where should the top element go?"_

That's the entire idea. You:
1. **Pop** the top element (set it aside)
2. **Recursively sort** the rest of the stack
3. **Insert** the popped element back into its correct sorted position

The recursion stack itself temporarily holds the removed elements — no extra array or stack needed.

---

## Recursive Pattern

```
sort(stack):
    if stack is empty → return

    top = pop from stack
    sort(stack)                  ← sort the rest
    insertSorted(stack, top)     ← place top in correct position
```

```
insertSorted(stack, x):
    if stack is empty OR top of stack ≤ x → push x and return

    top = pop from stack
    insertSorted(stack, x)       ← go deeper
    push top back                ← restore
```

The key observation: **insertSorted moves elements out of the way, inserts x, then puts them back** — just like insertion sort, but using the call stack instead of a loop.

---

## Dry Run

### Initial Stack
```
Top → [ 2, 4, 1, 3 ]   (2 is on top)
```

### Phase 1 — Unwinding (popping & recursing)

| Call Depth | Action              | Stack State     | Saved (call stack) |
|------------|---------------------|-----------------|--------------------|
| 1          | pop 2, recurse      | [ 4, 1, 3 ]     | holds 2            |
| 2          | pop 4, recurse      | [ 1, 3 ]        | holds 4            |
| 3          | pop 1, recurse      | [ 3 ]           | holds 1            |
| 4          | pop 3, recurse      | [ ]             | holds 3            |
| 5          | base case (empty)   | [ ]             | —                  |

### Phase 2 — Rewinding (inserting in sorted order)

Now we unwind the call stack, inserting each saved element into the correct position.

**Insert 3** into `[]`
- Stack is empty → push 3
- Stack: `[ 3 ]`

**Insert 1** into `[ 3 ]`
- top (3) > 1 → pop 3, recurse insertSorted([], 1)
  - empty → push 1
- push 3 back
- Stack: `[ 3, 1 ]`  ← 3 on top, 1 below

Wait — we want descending (largest on top), so this means 3 > 1 ✓

**Insert 4** into `[ 3, 1 ]`
- top (3) ≤ 4 → push 4 directly
- Stack: `[ 4, 3, 1 ]`

**Insert 2** into `[ 4, 3, 1 ]`
- top (4) > 2 → pop 4, recurse insertSorted([3, 1], 2)
  - top (3) > 2 → pop 3, recurse insertSorted([1], 2)
    - top (1) ≤ 2 → push 2
    - Stack: `[ 1, 2 ]`
  - push 3 back → `[ 1, 2, 3 ]`
- push 4 back → `[ 1, 2, 3, 4 ]`

### Final Stack
```
Top → [ 4, 3, 2, 1 ]   ✓ Sorted descending
```

---

## C++ Code

```cpp
#include <iostream>
#include <stack>
using namespace std;

// Insert x into its correct position in a sorted stack
void insertSorted(stack<int>& st, int x) {
    // Correct position found: stack empty OR top is less than x
    if (st.empty() || st.top() <= x) {
        st.push(x);
        return;
    }

    int topElement = st.top();
    st.pop();

    insertSorted(st, x);       // recurse deeper to find correct spot

    st.push(topElement);       // restore element above x
}

// Sort stack using recursion
void sortStack(stack<int>& st) {
    if (st.empty()) return;    // base case

    int topElement = st.top();
    st.pop();

    sortStack(st);             // sort the remaining stack

    insertSorted(st, topElement); // insert top in correct sorted position
}

int main() {
    stack<int> st;
    st.push(3);
    st.push(1);
    st.push(4);
    st.push(2);

    sortStack(st);

    while (!st.empty()) {
        cout << st.top() << " ";
        st.pop();
    }
    // Output: 4 3 2 1

    return 0;
}
```

---

## Complexity Analysis

### Time Complexity — O(n²)

- `sortStack` makes **n** recursive calls (one per element).
- Each call invokes `insertSorted`, which in the worst case traverses the entire already-sorted portion.
- For element at depth `k`, insertSorted does up to `k` comparisons.

```
Total work = 0 + 1 + 2 + ... + (n-1) = n(n-1)/2 = O(n²)
```

This mirrors **Insertion Sort** — and for the same reason.

### Space Complexity — O(n)

- No auxiliary stack or array is used.
- The recursion call stack holds at most `n` frames (one per element) during the unwinding phase.
- `insertSorted` adds up to `n` more frames on top, but these are sequential, not simultaneous.
- Overall: **O(n) recursive stack space**.

---

## Key Insight

> The **recursion call stack itself** acts as the temporary storage.

When `sortStack` pops an element and recurses, that element lives in the function's local variable — on the system call stack — until the recursion returns. This is equivalent to using a second stack, but it's implicit. No extra data structure is declared; the language runtime provides the auxiliary storage through the call stack.

---

## Visual Summary

```
sortStack([2,4,1,3])
│
├── pop 2 → sortStack([4,1,3])
│           │
│           ├── pop 4 → sortStack([1,3])
│           │           │
│           │           ├── pop 1 → sortStack([3])
│           │           │           │
│           │           │           ├── pop 3 → sortStack([])  ← base
│           │           │           └── insertSorted([], 3)   → [3]
│           │           └── insertSorted([3], 1)              → [3,1]
│           └── insertSorted([3,1], 4)                        → [4,3,1]
└── insertSorted([4,3,1], 2)                                  → [4,3,2,1]
```