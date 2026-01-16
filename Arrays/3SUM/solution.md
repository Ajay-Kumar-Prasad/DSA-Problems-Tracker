# 3Sum – Solution

## Approaches Overview

1. Brute Force  
2. Better Approach (Hashing)  
3. Optimal Approach (Sorting + Two Pointers)

---

## 1. Brute Force Approach

### Idea
Check all possible triplets using three nested loops and count those whose sum equals zero.

---

### Algorithm
1. Iterate over all `i`
2. Iterate over all `j > i`
3. Iterate over all `k > j`
4. If `nums[i] + nums[j] + nums[k] == 0`, store the triplet

---

### Time Complexity
- **O(n³)**

### Space Complexity
- **O(1)**

### Verdict
❌ Too slow for large inputs.

---

## 2. Better Approach (Using Hashing)

### Idea
Fix one element and reduce the problem to **2Sum** using a hash set.
Use a set of triplets to avoid duplicates.

---

### Algorithm
1. Fix index `i`
2. Create an empty set for tracking seen elements
3. For every `j > i`:
   - Compute `rem = -(nums[i] + nums[j])`
   - If `rem` exists in the set, form a valid triplet
4. Sort each triplet and store it in a set to avoid duplicates

---

### C++ Implementation
```cpp
class Solution {
public:
    vector<vector<int>> threeSum(vector<int>& nums) {
        int n = nums.size();
        vector<vector<int>> ans;
        set<vector<int>> triplets;

        for(int i = 0; i < n; i++){
            set<int> st;
            for(int j = i + 1; j < n; j++){
                int rem = -nums[i] - nums[j];
                if(st.find(rem) != st.end()){
                    vector<int> triplet = {nums[i], nums[j], rem};
                    sort(triplet.begin(), triplet.end());
                    if(triplets.find(triplet) == triplets.end()){
                        ans.push_back(triplet);
                        triplets.insert(triplet);
                    }
                }
                st.insert(nums[j]);
            }
        }
        return ans;
    }
};
```
---
### Time Complexity
- **O(n² log n)**

### Space Complexity
- **O(n)**

### Verdict
⚠️ Better than brute force, but still inefficient and messy.

---

# 3Sum – Optimal Solution (Sorting + Two Pointers)

## Core Idea

The problem asks for all **unique triplets** whose sum is `0`.

Key observation:
- If we **fix one element**, the problem reduces to a **2Sum** problem on the remaining array.

Sorting the array allows:
- Efficient two-pointer traversal
- Easy elimination of duplicate triplets

---

## Intuition

1. Sort the array.
2. Fix one number `nums[i]`.
3. Look for two numbers `nums[j]` and `nums[k]` such that:

`nums[i] + nums[j] + nums[k] = 0`

4. Since the array is sorted:
- If the sum is too small → move `j` right
- If the sum is too large → move `k` left
5. Skip duplicate values to avoid repeating triplets.

This ensures every valid triplet is found **exactly once**.

---

## Algorithm

1. Sort the array.
2. Loop through the array with index `i`:
- Skip duplicate values for `i`.
3. Set two pointers:
- `j = i + 1`
- `k = n - 1`
4. While `j < k`:
- If `nums[i] + nums[j] + nums[k] == 0`:
  - Store the triplet.
  - Move both pointers.
  - Skip duplicates for `j` and `k`.
- If sum < 0 → increment `j`
- If sum > 0 → decrement `k`
5. Return the list of triplets.

---

## C++ Implementation

```cpp
class Solution {
public:
 vector<vector<int>> threeSum(vector<int>& nums) {
     sort(nums.begin(), nums.end());
     int n = nums.size();
     vector<vector<int>> ans;

     for(int i = 0; i < n; i++){
         if(i > 0 && nums[i] == nums[i - 1])
             continue;

         int j = i + 1, k = n - 1;
         while(j < k){
             int sum = nums[i] + nums[j] + nums[k];

             if(sum == 0){
                 ans.push_back({nums[i], nums[j], nums[k]});
                 j++;
                 k--;

                 while(j < k && nums[j] == nums[j - 1]) j++;
                 while(j < k && nums[k] == nums[k + 1]) k--;
             }
             else if(sum < 0){
                 j++;
             }
             else{
                 k--;
             }
         }
     }
     return ans;
 }
};
```
---
### Time Complexity
- **O(n²)**

### Space Complexity
- **O(1)** `(excluding output storage)`

### Verdict
✅ Optimal and interview-approved

---