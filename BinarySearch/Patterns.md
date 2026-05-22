# 🔍 Binary Search DSA Patterns

> A complete reference covering Binary Search on 1D Arrays, 2D Matrices, and on Answers — with every classic problem solved.

---

## 📚 Table of Contents

| Section | Problems |
|---------|---------|
| [🔧 Core Templates](#-core-templates) | Standard, Lower Bound, Upper Bound |
| [📌 1D Array Problems](#-1d-array-problems) | Search, Floor/Ceil, Rotated Array, Peak |
| [🎯 Binary Search on Answers](#-binary-search-on-answers) | Koko, Bouquets, Aggressive Cows, Median |
| [🟦 2D Matrix Problems](#-2d-matrix-problems) | Search in Matrix, Peak II, Median |
| [🧠 Pattern Decision Guide](#-pattern-decision-guide) | When to use which template |

---

## 🔧 Core Templates

### The Golden Rule
> Binary search works on any **monotonic** space — not just sorted arrays. If you can define a condition `f(x)` such that it is `false ... false ... true ... true`, binary search finds the boundary.

---

### Template 1 — Classic Search
```cpp
int left = 0, right = n - 1;

while (left <= right) {
    int mid = left + (right - left) / 2;   // never (left+right)/2 — overflows

    if (arr[mid] == target)  return mid;
    else if (arr[mid] < target) left  = mid + 1;
    else                        right = mid - 1;
}

return -1;
```

---

### Template 2 — Lower Bound (First index ≥ target)
```cpp
// Returns first index where arr[index] >= target
int left = 0, right = n;   // right = n (answer can be n = "not found")

while (left < right) {
    int mid = left + (right - left) / 2;

    if (arr[mid] >= target) right = mid;    // mid could be answer
    else                    left  = mid + 1;
}

return left;   // left == right == answer
```

---

### Template 3 — Upper Bound (First index > target)
```cpp
// Returns first index where arr[index] > target
int left = 0, right = n;

while (left < right) {
    int mid = left + (right - left) / 2;

    if (arr[mid] > target) right = mid;
    else                   left  = mid + 1;
}

return left;
```

---

### Lower Bound vs Upper Bound Summary

| | Lower Bound | Upper Bound |
|---|---|---|
| Condition | `arr[mid] >= target` | `arr[mid] > target` |
| Finds | First index ≥ target | First index > target |
| Count of target | `upper - lower` | — |
| Initial `right` | `n` | `n` |

---

## 📌 1D Array Problems

---

### 1. Search X in Sorted Array
**Difficulty:** Easy

**Idea:** Classic binary search.

```cpp
int search(vector<int>& arr, int target) {
    int left = 0, right = arr.size() - 1;

    while (left <= right) {
        int mid = left + (right - left) / 2;

        if (arr[mid] == target) return mid;
        else if (arr[mid] < target) left  = mid + 1;
        else                        right = mid - 1;
    }

    return -1;
}
```

**Complexity:** Time O(log n) · Space O(1)

---

### 2. Lower Bound
**Difficulty:** Easy

**Idea:** Find first index where `arr[index] >= target`.

```cpp
int lowerBound(vector<int>& arr, int target) {
    int left = 0, right = arr.size();

    while (left < right) {
        int mid = left + (right - left) / 2;
        if (arr[mid] >= target) right = mid;
        else                    left  = mid + 1;
    }

    return left;
}
```

**Complexity:** Time O(log n) · Space O(1)

---

### 3. Upper Bound
**Difficulty:** Easy

**Idea:** Find first index where `arr[index] > target`.

```cpp
int upperBound(vector<int>& arr, int target) {
    int left = 0, right = arr.size();

    while (left < right) {
        int mid = left + (right - left) / 2;
        if (arr[mid] > target) right = mid;
        else                   left  = mid + 1;
    }

    return left;
}
```

**Complexity:** Time O(log n) · Space O(1)

---

### 4. Search Insert Position
**Difficulty:** Easy

**Idea:** Return index of target if found; otherwise return where it would be inserted = lower bound.

```cpp
int searchInsert(vector<int>& arr, int target) {
    int left = 0, right = arr.size();

    while (left < right) {
        int mid = left + (right - left) / 2;
        if (arr[mid] >= target) right = mid;
        else                    left  = mid + 1;
    }

    return left;   // same as lower bound
}
```

**Complexity:** Time O(log n) · Space O(1)

---

### 5. Floor and Ceil in Sorted Array
**Difficulty:** Easy

**Floor** = largest element ≤ target  
**Ceil**  = smallest element ≥ target (= lower bound)

```cpp
int findFloor(vector<int>& arr, int target) {
    int left = 0, right = arr.size() - 1, floor = -1;

    while (left <= right) {
        int mid = left + (right - left) / 2;

        if (arr[mid] <= target) {
            floor = arr[mid];   // candidate
            left  = mid + 1;    // try larger
        } else {
            right = mid - 1;
        }
    }

    return floor;
}

int findCeil(vector<int>& arr, int target) {
    int left = 0, right = arr.size() - 1, ceil = -1;

    while (left <= right) {
        int mid = left + (right - left) / 2;

        if (arr[mid] >= target) {
            ceil  = arr[mid];   // candidate
            right = mid - 1;    // try smaller
        } else {
            left = mid + 1;
        }
    }

    return ceil;
}
```

**Complexity:** Time O(log n) · Space O(1)

---

### 6. First and Last Occurrence
**Difficulty:** Easy

**Idea:** First occurrence = lower bound. Last occurrence = upper bound − 1.

```cpp
vector<int> firstAndLast(vector<int>& arr, int target) {
    int n = arr.size();

    int first = lowerBound(arr, target);
    if (first == n || arr[first] != target)
        return {-1, -1};

    int last = upperBound(arr, target) - 1;
    return {first, last};
}
```

**Complexity:** Time O(log n) · Space O(1)

---

### 7. Count Occurrences in Sorted Array
**Difficulty:** Easy

**Idea:** `count = upper_bound - lower_bound`

```cpp
int countOccurrences(vector<int>& arr, int target) {
    int first = lowerBound(arr, target);

    if (first == arr.size() || arr[first] != target)
        return 0;

    int last = upperBound(arr, target);
    return last - first;
}
```

**Complexity:** Time O(log n) · Space O(1)

---

### 8. Search in Rotated Sorted Array — I (No Duplicates)
**Difficulty:** Medium

**Idea:** One half is always sorted. Determine which half, then decide where target lies.

```cpp
int searchRotated(vector<int>& arr, int target) {
    int left = 0, right = arr.size() - 1;

    while (left <= right) {
        int mid = left + (right - left) / 2;

        if (arr[mid] == target) return mid;

        if (arr[left] <= arr[mid]) {            // left half is sorted
            if (arr[left] <= target && target < arr[mid])
                right = mid - 1;
            else
                left  = mid + 1;
        } else {                                 // right half is sorted
            if (arr[mid] < target && target <= arr[right])
                left  = mid + 1;
            else
                right = mid - 1;
        }
    }

    return -1;
}
```

**Complexity:** Time O(log n) · Space O(1)

---

### 9. Search in Rotated Sorted Array — II (With Duplicates)
**Difficulty:** Medium

**Key Difference:** When `arr[left] == arr[mid] == arr[right]`, we cannot determine which half is sorted → shrink both ends.

```cpp
bool searchRotatedII(vector<int>& arr, int target) {
    int left = 0, right = arr.size() - 1;

    while (left <= right) {
        int mid = left + (right - left) / 2;

        if (arr[mid] == target) return true;

        // Can't determine sorted half — shrink both
        if (arr[left] == arr[mid] && arr[mid] == arr[right]) {
            left++; right--; continue;
        }

        if (arr[left] <= arr[mid]) {
            if (arr[left] <= target && target < arr[mid])
                right = mid - 1;
            else
                left  = mid + 1;
        } else {
            if (arr[mid] < target && target <= arr[right])
                left  = mid + 1;
            else
                right = mid - 1;
        }
    }

    return false;
}
```

**Complexity:** Time O(log n) average, O(n) worst (all duplicates) · Space O(1)

---

### 10. Find Minimum in Rotated Sorted Array
**Difficulty:** Easy

**Idea:** The minimum is in the unsorted half. Keep shrinking toward the rotation point.

```cpp
int findMin(vector<int>& arr) {
    int left = 0, right = arr.size() - 1;
    int minVal = INT_MAX;

    while (left <= right) {
        int mid = left + (right - left) / 2;

        if (arr[left] <= arr[mid]) {       // left half sorted
            minVal = min(minVal, arr[left]);
            left   = mid + 1;              // minimum must be in right half
        } else {                            // right half sorted
            minVal = min(minVal, arr[mid]);
            right  = mid - 1;              // minimum could be arr[mid] or left
        }
    }

    return minVal;
}
```

**Complexity:** Time O(log n) · Space O(1)

---

### 11. Find How Many Times Array Is Rotated
**Difficulty:** Easy

**Idea:** The index of the minimum element = number of rotations.

```cpp
int countRotations(vector<int>& arr) {
    int left = 0, right = arr.size() - 1;
    int minIdx = 0;

    while (left <= right) {
        if (arr[left] <= arr[right])  {    // already sorted segment
            if (arr[left] < arr[minIdx]) minIdx = left;
            break;
        }

        int mid = left + (right - left) / 2;

        if (arr[left] <= arr[mid]) {
            if (arr[left] < arr[minIdx]) minIdx = left;
            left = mid + 1;
        } else {
            if (arr[mid] < arr[minIdx]) minIdx = mid;
            right = mid - 1;
        }
    }

    return minIdx;
}
```

**Complexity:** Time O(log n) · Space O(1)

---

### 12. Single Element in Sorted Array
**Difficulty:** Medium

**Idea:** All pairs satisfy `arr[even] == arr[odd]` before the single element. After it, this pattern breaks. Binary search on even indices.

```cpp
int singleNonDuplicate(vector<int>& arr) {
    int left = 0, right = arr.size() - 1;

    while (left < right) {
        int mid = left + (right - left) / 2;

        if (mid % 2 == 1) mid--;    // ensure mid is even

        if (arr[mid] == arr[mid + 1])
            left  = mid + 2;        // single is to the right
        else
            right = mid;            // single is at mid or to the left
    }

    return arr[left];
}
```

**Invariant:** `left` always points to an even index. The single element lies at `left` when `left == right`.

**Complexity:** Time O(log n) · Space O(1)

---

### 13. Find Peak Element (1D)
**Difficulty:** Medium

**Idea:** A peak is any element greater than its neighbors. If `arr[mid] < arr[mid+1]`, a peak exists to the right. Otherwise to the left (or at mid).

```cpp
int findPeak(vector<int>& arr) {
    int left = 0, right = arr.size() - 1;

    while (left < right) {
        int mid = left + (right - left) / 2;

        if (arr[mid] < arr[mid + 1])
            left  = mid + 1;    // ascending — peak is to the right
        else
            right = mid;        // descending — peak is at mid or left
    }

    return left;   // index of peak
}
```

**Complexity:** Time O(log n) · Space O(1)

---

## 🎯 Binary Search on Answers

### Core Framework

Use when:
- You're asked to **minimize the maximum** or **maximize the minimum**
- The answer lies in a value range `[lo, hi]`
- You can write a `feasible(mid)` function that checks if `mid` works

```
Can we achieve answer ≤ mid?   →  minimize: search left when true
Can we achieve answer ≥ mid?   →  maximize: search right when true
```

### Universal Template
```cpp
int left = minPossibleAnswer;
int right = maxPossibleAnswer;

while (left < right) {
    int mid = left + (right - left) / 2;

    if (feasible(mid))  right = mid;      // mid works; try smaller (minimize)
    else                left  = mid + 1;  // mid too small
}

return left;
```

---

### 14. Find Square Root of N
**Difficulty:** Medium

**Search space:** `[1, n]`. Find largest `mid` where `mid * mid <= n`.

```cpp
int sqrtN(int n) {
    if (n < 2) return n;
    int left = 1, right = n / 2, ans = 1;

    while (left <= right) {
        long long mid = left + (right - left) / 2;

        if (mid * mid <= n) {
            ans  = mid;
            left = mid + 1;
        } else {
            right = mid - 1;
        }
    }

    return ans;
}
```

**Complexity:** Time O(log n) · Space O(1)

---

### 15. Find Nth Root of M
**Difficulty:** Medium

**Search space:** `[1, m]`. Find `mid` where `mid^n == m`.

```cpp
long long power(long long base, int exp, long long limit) {
    long long result = 1;
    for (int i = 0; i < exp; i++) {
        result *= base;
        if (result > limit) return result;  // early exit
    }
    return result;
}

int nthRoot(int n, int m) {
    int left = 1, right = m;

    while (left <= right) {
        int mid = left + (right - left) / 2;
        long long val = power(mid, n, m);

        if (val == m)      return mid;
        else if (val < m)  left  = mid + 1;
        else               right = mid - 1;
    }

    return -1;  // not a perfect nth root
}
```

**Complexity:** Time O(n · log m) · Space O(1)

---

### 16. Koko Eating Bananas
**Difficulty:** Medium

**Search space:** `[1, max(piles)]`. Find minimum speed `k` such that all piles are eaten in `h` hours.

```cpp
bool canEat(vector<int>& piles, int speed, int h) {
    long long hours = 0;
    for (int p : piles)
        hours += (p + speed - 1) / speed;  // ceil(p / speed)
    return hours <= h;
}

int minEatingSpeed(vector<int>& piles, int h) {
    int left = 1, right = *max_element(piles.begin(), piles.end());

    while (left < right) {
        int mid = left + (right - left) / 2;
        if (canEat(piles, mid, h)) right = mid;
        else                       left  = mid + 1;
    }

    return left;
}
```

**Complexity:** Time O(n · log(max)) · Space O(1)

---

### 17. Minimum Days to Make M Bouquets
**Difficulty:** Medium

**Search space:** `[1, max(bloomDay)]`. Find minimum day when you can make `m` bouquets of `k` consecutive flowers.

```cpp
bool canMake(vector<int>& bloomDay, int day, int m, int k) {
    int bouquets = 0, consecutive = 0;

    for (int d : bloomDay) {
        if (d <= day) {
            consecutive++;
            if (consecutive == k) { bouquets++; consecutive = 0; }
        } else {
            consecutive = 0;
        }
    }

    return bouquets >= m;
}

int minDays(vector<int>& bloomDay, int m, int k) {
    if ((long long)m * k > bloomDay.size()) return -1;

    int left = 1, right = *max_element(bloomDay.begin(), bloomDay.end());

    while (left < right) {
        int mid = left + (right - left) / 2;
        if (canMake(bloomDay, mid, m, k)) right = mid;
        else                              left  = mid + 1;
    }

    return left;
}
```

**Complexity:** Time O(n · log(max)) · Space O(1)

---

### 18. Find the Smallest Divisor
**Difficulty:** Medium

**Search space:** `[1, max(arr)]`. Find smallest divisor such that sum of `ceil(arr[i] / divisor)` ≤ threshold.

```cpp
int sumWithDivisor(vector<int>& arr, int d) {
    int total = 0;
    for (int x : arr) total += (x + d - 1) / d;
    return total;
}

int smallestDivisor(vector<int>& arr, int threshold) {
    int left = 1, right = *max_element(arr.begin(), arr.end());

    while (left < right) {
        int mid = left + (right - left) / 2;
        if (sumWithDivisor(arr, mid) <= threshold) right = mid;
        else                                        left  = mid + 1;
    }

    return left;
}
```

**Complexity:** Time O(n · log(max)) · Space O(1)

---

### 19. Capacity to Ship Packages Within D Days
**Difficulty:** Medium

**Search space:** `[max(weights), sum(weights)]`. Find minimum capacity such that all packages ship in `d` days.

```cpp
bool canShip(vector<int>& weights, int capacity, int days) {
    int daysNeeded = 1, load = 0;

    for (int w : weights) {
        if (load + w > capacity) { daysNeeded++; load = 0; }
        load += w;
    }

    return daysNeeded <= days;
}

int shipWithinDays(vector<int>& weights, int days) {
    int left  = *max_element(weights.begin(), weights.end());
    int right = accumulate(weights.begin(), weights.end(), 0);

    while (left < right) {
        int mid = left + (right - left) / 2;
        if (canShip(weights, mid, days)) right = mid;
        else                             left  = mid + 1;
    }

    return left;
}
```

**Complexity:** Time O(n · log(sum)) · Space O(1)

---

### 20. Kth Missing Positive Number
**Difficulty:** Medium

**Idea:** At index `i`, the number of missing positives = `arr[i] - (i + 1)`. Binary search for first index where missing count ≥ k.

```cpp
int findKthMissing(vector<int>& arr, int k) {
    int left = 0, right = arr.size();

    while (left < right) {
        int mid = left + (right - left) / 2;
        int missing = arr[mid] - (mid + 1);  // missing count up to arr[mid]

        if (missing >= k) right = mid;
        else              left  = mid + 1;
    }

    // left = first index where missing >= k
    // answer = left + k  (left elements before, k more missing)
    return left + k;
}
```

**Complexity:** Time O(log n) · Space O(1)

---

### 21. Aggressive Cows
**Difficulty:** Hard

**Search space:** `[1, max(stalls) - min(stalls)]`. Maximize minimum distance between `c` cows placed in stalls.

```cpp
bool canPlace(vector<int>& stalls, int cows, int minDist) {
    int count = 1, last = stalls[0];

    for (int i = 1; i < stalls.size(); i++) {
        if (stalls[i] - last >= minDist) {
            count++;
            last = stalls[i];
            if (count == cows) return true;
        }
    }

    return false;
}

int aggressiveCows(vector<int>& stalls, int cows) {
    sort(stalls.begin(), stalls.end());

    int left = 1, right = stalls.back() - stalls.front(), ans = 0;

    while (left <= right) {
        int mid = left + (right - left) / 2;

        if (canPlace(stalls, cows, mid)) {
            ans  = mid;        // valid — try larger minimum distance
            left = mid + 1;
        } else {
            right = mid - 1;
        }
    }

    return ans;
}
```

> ⚠️ This is a **maximize** problem — use `left <= right` and track `ans`, or flip the feasibility condition.

**Complexity:** Time O(n · log(max_dist)) · Space O(1)

---

### 22. Book Allocation Problem
**Difficulty:** Hard

**Search space:** `[max(pages), sum(pages)]`. Minimize the maximum pages assigned to any student with `m` students.

```cpp
bool canAllocate(vector<int>& pages, int students, int maxPages) {
    int count = 1, current = 0;

    for (int p : pages) {
        if (p > maxPages) return false;    // single book exceeds limit

        if (current + p > maxPages) {
            count++;
            current = 0;
        }
        current += p;
    }

    return count <= students;
}

int bookAllocation(vector<int>& pages, int m) {
    int left  = *max_element(pages.begin(), pages.end());
    int right = accumulate(pages.begin(), pages.end(), 0);

    while (left < right) {
        int mid = left + (right - left) / 2;
        if (canAllocate(pages, m, mid)) right = mid;
        else                            left  = mid + 1;
    }

    return left;
}
```

**Complexity:** Time O(n · log(sum)) · Space O(1)

---

### 23. Split Array — Largest Sum
**Difficulty:** Hard

**Identical to Book Allocation.** Minimize the maximum subarray sum when splitting into `k` subarrays.

```cpp
// Same as bookAllocation with k students
int splitArray(vector<int>& nums, int k) {
    return bookAllocation(nums, k);  // exact same logic
}
```

**Complexity:** Time O(n · log(sum)) · Space O(1)

---

### 24. Painter's Partition
**Difficulty:** Medium

**Identical pattern to Book Allocation.** Minimize the maximum time with `k` painters.

---

### 25. Minimize Max Distance to Gas Station
**Difficulty:** Hard

**Search space:** `[0, max_gap]` on real numbers. Use a count-based feasibility check.

```cpp
bool canPlace(vector<int>& stations, int k, double dist) {
    int count = 0;
    for (int i = 1; i < stations.size(); i++)
        count += (int)((stations[i] - stations[i-1]) / dist);  // stations to add
    return count <= k;
}

double minimizeMaxDist(vector<int>& stations, int k) {
    double left = 0, right = 1e8;

    for (int iter = 0; iter < 100; iter++) {   // 100 iterations = ~1e-30 precision
        double mid = (left + right) / 2.0;
        if (canPlace(stations, k, mid)) right = mid;
        else                            left  = mid;
    }

    return right;
}
```

**Complexity:** Time O(n · 100) = O(n) · Space O(1)

---

### 26. Median of Two Sorted Arrays
**Difficulty:** Hard

**Idea:** Binary search on the partition index of the smaller array. Ensure `left half elements ≤ right half elements`.

```cpp
double findMedianSortedArrays(vector<int>& A, vector<int>& B) {
    if (A.size() > B.size()) return findMedianSortedArrays(B, A);  // always search smaller

    int n = A.size(), m = B.size();
    int left = 0, right = n;
    int half = (n + m + 1) / 2;

    while (left <= right) {
        int i = left + (right - left) / 2;   // partition in A
        int j = half - i;                     // partition in B

        int Aleft  = (i == 0) ? INT_MIN : A[i - 1];
        int Aright = (i == n) ? INT_MAX : A[i];
        int Bleft  = (j == 0) ? INT_MIN : B[j - 1];
        int Bright = (j == m) ? INT_MAX : B[j];

        if (Aleft <= Bright && Bleft <= Aright) {
            // correct partition
            int maxLeft  = max(Aleft, Bleft);
            int minRight = min(Aright, Bright);

            if ((n + m) % 2 == 1) return maxLeft;
            return (maxLeft + minRight) / 2.0;
        } else if (Aleft > Bright) {
            right = i - 1;
        } else {
            left = i + 1;
        }
    }

    return 0.0;
}
```

**Complexity:** Time O(log(min(n, m))) · Space O(1)

---

### 27. Kth Element of Two Sorted Arrays
**Difficulty:** Hard

**Idea:** Binary search on the partition of the smaller array. Ensure exactly `k` elements on the left side.

```cpp
int kthElement(vector<int>& A, vector<int>& B, int k) {
    if (A.size() > B.size()) return kthElement(B, A, k);

    int n = A.size(), m = B.size();
    int left = max(0, k - m), right = min(k, n);

    while (left <= right) {
        int i = left + (right - left) / 2;
        int j = k - i;

        int Aleft  = (i == 0) ? INT_MIN : A[i - 1];
        int Aright = (i == n) ? INT_MAX : A[i];
        int Bleft  = (j == 0) ? INT_MIN : B[j - 1];
        int Bright = (j == m) ? INT_MAX : B[j];

        if (Aleft <= Bright && Bleft <= Aright)
            return max(Aleft, Bleft);
        else if (Aleft > Bright)
            right = i - 1;
        else
            left  = i + 1;
    }

    return -1;
}
```

**Complexity:** Time O(log(min(n, m))) · Space O(1)

---

## 🟦 2D Matrix Problems

---

### 28. Find Row with Maximum 1s
**Difficulty:** Easy

**Idea:** For each row (sorted), find the first 1 using lower bound. Track the row with smallest index of 1.

```cpp
int rowWithMax1s(vector<vector<int>>& mat) {
    int rows = mat.size(), cols = mat[0].size();
    int maxRow = -1, maxCount = 0;

    for (int i = 0; i < rows; i++) {
        int firstOne = lowerBound(mat[i], 1);  // first index with value >= 1
        int count = cols - firstOne;

        if (count > maxCount) {
            maxCount = count;
            maxRow = i;
        }
    }

    return maxRow;
}
```

**Complexity:** Time O(rows · log cols) · Space O(1)

---

### 29. Search in a 2D Matrix (Sorted Row + Column)
**Difficulty:** Hard

**Idea:** Treat the entire matrix as a flattened sorted array. Map 1D index to 2D: `row = mid / cols`, `col = mid % cols`.

```cpp
bool searchMatrix(vector<vector<int>>& mat, int target) {
    int rows = mat.size(), cols = mat[0].size();
    int left = 0, right = rows * cols - 1;

    while (left <= right) {
        int mid = left + (right - left) / 2;
        int val = mat[mid / cols][mid % cols];

        if (val == target) return true;
        else if (val < target) left  = mid + 1;
        else                   right = mid - 1;
    }

    return false;
}
```

**Precondition:** Each row is sorted, and the first element of each row > last element of previous row.

**Complexity:** Time O(log(rows × cols)) · Space O(1)

---

### 30. Search in 2D Matrix — II (Sorted Rows and Columns)
**Difficulty:** Hard

**Idea:** Start from the **top-right corner**. If current > target, move left. If current < target, move down. This eliminates one row or column per step.

```cpp
bool searchMatrixII(vector<vector<int>>& mat, int target) {
    int row = 0, col = mat[0].size() - 1;

    while (row < mat.size() && col >= 0) {
        if (mat[row][col] == target) return true;
        else if (mat[row][col] > target) col--;    // too big — go left
        else                             row++;    // too small — go down
    }

    return false;
}
```

**Precondition:** Each row and each column is independently sorted in ascending order.

**Complexity:** Time O(rows + cols) · Space O(1)

---

### 31. Find Peak Element — II (2D Matrix)
**Difficulty:** Medium

**Idea:** Binary search on columns. For each `mid` column, find the row with the global maximum in that column. If the peak condition fails, move toward the larger neighbor column.

```cpp
int findPeak2D(vector<vector<int>>& mat) {
    int rows = mat.size(), cols = mat[0].size();
    int left = 0, right = cols - 1;

    while (left <= right) {
        int midCol = left + (right - left) / 2;

        // Find row with max value in midCol
        int maxRow = 0;
        for (int r = 0; r < rows; r++)
            if (mat[r][midCol] > mat[maxRow][midCol])
                maxRow = r;

        int curr  = mat[maxRow][midCol];
        int leftV  = (midCol > 0)      ? mat[maxRow][midCol - 1] : -1;
        int rightV = (midCol < cols-1) ? mat[maxRow][midCol + 1] : -1;

        if (curr > leftV && curr > rightV)
            return maxRow * cols + midCol;  // peak found (encode as flat index)
        else if (leftV > curr)
            right = midCol - 1;
        else
            left  = midCol + 1;
    }

    return -1;
}
```

**Complexity:** Time O(rows · log cols) · Space O(1)

---

### 32. Matrix Median
**Difficulty:** Hard

**Idea:** Binary search on value range `[min, max]`. For each candidate median, count elements ≤ mid across all rows using binary search. Find smallest value where count ≥ `(rows × cols + 1) / 2`.

```cpp
int countLessEq(vector<int>& row, int mid) {
    return upper_bound(row.begin(), row.end(), mid) - row.begin();
}

int matrixMedian(vector<vector<int>>& mat) {
    int rows = mat.size(), cols = mat[0].size();
    int desired = (rows * cols + 1) / 2;

    int left = 1, right = 1e9;

    while (left < right) {
        int mid = left + (right - left) / 2;
        int count = 0;

        for (auto& row : mat)
            count += countLessEq(row, mid);

        if (count >= desired) right = mid;
        else                  left  = mid + 1;
    }

    return left;
}
```

**Precondition:** Each row is sorted.

**Complexity:** Time O(rows · log(cols) · log(max_val)) · Space O(1)

---

## 🧠 Pattern Decision Guide

```
Problem involves a sorted array?
├── Find exact value          → Classic Binary Search (Template 1)
├── Find first index ≥ x      → Lower Bound (Template 2)
├── Find first index > x      → Upper Bound (Template 3)
├── Array is rotated?
│   ├── No duplicates         → Search Rotated I
│   └── Has duplicates        → Search Rotated II (shrink both ends)
├── Find min in rotated       → Track sorted half minimum
├── Single non-duplicate      → Binary search on even indices
└── Find peak                 → Compare arr[mid] vs arr[mid+1]

Problem asks to minimize/maximize a value?
└── Binary Search on Answers
    ├── Minimize maximum      → feasible checks upper bound
    ├── Maximize minimum      → feasible checks lower bound
    └── Key: define feasible(mid) in O(n), search space is value range

Problem is on a 2D matrix?
├── Row-sorted + globally sorted  → Flatten to 1D binary search
├── Row + column sorted           → Top-right corner walk
├── Find max 1s row               → Lower bound per row
├── Find peak element             → Binary search on columns
└── Find median                   → Binary search on value range
```

---

## 📋 Complete Problem Reference

| # | Problem | Difficulty | Technique | Time |
|---|---------|-----------|-----------|------|
| 1 | Search X in Sorted Array | Easy | Classic BS | O(log n) |
| 2 | Lower Bound | Easy | Template 2 | O(log n) |
| 3 | Upper Bound | Easy | Template 3 | O(log n) |
| 4 | Search Insert Position | Easy | Lower Bound | O(log n) |
| 5 | Floor and Ceil | Easy | Two-sided BS | O(log n) |
| 6 | First and Last Occurrence | Easy | LB + UB | O(log n) |
| 7 | Count Occurrences | Easy | UB − LB | O(log n) |
| 8 | Search Rotated Array I | Medium | Sorted half check | O(log n) |
| 9 | Search Rotated Array II | Medium | Shrink on ambiguity | O(log n) avg |
| 10 | Min in Rotated Array | Easy | Track sorted half | O(log n) |
| 11 | Count Rotations | Easy | Index of minimum | O(log n) |
| 12 | Single Element in Sorted | Medium | Even index BS | O(log n) |
| 13 | Find Peak Element | Medium | Slope direction | O(log n) |
| 14 | Square Root | Medium | BS on answer | O(log n) |
| 15 | Nth Root | Medium | BS on answer | O(n log m) |
| 16 | Koko Eating Bananas | Medium | BS on speed | O(n log max) |
| 17 | Min Days for Bouquets | Medium | BS on days | O(n log max) |
| 18 | Smallest Divisor | Medium | BS on divisor | O(n log max) |
| 19 | Ship Packages in D Days | Medium | BS on capacity | O(n log sum) |
| 20 | Kth Missing Positive | Medium | BS on index | O(log n) |
| 21 | Aggressive Cows | Hard | BS on distance | O(n log d) |
| 22 | Book Allocation | Hard | BS on max pages | O(n log sum) |
| 23 | Split Array Largest Sum | Hard | Same as #22 | O(n log sum) |
| 24 | Painter's Partition | Medium | Same as #22 | O(n log sum) |
| 25 | Min Max Distance Gas Station | Hard | BS on real value | O(100n) |
| 26 | Median of 2 Sorted Arrays | Hard | Partition BS | O(log min(n,m)) |
| 27 | Kth of 2 Sorted Arrays | Hard | Partition BS | O(log min(n,m)) |
| 28 | Row with Max 1s | Easy | LB per row | O(n log m) |
| 29 | Search in 2D Matrix | Hard | Flatten + BS | O(log nm) |
| 30 | Search in 2D Matrix II | Hard | Top-right walk | O(n + m) |
| 31 | Find Peak Element II | Medium | BS on columns | O(n log m) |
| 32 | Matrix Median | Hard | BS on value | O(n log m log V) |

---

## 🐛 Common Mistakes

| Mistake | Fix |
|---------|-----|
| `mid = (left + right) / 2` | Always use `left + (right - left) / 2` |
| Wrong `right` initial value | Use `n-1` for classic, `n` for lower/upper bound |
| `while (left <= right)` vs `while (left < right)` | `<=` when returning mid directly; `<` when converging to boundary |
| Not handling empty/single-element array | Check `n == 0` or `n == 1` before searching |
| Greedy feasibility off-by-one | Trace through a small example manually |
| Rotated array: not handling `arr[left] == arr[mid]` | Shrink both ends when duplicates present |

---

## 🎯 Final Advice

Binary Search has **one pattern, infinite disguises.**

Every binary search problem reduces to:
1. **Define the search space** — what are `left` and `right`?
2. **Write `feasible(mid)`** — what condition are you checking?
3. **Decide direction** — which half eliminates impossible answers?
4. **Pick the template** — returning value directly vs converging to boundary

Master the three templates and the "binary search on answer" mindset, and every problem in this list becomes a variation of the same idea.

---

*Generated for DSA Interview Preparation · Binary Search Patterns Reference*