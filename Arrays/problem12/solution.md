## Approaches Overview

Three approaches are commonly discussed:
1. Brute Force
2. Better Approach (Sorting)
3. Optimal Approach (Hashing)

---

## 1. Brute Force Approach

### Concept
For every element `x` in the array, try to build a consecutive sequence by checking whether `x+1`, `x+2`, `x+3`, etc. exist in the array.

### Algorithm
- Initialize `longest = 1`
- For each element `x`:
  - Set `current_count = 1`
  - While `(x + 1)` exists in the array (linear search):
    - Increment `x`
    - Increment `current_count`
  - Update `longest = max(longest, current_count)`

### Time Complexity
- **O(N²)**  
  For each element, a linear search is performed.

### Space Complexity
- **O(1)**  
  No extra data structures used.

### Verdict
❌ Too slow for large inputs.

---

## 2. Better Approach (Sorting)

### Concept
Sort the array first so that consecutive numbers appear next to each other.

### Algorithm
1. Sort the array.
2. Initialize:
   - `longest = 1`
   - `current_count = 1`
   - `last_smaller = INT_MIN`
3. Traverse the sorted array:
   - If `nums[i] == last_smaller + 1`:
     - Increment `current_count`
   - Else if `nums[i] == last_smaller`:
     - Skip (duplicate element)
   - Else:
     - Reset `current_count = 1`
   - Update `last_smaller = nums[i]`
   - Update `longest = max(longest, current_count)`

### Time Complexity
- **O(N log N)** due to sorting

### Space Complexity
- **O(1)** (if sorting in-place)
- **O(N)** otherwise

### Verdict
⚠️ Better, but still not optimal.

---

## 3. Optimal Approach (Hashing)

### Concept
Use a **hash set** to allow O(1) lookups.  
Only start counting a sequence when the current number is the **first element** of a sequence.

### Key Insight
A number `x` is the **start of a sequence** if:
`(x - 1) does NOT exist in the set`


This avoids redundant work.

---

### Algorithm
1. Insert all elements into an unordered set.
2. Initialize `longest = 0`.
3. For each number `x` in the array:
   - If `(x - 1)` does not exist in the set:
     - Set `current_count = 1`
     - Set `current_num = x`
     - While `(current_num + 1)` exists in the set:
       - Increment `current_num`
       - Increment `current_count`
     - Update `longest = max(longest, current_count)`
4. Return `longest`.

---

### Example Walkthrough

**Input:**
`[100, 4, 200, 1, 3, 2]`

**Hash Set:**
`{100, 4, 200, 1, 3, 2}`


Start points:
- `1` (0 not present) → sequence: `1 → 2 → 3 → 4` → length = 4
- `100` → length = 1
- `200` → length = 1

**Final Answer:** `4`

---

### Time Complexity
- **O(N)** on average  
  Each element is processed at most twice.

### Space Complexity
- **O(N)** for the hash set

### Verdict
✅ Optimal and interview-approved.

---

## C++ Implementation (Optimal Solution)

```cpp
class Solution {
public:
    int longestConsecutive(vector<int>& nums) {
        if (nums.empty()) return 0;

        unordered_set<int> st(nums.begin(), nums.end());
        int longest = 0;

        for (int x : st) {
            if (st.find(x - 1) == st.end()) {
                int current_num = x;
                int current_count = 1;

                while (st.find(current_num + 1) != st.end()) {
                    current_num++;
                    current_count++;
                }

                longest = max(longest, current_count);
            }
        }
        return longest;
    }
};
```

---
## Summary

| Approach    | Time Complexity | Space Complexity | Verdict |
| ----------- | --------------- | ---------------- | ------- |
| Brute Force | O(N²)           | O(1)             | ❌       |
| Sorting     | O(N log N)      | O(1) / O(N)      | ⚠️      |
| Hashing     | O(N)            | O(N)             | ✅       |

---