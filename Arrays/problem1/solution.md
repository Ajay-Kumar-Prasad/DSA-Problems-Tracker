# ✅ Solution: Check if Array Is Sorted and Rotated

## 🧠 Intuition

If the array breaks the non-decreasing (ascending) order **more than once**, it means it can’t be formed by rotating a sorted array.

In a sorted and rotated array:
- The elements increase up to a point.
- Then, it "wraps around" to the smallest element and continues increasing.
- This "break" (where `nums[i-1] > nums[i]`) can happen **at most once**.

If it happens **more than once**, return `false`.

---

## 🧩 Approach
1. Initialize a counter `count = 0`.
2. Traverse the array and count how many times `nums[i-1] > nums[i]`.
3. Also, check if the last element is greater than the first (`nums[n-1] > nums[0]`), since the array is circular.
4. If the total count ≤ 1, return `true`.  
   Else, return `false`.

---

## ⏱️ Complexity
- **Time Complexity:** O(n)  
- **Space Complexity:** O(1)

---

## 💻 Code (C++)
```cpp
class Solution {
public:
    bool check(vector<int>& nums) {
        int count = 0;
        int n = nums.size();
        
        for(int i = 1; i < n; i++) {
            if(nums[i - 1] > nums[i])
                count++;
        }

        if(nums[n - 1] > nums[0])
            count++;

        return count <= 1;
    }
};
