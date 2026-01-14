# Set Matrix Zeroes (LeetCode 73)

## Problem Statement

Given an `m x n` integer matrix, if an element is `0`, set its **entire row and column** to `0`.

The operation must be done **in-place**.

---

## Examples

### Example 1
**Input**
`[[1,1,1],
[1,0,1],
[1,1,1]]`

**Output**
`[[1,0,1],
[0,0,0],
[1,0,1]]`

---

### Example 2
**Input**
`[[0,1,2,0],
[3,4,5,2],
[1,3,1,5]]`

**Output**
`[[0,0,0,0],
[0,4,5,0],
[0,3,1,0]]`

---

## Constraints

- `1 <= m, n <= 200`
- `-2³¹ <= matrix[i][j] <= 2³¹ - 1`

---

## Follow-up

- O(m × n) extra space is wasteful
- O(m + n) space is better
- **O(1) extra space** is what interviewers actually want

---