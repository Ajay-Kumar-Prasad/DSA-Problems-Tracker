# ✅ Solutions for "Maximum Subarray Sum"

---
## 🧠 Approach 1: Brute Force

### 💡 Idea
We can find the sum of every possible subarray and track the maximum among them.

- Fix a starting index `i`
- For every `i`, iterate over `j` (`i ≤ j < n`)
- Compute the sum of subarray `nums[i...j]`
- Keep updating the maximum sum found so far

This approach checks **all possible subarrays**, leading to a cubic time complexity.

### 🧮 Complexity
- **Time:** O(n³) — three nested loops  
- **Space:** O(1) — no extra space used

### 💻 Code
```cpp
class Solution {
public:
    int maxSubArray(vector<int>& nums) {
        int n = nums.size();
        int maxSum = INT_MIN;

        // Try every subarray
        for (int i = 0; i < n; i++) {
            for (int j = i; j < n; j++) {
                int sum = 0;
                for (int k = i; k <= j; k++) {
                    sum += nums[k];
                }
                maxSum = max(maxSum, sum);
            }
        }

        return maxSum;
    }
};
```
---


---

## ⚙️ Approach: Better Solution (O(n²))

### 🔹 Intuition
Instead of checking all possible subarrays (which would be **O(n³)**),  
we can **fix a starting point** and then expand the subarray to the right, calculating the sum on the go.

This way, we avoid recalculating sums repeatedly for overlapping subarrays.

---

### 🔹 Steps
1. Initialize `maxSum` as the first element.
2. Loop through each starting index `i`.
3. For each `i`, expand to the right (loop `j = i...n-1`) and accumulate the sum.
4. After adding each element, compare and update `maxSum`.
5. Return `maxSum` after all iterations.

---

### ⏱️ Time Complexity
- **O(n²)** — Two nested loops for all start and end indices.
- **O(1)** — Constant extra space.

---

### 💻 Code Implementation (C++)
```cpp
class Solution {
public:
    int maxSubArray(vector<int>& nums) {
        int n = nums.size();
        long long maxSum = nums[0];
        
        for (int i = 0; i < n; i++) {
            long long sum = 0;
            for (int j = i; j < n; j++) {
                sum += nums[j];
                if (sum > maxSum) {
                    maxSum = sum;
                }
            }
        }
        return maxSum;
    }
};
```
---


---

## ⚙️ Approach: Kadane’s Algorithm (O(n) Time, O(1) Space)

### 🔹 Intuition
Kadane’s Algorithm is a legendary dynamic programming technique that tracks the **maximum subarray sum ending at each index**.

The idea is simple:
- As you move through the array, keep adding elements to the current subarray sum.  
- If the running sum becomes negative, discard it—because any future subarray including it will only be worse.  
- Keep track of the **maximum sum seen so far** during the process.

This makes it possible to solve the problem in a single pass with **constant space**.

---

### 🔹 Steps
1. Initialize:
   - `maxSum` = smallest possible integer (e.g. `LONG_MIN`)
   - `sum` = 0  
2. Iterate through each number:
   - Add it to `sum`.
   - Update `maxSum` if `sum` is greater.
   - If `sum` becomes negative, reset it to 0 (start a new subarray).
3. Return `maxSum`.

---

### ⏱️ Time Complexity
- **O(n)** — Single traversal through the array.  
- **O(1)** — Constant extra space.

---

### 💻 Code Implementation (C++)
```cpp
class Solution {
public:
    int maxSubArray(vector<int>& nums) {
        long long maxSum = LONG_MIN;
        long long sum = 0;
        int n = nums.size();

        for (int i = 0; i < n; i++) {
            sum += nums[i];
            if (sum > maxSum) maxSum = sum;
            if (sum < 0) sum = 0; // drop that subarray
        }

        return maxSum;
    }
};
