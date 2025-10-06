# 283. Move Zeroes

### 🧩 Problem Statement
Given an integer array `nums`, move all `0`'s to the end of it **while maintaining the relative order** of the non-zero elements.

You must do this **in-place**, without making a copy of the array.

---

### 🔢 Example 1:
**Input:**  
`nums = [0,1,0,3,12]`  
**Output:**  
`[1,3,12,0,0]`

---

### 🔢 Example 2:
**Input:**  
`nums = [0]`  
**Output:**  
`[0]`

---

### ⚙️ Constraints:
- `1 <= nums.length <= 10^4`
- `-2^31 <= nums[i] <= 2^31 - 1`

---

### 💡 Topics:
- Array  
- Two Pointers  

---

### 🧭 Hints:
1. “In-place” means no new array should be used.
2. A **two-pointer approach** can help — one pointer tracks the current position, and another places non-zero elements in order.
