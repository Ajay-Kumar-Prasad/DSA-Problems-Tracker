# Next Permutation – Detailed Solution

## Approach Overview

There are three possible approaches:

### 1. Brute Force (Bad Idea)
- Generate all permutations
- Sort them
- Find the current permutation
- Return the next one

**Time Complexity:** `O(n! * n)`  
**Verdict:** Completely impractical. Move on.

---

### 2. Better Approach (Using STL)
C++ provides `next_permutation()` which does exactly this.

```cpp
next_permutation(nums.begin(), nums.end());
```

- Verdict: Works, but banned in interviews unless you enjoy judgmental silence.

# Next Permutation – Optimal Approach

## Approach (Expected in Interviews)

The optimal solution generates the **next lexicographically greater permutation** of the given array **in-place**, using **constant extra memory**.

---

## Complexity Analysis

- **Time Complexity:** `O(n)`
- **Space Complexity:** `O(1)`

---

## Key Observations

- The next permutation should change the array **as little as possible**.
- A permutation stops increasing when it becomes **non-increasing from right to left**.
- We locate a **breakpoint (pivot)** where this increasing order breaks.

---

## Step-by-Step Algorithm

### Step 1: Find the Breakpoint
Traverse the array from right to left and find the first index `i` such that:
`nums[i] < nums[i + 1]`


This index is called the **pivot**.

If no such index exists:
- The array is in descending order
- Reverse the entire array
- The result is the smallest permutation

---

### Step 2: Find the Next Larger Element
From the right end of the array:
- Find the **smallest element greater than `nums[pivot]`**
- Swap it with the pivot element

---

### Step 3: Reverse the Suffix
Reverse the subarray **after the pivot index** to obtain the smallest lexicographical order.

---

## Visual Example

**Input:**
`[1, 3, 5, 4, 2]`


**Steps:**
- Breakpoint: `3 < 5` → index = `1`
- Swap `3` with next greater element `4`
- Reverse the suffix

**Output:**
`[1, 4, 2, 3, 5]`


---

## C++ Implementation

```cpp
class Solution {
public:
    void nextPermutation(vector<int>& nums) {
        int n = nums.size();
        int indx = -1;

        // Step 1: Find breakpoint
        for (int i = n - 2; i >= 0; i--) {
            if (nums[i] < nums[i + 1]) {
                indx = i;
                break;
            }
        }

        // Step 2: If no breakpoint exists
        if (indx == -1) {
            reverse(nums.begin(), nums.end());
            return;
        }

        // Step 3: Find next greater element and swap
        for (int i = n - 1; i > indx; i--) {
            if (nums[i] > nums[indx]) {
                swap(nums[i], nums[indx]);
                break;
            }
        }

        // Step 4: Reverse the suffix
        reverse(nums.begin() + indx + 1, nums.end());
    }
};
```
---

## Summary

| Approach     | Time  | Space | Verdict |
| ------------ | ----- | ----- | ------- |
| Brute Force  | O(n!) | O(n!) | ❌       |
| STL Function | O(n)  | O(1)  | ⚠️      |
| Optimal      | O(n)  | O(1)  | ✅       |

---
