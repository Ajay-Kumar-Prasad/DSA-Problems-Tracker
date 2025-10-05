# 🐢 Brute Force Solution — Remove Duplicates from Sorted Array

## 🧩 Approach
We can use a **set** (or similar structure) to automatically remove duplicates and then copy the unique elements back to the array.

1. Create a `set<int>` and insert all elements from `nums`.
2. Clear the original array.
3. Copy all elements from the set back into `nums`.
4. Return the size of the set.

Although this removes duplicates correctly, it uses **extra space**, violating the "in-place" constraint.

---

## 💻 Code (C++)
```cpp
class Solution {
public:
    int removeDuplicates(vector<int>& nums) {
        set<int> unique(nums.begin(), nums.end());
        nums.assign(unique.begin(), unique.end());
        return nums.size();
    }
};
```
---

# ⚡ Optimal Solution — Remove Duplicates from Sorted Array

## 🧠 Intuition
Since the array is already **sorted**, all duplicates are **next to each other**.  
We can use a **two-pointer approach** to overwrite duplicates in-place.

---

## 🧩 Approach
1. Initialize `i = 0` (slow pointer) and `j = 1` (fast pointer).
2. Traverse the array while `j < n`:
   - If `nums[j] > nums[i]`, increment `i` and update `nums[i] = nums[j]`.
   - Otherwise, skip.
3. Return `i + 1` as the number of unique elements.

---

## 💻 Code (C++)
```cpp
class Solution {
public:
    int removeDuplicates(vector<int>& nums) {
        int n = nums.size();
        if (n == 0) return 0;
        
        int i = 0; // slow pointer
        for (int j = 1; j < n; j++) {
            if (nums[j] != nums[i]) {
                i++;
                nums[i] = nums[j];
            }
        }
        return i + 1;
    }
};
