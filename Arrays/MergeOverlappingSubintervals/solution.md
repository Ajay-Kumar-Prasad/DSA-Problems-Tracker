# 56. Merge Intervals — Solution

---

## 🧠 Intuition

### 🔴 Why do we sort first?

If intervals are not sorted, overlapping intervals may not be adjacent.

Example:
[[4,7],[1,4]]

Without sorting, you might process [4,7] first and miss merging properly.

After sorting:
[[1,4],[4,7]]

Now overlapping intervals are adjacent and easy to merge.

Sorting ensures:
If two intervals overlap → they will appear next to each other.

---

# 🟠 Brute Force Approach

## 🔹 Idea

1. Sort intervals based on start time.
2. Pick one interval.
3. Compare it with all future intervals.
4. If overlapping → merge.
5. If not overlapping → stop checking further (since sorted).

---

## 🔹 How Overlapping is Checked?

Two intervals overlap if:

current_end >= next_start

---

## 🔹 Step-by-Step Logic

- Sort intervals.
- For each interval `i`:
  - If already included in a previous merge → skip.
  - Start merging from `j = i + 1`
  - Keep merging until intervals stop overlapping.
  - Push merged interval into answer.

---

```cpp
class Solution {
public:
    vector<vector<int>> merge(vector<vector<int>>& intervals) {
        
        // Step 1: Sort intervals
        sort(intervals.begin(), intervals.end());
        
        int n = intervals.size();
        vector<vector<int>> ans;
        
        for (int i = 0; i < n; i++) {
            
            int start = intervals[i][0];
            int end = intervals[i][1];
            
            // If current interval already merged, skip it
            if (!ans.empty() && end <= ans.back()[1]) {
                continue;
            }
            
            // Step 2: Check further intervals
            for (int j = i + 1; j < n; j++) {
                
                // If overlapping
                if (intervals[j][0] <= end) {
                    end = max(end, intervals[j][1]);
                }
                else {
                    break;  // No more overlap (since sorted)
                }
            }
            
            ans.push_back({start, end});
        }
        
        return ans;
    }
};
```
---

## 🔹 Time Complexity

- Sorting → O(N log N)
- Nested comparison → O(N)
- Overall → O(N log N + 2N)

## 🔹 Space Complexity

- O(N) for result storage.

---

# 🟢 Optimal Approach (Single Pass Merge)

## 🔹 Core Intuition

After sorting:

If intervals overlap → they must be adjacent.

So instead of checking all future intervals,
we only compare with the last merged interval.

That’s the optimization.

---

## 🔹 Key Observation

Let:
Last merged interval = ans.back()
Current interval = intervals[i]

If:
intervals[i][0] <= ans.back()[1]

Then they overlap.

Otherwise:
No overlap → push as new interval.

---

## 🔹 Why This Works

Because:
- Sorting guarantees intervals are arranged by start time.
- If current interval doesn't overlap with the last merged one,
  it will never overlap with any earlier interval.

So one comparison is enough.

---

## 🔹 Algorithm

1. Sort intervals.
2. Initialize empty result vector.
3. Traverse intervals:
   - If result empty OR no overlap → push interval.
   - Else → merge by updating end:
     ans.back()[1] = max(ans.back()[1], intervals[i][1])
4. Return result.

---

## 🔹 Code (C++)

```cpp
class Solution {
public:
    vector<vector<int>> merge(vector<vector<int>>& intervals) {
        sort(intervals.begin(), intervals.end());
        
        vector<vector<int>> ans;
        
        for (int i = 0; i < intervals.size(); i++) {
            
            // If no overlap or first interval
            if (ans.empty() || intervals[i][0] > ans.back()[1]) {
                ans.push_back(intervals[i]);
            }
            else {
                // Merge intervals
                ans.back()[1] = max(ans.back()[1], intervals[i][1]);
            }
        }
        
        return ans;
    }
};
```
---

## 🔹 Time Complexity

- Sorting → O(N log N)
- Single traversal → O(N)
- Overall → O(N log N)

## 🔹 Space Complexity

- O(N) for storing merged intervals.

---
