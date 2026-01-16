# Majority Element II (LeetCode 229)

## Problem Statement

Given an integer array `nums` of size `n`, return **all elements** that appear **more than ⌊n / 3⌋ times**.

---

## Examples

### Example 1
**Input**
`nums = [3,2,3]`

**Output**
`[3]`

### Example 2
**Input**
`nums = [1]`

**Output**
`[1]`

### Example 3
**Input**
`nums = [1,2]`

**Output**
`[1,2]`

---

## Constraints

- `1 <= nums.length <= 5 × 10⁴`
- `-10⁹ <= nums[i] <= 10⁹`

---

## Key Observation

There can be **at most two elements** that appear more than `⌊n / 3⌋` times.

Why?
- If three different elements each appeared more than `n/3` times, their total count would exceed `n`, which is impossible.

---

## Follow-up

Can you solve the problem:
- In **O(n)** time?
- Using **O(1)** extra space?

---

## Topics

- Array
- Hash Table
- Boyer–Moore Voting Algorithm


