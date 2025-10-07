### 💻 Solutions for Two Sum

----

## 🧠 Brute Force Approach
### 🔹 Idea:

- Try every possible pair of elements and check whether their sum equals the given target.
- If yes, return their indices immediately.

- This is the simplest way to solve the problem but inefficient for large arrays.

### 🧩 Algorithm:

1. Loop i from 0 to n-1.
2. For each i, loop j from i+1 to n-1.
3. If nums[i] + nums[j] == target, return `{i, j}`.
4. If no pair is found (though the problem guarantees one), return an empty array.

### ⏱️ Time Complexity:
- **O(n²)** — two nested loops.

### 💾 Space Complexity:
- **O(1)** — only constant extra space used.

### 🧩 Code:
```cpp
class Solution {
public:
    vector<int> twoSum(vector<int>& nums, int target) {
        int n = nums.size();
        for (int i = 0; i < n - 1; i++) {
            for (int j = i + 1; j < n; j++) {
                if (nums[i] + nums[j] == target)
                    return {i, j};
            }
        }
        return {};
    }
};
```
---

### ⚙️ Better Solution (Using Hashing)
### 🔹 Idea:

- Use a hashmap to store elements and their indices for faster lookups.
- For each number nums[i], calculate its complement → target - nums[i].
- If that complement already exists in the map, we’ve found our pair.

### 💡 Intuition:

1. Instead of checking all pairs (which takes O(n²)), we can store elements we’ve seen so far in a hash map.
2. When we encounter a new number, we instantly check whether its complement (needed to reach target) is already in the map.
3. This reduces the time complexity to O(n) on average because hashmap lookups and insertions take O(1).

### 🧩 Algorithm:

1. Initialize an empty hashmap mpp (key = number, value = index).

2. Loop through the array nums:

3. Calculate complement = target - nums[i].

4. If complement exists in mpp, return {mpp[complement], i}.

5. Otherwise, store nums[i] with its index in mpp.

6. If no pair found (not possible per constraints), return {}.

### ⏱️ Time Complexity:

- **O(n)** average, due to single traversal and constant-time map operations.

## 💾 Space Complexity:

- **O(n)** for storing elements in the hashmap.

### 🧩 Code:
```cpp
class Solution {
public:
    vector<int> twoSum(vector<int>& nums, int target) {
        unordered_map<int, int> mpp; // {value : index}
        for (int i = 0; i < nums.size(); i++) {
            int complement = target - nums[i];
            if (mpp.find(complement) != mpp.end()) {
                return {mpp[complement], i};
            }
            mpp[nums[i]] = i;
        }
        return {};
    }
};
```
----

### ✅ Optimal Solution (Two Pointers – Theoretical Approach)
### 💡 Intuition:

- If we don’t need the original indices, sorting the array allows us to use the two-pointer technique.

- Place one pointer at the start (i) and another at the end (j).

- Compute the sum of nums[i] + nums[j].

- If it equals target, we found the pair.

- If the sum is greater than target, move the right pointer left (j--).

- If the sum is smaller than target, move the left pointer right (i++).

- Since sorting takes O(n log n) and the two-pointer pass is O(n), this is an O(n log n) solution overall.

- **⚠️ Note**: This approach changes the order of elements, so we must store the original indices before sorting.

### 🧩 Algorithm:

1. Create a vector of pairs {value, index}.
2. Sort the vector by value.
3. Initialize two pointers: i = 0, j = n - 1.
4. While i < j:
    Compute sum = v[i].first + v[j].first.
    If sum == target, return {v[i].second, v[j].second}.
    If sum < target, move i++.
    Else move j--.
5. Return {}.

### ⏱️ Time Complexity:
- **O(n log n)** — due to sorting.

### 💾 Space Complexity:
- **O(n)** — for storing pairs of (value, index).

### 🧩 Code:
```cpp
class Solution {
public:
    vector<int> twoSum(vector<int>& nums, int target) {
        vector<pair<int, int>> v;
        for (int i = 0; i < nums.size(); i++) {
            v.push_back({nums[i], i});
        }

        sort(v.begin(), v.end());
        int i = 0, j = v.size() - 1;

        while (i < j) {
            int sum = v[i].first + v[j].first;
            if (sum == target)
                return {v[i].second, v[j].second};
            else if (sum < target)
                i++;
            else
                j--;
        }
        return {};
    }
};
```
---

### 🏁 Summary:

| Approach              | Time Complexity | Space Complexity | Description                        |
| --------------------- | --------------- | ---------------- | ---------------------------------- |
| Brute Force           | O(n²)           | O(1)             | Check every possible pair          |
| Hashmap (Better)      | O(n)            | O(n)             | Store and find complements quickly |
| Two Pointer (Optimal) | O(n log n)      | O(n)             | Sort + two pointer (theoretical)   |

