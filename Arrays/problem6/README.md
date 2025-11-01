# 🧮 Longest Subarray with Given Sum K (Positive Elements Only)

## 📘 Problem Statement
Given an array of **positive integers** and an integer `K`, find the **length of the longest subarray** whose sum equals `K`.

---

## 🧠 Examples

### Example 1
**Input:**  
`arr = [1, 2, 3, 1, 1, 1, 1, 4, 2, 3]`, `K = 6`  

**Output:**  
`4`  

**Explanation:**  
The longest subarray with sum = 6 is `[3, 1, 1, 1]`, which has length 4.

---

### Example 2
**Input:**  
`arr = [2, 3, 5, 1, 1, 1, 2]`, `K = 5`  

**Output:**  
`4`  

**Explanation:**  
The longest subarray with sum = 5 is `[1, 1, 1, 2]`, which has length 4.

---

## ⚙️ Constraints
- `1 <= arr.length <= 10^5`  
- `1 <= arr[i] <= 10^4`  
- `1 <= K <= 10^9`

---

## 🧵 Topics
- Arrays  
- Sliding Window / Two Pointers  
- Prefix Sum  
- Hashing (for extended variants)
