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

⏱️ Complexity
| Metric               | Analysis                                   |
| -------------------- | ------------------------------------------ |
| **Time Complexity**  | O(n log n) — due to insertion into the set |
| **Space Complexity** | O(n) — for storing unique elements         |

---

# ⚡ Optimal Solution — Remove Duplicates from Sorted Array

## 🧠 Intuition
🔍 Intuition

The array is already sorted — that’s a key observation.

👉 If it’s sorted, all duplicates appear next to each other.
So we can remove them without using extra space by overwriting duplicates in place.

We’ll use two pointers:

- i → points to the position of the last unique element

- j → scans through the array looking for new unique elements

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
```

# ⏱️ Complexity

| Metric               | Analysis                |
| -------------------- | ----------------------- |
| **Time Complexity**  | O(n) — single traversal |
| **Space Complexity** | O(1) — in-place         |

