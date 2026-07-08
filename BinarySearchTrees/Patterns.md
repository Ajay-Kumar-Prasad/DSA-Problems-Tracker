# Binary Search Tree Patterns — Complete Reference

> "Left smaller, right larger." One ordering rule, ~20 recurring patterns, and an entire industry of interview questions built on top of it. Recognize the pattern, and the implementation follows a template.

**The one meta-question that unlocks almost every BST problem:**
> *Can I use the ordering to eliminate one whole side of the tree, or do I need to visit every node anyway?*

- If ordering lets you skip a subtree → **O(h) pattern** (Search, Insert, Delete, Floor/Ceil, LCA)
- If you need every node but in sorted sequence → **Inorder pattern** (Kth Smallest, Validate, Recover, Convert)
- If you're combining two BSTs or building one from scratch → **Construction / Merge pattern**

```cpp
struct Node {
    int val;
    Node *left, *right;
    Node(int v) : val(v), left(nullptr), right(nullptr) {}
};
```

---

## Table of Contents

1. [Basic BST Traversal](#1-basic-bst-traversal)
2. [BST Search](#2-bst-search)
3. [Find Min / Max](#3-find-min--max)
4. [BST Insert](#4-bst-insert)
5. [Delete Node](#5-delete-node)
6. [Inorder Traversal (Sorted Property)](#6-inorder-traversal-sorted-property)
7. [Reverse Inorder](#7-reverse-inorder)
8. [Validate BST](#8-validate-bst)
9. [Min-Max Boundary (Construction Bounds)](#9-min-max-boundary)
10. [Lowest Common Ancestor](#10-lowest-common-ancestor)
11. [Successor / Predecessor](#11-successor--predecessor)
12. [Floor and Ceil](#12-floor-and-ceil)
13. [Kth Smallest / Largest](#13-kth-smallest--largest)
14. [Morris Traversal](#14-morris-traversal)
15. [Recover Swapped BST](#15-recover-swapped-bst)
16. [BST Iterator](#16-bst-iterator)
17. [Range Query](#17-range-query)
18. [Trim BST](#18-trim-bst)
19. [Convert BST (Greater Tree)](#19-convert-bst-greater-tree)
20. [Two Sum in BST](#20-two-sum-in-bst)
21. [Build BST from Traversal / Sorted Array](#21-build-bst-from-traversal--sorted-array)
22. [Largest BST in Binary Tree](#22-largest-bst-in-binary-tree)
23. [Merge BSTs](#23-merge-bsts)
24. [Balance a BST](#24-balance-a-bst)
25. [Master Complexity Cheat Sheet](#master-complexity-cheat-sheet)
26. [Keyword → Pattern Lookup Table](#keyword--pattern-lookup-table)
27. [60-Second Revision Sheet](#60-second-revision-sheet)

---

## Pattern Roadmap

| Pattern | Sample Questions | Difficulty |
|---|---|---|
| Basic Traversal | Count nodes, Sum, Symmetry | Easy |
| Search | Search BST | Easy |
| Min/Max | Find Min/Max in BST | Easy |
| Insert | Insert into BST | Easy |
| Delete | Delete Node in BST | Medium |
| Inorder | Kth Smallest, BST Iterator | Easy-Medium |
| Reverse Inorder | Kth Largest, Greater Tree | Medium |
| Validate | Validate BST | Medium |
| Min-Max Bounds | Construct BST from Preorder | Medium |
| LCA | Lowest Common Ancestor BST | Medium |
| Successor/Predecessor | Inorder Successor/Predecessor | Medium |
| Floor/Ceil | Floor and Ceil in BST | Medium |
| Kth Smallest/Largest | Kth Smallest/Largest Element | Medium |
| Morris | O(1) space inorder | Hard |
| Recover BST | Two nodes swapped | Hard |
| BST Iterator | next()/hasNext() | Medium |
| Range Query | Range Sum BST | Medium |
| Trim BST | Trim outside [L,R] | Medium |
| Convert BST | BST to Greater Tree | Medium |
| Two Sum | Two Sum IV | Medium |
| Build BST | From preorder / sorted array | Medium-Hard |
| Largest BST | Largest BST in Binary Tree | Hard |
| Merge BSTs | All Elements in Two BSTs | Hard |
| Balance BST | Balance a BST | Medium |

---

## 1. Basic BST Traversal

**Questions:** Count nodes, Sum of nodes, Maximum depth, Check symmetry.

### How to identify
The question needs information about the whole tree, and **does not** care about the BST ordering property — it would be asked identically on a plain binary tree.

### Intuition
Ignore the BST rule entirely. Treat it as an ordinary binary tree and run a standard DFS.

### Code
```cpp
void dfs(Node* root, int& count, int& sum) {
    if (!root) return;
    count++;
    sum += root->val;
    dfs(root->left, count, sum);
    dfs(root->right, count, sum);
}
```

### Complexity
Time: O(n) — Space: O(h)

### Hint
If the question would make just as much sense on a non-BST binary tree, don't overthink it — it belongs here, not in a "BST-specific" pattern.

---

## 2. BST Search

**Questions:** Search in a BST, Search BST.

### How to identify
Keywords: *search, find, exists, locate*.

### Intuition
The ordering rule lets you discard one whole subtree at every step:
```
target < node->val  → answer can only be in the left subtree
target > node->val  → answer can only be in the right subtree
target == node->val → found
```
Never search both sides — that would waste the entire benefit of the BST.

### Code
```cpp
Node* searchBST(Node* root, int target) {
    while (root) {
        if (root->val == target) return root;
        root = (target < root->val) ? root->left : root->right;
    }
    return nullptr;
}
```

### Complexity
Average: O(log n) — Worst (skewed tree): O(n)
Space: O(1) iterative, O(h) recursive.

---

## 3. Find Min / Max

**Questions:** Find Min/Max in BST.

### How to identify
Keywords: *minimum, maximum, smallest value, largest value*.

### Intuition
The structure hands you the answer directly — no comparisons needed:
```
Minimum → keep going left until left == NULL
Maximum → keep going right until right == NULL
```

### Code
```cpp
Node* findMin(Node* root) {
    while (root->left) root = root->left;
    return root;
}
Node* findMax(Node* root) {
    while (root->right) root = root->right;
    return root;
}
```

### Complexity
Time: O(h) — Space: O(1)

---

## 4. BST Insert

**Questions:** Insert into BST, Insert a given node in BST.

### How to identify
The question asks to add a new value while preserving BST order.

### Intuition
Walk down exactly like `search`, following the branch the value belongs to. When you hit `nullptr`, that's the leaf position — insert there. **A new node always becomes a leaf**, it never displaces an existing node.

### Code
```cpp
Node* insertIntoBST(Node* root, int val) {
    if (!root) return new Node(val);
    if (val < root->val) root->left  = insertIntoBST(root->left, val);
    else                 root->right = insertIntoBST(root->right, val);
    return root;
}
```

### Complexity
Time: O(h) — Space: O(h) recursive / O(1) iterative

---

## 5. Delete Node

**Questions:** Delete Node in BST, Delete a node in BST.

### How to identify
Keywords: *delete, remove node*.

### Intuition — three cases
```
Case 1 — Leaf:           just remove it, return NULL.
Case 2 — One child:      return that child in its place.
Case 3 — Two children:   replace value with inorder successor
                          (smallest node in right subtree),
                          then delete the successor from the right subtree.
```
The successor swap is safe specifically **because** it's the smallest value greater than everything in the left subtree and smaller than everything remaining in the right subtree — BST order is preserved.

### Code
```cpp
Node* deleteNode(Node* root, int key) {
    if (!root) return nullptr;
    if (key < root->val) {
        root->left = deleteNode(root->left, key);
    } else if (key > root->val) {
        root->right = deleteNode(root->right, key);
    } else {
        // found the node to delete
        if (!root->left)  return root->right;
        if (!root->right) return root->left;
        // two children: find inorder successor (min of right subtree)
        Node* succ = root->right;
        while (succ->left) succ = succ->left;
        root->val = succ->val;
        root->right = deleteNode(root->right, succ->val);
    }
    return root;
}
```

### Complexity
Time: O(h) — Space: O(h)

---

## 6. Inorder Traversal (Sorted Property)

**Questions:** Kth Smallest, BST Iterator, Increasing Order BST.

### How to identify
Keywords: *sorted, kth smallest, increasing order*.

### Intuition
This is the single most important BST fact:
```
Inorder(BST) = Left → Root → Right = strictly sorted sequence
```
Any problem that needs sorted access to a BST's values should immediately trigger "just inorder-traverse it."

### Code
```cpp
void inorder(Node* root, vector<int>& out) {
    if (!root) return;
    inorder(root->left, out);
    out.push_back(root->val);
    inorder(root->right, out);
}
```

### Complexity
Time: O(n) — Space: O(h)

---

## 7. Reverse Inorder

**Questions:** Kth Largest, Convert BST to Greater Tree.

### How to identify
Keywords: *largest, kth largest, descending*.

### Intuition
Flip the traversal order:
```
Right → Root → Left = strictly descending sequence
```

### Code
```cpp
void reverseInorder(Node* root, vector<int>& out) {
    if (!root) return;
    reverseInorder(root->right, out);
    out.push_back(root->val);
    reverseInorder(root->left, out);
}
```

### Complexity
Time: O(n) — Space: O(h)

---

## 8. Validate BST

**Questions:** Validate Binary Search Tree, Check if a tree is a BST.

### How to identify
"Is this tree a valid BST?"

### Intuition
**Common mistake:** comparing only a node against its immediate children. That misses violations from grandparents:
```
      10
    /    \
   5      20
        /
      15      <-- 15 > 10, but it's nested under 20, so a naive
                  parent-only check would miss that 15 must be > 10
```
**Correct idea:** every node carries a valid `(min, max)` range inherited from its ancestors.

### Code
```cpp
bool validate(Node* node, long long minVal, long long maxVal) {
    if (!node) return true;
    if (node->val <= minVal || node->val >= maxVal) return false;
    return validate(node->left, minVal, node->val) &&
           validate(node->right, node->val, maxVal);
}
bool isValidBST(Node* root) {
    return validate(root, LLONG_MIN, LLONG_MAX);
}
```

### Complexity
Time: O(n) — Space: O(h)

---

## 9. Min-Max Boundary

**Questions:** Construct BST from Preorder, (generalized) Validate BST.

### How to identify
The problem needs each recursive call to know the **valid range** of values it's allowed to place.

### Intuition
Same `(low, high)` bound-passing idea as validation, but used to **build** rather than check. While consuming the preorder array, each subtree only accepts values that fit its inherited bound.

### Code
```cpp
int idx = 0;
Node* buildFromPreorder(vector<int>& preorder, int bound) {
    if (idx == preorder.size() || preorder[idx] > bound) return nullptr;
    Node* root = new Node(preorder[idx++]);
    root->left  = buildFromPreorder(preorder, root->val);
    root->right = buildFromPreorder(preorder, bound);
    return root;
}
// call as buildFromPreorder(preorder, INT_MAX)
```

### Complexity
Time: O(n) — Space: O(h)

---

## 10. Lowest Common Ancestor

**Questions:** Lowest Common Ancestor of a BST.

### How to identify
Two nodes given, need their common ancestor — and it's specifically a BST (not a generic tree).

### Intuition
Ordering tells you which direction the split happens, no need to search both sides:
```
both a, b < node   → LCA is in left subtree
both a, b > node   → LCA is in right subtree
split (one smaller, one larger, or one equals node) → node itself is the LCA
```
```
      8
    /   \
   4     12
  / \   /  \
 1   6 10   15

LCA(6, 10): 6 < 8 and 10 > 8 → split → answer is 8
```

### Code
```cpp
Node* lowestCommonAncestor(Node* root, Node* a, Node* b) {
    while (root) {
        if (a->val < root->val && b->val < root->val) root = root->left;
        else if (a->val > root->val && b->val > root->val) root = root->right;
        else return root;   // split point (or one equals root)
    }
    return nullptr;
}
```

### Complexity
Time: O(h) — Space: O(1) iterative

---

## 11. Successor / Predecessor

**Questions:** Inorder Successor in BST, Inorder Predecessor in BST.

### How to identify
Keywords: *next greater, next smaller*.

### Intuition
```
Successor  (next greater) = smallest value larger than target
Predecessor (next smaller) = largest value smaller than target

If node has a right child  → successor = leftmost node of right subtree
If node has no right child → successor = the last ancestor for which
                              we went left to reach this node
```
(Predecessor is the mirror: use the left subtree / last ancestor we went right from.)

### Code
```cpp
Node* inorderSuccessor(Node* root, Node* p) {
    Node* succ = nullptr;
    while (root) {
        if (p->val < root->val) {
            succ = root;          // candidate ancestor
            root = root->left;
        } else {
            root = root->right;
        }
    }
    return succ;
}

Node* inorderPredecessor(Node* root, Node* p) {
    Node* pred = nullptr;
    while (root) {
        if (p->val > root->val) {
            pred = root;
            root = root->right;
        } else {
            root = root->left;
        }
    }
    return pred;
}
```

### Complexity
Time: O(h) — Space: O(1)

---

## 12. Floor and Ceil

**Questions:** Floor and Ceil in a BST, Floor in a Binary Search Tree.

### How to identify
Keywords: *floor, ceil, closest value not exceeding / not below*.

### Intuition
Same descent as Search, but instead of giving up when the exact value isn't found, remember the best candidate seen so far:
```
Floor (largest value <= target):
    node->val <= target → record as candidate, move right (look for something closer)
    node->val >  target → move left

Ceil (smallest value >= target):
    node->val >= target → record as candidate, move left (look for something closer)
    node->val <  target → move right
```

### Code
```cpp
int findFloor(Node* root, int target) {
    int floorVal = -1;
    while (root) {
        if (root->val == target) return root->val;
        if (root->val < target) { floorVal = root->val; root = root->right; }
        else                    { root = root->left; }
    }
    return floorVal;
}

int findCeil(Node* root, int target) {
    int ceilVal = -1;
    while (root) {
        if (root->val == target) return root->val;
        if (root->val > target) { ceilVal = root->val; root = root->left; }
        else                    { root = root->right; }
    }
    return ceilVal;
}
```

### Complexity
Time: O(h) — Space: O(1)

---

## 13. Kth Smallest / Largest

**Questions:** Kth Smallest Element in BST, Kth Largest, median/rank problems.

### How to identify
Keywords: *kth smallest, kth largest, median, rank*.

### Intuition
Inorder gives sorted order — don't build the whole array, just count as you go and stop at k.

### Code
```cpp
int count = 0, result = -1;
void kthSmallest(Node* root, int k) {
    if (!root || result != -1) return;
    kthSmallest(root->left, k);
    count++;
    if (count == k) { result = root->val; return; }
    kthSmallest(root->right, k);
}
```

### Complexity
Time: O(h + k) best case, O(n) worst case — Space: O(h)

---

## 14. Morris Traversal

**Questions:** Morris Inorder Traversal, Recover BST (O(1) space variant), Kth Smallest (O(1) space variant).

### How to identify
Keywords: *no recursion, no stack, O(1) space inorder*.

### Intuition
Temporarily thread the tree: the inorder predecessor's `right` pointer is pointed back at the current node, letting you "return" without a stack. Remove the thread on the way out.

### Code
```cpp
vector<int> morrisInorder(Node* root) {
    vector<int> res;
    Node* curr = root;
    while (curr) {
        if (!curr->left) {
            res.push_back(curr->val);
            curr = curr->right;
        } else {
            Node* pred = curr->left;
            while (pred->right && pred->right != curr) pred = pred->right;
            if (!pred->right) {
                pred->right = curr;     // create thread
                curr = curr->left;
            } else {
                pred->right = nullptr;  // remove thread (restore original tree)
                res.push_back(curr->val);
                curr = curr->right;
            }
        }
    }
    return res;
}
```

### Complexity
Time: O(n) — Space: O(1)

### Edge case
Always remove the thread you created — leaving it behind corrupts the tree structure.

---

## 15. Recover Swapped BST

**Questions:** Recover Binary Search Tree, Correct BST with two nodes swapped.

### How to identify
"Exactly two nodes were swapped by mistake."

### Intuition
A correct BST's inorder sequence is sorted. Swapping two nodes creates exactly one or two **inversions** (places where a value is followed by something smaller):
```
Inorder: 1  4  3  2  5
Violations: 4>3 (first), 3>2 (second)
first  = the node before the first violation (4)
last   = the node after the second violation  (2)
```
Track `first`, `middle` (only used for adjacent swaps), and `last` while inorder-traversing, then swap `first->val` and `last->val`.

### Code
```cpp
Node *first = nullptr, *middle = nullptr, *last = nullptr, *prev = nullptr;

void inorderScan(Node* root) {
    if (!root) return;
    inorderScan(root->left);
    if (prev && prev->val > root->val) {
        if (!first) { first = prev; middle = root; }
        else        { last = root; }
    }
    prev = root;
    inorderScan(root->right);
}

void recoverTree(Node* root) {
    inorderScan(root);
    if (first && last) swap(first->val, last->val);
    else if (first && middle) swap(first->val, middle->val); // adjacent swap
}
```

### Complexity
Time: O(n) — Space: O(h)

---

## 16. BST Iterator

**Questions:** BST Iterator (`next()`, `hasNext()`).

### How to identify
Need controlled, on-demand sorted access rather than a full traversal upfront.

### Intuition
Maintain a stack holding the current left-spine. `next()` pops the top, then pushes the entire left-spine of its right child (mirrors what a normal inorder traversal would visit next).

### Code
```cpp
class BSTIterator {
    stack<Node*> st;
    void pushLeft(Node* node) {
        while (node) { st.push(node); node = node->left; }
    }
public:
    BSTIterator(Node* root) { pushLeft(root); }
    bool hasNext() { return !st.empty(); }
    int next() {
        Node* node = st.top(); st.pop();
        pushLeft(node->right);
        return node->val;
    }
};
```

### Complexity
Time: O(1) amortized per `next()`, O(h) for initialization — Space: O(h)

---

## 17. Range Query

**Questions:** Range Sum of BST.

### How to identify
"Values between L and R."

### Intuition
Prune entire branches that can't possibly contain valid values:
```
node->val < L → left subtree is entirely too small, only recurse right
node->val > R → right subtree is entirely too large, only recurse left
otherwise     → node counts, recurse both sides
```

### Code
```cpp
int rangeSumBST(Node* root, int low, int high) {
    if (!root) return 0;
    if (root->val < low)  return rangeSumBST(root->right, low, high);
    if (root->val > high) return rangeSumBST(root->left, low, high);
    return root->val + rangeSumBST(root->left, low, high)
                      + rangeSumBST(root->right, low, high);
}
```

### Complexity
Average: O(log n + k) — Worst: O(n), where k = nodes in range

---

## 18. Trim BST

**Questions:** Trim a Binary Search Tree.

### How to identify
"Delete everything outside [L, R], return the remaining valid BST."

### Intuition
```
node->val < L  → the entire left subtree is too small — discard it,
                 and the answer is whatever trimming the right subtree gives
node->val > R  → mirror case, discard entire right subtree
otherwise      → keep node, trim both children recursively
```

### Code
```cpp
Node* trimBST(Node* root, int low, int high) {
    if (!root) return nullptr;
    if (root->val < low)  return trimBST(root->right, low, high);
    if (root->val > high) return trimBST(root->left, low, high);
    root->left  = trimBST(root->left, low, high);
    root->right = trimBST(root->right, low, high);
    return root;
}
```

### Complexity
Time: O(n) — Space: O(h)

---

## 19. Convert BST (Greater Tree)

**Questions:** Convert BST to Greater Tree.

### How to identify
Keywords: *replace each value with the sum of all greater values, greater tree*.

### Intuition
Reverse inorder (Right → Root → Left) visits nodes in **descending** order — maintain a running sum as you go and add it into each node.

### Code
```cpp
int runningSum = 0;
void convertBST(Node* root) {
    if (!root) return;
    convertBST(root->right);
    runningSum += root->val;
    root->val = runningSum;
    convertBST(root->left);
}
```

### Complexity
Time: O(n) — Space: O(h)

---

## 20. Two Sum in BST

**Questions:** Two Sum IV - Input is a BST.

### How to identify
"Find two nodes whose values sum to k."

### Intuition — three approaches
```
1. HashSet + DFS:        O(n) time, O(n) space — check (k - val) as you go
2. Inorder + two pointers: build sorted array, standard two-pointer scan
3. Two BST Iterators:     one forward (smallest-first), one backward
                          (largest-first) — meet in the middle, O(h) space
```

### Code (two-iterator approach — most elegant)
```cpp
bool findTarget(Node* root, int k) {
    BSTIterator forward(root);   // ascending
    // build a mirrored "reverse" iterator the same way but pushing right-first
    // then advance whichever pointer needs to grow/shrink the sum, like
    // the classic sorted-array two-pointer pattern.
    // (conceptually identical to two-pointer on the inorder sequence)
    vector<int> vals;
    inorder(root, vals);
    int i = 0, j = (int)vals.size() - 1;
    while (i < j) {
        int sum = vals[i] + vals[j];
        if (sum == k) return true;
        else if (sum < k) i++;
        else j--;
    }
    return false;
}
```

### Complexity
Time: O(n) — Space: O(n) for the array approach, O(h) with true dual iterators

---

## 21. Build BST from Traversal / Sorted Array

**Questions:** Construct BST from Preorder Traversal, Convert Sorted Array to BST.

### How to identify
"Build/construct a BST from ..."

### Intuition
```
From sorted array → pick the middle element as root (guarantees balance),
                     recurse on left half and right half.
From preorder     → root is always first; use the Min-Max Boundary
                     pattern (Pattern 9) to decide where each subtree ends.
```

### Code (sorted array → balanced BST)
```cpp
Node* sortedArrayToBST(vector<int>& nums, int lo, int hi) {
    if (lo > hi) return nullptr;
    int mid = lo + (hi - lo) / 2;
    Node* root = new Node(nums[mid]);
    root->left  = sortedArrayToBST(nums, lo, mid - 1);
    root->right = sortedArrayToBST(nums, mid + 1, hi);
    return root;
}
```

### Complexity
Time: O(n) — Space: O(log n) recursion (balanced)

---

## 22. Largest BST in Binary Tree

**Questions:** Largest BST Subtree (in a general, possibly-invalid binary tree).

### How to identify
Input is a **plain binary tree** (not guaranteed to be a BST); find the largest subtree that *is* a valid BST.

### Intuition
Bottom-up DP: each node returns four pieces of information to its parent —
```
isBST   : is this subtree itself a valid BST?
size    : number of nodes in it
minVal  : minimum value in it
maxVal  : maximum value in it
```
A node forms a valid BST with its children only if both children are valid BSTs **and** `left.max < node->val < right.min`.

### Code
```cpp
struct Info { bool isBST; int size, minVal, maxVal; };
int best = 0;

Info largestBSTHelper(Node* root) {
    if (!root) return {true, 0, INT_MAX, INT_MIN};
    Info L = largestBSTHelper(root->left);
    Info R = largestBSTHelper(root->right);

    if (L.isBST && R.isBST && L.maxVal < root->val && root->val < R.minVal) {
        int size = L.size + R.size + 1;
        best = max(best, size);
        return {true, size, min(L.minVal, root->val), max(R.maxVal, root->val)};
    }
    return {false, 0, 0, 0};   // not a BST, size doesn't matter upward
}
```

### Complexity
Time: O(n) — Space: O(h)

---

## 23. Merge BSTs

**Questions:** All Elements in Two BSTs, Merge 2 BST's.

### How to identify
"Two BSTs, produce a single sorted result."

### Intuition
```
Approach 1: inorder both into two sorted arrays, then merge (like merge sort's merge step)
Approach 2: two BST iterators, always advance whichever is currently smaller
```

### Code
```cpp
vector<int> getAllElements(Node* root1, Node* root2) {
    vector<int> a, b, merged;
    inorder(root1, a);
    inorder(root2, b);
    int i = 0, j = 0;
    while (i < a.size() && j < b.size())
        merged.push_back(a[i] <= b[j] ? a[i++] : b[j++]);
    while (i < a.size()) merged.push_back(a[i++]);
    while (j < b.size()) merged.push_back(b[j++]);
    return merged;
}
```

### Complexity
Time: O(m + n) — Space: O(m + n)

---

## 24. Balance a BST

**Questions:** Balance a Binary Search Tree.

### How to identify
"Rebalance this BST" — height is skewed but values are still valid.

### Intuition
```
Inorder traverse → get a sorted array (throws away the skewed shape)
Rebuild using the sorted-array-to-balanced-BST recipe (Pattern 21)
```

### Code
```cpp
Node* balanceBST(Node* root) {
    vector<int> vals;
    inorder(root, vals);
    return sortedArrayToBST(vals, 0, (int)vals.size() - 1);
}
```

### Complexity
Time: O(n) — Space: O(n)

---

## Master Complexity Cheat Sheet

| Pattern | Time | Space |
|---|---|---|
| Basic Traversal | O(n) | O(h) |
| Search | O(log n) avg / O(n) worst | O(1) |
| Min/Max | O(h) | O(1) |
| Insert | O(h) | O(h) |
| Delete | O(h) | O(h) |
| Inorder | O(n) | O(h) |
| Reverse Inorder | O(n) | O(h) |
| Validate | O(n) | O(h) |
| Min-Max Boundary | O(n) | O(h) |
| LCA | O(h) | O(1) |
| Successor/Predecessor | O(h) | O(1) |
| Floor/Ceil | O(h) | O(1) |
| Kth Smallest/Largest | O(h+k) avg / O(n) worst | O(h) |
| Morris | O(n) | O(1) |
| Recover BST | O(n) | O(h) |
| BST Iterator | O(1) amortized | O(h) |
| Range Query | O(log n + k) avg / O(n) worst | O(h) |
| Trim BST | O(n) | O(h) |
| Convert (Greater Tree) | O(n) | O(h) |
| Two Sum | O(n) | O(n) or O(h) |
| Build BST | O(n) | O(log n)–O(n) |
| Largest BST | O(n) | O(h) |
| Merge BSTs | O(m+n) | O(m+n) |
| Balance BST | O(n) | O(n) |

---

## Keyword → Pattern Lookup Table

| If the question says... | Think... |
|---|---|
| Search / Find | BST Search |
| Minimum / Maximum value | Find Min/Max |
| Insert | BST Insert |
| Delete | Delete Node (3 cases) |
| Floor / Ceil | Floor & Ceil Descent |
| Kth smallest | Inorder + Counter |
| Kth largest | Reverse Inorder + Counter |
| Next greater | Successor |
| Next smaller | Predecessor |
| Is this a valid BST | Min-Max Validation |
| Lowest common ancestor | BST-directed LCA |
| Sorted order | Inorder |
| Descending order | Reverse Inorder |
| Range [L,R] | Pruned Range Query |
| Remove everything outside range | Trim BST |
| Greater Tree / running sum | Reverse Inorder + Running Sum |
| Two nodes summing to k | HashSet / Two Iterators |
| Build BST from preorder/array | Bounds / Sorted-Array Recursion |
| Two nodes swapped | Inorder Inversions |
| O(1) space traversal | Morris |
| Largest BST in a binary tree | Bottom-up DP (isBST, size, min, max) |
| Merge two BSTs | Merge sorted inorder sequences |
| Rebalance a BST | Inorder → rebuild balanced |

---

## 60-Second Revision Sheet

1. **Search/Insert** — one direction only, never both: `target < node → left, else → right`.
2. **Min/Max** — leftmost = min, rightmost = max, no comparisons needed.
3. **Delete** — leaf → null; one child → promote it; two children → copy inorder successor's value, then delete successor.
4. **Inorder = Sorted** — the single fact behind Kth Smallest, BST Iterator, Validate, Recover.
5. **Reverse Inorder = Descending** — used for Kth Largest and Greater Tree running sums.
6. **Validate** — pass `(min, max)` bounds down, never compare only to the immediate parent.
7. **LCA** — first node where the two targets "split" across left/right (or one equals the node).
8. **Successor/Predecessor** — has right child → leftmost of right subtree; else → last ancestor you turned away from.
9. **Floor/Ceil** — descend like search, but remember the best candidate before moving on.
10. **Kth Smallest** — inorder with an early-stop counter, don't materialize the whole array.
11. **Morris** — thread the predecessor's right pointer, always undo it on the way out.
12. **Recover BST** — inorder scan tracking `first`/`middle`/`last` around each inversion.
13. **Range/Trim** — if a node is outside the bound, you can discard one entire side immediately.
14. **Largest BST Subtree** — bottom-up returns `{isBST, size, min, max}`; check `left.max < val < right.min`.
15. **Build from sorted array** — always pick the middle element as root for guaranteed balance.

**The single question to ask before coding any BST problem:**
> *Does the ordering property let me skip a whole subtree, or do I need every node in sorted sequence anyway?*