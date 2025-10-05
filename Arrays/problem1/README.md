# 1752. Check if Array Is Sorted and Rotated

## 📝 Problem Statement

Given an array `nums`, return `true` if the array was originally sorted in non-decreasing order, then rotated some number of positions (including zero). Otherwise, return `false`.

There may be duplicates in the original array.

---

### 🔹 Example 1
**Input:**  
`nums = [3,4,5,1,2]`  
**Output:**  
`true`  
**Explanation:**  
`[1,2,3,4,5]` is the original sorted array.  
You can rotate it by `x = 2` positions to get `[3,4,5,1,2]`.

---

### 🔹 Example 2
**Input:**  
`nums = [2,1,3,4]`  
**Output:**  
`false`  
**Explanation:**  
There is no sorted array once rotated that can make `nums`.

---

### 🔹 Example 3
**Input:**  
`nums = [1,2,3]`  
**Output:**  
`true`  
**Explanation:**  
`[1,2,3]` is already sorted and rotated `0` times.

---

### 🔸 Constraints
- `1 <= nums.length <= 100`  
- `1 <= nums[i] <= 100`

---

### 💡 Topics
- Array

### 💭 Hint
Try brute-forcing by checking if it’s possible for a sorted array to start from each position.
