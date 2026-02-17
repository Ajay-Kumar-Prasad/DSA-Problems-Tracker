# 88. Merge Sorted Array

## 🔹 Problem Statement

You are given two integer arrays `nums1` and `nums2`, sorted in non-decreasing order, and two integers `m` and `n`, representing the number of valid elements in `nums1` and `nums2` respectively.

Merge `nums1` and `nums2` into a single array sorted in non-decreasing order.

The final sorted array must be stored inside `nums1`.

Note:
- `nums1` has length `m + n`
- First `m` elements are valid
- Last `n` elements are 0 and should be ignored
- `nums2` has length `n`

---

## 🧠 Examples

### Example 1
Input:
nums1 = [1,2,3,0,0,0], m = 3  
nums2 = [2,5,6], n = 3  

Output:
[1,2,2,3,5,6]

---

### Example 2
Input:
nums1 = [1], m = 1  
nums2 = [], n = 0  

Output:
[1]

---

### Example 3
Input:
nums1 = [0], m = 0  
nums2 = [1], n = 1  

Output:
[1]

---

## 🔒 Constraints

- nums1.length == m + n
- nums2.length == n
- 0 <= m, n <= 200
- 1 <= m + n <= 200
- -10^9 <= nums1[i], nums2[j] <= 10^9

---

## 🏷 Topics

- Array
- Two Pointers
- Sorting

---

## 💡 Approaches Covered

1. Brute Force (Extra Array)
2. Optimal Two Pointer (Backward Merge)
3. Swap + Sort Strategy
4. Gap Method (Shell Sort Inspired)
