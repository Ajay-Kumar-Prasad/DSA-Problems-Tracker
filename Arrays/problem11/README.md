# Next Permutation (LeetCode 31)

## Problem Statement

Given an array of integers `nums`, rearrange it into the **next lexicographically greater permutation** of numbers.

If such a permutation does **not** exist (i.e., the array is in descending order), rearrange it into the **lowest possible order** (ascending order).

The replacement must be:
- **In-place**
- Use **constant extra memory**

---

## Lexicographical Order

Lexicographical order is the same order used in dictionaries.

Example permutations of `[1,2,3]`:

```
[1,2,3]
[1,3,2]
[2,1,3]
[2,3,1]
[3,1,2]
[3,2,1]
```

So:
- Next permutation of `[1,2,3]` → `[1,3,2]`
- Next permutation of `[3,2,1]` → `[1,2,3]`

---

## Examples

### Example 1
**Input:**  
`nums = [1,2,3]`  
**Output:**  
`[1,3,2]`

### Example 2
**Input:**  
`nums = [3,2,1]`  
**Output:**  
`[1,2,3]`

### Example 3
**Input:**  
`nums = [1,1,5]`  
**Output:**  
`[1,5,1]`

---

## Constraints

- `1 <= nums.length <= 100`
- `0 <= nums[i] <= 100`

---

## Topics

- Array  
- Two Pointers  

---