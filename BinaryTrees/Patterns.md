# Binary Tree Patterns — Complete Reference

> 95% of binary tree interview questions collapse into ~12-15 core patterns. Recognize the pattern, and the implementation follows a template.

**The one meta-question that unlocks almost every tree problem:**
> *What information should each node return to its parent?*

- Returns a traversal order → **Traversal**
- Returns a height → **Height / Diameter / Balance**
- Returns a gain/score → **Tree DP**
- Returns a boolean → **Comparison / Validation**
- Returns a path → **Backtracking**
- Updates a global variable, returns nothing useful → **Aggregation (diameter, max path sum)**

---

## Table of Contents

1. [Traversal](#1-traversal)
2. [Height / Information Gathering](#2-height--information-gathering)
3. [Tree DP (Bottom-up)](#3-tree-dp-bottom-up)
4. [Tree Comparison](#4-tree-comparison)
5. [BFS Level Processing](#5-bfs-level-processing)
6. [Coordinate Mapping (Vertical/Top/Bottom View)](#6-coordinate-mapping)
7. [Boundary Traversal](#7-boundary-traversal)
8. [Root to Node Path](#8-root-to-node-path)
9. [Lowest Common Ancestor](#9-lowest-common-ancestor)
10. [Tree → Graph Conversion](#10-tree--graph-conversion)
11. [Complete Binary Tree Optimization](#11-complete-binary-tree-optimization)
12. [Tree Construction](#12-tree-construction)
13. [Serialization / Deserialization](#13-serialization--deserialization)
14. [Morris Traversal](#14-morris-traversal)
15. [Flatten Binary Tree](#15-flatten-binary-tree)
16. [Master Complexity Cheat Sheet](#master-complexity-cheat-sheet)
17. [Keyword → Pattern Lookup Table](#keyword--pattern-lookup-table)
18. [60-Second Revision Sheet](#60-second-revision-sheet)

---

## Pattern Roadmap

| Pattern | Sample Questions | Difficulty |
|---|---|---|
| Traversal | Pre/In/Post/Level/Morris | Easy |
| DFS Info Gathering | Height, Balanced, Diameter | Easy-Medium |
| Bottom-up DP | Max Path Sum, Children Sum | Medium |
| Tree Comparison | Same Tree, Symmetric | Easy |
| BFS Level Processing | Zigzag, Left/Right View | Medium |
| Coordinate Mapping | Vertical, Top, Bottom View | Medium |
| Path Problems | Root→Leaf, LCA | Medium |
| Parent Graph Conversion | Distance K, Burn Tree | Hard |
| Complete Tree Optimization | Count Nodes | Medium |
| Tree Construction | Build from Traversals | Hard |
| Serialization | Serialize/Deserialize | Hard |
| Tree Modification | Flatten | Medium |

```cpp
struct Node {
    int val;
    Node *left, *right;
    Node(int v) : val(v), left(nullptr), right(nullptr) {}
};
```

---

## 1. Traversal

**Questions:** Preorder, Inorder, Postorder, Iterative versions, Morris, One-Pass traversal

### How to identify
Question asks to: visit every node, print/collect values, convert the tree into a sequence.

### Intuition
Every tree problem starts as:
```
process(root) → process(left) → process(right)
```
Only the *order* changes.

```
        A
      /   \
     B     C
    / \
   D   E

Preorder  (Root→L→R): A B D E C
Inorder   (L→Root→R): D B E A C
Postorder (L→R→Root): D E B C A
```

### Code
```cpp
void preorder(Node* root, vector<int>& out) {
    if (!root) return;
    out.push_back(root->val);
    preorder(root->left, out);
    preorder(root->right, out);
}

void inorder(Node* root, vector<int>& out) {
    if (!root) return;
    inorder(root->left, out);
    out.push_back(root->val);
    inorder(root->right, out);
}

void postorder(Node* root, vector<int>& out) {
    if (!root) return;
    postorder(root->left, out);
    postorder(root->right, out);
    out.push_back(root->val);
}
```

### Complexity
| | Time | Space |
|---|---|---|
| Recursive | O(n) | O(h) |
| Balanced | O(n) | O(log n) |
| Skewed | O(n) | O(n) |

### Edge cases
Empty tree, single node, left-skewed, right-skewed.

### Hint
"Process children before parent" → **Postorder**.

---

## 2. Height / Information Gathering

**Questions:** Maximum Depth, Balanced Tree Check, Diameter

### How to identify
Keywords: *maximum, minimum, height, longest, depth, balance*.

### Intuition
Each node asks its children "tell me your height," computes its own, and passes it up. Information flows **bottom → top**.

### Height
```cpp
int height(Node* root) {
    if (!root) return 0;
    return 1 + max(height(root->left), height(root->right));
}
```
O(n) time.

### Balanced Tree
Naive (recompute height at every node) = O(n²). Optimized: return height, or -1 the moment imbalance is found.
```cpp
int checkBalanced(Node* root) {
    if (!root) return 0;
    int lh = checkBalanced(root->left);
    if (lh == -1) return -1;
    int rh = checkBalanced(root->right);
    if (rh == -1) return -1;
    if (abs(lh - rh) > 1) return -1;
    return 1 + max(lh, rh);
}
bool isBalanced(Node* root) { return checkBalanced(root) != -1; }
```
O(n) time, O(h) space.

### Diameter
```cpp
int diameter = 0;
int diameterDFS(Node* root) {
    if (!root) return 0;
    int lh = diameterDFS(root->left);
    int rh = diameterDFS(root->right);
    diameter = max(diameter, lh + rh);   // path through this node
    return 1 + max(lh, rh);              // height passed up
}
```
O(n) time.

### Trick
Need answer from children **and** need height → solve both in **one DFS**.

---

## 3. Tree DP (Bottom-up)

**Questions:** Maximum Path Sum, Children Sum Property

### How to identify
Keywords: *maximum, best, optimal, profit, gain, contribution*.

### Intuition
Every child contributes something; the parent decides to **take it or ignore it** (drop negative contributions).

### Maximum Path Sum
```cpp
int maxSum = INT_MIN;   // never initialize to 0 — all-negative trees break that

int maxPathDFS(Node* root) {
    if (!root) return 0;
    int left  = max(0, maxPathDFS(root->left));
    int right = max(0, maxPathDFS(root->right));
    maxSum = max(maxSum, root->val + left + right);  // path through this node
    return root->val + max(left, right);              // one branch returned up
}
```
O(n) time, O(h) space.

### Edge case
All-negative tree → initialize the global answer with `INT_MIN`, never `0`.

---

## 4. Tree Comparison

**Questions:** Same Tree, Symmetric Tree

### How to identify
Keywords: *same, mirror, equal, identical*.

### Same Tree
```cpp
bool isSame(Node* p, Node* q) {
    if (!p && !q) return true;
    if (!p || !q) return false;
    return p->val == q->val &&
           isSame(p->left, q->left) &&
           isSame(p->right, q->right);
}
```

### Symmetric Tree
Cross-compare: `left.left ↔ right.right` and `left.right ↔ right.left`.
```cpp
bool isMirror(Node* a, Node* b) {
    if (!a && !b) return true;
    if (!a || !b) return false;
    return a->val == b->val &&
           isMirror(a->left, b->right) &&
           isMirror(a->right, b->left);
}
bool isSymmetric(Node* root) { return !root || isMirror(root->left, root->right); }
```

Complexity: O(n) time, O(h) space for both.

---

## 5. BFS Level Processing

**Questions:** Level Order, Zigzag Traversal, Left View, Right View

### How to identify
Keywords: *level, breadth, layer, each level*.

### Template
```cpp
vector<vector<int>> levelOrder(Node* root) {
    vector<vector<int>> result;
    if (!root) return result;
    queue<Node*> q;
    q.push(root);
    while (!q.empty()) {
        int size = q.size();
        vector<int> level;
        for (int i = 0; i < size; i++) {
            Node* node = q.front(); q.pop();
            level.push_back(node->val);
            if (node->left)  q.push(node->left);
            if (node->right) q.push(node->right);
        }
        result.push_back(level);
    }
    return result;
}
```

- **Zigzag:** reverse alternate levels (or insert front/back alternately using a deque).
- **Left View:** first node processed at each level.
- **Right View:** last node processed at each level.

Complexity: O(n) time, O(n) space (queue can hold up to n/2 nodes at the widest level).

### Edge cases
Single node, empty tree.

---

## 6. Coordinate Mapping

**Questions:** Vertical Order Traversal, Top View, Bottom View

### How to identify
Keywords: *vertical, horizontal, columns, shadow, projection*.

### Intuition
Assign coordinates: root = `(0,0)`. Left child → `(x-1, y+1)`. Right child → `(x+1, y+1)`. Store in `map<x, map<y, multiset<val>>>`.

```cpp
void verticalDFS(Node* root, int x, int y,
                  map<int, map<int, multiset<int>>>& mp) {
    if (!root) return;
    mp[x][y].insert(root->val);
    verticalDFS(root->left,  x - 1, y + 1, mp);
    verticalDFS(root->right, x + 1, y + 1, mp);
}
```

- **Top View:** first node encountered at each column (BFS ensures topmost).
- **Bottom View:** last node encountered at each column (later overwrites earlier).
- **Vertical Order:** sort by `column → row → value`.

Complexity: O(n log n) — driven by the map.

---

## 7. Boundary Traversal

### How to identify
Keywords: *boundary, perimeter, outside nodes*.

### Intuition
Three parts, stitched together without duplicating leaves:
```
Left Boundary (top→down, excluding leaves)
+ Leaves (left→right)
+ Right Boundary (bottom→up, excluding leaves, reversed)
```

Complexity: O(n) time, O(h) space.

---

## 8. Root to Node Path

**Questions:** Root→Leaf Paths, Path Sum, general LCA prep

### How to identify
The question needs an actual **path**, not just a value.

### Template (classic backtracking)
```cpp
bool findPath(Node* root, int target, vector<int>& path) {
    if (!root) return false;
    path.push_back(root->val);
    if (root->val == target) return true;
    if (findPath(root->left, target, path) ||
        findPath(root->right, target, path)) return true;
    path.pop_back();   // backtrack
    return false;
}
```

Complexity: O(n) time, O(h) space.

---

## 9. Lowest Common Ancestor

### How to identify
Keywords: *nearest ancestor, common parent*.

### Logic
```cpp
Node* lca(Node* root, Node* a, Node* b) {
    if (!root || root == a || root == b) return root;
    Node* left  = lca(root->left, a, b);
    Node* right = lca(root->right, a, b);
    if (left && right) return root;      // a and b found on both sides
    return left ? left : right;          // bubble up whichever side found something
}
```

Complexity: O(n) time, O(h) space.

---

## 10. Tree → Graph Conversion

**Questions:** Distance K from a Node, Burning/Burn Tree, Amount of Time to Burn

### How to identify
Need to move **upward** — but a binary tree only has downward pointers.

### Step 1 — build a parent map
```cpp
void mapParents(Node* root, unordered_map<Node*, Node*>& parent) {
    if (!root) return;
    if (root->left)  parent[root->left] = root;
    if (root->right) parent[root->right] = root;
    mapParents(root->left, parent);
    mapParents(root->right, parent);
}
```

### Step 2 — BFS treating the tree as an undirected graph (left, right, parent)
```cpp
// starting from target node, BFS K levels for Distance K,
// or BFS until queue empties, tracking levels, for Burn Tree time.
```

Complexity: O(n) time, O(n) space.

---

## 11. Complete Binary Tree Optimization

**Questions:** Count Complete Tree Nodes

### How to identify
Problem explicitly states the tree is **complete**.

### Observation
A perfect subtree has `2^h - 1` nodes. Compare left-height vs right-height:
- Equal → subtree is perfect, compute directly.
- Not equal → recurse into both children.

```cpp
int leftHeight(Node* node) {
    int h = 0;
    while (node) { h++; node = node->left; }
    return h;
}
int rightHeight(Node* node) {
    int h = 0;
    while (node) { h++; node = node->right; }
    return h;
}
int countNodes(Node* root) {
    if (!root) return 0;
    int lh = leftHeight(root), rh = rightHeight(root);
    if (lh == rh) return (1 << lh) - 1;
    return 1 + countNodes(root->left) + countNodes(root->right);
}
```

Complexity: O(log² n) time.

---

## 12. Tree Construction

**Questions:** Build tree from Preorder+Inorder, Postorder+Inorder

### How to identify
Given traversal arrays, reconstruct the tree.

### Observation
- Preorder → root comes **first**.
- Postorder → root comes **last**.
- Inorder → splits into `[left subtree | root | right subtree]`.
- Use a hashmap `value → index` to avoid O(n²) lookups.

```cpp
unordered_map<int,int> inMap;

Node* build(vector<int>& preorder, int& preIdx, int inStart, int inEnd) {
    if (inStart > inEnd) return nullptr;
    int rootVal = preorder[preIdx++];
    Node* root = new Node(rootVal);
    int inRoot = inMap[rootVal];
    root->left  = build(preorder, preIdx, inStart, inRoot - 1);
    root->right = build(preorder, preIdx, inRoot + 1, inEnd);
    return root;
}
```

Complexity: O(n) time with hashmap.

---

## 13. Serialization / Deserialization

### How to identify
"Store the tree" and "recover the tree" (design-style question).

### Method — level order with null markers
```cpp
string serialize(Node* root) {
    if (!root) return "N";
    string s = to_string(root->val);
    queue<Node*> q; q.push(root);
    while (!q.empty()) {
        Node* node = q.front(); q.pop();
        if (node->left)  { s += "," + to_string(node->left->val);  q.push(node->left); }
        else s += ",N";
        if (node->right) { s += "," + to_string(node->right->val); q.push(node->right); }
        else s += ",N";
    }
    return s;
}
// Deserialize: read tokens back with a queue, rebuilding left/right in the same order.
```

Complexity: O(n) time, O(n) space.

---

## 14. Morris Traversal

### How to identify
Keywords: *no recursion, no stack, O(1) space*.

### Intuition
Temporarily thread the tree: connect each node's inorder predecessor's right pointer back to itself, traverse, then restore.

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
                pred->right = curr;   // create thread
                curr = curr->left;
            } else {
                pred->right = nullptr; // remove thread (restore)
                res.push_back(curr->val);
                curr = curr->right;
            }
        }
    }
    return res;
}
```

Complexity: O(n) time, **O(1) space**.

### Edge case
Always restore the pointers you threaded — never leave the tree mutated.

---

## 15. Flatten Binary Tree

**Questions:** Flatten Binary Tree to Linked List

### How to identify
"Convert tree → linked list" (usually via `right` pointers, preorder shape).

### Intuition — reverse preorder (Right → Left → Root)
```cpp
Node* prev = nullptr;
void flatten(Node* root) {
    if (!root) return;
    flatten(root->right);
    flatten(root->left);
    root->right = prev;
    root->left = nullptr;
    prev = root;
}
```

Complexity: O(n) time, O(h) space (or O(1) with a Morris-style approach).

---

## Master Complexity Cheat Sheet

| Pattern | Time | Space |
|---|---|---|
| DFS Traversal | O(n) | O(h) |
| BFS | O(n) | O(n) |
| Height | O(n) | O(h) |
| Balanced | O(n) | O(h) |
| Diameter | O(n) | O(h) |
| Max Path Sum | O(n) | O(h) |
| Same Tree | O(n) | O(h) |
| LCA | O(n) | O(h) |
| Distance K | O(n) | O(n) |
| Burn Tree | O(n) | O(n) |
| Vertical Order | O(n log n) | O(n) |
| Top View | O(n log n) | O(n) |
| Bottom View | O(n log n) | O(n) |
| Count Complete Tree | O(log² n) | O(log n) |
| Construct Tree | O(n) | O(n) |
| Serialize | O(n) | O(n) |
| Morris | O(n) | O(1) |
| Flatten | O(n) | O(h) |

---

## Keyword → Pattern Lookup Table

| If the question says... | Think... |
|---|---|
| Visit every node | DFS Traversal |
| Level, breadth, layer | BFS |
| Height, depth, longest | Bottom-up DFS |
| Maximum, best, gain | Tree DP |
| Same, identical, mirror | Tree Comparison |
| Left view, right view, zigzag | Level-order BFS |
| Vertical, top, bottom, column | Coordinate Mapping |
| Root to leaf, path | Backtracking |
| Common ancestor | LCA |
| Distance K, burn, move upward | Parent Map + BFS |
| Complete binary tree | Height optimization |
| Build from traversals | Divide & Conquer |
| Save and reconstruct | Serialization |
| O(1) space traversal | Morris |
| Convert to linked list | Pointer rewiring / Reverse preorder |

---

## 60-Second Revision Sheet

1. **Traversal** — Root position changes: Pre (first), In (middle), Post (last).
2. **Height/Diameter** — One DFS, return height, update a global max on the way.
3. **Tree DP** — `max(0, child)` to discard negative branches; init global with `INT_MIN`.
4. **Comparison** — Same = straight compare; Symmetric = cross compare (`L.left↔R.right`).
5. **BFS Level** — `queue`, snapshot `size = q.size()` before each level loop.
6. **Coordinate Mapping** — `(x,y)` pairs, `map<x,map<y,multiset>>`, sort at the end.
7. **Boundary** — Left boundary + leaves + reversed right boundary, no duplicate leaves.
8. **Root→Node Path** — Push, check, recurse, **pop** (backtrack) if not found.
9. **LCA** — If both sides return non-null, current node is the LCA.
10. **Tree→Graph** — Build a `child→parent` map first, then BFS in all 3 directions.
11. **Complete Tree Count** — Compare left/right height; if equal, use `2^h - 1` directly.
12. **Construction** — Preorder gives root first, Postorder gives root last; use inorder to split; hashmap for O(1) index lookup.
13. **Serialization** — Level order + explicit null markers ("N").
14. **Morris** — Thread via predecessor's right pointer; always undo the thread.
15. **Flatten** — Reverse preorder (Right, Left, Root), rewire with a `prev` pointer.

**The single question to ask before coding any tree problem:**
> *What does each node need to return to its parent — and do I need DFS or BFS to get it?*