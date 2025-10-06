# 💻 Solutions for 349. Intersection of Two Arrays

---

## 🧠 Brute Force Approach
### 🔹 Idea:
- Compare every element of `nums1` with every element of `nums2`.
- Use a `set` to avoid duplicates.

### 🧩 Algorithm:
1. Loop through all pairs `(i, j)` where `nums1[i] == nums2[j]`.
2. Store matching elements in a set to ensure uniqueness.
3. Return the set as a list.

### ⏱️ Time Complexity:
- **O(n1 * n2)** — nested loops for comparison.  
- **O(n2)** extra space (for visited or set).

### 💾 Space Complexity:
- **O(n2)** due to the use of a set.

### 🧩 Code (Brute Force)
```cpp
class Solution {
public:
    vector<int> intersection(vector<int>& nums1, vector<int>& nums2) {
        int n1 = nums1.size();
        int n2 = nums2.size();
        set<int> st;

        for(int i = 0; i < n1; i++) {
            for(int j = 0; j < n2; j++) {
                if(nums1[i] == nums2[j]) st.insert(nums1[i]);
            }
        }

        return vector<int>(st.begin(), st.end());
    }
};
```
## 🚀 Optimal Solution (Two-Pointer + Sorting)
## 🔹 Idea:

    Sort both arrays.

    Use two pointers to find common elements efficiently.

    Skip duplicates using a set.

## 🧩 Algorithm:

    Sort both arrays.

    Use two pointers i and j.

    If elements match → store in set and move both pointers.

    If nums1[i] < nums2[j] → move i++.

    Else → move j++.

## 🧩 Code (Optimal)
```cpp
class Solution {
public:
    vector<int> intersection(vector<int>& nums1, vector<int>& nums2) {
        sort(nums1.begin(), nums1.end());
        sort(nums2.begin(), nums2.end());
        int i = 0, j = 0;
        set<int> st;
        vector<int> ans;

        while (i < nums1.size() && j < nums2.size()) {
            if (nums1[i] == nums2[j]) {
                st.insert(nums1[i]);
                i++;
                j++;
            } else if (nums1[i] < nums2[j]) {
                i++;
            } else {
                j++;
            }
        }

        for (auto x : st) ans.push_back(x);
        return ans;
    }
};
```

🏁 Time and Space Complexity

| Approach    | Time Complexity | Space Complexity | Key Idea            |
| ----------- | --------------- | ---------------- | ------------------- |
| Brute Force | O(n1 × n2)      | O(n2)            | Compare every pair  |
| Optimal     | O(n log n)      | O(1)             | Sort + Two Pointers |
