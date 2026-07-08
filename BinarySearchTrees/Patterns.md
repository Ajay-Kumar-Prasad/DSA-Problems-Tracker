Binary Search Trees look deceptively simple. "Left smaller, right larger." Then interviews proceed to ask 40 different questions that all secretly use about 10 recurring patterns. Humanity has turned one property into an entire industry of coding interviews. Efficient? Debatable. Predictable? Absolutely.

If you master these patterns instead of memorizing problems, you can solve almost every BST interview question.

---

# 0. Introduction to BST

## Identification

Foundational concept, not a "pattern" by itself — every other pattern in this document depends on it.

## Intuition

A Binary Search Tree is a binary tree with one invariant enforced at **every** node:

```
left subtree  < node

node          < right subtree
```

This single ordering rule is what makes search, insert, delete, and range queries all run in O(h) instead of O(n) — the tree height `h` becomes the budget for almost every operation.

```
      8
    /   \
   4     12
  / \   /  \
 1   6 10   15
```

Everything below — search, insert, delete, kth smallest, LCA, validation — is just a different way of exploiting this one rule.

### TC / SC

Not applicable on its own — depends on the operation performed.

---

# 1. Basic BST Traversal Pattern

## Identification

* Visit every node
* No searching based on value
* Need tree information

Examples

* Count nodes
* Sum of nodes
* Maximum depth
* Check symmetry

## Intuition

Treat BST like a normal binary tree.

Ignore BST property.

Usually DFS.

```cpp
void dfs(TreeNode* root){
    if(!root) return;

    dfs(root->left);

    // Process

    dfs(root->right);
}
```

### TC

O(n)

### SC

O(h)

---

Top Problems

* Maximum Depth
* Minimum Depth
* Same Tree
* Balanced Binary Tree
* Diameter

---

# 2. BST Search Pattern

## Identification

Question asks

* Find x
* Does x exist?
* Locate node

Keywords

> search
>
> find
>
> exists

## Intuition

Use BST ordering.

```
target < node

Go left

target > node

Go right
```

Never search both sides.

```cpp
while(root){

    if(root->val==target)
        return root;

    if(target<root->val)
        root=root->left;
    else
        root=root->right;
}
```

### TC

Average

O(log n)

Worst

O(n)

### SC

Iterative

O(1)

Recursive

O(h)

---

Top Problems

* Search BST
* Insert into BST
* Delete Node in BST

---

# 2b. Find Min / Max in BST

## Identification

Question asks

* Smallest element in BST
* Largest element in BST
* Leftmost / Rightmost node

Keywords

> minimum
>
> maximum
>
> smallest value
>
> largest value

## Intuition

BST ordering guarantees:

```
Minimum → keep going left until left is NULL

Maximum → keep going right until right is NULL
```

No comparisons needed — the structure already tells you where the extremes live.

```cpp
TreeNode* findMin(TreeNode* root){
    while(root->left) root = root->left;
    return root;
}

TreeNode* findMax(TreeNode* root){
    while(root->right) root = root->right;
    return root;
}
```

### TC

O(h)

### SC

O(1)

---

Problems

* Find Min/Max in BST

---

# 3. BST Insert Pattern

## Identification

Question asks

Insert new value.

## Intuition

Search until null.

Insert there.

```
20

     20
       \
       30
```

Insertion always occurs at leaf.

### TC

O(h)

### SC

O(1)

---

Problems

* Insert into BST
* Insert a given node in BST

---

# 4. Delete Node Pattern

One of the most important.

---

## Identification

Delete node.

---

## Cases

### Case 1

Leaf

```
5

Delete

NULL
```

---

### Case 2

One child

```
5
 \
 8

Delete

8
```

---

### Case 3

Two children

Replace with

* inorder successor
  or
* inorder predecessor

Then delete successor.

---

## Intuition

Successor is

Smallest element in right subtree.

Because BST order remains valid.

### TC

O(h)

### SC

O(h)

---

Problems

* Delete Node in BST
* Delete a node in BST

---

# 5. Inorder Traversal Pattern

Most important BST pattern.

---

## Identification

Question mentions

* sorted
* kth smallest
* increasing order

## Intuition

BST inorder

```
Left

Root

Right
```

Produces

```
Sorted array
```

Always remember

> BST + Inorder = Sorted

---

### TC

O(n)

### SC

O(h)

---

Problems

* Kth Smallest
* BST Iterator
* Increasing Order BST

---

# 6. Reverse Inorder Pattern

## Identification

Need

Largest

Kth largest

Descending

---

Traversal

```
Right

Root

Left
```

Produces

Descending order.

---

Problems

* Kth Largest
* Convert BST to Greater Tree

---

# 7. Validate BST Pattern

## Identification

Question

Is this tree a BST?

---

Wrong idea

Only compare parent.

Correct idea

Every node has

```
Lower bound

Upper bound
```

Example

```
      10

    5    20

      15
```

15 is greater than 10.

Cannot be inside left subtree.

---

Use range.

```cpp
dfs(node,min,max)

min<node<max
```

---

TC

O(n)

SC

O(h)

---

Problems

* Validate BST
* Check if a tree is a BST or not

---

# 8. Min-Max Boundary Pattern

Generalization of validation.

---

Identification

Need

Upper limit

Lower limit

Constraints propagate.

---

Example

Build BST from preorder.

Every recursive call has

```
(low,high)
```

---

Problems

* Construct BST from preorder
* Validate BST

---

# 9. Lowest Common Ancestor

Huge interview favorite.

---

Identification

Two nodes given.

Find ancestor.

---

BST intuition

```
Both smaller

Go left

Both larger

Go right

Split

Current node answer
```

```
      8

    4   12

1 6 10 15
```

Find

6 and 10

Split at 8.

---

TC

O(h)

SC

O(1)

---

Problems

* Lowest Common Ancestor BST
* LCA in BST

---

# 10. Successor / Predecessor Pattern

---

Identification

Next greater

Next smaller

---

Successor

```
Smallest larger value
```

Predecessor

```
Largest smaller value
```

---

Cases

Has right child

Go right

Then extreme left.

No right child

Keep ancestor.

---

Problems

* Inorder Successor
* Inorder Predecessor
* Inorder Successor/Predecessor in BST

---

# 10b. Floor and Ceil in BST

## Identification

Question asks

* Floor of a value (largest value ≤ target that exists in BST)
* Ceil of a value (smallest value ≥ target that exists in BST)

Keywords

> floor
>
> ceil
>
> closest value not exceeding / not below

## Intuition

Same descent as BST Search, but instead of stopping at "not found," keep track of the best candidate seen so far.

```
Floor:
    node->val <= target  → candidate, go right (look for something closer/bigger)
    node->val >  target  → go left (too big)

Ceil:
    node->val >= target  → candidate, go left (look for something closer/smaller)
    node->val <  target  → go right (too small)
```

```cpp
int findFloor(TreeNode* root, int target){
    int floorVal = -1;
    while(root){
        if(root->val == target) return root->val;
        if(root->val < target){
            floorVal = root->val;
            root = root->right;
        } else {
            root = root->left;
        }
    }
    return floorVal;
}

int findCeil(TreeNode* root, int target){
    int ceilVal = -1;
    while(root){
        if(root->val == target) return root->val;
        if(root->val > target){
            ceilVal = root->val;
            root = root->left;
        } else {
            root = root->right;
        }
    }
    return ceilVal;
}
```

### TC

O(h)

### SC

O(1)

---

Problems

* Floor and Ceil in a BST
* Floor in a Binary Search Tree

---

# 11. Kth Smallest Pattern

---

Identification

kth smallest

median

rank

---

Intuition

Inorder is sorted.

Count nodes.

Stop at kth.

---

Optimized

Don't store array.

Maintain counter.

---

TC

O(h+k)

Worst

O(n)

SC

O(h)

---

Problems

* Kth Smallest
* Kth Smallest and Largest element in BST

---

# 12. Morris Traversal Pattern

Advanced.

---

Identification

Need

O(1) space inorder.

---

Idea

Temporarily connect predecessor.

```
Left subtree

↓

Back edge

↓

Current
```

Remove connection later.

---

TC

O(n)

SC

O(1)

---

Problems

* Morris Traversal
* Recover BST
* Kth Smallest

---

# 13. Recover Swapped BST

---

Identification

Exactly two nodes swapped.

---

Observation

Inorder should be sorted.

Swapped nodes create inversion.

```
1

4

3

2

5
```

Violations

4>3

3>2

---

Store

first

middle

last

Swap answer.

---

TC

O(n)

SC

O(h)

---

Problems

* Recover BST
* Correct BST with two nodes swapped

---

# 14. BST Iterator

---

Identification

Need

next()

hasNext()

---

Idea

Maintain stack of left path.

```
Next

Pop

Push left chain of right child.
```

---

TC

Amortized

O(1)

Initialization

O(h)

SC

O(h)

---

Problems

* BST Iterator

---

# 15. Range Query Pattern

---

Identification

Values between

L and R.

---

Intuition

Prune unnecessary branches.

```
node<L

Go right only

node>R

Go left only
```

---

TC

Average

O(log n+k)

Worst

O(n)

---

Problems

* Range Sum BST

---

# 16. Trim BST Pattern

---

Identification

Delete everything outside range.

---

Observation

```
node<L

Entire left useless.
```

```
node>R

Entire right useless.
```

---

Return valid subtree.

---

Problems

* Trim BST

---

# 17. Convert BST Pattern

---

Identification

Need

Modify values.

Examples

Greater tree.

Prefix sums.

---

Idea

Reverse inorder.

Running sum.

```
Right

Node

Left
```

---

Problems

* Convert BST to Greater Tree

---

# 18. Two Sum in BST

---

Identification

Need two nodes.

---

Approaches

### HashSet

DFS

Store values.

TC

O(n)

SC

O(n)

---

### Inorder

Sorted array

Two pointers.

---

### BST Iterators

Forward iterator

Backward iterator

O(h) space.

---

Problems

* Two Sum IV - Input is a BST
* Two Sum In BST | Check if there exists a pair with Sum K

---

# 19. Build BST Pattern

---

Identification

Construct BST

From

* preorder
* sorted array

---

Sorted array

Middle becomes root.

Balanced BST.

---

Preorder

Use upper bound.

---

Problems

* Convert Sorted Array to BST
* Construct BST from Preorder Traversal
* Construct a BST from a preorder traversal

---

# 20. Largest BST Pattern

---

Identification

Binary tree

Find largest BST.

---

Return

```
isBST

size

min

max
```

Bottom-up DP.

---

TC

O(n)

SC

O(h)

---

Problems

* Largest BST in Binary Tree (common interview problem)

---

# 21. Merge BST Pattern

---

Identification

Two BSTs.

Need sorted output.

---

Approaches

* Two inorder arrays
* Two iterators

---

Problems

* All Elements in Two BSTs
* Merge 2 BST's

---

# 22. Balanced BST Pattern

---

Identification

Need balance.

---

Idea

Inorder

↓

Sorted array

↓

Build balanced BST.

---

Problems

* Balance BST

---

# 23. BST + Prefix Information

Maintain information during traversal.

Examples

* Running sum
* Node count
* Previous node
* Parent pointer

---

Useful in

* Recover BST
* Greater Tree
* Validate BST
* Modes

---

# 24. Augmented BST Pattern (Advanced)

Store extra information per node.

Examples

```
subtree size

frequency

sum

height
```

Useful for

* Order statistics
* Median
* Dynamic kth smallest

TC

Search/Insert/Delete remain **O(h)** while queries like kth smallest become **O(h)** instead of traversing the whole tree.

---

# Pattern Recognition Cheat Sheet

| If the question says...    | Pattern                       |
| --------------------------- | ------------------------------ |
| Search / Find              | BST Search                    |
| Minimum / Maximum value    | Find Min/Max                  |
| Insert                     | BST Insert                    |
| Delete                     | Delete Cases                  |
| Floor / Ceil               | Floor & Ceil Descent           |
| kth smallest               | Inorder + Counter             |
| kth largest                | Reverse Inorder               |
| Next greater               | Successor                     |
| Next smaller               | Predecessor                   |
| Is BST                     | Min-Max Validation            |
| Lowest common ancestor     | BST Property                  |
| Sorted order               | Inorder                       |
| Descending                 | Reverse Inorder               |
| Range [L,R]                | Pruning                       |
| Outside range              | Trim BST                      |
| Greater Tree               | Reverse Inorder + Running Sum |
| Two nodes sum              | HashSet / Two Iterators       |
| Build BST                  | Divide & Conquer / Bounds     |
| Swapped nodes              | Inorder Inversions            |
| O(1) space traversal       | Morris Traversal              |
| Largest BST in Binary Tree | Bottom-up DP (min/max/size)   |
| Balance BST                | Inorder + Rebuild             |

---

# Must-Do BST Problem List (in learning order)

1. Introduction to BST
2. Search in a Binary Search Tree
3. Find Min/Max in BST
4. Floor and Ceil in a BST
5. Floor in a Binary Search Tree
6. Insert a given node in BST
7. Delete a node in BST
8. Validate BST
9. Kth Smallest and Largest element in BST
10. Check if a tree is a BST or not
11. Lowest Common Ancestor of a BST / LCA in BST
12. Inorder Successor/Predecessor in BST
13. Range Sum of BST
14. Trim a BST
15. Convert BST to Greater Tree
16. Two Sum IV - Input is a BST / Two Sum In BST
17. BST Iterator
18. Correct BST with two nodes swapped / Recover Binary Search Tree
19. Construct a BST from a preorder traversal
20. Convert Sorted Array to BST
21. Balance BST
22. Merge 2 BST's / All Elements in Two BSTs
23. Largest BST in Binary Tree
24. Morris Inorder Traversal

If you can recognize these two dozen patterns on sight, you'll find that most BST interview questions stop feeling like puzzles and start feeling like matching exercises. The tree isn't trying to be clever. It's just sitting there, obeying one ordering rule while interviewers keep inventing new ways to ask about it.