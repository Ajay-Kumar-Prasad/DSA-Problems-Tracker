# ✅ Solutions for "Majority Element"

---

## 🪓 Brute Force Approach

**Idea:**  
For each element in the array, count its frequency.  
If it appears more than ⌊n / 2⌋ times, return it.

**Code:**

```cpp
class Solution {
public:
    int lengthOfLongestSubstring(string s) {
        int n = s.size();
        int maxLen = 0;
        for (int i = 0; i < n; i++) {
            vector<int> visited(256, 0);
            for (int j = i; j < n; j++) {
                if (visited[s[j]] == 1) break;
                visited[s[j]] = 1;
                maxLen = max(maxLen, j - i + 1);
            }
        }
        return maxLen;
    }
};
```

**Complexity:**  
- Time: `O(n²)`  
- Space: `O(1)`  

Very slow for large inputs, but conceptually simple.

---

## ⚙️ Better Approach — Using Hash Map

**Logic:**  
Store the number of occurrences of each element in a map.  
Then, iterate to find the element with frequency greater than ⌊n / 2⌋.

**Steps:**  
1. Traverse the array and store counts in a map.  
2. Check which element’s count exceeds `n / 2`.  

## Code
```cpp
class Solution {
public:
    int majorityElement(vector<int>& nums) {
        int n = nums.size();
        map<int,int> mpp;
        for(int i=0;i<n;i++) mpp[nums[i]]++;
        for(auto x: mpp){
            if(x.second > n/2) return x.first;
        }
        return -1;
    }
}
```

**Complexity:**  
- Time: `O(n log n + n)` (due to map operations)  
- Space: `O(n)`  

---

## 🚀 Optimal Approach — Moore’s Voting Algorithm

**Intuition:**  
There’s always a majority element, so if we pair each occurrence of a different element with the majority, the majority will still remain.

**Logic:**  
1. Initialize two variables: `ele` (candidate) and `count = 0`.  
2. Traverse the array:  
   - If `count == 0`, take current element as `ele`.  
   - If the current element equals `ele`, increment `count`.  
   - Otherwise, decrement `count`.  
3. After one pass, `ele` will hold the majority candidate.  
4. Optionally verify it by counting again.

---

### 🧩 Algorithm (C++ Implementation)
```cpp
class Solution {
public:
    int majorityElement(vector<int>& nums) {
        int n = nums.size();
        int ele;
        int c = 0;

        // Step 1: Find candidate using Moore’s Voting
        for(int i = 0; i < n; i++) {
            if(c == 0) {
                ele = nums[i];
                c = 1;
            }
            else if(nums[i] == ele) c++;
            else c--;
        }

        // Step 2: Verify candidate
        int freq = 0;
        for(int i = 0; i < n; i++) {
            if(nums[i] == ele) freq++;
        }

        if(freq > n / 2) return ele;
        return -1; // Not expected due to problem guarantee
    }
};
```

## Summary

| Approach    | Description               | Time           | Space |
| ----------- | ------------------------- | -------------- | ----- |
| Brute Force | Check every element count | O(n²)          | O(1)  |
| Better      | Hash map counting         | O(n log n + n) | O(n)  |
| Optimal     | Moore’s Voting Algorithm  | O(n)           | O(1)  |
