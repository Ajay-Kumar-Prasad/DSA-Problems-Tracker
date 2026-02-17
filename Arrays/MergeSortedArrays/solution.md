# 88. Merge Sorted Array — Solution

---

# 🔴 1. Brute Force Approach (Using Extra Array)

## 🧠 Intuition

Since both arrays are sorted, we can merge them like the merge step of Merge Sort.

Steps:
1. Create a temporary array of size m + n.
2. Use two pointers:
   - i → nums1
   - j → nums2
3. Compare elements and insert the smaller one.
4. Copy result back to nums1.

---

## ⏱ Complexity

- Time: `O(2(M + N))  `
- Space: `O(M + N)`

---

## ✅ Code

```cpp
class Solution {
public:
    void merge(vector<int>& nums1, int m, vector<int>& nums2, int n) {
        
        vector<int> temp;
        int i = 0, j = 0;
        
        while (i < m && j < n) {
            if (nums1[i] <= nums2[j])
                temp.push_back(nums1[i++]);
            else
                temp.push_back(nums2[j++]);
        }
        
        while (i < m)
            temp.push_back(nums1[i++]);
        
        while (j < n)
            temp.push_back(nums2[j++]);
        
        for (int k = 0; k < m + n; k++)
            nums1[k] = temp[k];
    }
};
```
# 🟢 2. Optimal Solution (Two Pointers from Back)
**🧠 Core Insight**

- Instead of merging from front, merge from back.

- Because nums1 has extra space at the end, we can fill from right to left.

- Pointers:

`i = m - 1`

`j = n - 1`

`k = m + n - 1`

- Place larger element at position k.

## ⏱ Complexity

- Time: `O(M + N)`
- Space: `O(1)`

## Code
```cpp
class Solution {
public:
    void merge(vector<int>& nums1, int m, vector<int>& nums2, int n) {
        
        int i = m - 1;
        int j = n - 1;
        int k = m + n - 1;
        
        while (i >= 0 && j >= 0) {
            if (nums1[i] > nums2[j])
                nums1[k--] = nums1[i--];
            else
                nums1[k--] = nums2[j--];
        }
        
        while (j >= 0)
            nums1[k--] = nums2[j--];
    }
};
```
---

- This is the best interview solution.

## 🟡 3. Swap + Sort Strategy
**🧠 Intuition**

- Compare last element of nums1 with first of nums2.

- If nums1 element is greater → swap.

- Continue this process.

- Finally sort both arrays separately.

**Idea:**
- Push larger elements toward nums2, smaller toward nums1.

# ⏱ Complexity

**Time:**
`O(min(M, N)) + O(M log M) + O(N log N)`

**Space:** ` O(1)`

## Code
```cpp
class Solution {
public:
    void merge(vector<int>& nums1, int m, vector<int>& nums2, int n) {
        
        int left = m - 1;
        int right = 0;
        
        while (left >= 0 && right < n) {
            if (nums1[left] > nums2[right]) {
                swap(nums1[left], nums2[right]);
                left--;
                right++;
            } else {
                break;
            }
        }
        
        sort(nums1.begin(), nums1.begin() + m);
        sort(nums2.begin(), nums2.end());
        
        for (int i = 0; i < n; i++)
            nums1[m + i] = nums2[i];
    }
};
```
---

- Not the cleanest, but valid.

## 🔵 4. Gap Method (Shell Sort Inspired)
**🧠 Intuition**

- Treat nums1 and nums2 as a single combined array of size m + n.

- Use a shrinking gap:

`gap = ceil((m + n) / 2)`

- Compare elements gap distance apart.
- If out of order → swap.

- Reduce gap until `gap = 1`.

- This is similar to Shell Sort.

# ⏱ Complexity

**Time:** `O((M + N) * log(M + N))`
**Space**: `O(1)`

## Code
```cpp
class Solution {
public:
    void swapIfGreater(vector<int>& a, vector<int>& b, int i, int j) {
        if (a[i] > b[j])
            swap(a[i], b[j]);
    }

    void merge(vector<int>& nums1, int m, vector<int>& nums2, int n) {
        
        int len = m + n;
        int gap = (len / 2) + (len % 2);
        
        while (gap > 0) {
            
            int left = 0;
            int right = left + gap;
            
            while (right < len) {
                
                // nums1 & nums1
                if (left < m && right < m)
                    swapIfGreater(nums1, nums1, left, right);
                
                // nums1 & nums2
                else if (left < m && right >= m)
                    swapIfGreater(nums1, nums2, left, right - m);
                
                // nums2 & nums2
                else
                    swapIfGreater(nums2, nums2, left - m, right - m);
                
                left++;
                right++;
            }
            
            if (gap == 1) break;
            gap = (gap / 2) + (gap % 2);
        }
        
        for (int i = 0; i < n; i++)
            nums1[m + i] = nums2[i];
    }
};
``` 
---

### 🚀 Final Comparison

| Approach             | Time Complexity       | Space    | Interview Friendly |
| -------------------- | --------------------- | -------- | ------------------ |
| Brute Force          | O(M + N)              | O(M + N) | Basic              |
| Two Pointer Backward | O(M + N)              | O(1)     | Best               |
| Swap + Sort          | Higher                | O(1)     | Moderate           |
| Gap Method           | O((M + N) log(M + N)) | O(1)     | Advanced           |

---