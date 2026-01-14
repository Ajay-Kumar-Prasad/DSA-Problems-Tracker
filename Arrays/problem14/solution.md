## Approaches Overview

1. Brute Force  
2. Optimal Approach (Prefix Sum + Hash Map)

---

## 1. Brute Force Approach

### Idea
Check **all possible subarrays** using two nested loops and count those whose sum equals `k`.

---

### Algorithm
1. For every starting index `i`:
   - Initialize `sum = 0`
2. For every ending index `j ≥ i`:
   - Add `nums[j]` to `sum`
   - If `sum == k`, increment count

---

### Time Complexity
- **O(N²)**

### Space Complexity
- **O(1)**

### Verdict
❌ Too slow for large inputs.

---

## 2. Optimal Approach (Prefix Sum + Hash Map)

### Core Idea

Use **prefix sums** and a hash map to count how many times a required sum has occurred before.

---

### Key Insight

Let: `prefixSum[j] = nums[0] + nums[1] + ... + nums[j]`

Then the sum of subarray `(i + 1 → j)` is:
`prefixSum[j] - prefixSum[i]`

So, if:
`prefixSum[j] - k = prefixSum[i]`

We just need to know **how many times `prefixSum[j] - k` has appeared before**.

---

### Algorithm
1. Initialize:
   - `sum = 0`
   - `count = 0`
   - Hash map `preSumMap` storing prefix sum frequencies
2. Insert `preSumMap[0] = 1` (important base case)
3. Traverse the array:
   - Add current element to `sum`
   - Compute `rem = sum - k`
   - Add `preSumMap[rem]` to `count`
   - Increment `preSumMap[sum]`
4. Return `count`

---

### C++ Implementation

```cpp
class Solution {
public:
    int subarraySum(vector<int>& nums, int k) {
        int n = nums.size();
        long long sum = 0;
        int count = 0;
        map<long long, int> preSumMap;

        preSumMap[0] = 1;

        for(int i = 0; i < n; i++){
            sum += nums[i];
            long long rem = sum - k;
            count += preSumMap[rem];
            preSumMap[sum]++;
        }
        return count;
    }
};
```

## Complexity Analysis

**Time Complexity:** ` O(N log N)`
`(map operations take log N; can be reduced to O(N) using unordered_map)`

**Space Complexity:** `O(N)`

--- 

## Summary

| Approach         | Time Complexity | Space Complexity | Verdict |
| ---------------- | --------------- | ---------------- | ------- |
| Brute Force      | O(N²)           | O(1)             | ❌       |
| Prefix Sum + Map | O(N log N)      | O(N)             | ✅       |

---