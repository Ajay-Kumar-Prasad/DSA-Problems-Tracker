# 152. Maximum Product Subarray

## 🔹 Problem Statement

Given an integer array `nums`, find a contiguous subarray within the array that has the largest product, and return that product.

The product of a subarray containing a single element is equal to that element.

The test cases are generated such that the answer fits within a 32-bit integer.

---

## 🧠 Examples

### Example 1

Input:
nums = [2,3,-2,4]

Output:
6

Explanation:
The subarray [2,3] has the largest product = 6.

---

### Example 2

Input:
nums = [-2,0,-1]

Output:
0

Explanation:
The result cannot be 2 because [-2,-1] is not a contiguous subarray.
The maximum product is 0.

---

## 🔒 Constraints

- 1 <= nums.length <= 2 * 10^4
- -10 <= nums[i] <= 10
- The product of any subarray of nums fits in a 32-bit integer.

---

## 🏷 Topics

- Array
- Dynamic Programming

---

## 📌 Notes

- The subarray must be contiguous.
- Negative numbers can flip the sign of the product.
- Zero can reset the product calculation.
- This problem requires careful handling of positive, negative, and zero values.
