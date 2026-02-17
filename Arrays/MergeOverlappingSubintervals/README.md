# 56. Merge Intervals

## 🔹 Problem Statement

Given an array of intervals where `intervals[i] = [starti, endi]`, merge all overlapping intervals and return an array of non-overlapping intervals that cover all the intervals in the input.

---

## 🧠 Examples

### Example 1
Input:
intervals = [[1,3],[2,6],[8,10],[15,18]]

Output:
[[1,6],[8,10],[15,18]]

Explanation:
[1,3] and [2,6] overlap, so they are merged into [1,6].

---

### Example 2
Input:
intervals = [[1,4],[4,5]]

Output:
[[1,5]]

Explanation:
[1,4] and [4,5] are considered overlapping.

---

### Example 3
Input:
intervals = [[4,7],[1,4]]

Output:
[[1,7]]

Explanation:
After sorting → [[1,4],[4,7]]
They overlap, so merge into [1,7].

---

## 🔒 Constraints

- 1 <= intervals.length <= 10^4
- intervals[i].length == 2
- 0 <= starti <= endi <= 10^4

---

## 🏷 Topics

- Array
- Sorting

---

<!-- ## 💡 Approach Overview

### 1️⃣ Brute Force Approach
- Sort the intervals
- For each interval:
  - Check if it is already covered
  - Compare with next intervals
  - Merge overlapping intervals
- Continue until all intervals are processed

Time Complexity: **O(N log N + 2N)**  
Space Complexity: **O(N)**  

---

### 2️⃣ Optimal Approach
- Sort the intervals
- Use a single pass to merge overlapping intervals
- Compare current interval with last added interval
- Merge if overlapping, else push new interval

Time Complexity: **O(N log N)**  
Space Complexity: **O(N)**  

--- -->

## 📂 Files

- `solution.md` → Contains detailed explanation and implementation.
