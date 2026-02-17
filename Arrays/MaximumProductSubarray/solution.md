# 152. Maximum Product Subarray — Solution

---

# 🔴 1. Brute Force Approach (O(N³))

## 🧠 Intuition

Check every possible subarray.
For each subarray:
- Compute its product.
- Update the maximum product.

Three nested operations:
1. Choose starting index.
2. Choose ending index.
3. Compute product inside that range.

---

## ⏱ Complexity

Time: O(N³)  
Space: O(1)

This is purely conceptual and will TLE for large inputs.

---

# 🟠 2. Better Approach (O(N²))

## 🧠 Intuition

Instead of recomputing product every time:

Fix a starting index `i`,  
Keep multiplying elements as we extend the subarray.

So we eliminate one loop.

---

## 🔹 Algorithm

For i = 0 to n-1:
- product = 1
- For j = i to n-1:
  - product *= nums[j]
  - maxProduct = max(maxProduct, product)

---

## ⏱ Complexity

Time: O(N²)  
Space: O(1)

---

##  Code

```cpp
class Solution {
public:
    int maxProduct(vector<int>& nums) {
        int n = nums.size();
        int maxProduct = INT_MIN;
        
        for (int i = 0; i < n; i++) {
            int product = 1;
            
            for (int j = i; j < n; j++) {
                product *= nums[j];
                maxProduct = max(maxProduct, product);
            }
        }
        
        return maxProduct;
    }
};
```
---
# 152. Maximum Product Subarray — Optimal Solution

---

# 🟢 Optimal Approach (Prefix & Suffix Traversal)

## 🧠 Core Intuition

The difficulty in this problem comes from negative numbers and zeros.

Observations:

- If all elements are positive → product is positive.
- If there are even number of negative elements → product is positive.
- If there are odd number of negative elements → product becomes negative.
- Zero breaks the product and splits the array into independent segments.

Key Idea:
If there are odd negatives, removing one negative from either left or right can make the product positive.

Instead of explicitly removing elements, we compute:

- Prefix product (left → right)
- Suffix product (right → left)

At every index, we take the maximum of both.

This automatically handles:
- Odd negatives
- Even negatives
- Zero resets

---

## 🔹 Why Prefix and Suffix?

If we only scan from left:

We might miss the case where removing a prefix negative gives maximum product.

If we only scan from right:

We might miss the case where removing a suffix negative gives maximum product.

So we compute both.

---

## 🔹 Algorithm

1. Initialize:
   - prefix = 1
   - suffix = 1
   - ans = INT_MIN

2. Traverse the array:
   - If prefix becomes 0 → reset to 1
   - If suffix becomes 0 → reset to 1
   - prefix *= nums[i]
   - suffix *= nums[n - i - 1]
   - ans = max(ans, max(prefix, suffix))

3. Return ans.

---

## ⏱ Complexity

Time: O(N)  
Space: O(1)

---

##  Code (C++)

```cpp
class Solution {
public:
    int maxProduct(vector<int>& nums) {
        int prefix = 1, suffix = 1;
        int n = nums.size();
        int ans = INT_MIN;

        for (int i = 0; i < n; i++) {

            if (prefix == 0) prefix = 1;
            if (suffix == 0) suffix = 1;

            prefix *= nums[i];
            suffix *= nums[n - i - 1];

            ans = max(ans, max(prefix, suffix));
        }

        return ans;
    }
};
```
---
## Final Comparison

| Approach    | Time  | Space |
| ----------- | ----- | ----- |
| Brute Force | O(N³) | O(1)  |
| Better      | O(N²) | O(1)  |
| Optimal     | O(N)  | O(1)  |

---