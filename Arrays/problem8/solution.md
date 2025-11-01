# ✅ Solutions for "Sort Array of 0s, 1s, and 2s"

---

## 🪓 Brute Force Approach — Merge Sort
**Idea:**  
Simply apply a generic sorting algorithm such as Merge Sort or any comparison-based sort.  

**Complexity:**  
- Time: `O(n log n)`  
- Space: `O(n)`  
- Not efficient for such a small range of elements.

---

## ⚙️ Better Approach — Counting Method

**Logic:**  
1. Count the number of 0s, 1s, and 2s in the array.  
2. Rewrite the array based on these counts.

**Steps:**  
- Initialize three counters: `count0`, `count1`, and `count2`.  
- Iterate once to count occurrences.  
- Iterate again to overwrite elements:  
  - First `count0` elements as 0  
  - Next `count1` elements as 1  
  - Remaining `count2` elements as 2  

**Complexity:**  
- Time: `O(2n)`  
- Space: `O(1)`  
- Simpler, but requires two passes.

---

## 🚀 Optimal Approach — Dutch National Flag Algorithm

**Concept:**  
Use three pointers — `low`, `mid`, and `high` — to partition the array into three sections:

| Section | Range | Contains |
|----------|--------|----------|
|1.| `[0 ... low-1]` | Sorted 0s |
|2.| `[low ... mid-1]` | Sorted 1s |
|3.|`[high+1 ... n-1]` | Sorted 2s |

---

### 🧩 Algorithm

```cpp
int low = 0
int mid = 0
int high = n - 1

while (mid <= high){
    if (nums[mid] == 0){
        swap(nums[low], nums[mid])
        low += 1
        mid += 1
    }
    elif (nums[mid] == 1) mid += 1
    else{ // nums[mid] == 2
        swap(nums[mid], nums[high])
        high -= 1
    }
}
```


**Explanation:**  
- Move 0s to the beginning by swapping with `low`.  
- Leave 1s in the middle (just move `mid`).  
- Move 2s to the end by swapping with `high`.  

**Complexity:**  
- Time: `O(n)`  
- Space: `O(1)`  

---

