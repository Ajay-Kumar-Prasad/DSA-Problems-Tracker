# ✅ Solution: Move Zeroes

---

## 🐢 Approach 1: Brute Force (Using Temporary Array)

### 🔍 Intuition
To maintain the order of non-zero elements, we can first **store them** in a temporary array and then **fill the remaining** positions in `nums` with zeroes.

This ensures correct ordering but uses extra space.

---

### 🧩 Steps
1. Create a `temp` vector.  
2. Traverse `nums` — push all **non-zero elements** into `temp`.  
3. Copy `temp` back into `nums` starting from index 0.  
4. Fill the rest of the array with `0`.

---

### 💻 Code (Brute Force)

```cpp
class Solution {
public:
    void moveZeroes(vector<int>& nums) {
        vector<int> temp;
        int n = nums.size();

        // Step 1: Collect all non-zero elements
        for (int i = 0; i < n; i++) {
            if (nums[i] != 0)
                temp.push_back(nums[i]);
        }

        // Step 2: Copy back non-zero elements
        for (int i = 0; i < temp.size(); i++) {
            nums[i] = temp[i];
        }

        // Step 3: Fill remaining positions with zeroes
        for (int i = temp.size(); i < n; i++) {
            nums[i] = 0;
        }
    }
};
```
# ⏱️ Complexity

| Metric    | Complexity | Reason                          |
| --------- | ---------- | ------------------------------- |
| **Time**  | O(n)       | Single traversal                |
| **Space** | O(x)       | x = number of non-zero elements |

## ⚡ Approach 2: Optimal (Two-Pointer In-Place)
### 🔍 Intuition

We can move non-zero elements forward while keeping zeros at the back without using extra space.

We use:

    j → points to the position where the next non-zero element should be placed.

    i → traverses through the array.

Whenever we encounter a non-zero at i, we swap nums[i] and nums[j], and move both pointers.

## 🧩 Step-by-Step

    Initialize j = -1 (index where first zero occurs).

    Find the first zero index and set j.

    From i = j + 1 to n - 1:

    If nums[i] != 0, swap nums[i] and nums[j], and increment j.

    Continue till end.

### 💻 Code (Optimal)
```cpp
class Solution {
public:
    void moveZeroes(vector<int>& nums) {
        int j = -1;
        int n = nums.size();

        // Step 1: Find index of first zero
        for (int i = 0; i < n; i++) {
            if (nums[i] == 0) {
                j = i;
                break;
            }
        }

        // Step 2: If there are no zeros, return
        if (j == -1) return;

        // Step 3: Shift non-zero elements forward
        for (int i = j + 1; i < n; i++) {
            if (nums[i] != 0) {
                swap(nums[i], nums[j]);
                j++;
            }
        }
    }
};
```

## ⏱️ Complexity

| Metric    | Complexity | Reason            |
| --------- | ---------- | ----------------- |
| **Time**  | O(n)       | Single traversal  |
| **Space** | O(1)       | In-place swapping |

