# Set Matrix Zeroes – Solution

## Approaches Overview

1. Brute Force  
2. Better Approach (Extra Row & Column Arrays)  
3. Optimal Approach (In-place Marking)

---

## 1. Brute Force Approach

### Concept
First, store the positions of all `0`s.  
Then, for each stored position, set the corresponding row and column to `0`.

---

### Algorithm
1. Traverse the matrix and store coordinates of all `0`s.
2. For each stored `(row, col)`:
   - Set the entire row to `0`
   - Set the entire column to `0`

---

### C++ Code
```cpp
class Solution {
public:
    void setZeroes(vector<vector<int>>& matrix) {
        int m = matrix.size();
        int n = matrix[0].size();
        vector<vector<int>> zeroes;

        for(int i = 0; i < m; i++){
            for(int j = 0; j < n; j++){
                if(matrix[i][j] == 0){
                    zeroes.push_back({i, j});
                }
            }
        }

        for(auto &z : zeroes){
            for(int j = 0; j < n; j++)
                matrix[z[0]][j] = 0;
            for(int i = 0; i < m; i++)
                matrix[i][z[1]] = 0;
        }
    }
};
```
---

## Time Complexity

- O((m × n) + k × (m + n))

**Worst case:** O((m × n)²)

## Space Complexity

- O(k) (number of zero elements)

**Verdict**
- ❌ Inefficient and unnecessary memory usage.

---

## Better Approach (Using Extra Space)

### Idea
Use two auxiliary arrays to keep track of which rows and columns need to be set to zero.

- `row[m]` → marks rows that should be zeroed
- `col[n]` → marks columns that should be zeroed

This avoids modifying the matrix while scanning it.

---

### Algorithm
1. Traverse the matrix:
   - If `matrix[i][j] == 0`, mark:
     - `row[i] = 1`
     - `col[j] = 1`
2. Traverse the matrix again:
   - If `row[i] == 1` **or** `col[j] == 1`, set `matrix[i][j] = 0`

---

### C++ Implementation
```cpp
class Solution {
public:
    void setZeroes(vector<vector<int>>& matrix) {
        int m = matrix.size();
        int n = matrix[0].size();
        vector<int> row(m, 0), col(n, 0);

        for(int i = 0; i < m; i++){
            for(int j = 0; j < n; j++){
                if(matrix[i][j] == 0){
                    row[i] = 1;
                    col[j] = 1;
                }
            }
        }

        for(int i = 0; i < m; i++){
            for(int j = 0; j < n; j++){
                if(row[i] || col[j])
                    matrix[i][j] = 0;
            }
        }
    }
};
```
---

## Complexity

**Time Complexity:** O(m × n)

**Space Complexity:** O(m + n)

**Verdict**
- Efficient but uses extra memory.

---

## Core Idea

Instead of using extra arrays, use the **first row and first column** of the matrix itself as markers.

---

## Key Observations

- `matrix[i][0] == 0` → row `i` must be zeroed
- `matrix[0][j] == 0` → column `j` must be zeroed
- A separate variable `col0` is required to track whether column `0` should be zeroed

---

## Algorithm

1. Traverse the matrix:
   - When a `0` is found, mark its row and column using the first row and column
2. Track column `0` separately using `col0`
3. Traverse the matrix from **bottom-right** to avoid overwriting markers
4. Zero out the first row and first column if required

---

## C++ Implementation

```cpp
class Solution {
public:
    void setZeroes(vector<vector<int>>& matrix) {
        int m = matrix.size();
        int n = matrix[0].size();
        int col0 = 1;

        // Step 1: Mark rows and columns
        for(int i = 0; i < m; i++){
            for(int j = 0; j < n; j++){
                if(matrix[i][j] == 0){
                    matrix[i][0] = 0;
                    if(j == 0) col0 = 0;
                    else matrix[0][j] = 0;
                }
            }
        }

        // Step 2: Update matrix using markers
        for(int i = m - 1; i >= 1; i--){
            for(int j = n - 1; j >= 1; j--){
                if(matrix[i][0] == 0 || matrix[0][j] == 0)
                    matrix[i][j] = 0;
            }
        }

        // Step 3: Handle first row
        if(matrix[0][0] == 0){
            for(int j = 0; j < n; j++)
                matrix[0][j] = 0;
        }

        // Step 4: Handle first column
        if(col0 == 0){
            for(int i = 0; i < m; i++)
                matrix[i][0] = 0;
        }
    }
};
```

---

### Complexity Analysis

**Time Complexity:** O(m × n)

**Space Complexity:** O(1)

---

### Summary

| Approach     | Time     | Space    | Verdict |
| ------------ | -------- | -------- | ------- |
| Brute Force  | O((mn)²) | O(k)     | ❌       |
| Extra Arrays | O(mn)    | O(m + n) | ⚠️      |
| In-place     | O(mn)    | O(1)     | ✅       |

---