# ✅ Solution: Rotate Array

---

## 🚀 Approach 1: Brute Force (Using Temporary Array)

### 🔍 Intuition
If we rotate the array `k` steps to the right,  
the **last k elements** come to the **front**,  
and the remaining elements shift right by `k` positions.

We can store the last `k` elements in a temporary array,  
then rearrange the rest accordingly.

---

### 🧩 Steps
1. Compute `k = k % n` (to handle when `k > n`).  
2. Store the last `k` elements in a temporary array.  
3. Shift the first `n-k` elements right by `k` steps.  
4. Copy back the stored elements from the temporary array to the beginning.

---

### 💻 Code (Brute Force)

```cpp
class Solution {
public:
    void rotate(vector<int>& nums, int k) {
        int n = nums.size();
        if (k > n) k = k % n;

        vector<int> temp;
        // Step 1: Store last k elements
        for (int i = n - k; i < n; i++) {
            temp.push_back(nums[i]);
        }

        // Step 2: Shift remaining elements to right
        for (int i = n - k - 1; i >= 0; i--) {
            nums[i + k] = nums[i];
        }

        // Step 3: Copy back the stored elements
        for (int i = 0; i < k; i++) {
            nums[i] = temp[i];
        }
    }
};
```
# ⏱️ Complexity

| Metric    | Complexity | Explanation          |
| --------- | ---------- | -------------------- |
| **Time**  | O(n + k)   | Copy + shift         |
| **Space** | O(k)       | Temporary array used |

# ⚡ Approach 2: Optimal (In-Place Reversal Method)
# 🔍 Intuition

We can rotate the array in-place using reversal:

- Reverse the first n - k elements.

- Reverse the last k elements.

- Reverse the entire array.

This effectively brings the last k elements to the front while maintaining order.

🧩 Example

Input: [1,2,3,4,5,6,7], k = 3
Steps:

1. Reverse first n-k → [4,3,2,1,5,6,7]
2. Reverse last k → [4,3,2,1,7,6,5]
3. Reverse all → [5,6,7,1,2,3,4]

✅ Final Output: [5,6,7,1,2,3,4]

# 💻 Code (Optimal)
```cpp
class Solution {
public:
    void rotate(vector<int>& nums, int k) {
        int n = nums.size();
        if (k > n) k = k % n;

        // Step 1: Reverse first n - k elements
        reverse(nums.begin(), nums.begin() + (n - k));

        // Step 2: Reverse last k elements
        reverse(nums.begin() + (n - k), nums.end());

        // Step 3: Reverse entire array
        reverse(nums.begin(), nums.end());
    }
};
```
# ⏱️ Complexity

| Metric    | Complexity   | Explanation        |
| --------- | ------------ | ------------------ |
| **Time**  | O(2n) ≈ O(n) | Reversals only     |
| **Space** | O(1)         | In-place operation |
