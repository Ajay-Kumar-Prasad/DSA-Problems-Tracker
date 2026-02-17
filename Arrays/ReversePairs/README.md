# 493. Reverse Pairs

## 🔹 Problem Statement

Given an integer array `nums`, return the number of reverse pairs in the array.

A reverse pair is defined as a pair `(i, j)` such that:

- 0 <= i < j < nums.length
- nums[i] > 2 * nums[j]

---

## 🧠 Examples

### Example 1

Input:
nums = [1,3,2,3,1]

Output:
2

Explanation:
The reverse pairs are:
(1, 4) → nums[1] = 3, nums[4] = 1 → 3 > 2 * 1  
(3, 4) → nums[3] = 3, nums[4] = 1 → 3 > 2 * 1  

---

### Example 2

Input:
nums = [2,4,3,5,1]

Output:
3

Explanation:
The reverse pairs are:
(1, 4) → nums[1] = 4, nums[4] = 1 → 4 > 2 * 1  
(2, 4) → nums[2] = 3, nums[4] = 1 → 3 > 2 * 1  
(3, 4) → nums[3] = 5, nums[4] = 1 → 5 > 2 * 1  

---

## 🔒 Constraints

- 1 <= nums.length <= 5 * 10^4
- -2^31 <= nums[i] <= 2^31 - 1

---

## 🏷 Topics

- Array
- Divide and Conquer
- Merge Sort
- Binary Search
- Binary Indexed Tree
- Segment Tree
- Ordered Set

---

## 💡 Key Observations

- This problem is a modified version of counting inversions.
- Brute force approach (checking all pairs) would take O(N²), which is too slow.
- Efficient solutions require:
  - Merge Sort (Divide and Conquer), or
  - Binary Indexed Tree / Segment Tree techniques.

---

## 📌 Hints

1. Use merge sort technique.
2. Divide the array into two parts and sort them.
3. While merging:
   - For each element in the left half,
   - Count how many elements in the right half satisfy:
     nums[i] > 2 * nums[j].
   - Use two pointers to efficiently count.

---