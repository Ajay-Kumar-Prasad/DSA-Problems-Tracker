# 💻 Solutions for Longest Subarray with Given Sum K (Positive Elements Only)

---

## 🧠 Brute Force Approach

### 🔹 Idea:
Generate all possible subarrays and check their sums.  
If the sum equals `K`, update the maximum length.

### 🧩 Algorithm:
1. Loop `i` from 0 to n-1.
2. For each `i`, loop `j` from `i` to n-1 and calculate the subarray sum.
3. If `sum == K`, update `maxLen`.
4. Return `maxLen`.

### ⏱️ Time Complexity:
- **O(n²)**  
### 💾 Space Complexity:
- **O(1)**

### 🧩 Code:
```cpp
int longestSubarrayBrute(vector<int>& arr, int k) {
    int n = arr.size();
    int maxLen = 0;
    for (int i = 0; i < n; i++) {
        int sum = 0;
        for (int j = i; j < n; j++) {
            sum += arr[j];
            if (sum == k) maxLen = max(maxLen, j - i + 1);
        }
    }
    return maxLen;
}
```
---

## ⚙️ Better Solution (Using Hashing)
### 🔹 Idea:

## Intuition:
Use a prefix sum + hashmap to store the first occurrence of each sum.
If prefixSum - K exists, we can find a subarray ending at i with sum K.

## 🧩 Algorithm:

    Initialize sum = 0, maxLen = 0.

    Use a hashmap to store {prefixSum: index}.

    For each element:

    Add it to sum.

    If sum == K, update maxLen = i + 1.

    If (sum - K) exists in map, update maxLen = max(maxLen, i - map[sum - K]).

    If sum not in map, store it with index i.

## ⏱️ Time Complexity:

 - O(n) on average

## 💾 Space Complexity:

- O(n)

## 🧩 Code:
```cpp
int longestSubarrayHashing(vector<int>& arr, int k) {
    unordered_map<int, int> prefix;
    int sum = 0, maxLen = 0;
    for (int i = 0; i < arr.size(); i++) {
        sum += arr[i];
        if (sum == k) maxLen = i + 1;
        if (prefix.find(sum - k) != prefix.end()) {
            maxLen = max(maxLen, i - prefix[sum - k]);
        }
        if (prefix.find(sum) == prefix.end()) {
            prefix[sum] = i;
        }
    }
    return maxLen;
}
```

---

# ✅ Optimal Solution – Longest Subarray with Given Sum K (Positive Elements Only)

---

## 💡 Intuition
Since all the elements in the array are **positive**, we can use the **two-pointer (sliding window)** technique.

The idea is simple:
- Keep a running window between two pointers `i` and `j`.
- Expand the right pointer `j` to increase the sum.
- If the sum exceeds `K`, shrink the window from the left by moving `i` forward.
- Whenever the sum equals `K`, calculate the window size and update the maximum length.

Because all elements are positive, increasing `j` will **only increase** the sum, and increasing `i` will **only decrease** it — this property guarantees a linear-time solution.

---

## 🧠 Approach
1. Initialize two pointers `i = 0`, `j = 0` and variables `sum = 0`, `maxLen = 0`.
2. Iterate while `j < n`:
   - Add `arr[j]` to `sum`.
   - If `sum` becomes greater than `K`, subtract elements from the left (`arr[i]`) until `sum <= K`.
   - If `sum == K`, compute the length `j - i + 1` and update `maxLen`.
   - Increment `j` and continue.
3. Return `maxLen` at the end.

---

## ⏱️ Time Complexity
- **O(2N)** → Each element is visited at most twice (once by `j`, once by `i`).

## 💾 Space Complexity
- **O(1)** → Only a few variables are used, no extra data structure.

---

## 💻 Code (C++)
```cpp
class Solution {
public:
    int longestSubarrayWithSumK(vector<int>& arr, int k) {
        int i = 0, j = 0;
        int sum = 0, maxLen = 0;
        int n = arr.size();

        while (j < n) {
            sum += arr[j];

            // Shrink the window if sum > K
            while (sum > k && i <= j) {
                sum -= arr[i];
                i++;
            }

            // If current sum equals K, update maxLen
            if (sum == k) {
                maxLen = max(maxLen, j - i + 1);
            }

            j++;
        }

        return maxLen;
    }
};
```

