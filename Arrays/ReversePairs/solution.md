# 493. Reverse Pairs — Solution

---

# 🔴 1. Brute Force Approach

## 🧠 Intuition

We are asked to count pairs (i, j) such that:

- i < j
- nums[i] > 2 * nums[j]

The most direct approach:

For every element nums[i],
check all elements to its right (nums[j] where j > i).

If the condition satisfies,
increment the counter.

This checks all possible pairs.

It is simple, direct, and slow.

---

## ⏱ Complexity

Time: O(N²)  
Space: O(1)

---

## Code

```cpp
class Solution {
public:
    int reversePairs(vector<int>& nums) {
        int n = nums.size();
        int cnt = 0;

        for (int i = 0; i < n; i++) {
            for (int j = i + 1; j < n; j++) {
                if ((long long)nums[i] > 2LL * nums[j]) {
                    cnt++;
                }
            }
        }

        return cnt;
    }
};
```
---

# 🟢 Optimal Approach (Merge Sort Based)

## 🧠 Core Intuition

This problem is a modified version of counting inversions.

We must count pairs (i, j) such that:

- i < j
- nums[i] > 2 * nums[j]

A brute force solution takes O(N²), which is too slow for N up to 5 * 10^4.

So we use Divide and Conquer (Merge Sort).

---

## 💡 Key Observation

During merge sort:

- Left half is sorted.
- Right half is sorted.

If we fix an element in the left half (nums[i]),  
we can efficiently count how many elements in the right half satisfy:

nums[i] > 2 * nums[j]

Because the right half is sorted:

If nums[i] > 2 * nums[j] is true for some j,  
it will also be true for all elements before j.

So we move a pointer forward only once.

This makes counting linear at each merge step.

---

## 🔹 Algorithm

1. Recursively divide the array into two halves.
2. Count reverse pairs in:
   - Left half
   - Right half
3. Count cross pairs between left and right halves.
4. Merge the two sorted halves.

---

## ⏱ Complexity

Time: O(2N log N)  
Space: O(N)

---

## Code (C++)

```cpp
class Solution {
public:

    int countPairs(vector<int>& arr, int low, int mid, int high) {
        int right = mid + 1;
        int cnt = 0;

        for (int i = low; i <= mid; i++) {
            while (right <= high && 
                   (long long)arr[i] > 2LL * arr[right]) {
                right++;
            }
            cnt += (right - (mid + 1));
        }

        return cnt;
    }

    void merge(vector<int>& arr, int low, int mid, int high) {
        vector<int> temp;
        int left = low;
        int right = mid + 1;

        while (left <= mid && right <= high) {
            if (arr[left] <= arr[right])
                temp.push_back(arr[left++]);
            else
                temp.push_back(arr[right++]);
        }

        while (left <= mid)
            temp.push_back(arr[left++]);

        while (right <= high)
            temp.push_back(arr[right++]);

        for (int i = low; i <= high; i++) {
            arr[i] = temp[i - low];
        }
    }

    int mergeSort(vector<int>& arr, int low, int high) {
        if (low >= high) return 0;

        int mid = (low + high) / 2;
        int cnt = 0;

        cnt += mergeSort(arr, low, mid);
        cnt += mergeSort(arr, mid + 1, high);
        cnt += countPairs(arr, low, mid, high);
        merge(arr, low, mid, high);

        return cnt;
    }

    int reversePairs(vector<int>& nums) {
        return mergeSort(nums, 0, nums.size() - 1);
    }
};
```