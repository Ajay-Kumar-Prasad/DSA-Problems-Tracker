# Majority Element II – Solution

## Approaches Overview

1. Brute Force
2. Better Approach (Hash Map)
3. Optimal Approach (Boyer–Moore Voting Algorithm)

---

## 1. Brute Force Approach

### Idea
Count the frequency of every element and return those whose count is greater than `n/3`.

---

### Algorithm
1. For each element in the array, count its occurrences.
2. If count > `n/3`, include it in the result.

---

### Time Complexity
- **O(n²)**

### Space Complexity
- **O(1)**

### Verdict
❌ Too slow for large inputs.

---

## 2. Better Approach (Using Hash Map)

### Idea
Use a hash map to store frequencies of all elements.

---

### Algorithm
1. Traverse the array and store counts in a map.
2. Traverse the map and collect elements with frequency > `n/3`.

---

### C++ Implementation
```cpp
class Solution {
public:
    vector<int> majorityElement(vector<int>& nums) {
        int n = nums.size();
        map<int, int> mpp;

        for(int i = 0; i < n; i++){
            mpp[nums[i]]++;
        }

        vector<int> ans;
        for(auto x : mpp){
            if(x.second > n / 3)
                ans.push_back(x.first);
        }
        return ans;
    }
};
```
---

## Complexity

**Time Complexity:** ` O(n log n)`
**Space Complexity:** `O(n)`

## Verdict
- Works, but not optimal.

---

# Majority Element II – Optimal Solution  
(Boyer–Moore Voting Algorithm)

---

## Core Idea & Intuition

An element is considered a majority element if it appears **more than ⌊n / 3⌋ times**.

### Key Insight
There can be **at most two such elements**.

Why?
- If three different elements each appeared more than `n/3` times,  
  their total count would exceed `n`, which is impossible.

So instead of counting frequencies for all elements, we only need to
**track two potential candidates**.

---

## Intuition Behind Boyer–Moore (n/3 Version)

Think of this as a **cancellation process**:

- A majority element cannot be completely cancelled out by other elements.
- Every time we see **three different elements**, one occurrence of each can be removed.
- After all cancellations, the remaining candidates (at most two) are the only ones that could possibly be majority elements.

This lets us:
- Eliminate non-majority elements
- Track only two candidates using constant space

---

## Algorithm

### Phase 1: Find Potential Candidates
1. Maintain:
   - Two candidates: `ele1`, `ele2`
   - Two counters: `c1`, `c2`
2. Traverse the array:
   - If current number equals `ele1`, increment `c1`
   - Else if it equals `ele2`, increment `c2`
   - Else if `c1 == 0`, assign current number to `ele1`
   - Else if `c2 == 0`, assign current number to `ele2`
   - Else, decrement both counters (cancelling step)

At the end of this phase, `ele1` and `ele2` are **potential candidates**.

---

### Phase 2: Verify the Candidates
1. Reset both counters to zero
2. Traverse the array again
3. Count actual occurrences of `ele1` and `ele2`
4. Add a candidate to the result if its count > `n / 3`

Verification is required because **Boyer–Moore only guarantees candidates**, not correctness.

---

## C++ Implementation

```cpp
class Solution {
public:
    vector<int> majorityElement(vector<int>& nums) {
        int n = nums.size();
        int ele1 = 0, ele2 = 0;
        int c1 = 0, c2 = 0;

        // Phase 1: Find candidates
        for(int i = 0; i < n; i++){
            if(c1 == 0 && nums[i] != ele2){
                ele1 = nums[i];
                c1 = 1;
            }
            else if(c2 == 0 && nums[i] != ele1){
                ele2 = nums[i];
                c2 = 1;
            }
            else if(nums[i] == ele1) c1++;
            else if(nums[i] == ele2) c2++;
            else{
                c1--;
                c2--;
            }
        }

        // Phase 2: Verify candidates
        c1 = 0;
        c2 = 0;
        for(int i = 0; i < n; i++){
            if(nums[i] == ele1) c1++;
            else if(nums[i] == ele2) c2++;
        }

        vector<int> ans;
        if(c1 > n / 3) ans.push_back(ele1);
        if(c2 > n / 3) ans.push_back(ele2);

        return ans;
    }
};
```
---

## Complexity Analysis

**Time Complexity:** `O(n)`
**Space Complexity:** `O(1)`

---

### Summary
| Approach    | Time       | Space | Verdict |
| ----------- | ---------- | ----- | ------- |
| Brute Force | O(n²)      | O(1)  | ❌       |
| Hash Map    | O(n log n) | O(n)  | ⚠️      |
| Boyer–Moore | O(n)       | O(1)  | ✅       |

---

