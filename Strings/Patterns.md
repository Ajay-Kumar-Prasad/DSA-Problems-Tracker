# 🧵 String DSA Patterns

> A complete reference for mastering String problems through reusable patterns — covering all classic and tricky interview problems.

---

## 📚 Table of Contents

| Section | Patterns |
|---------|---------|
| [🔧 Core String Techniques](#-core-string-techniques) | Traversal, Two Pointer, Hashing, Stack |
| [📌 Pattern 1 — Two Pointer / Reverse](#-pattern-1--two-pointer--reverse) | Reverse words, Palindrome check |
| [📌 Pattern 2 — Stack-based Parsing](#-pattern-2--stack-based-parsing) | Parentheses depth, Valid parentheses |
| [📌 Pattern 3 — Frequency / Hashing](#-pattern-3--frequency--hashing) | Anagram, Isomorphic, Sort by frequency |
| [📌 Pattern 4 — Prefix / Sliding Window on String](#-pattern-4--prefix--sliding-window-on-string) | Longest common prefix, Substrings |
| [📌 Pattern 5 — String Simulation](#-pattern-5--string-simulation) | atoi, Roman to Integer, Rotate string |
| [📌 Pattern 6 — Greedy on Strings](#-pattern-6--greedy-on-strings) | Largest odd number, Outermost parentheses |
| [📌 Pattern 7 — Palindrome Patterns](#-pattern-7--palindrome-patterns) | Longest palindromic substring, Expand around center |
| [📌 Pattern 8 — Substring Counting](#-pattern-8--substring-counting) | Count substrings, Sum of beauty |
| [🧠 Pattern Decision Guide](#-pattern-decision-guide) | Choosing the right approach |
| [📋 Complete Problem Reference](#-complete-problem-reference) | All problems with technique + complexity |

---

## 🔧 Core String Techniques

### Why Strings Are Tricky
Strings combine multiple data structure patterns — arrays, stacks, hashmaps, and two pointers — but with extra constraints around character encoding, immutability, and substring vs subsequence distinctions.

---

### Key Building Blocks

**Frequency array (lowercase only):**
```cpp
int freq[26] = {0};
for (char c : s) freq[c - 'a']++;
```

**Frequency hashmap (any characters):**
```cpp
unordered_map<char, int> freq;
for (char c : s) freq[c]++;
```

**Two-pointer on string:**
```cpp
int left = 0, right = s.size() - 1;
while (left < right) {
    // process s[left] and s[right]
    left++; right--;
}
```

**Sliding window on string:**
```cpp
unordered_map<char, int> window;
int left = 0, result = 0;

for (int right = 0; right < s.size(); right++) {
    window[s[right]]++;

    while (/* constraint violated */) {
        window[s[left]]--;
        if (window[s[left]] == 0) window.erase(s[left]);
        left++;
    }

    result = max(result, right - left + 1);
}
```

---

## 📌 Pattern 1 — Two Pointer / Reverse

### Core Idea
Use two pointers from opposite ends, or split string by delimiter and reverse parts. Most "reverse" problems reduce to in-place swapping or token-based reconstruction.

---

### Problem 1 — Reverse Words in a Given String
**Difficulty:** Medium

**Idea:** Split by spaces, reverse the array of words, rejoin. Handle multiple spaces carefully.

```cpp
string reverseWords(string s) {
    vector<string> words;
    string word = "";

    for (char c : s) {
        if (c != ' ') {
            word += c;
        } else if (!word.empty()) {
            words.push_back(word);
            word = "";
        }
    }
    if (!word.empty()) words.push_back(word);

    reverse(words.begin(), words.end());

    string result = "";
    for (int i = 0; i < words.size(); i++) {
        if (i > 0) result += " ";
        result += words[i];
    }

    return result;
}
```

**Complexity:** Time O(n) · Space O(n)

---

### Problem 2 — Reverse Every Word in a String
**Difficulty:** Easy

**Idea:** Split by spaces, reverse each individual word, rejoin.

```cpp
string reverseEachWord(string s) {
    stringstream ss(s);
    string word, result = "";

    while (ss >> word) {
        reverse(word.begin(), word.end());
        if (!result.empty()) result += " ";
        result += word;
    }

    return result;
}
```

**Complexity:** Time O(n) · Space O(n)

---

### Problem 3 — Palindrome Check
**Difficulty:** Medium

**Idea:** Two pointers from both ends. Skip non-alphanumeric characters if needed.

```cpp
// Simple palindrome
bool isPalindrome(string s) {
    int left = 0, right = s.size() - 1;

    while (left < right) {
        if (s[left] != s[right]) return false;
        left++; right--;
    }

    return true;
}

// Valid Palindrome (ignore non-alphanumeric, case-insensitive)
bool isValidPalindrome(string s) {
    int left = 0, right = s.size() - 1;

    while (left < right) {
        while (left < right && !isalnum(s[left]))  left++;
        while (left < right && !isalnum(s[right])) right--;

        if (tolower(s[left]) != tolower(s[right])) return false;

        left++; right--;
    }

    return true;
}
```

**Complexity:** Time O(n) · Space O(1)

---

## 📌 Pattern 2 — Stack-based Parsing

### Core Idea
Use a stack (or a depth counter) to track open/close brackets or nested structures. Push on open, pop on close, process at each level.

---

### Problem 4 — Remove Outermost Parentheses
**Difficulty:** Medium

**Idea:** Track depth. Characters at depth 0 → 1 (open) and depth 1 → 0 (close) are the outermost — skip them. Include everything else.

```cpp
string removeOuterParentheses(string s) {
    string result = "";
    int depth = 0;

    for (char c : s) {
        if (c == '(') {
            if (depth > 0) result += c;   // not outermost open
            depth++;
        } else {
            depth--;
            if (depth > 0) result += c;   // not outermost close
        }
    }

    return result;
}
```

**Dry Run:** `"(()())"`
```
( → depth 0→1, skip (outermost open)
( → depth 1→2, ADD '('
) → depth 2→1, ADD ')'
( → depth 1→2, ADD '('
) → depth 2→1, ADD ')'
) → depth 1→0, skip (outermost close)
Result: "()()"  ✓
```

**Complexity:** Time O(n) · Space O(n)

---

### Problem 5 — Maximum Nesting Depth of Parentheses
**Difficulty:** Medium

**Idea:** Track current depth with a counter. Maximum depth reached = answer.

```cpp
int maxDepth(string s) {
    int depth = 0, maxD = 0;

    for (char c : s) {
        if (c == '(') {
            depth++;
            maxD = max(maxD, depth);
        } else if (c == ')') {
            depth--;
        }
    }

    return maxD;
}
```

**Complexity:** Time O(n) · Space O(1)

---

## 📌 Pattern 3 — Frequency / Hashing

### Core Idea
Count character frequencies to detect equality (anagram), mapping consistency (isomorphic), or sort by occurrence.

---

### Problem 6 — Check if Two Strings Are Anagrams
**Difficulty:** Easy

**Idea:** Two strings are anagrams if and only if their character frequency maps are identical.

```cpp
bool isAnagram(string s, string t) {
    if (s.size() != t.size()) return false;

    int freq[26] = {0};

    for (int i = 0; i < s.size(); i++) {
        freq[s[i] - 'a']++;
        freq[t[i] - 'a']--;
    }

    for (int f : freq)
        if (f != 0) return false;

    return true;
}
```

**Complexity:** Time O(n) · Space O(1) (fixed 26-char array)

---

### Problem 7 — Sort Characters by Frequency
**Difficulty:** Easy

**Idea:** Count frequencies, then sort characters by descending count, rebuild string.

```cpp
string frequencySort(string s) {
    unordered_map<char, int> freq;
    for (char c : s) freq[c]++;

    // Bucket sort: index = frequency, value = list of chars
    vector<string> buckets(s.size() + 1, "");
    for (auto& [c, f] : freq)
        buckets[f] += string(f, c);

    string result = "";
    for (int i = buckets.size() - 1; i >= 1; i--)
        result += buckets[i];

    return result;
}
```

**Complexity:** Time O(n) · Space O(n)

---

### Problem 8 — Isomorphic Strings
**Difficulty:** Easy

**Idea:** Every character in `s` must map to exactly one character in `t`, and vice versa. Use two maps to enforce both directions.

```cpp
bool isIsomorphic(string s, string t) {
    unordered_map<char, char> s2t, t2s;

    for (int i = 0; i < s.size(); i++) {
        char a = s[i], b = t[i];

        if (s2t.count(a) && s2t[a] != b) return false;
        if (t2s.count(b) && t2s[b] != a) return false;

        s2t[a] = b;
        t2s[b] = a;
    }

    return true;
}
```

**Example:**
```
s = "egg", t = "add"
e → a  ✓
g → d  ✓
g → d  ✓  (consistent)
Result: true

s = "foo", t = "bar"
f → b  ✓
o → a  ✓
o → r  ✗ (o already mapped to a)
Result: false
```

**Complexity:** Time O(n) · Space O(1) (at most 256 ASCII chars)

---

## 📌 Pattern 4 — Prefix / Sliding Window on String

### Core Idea
For common prefix problems, compare column by column across strings. For substring problems with constraints, use a sliding window with a frequency map.

---

### Problem 9 — Longest Common Prefix
**Difficulty:** Easy

**Idea:** Take the first string as reference. For each character position, check if all strings have the same character at that position.

```cpp
string longestCommonPrefix(vector<string>& strs) {
    if (strs.empty()) return "";

    string prefix = strs[0];

    for (int i = 1; i < strs.size(); i++) {
        // Shrink prefix until it matches start of strs[i]
        while (strs[i].find(prefix) != 0) {
            prefix = prefix.substr(0, prefix.size() - 1);
            if (prefix.empty()) return "";
        }
    }

    return prefix;
}
```

**Alternate — column by column:**
```cpp
string longestCommonPrefix(vector<string>& strs) {
    for (int col = 0; col < strs[0].size(); col++) {
        char c = strs[0][col];
        for (int row = 1; row < strs.size(); row++) {
            if (col >= strs[row].size() || strs[row][col] != c)
                return strs[0].substr(0, col);
        }
    }
    return strs[0];
}
```

**Complexity:** Time O(S) where S = total characters · Space O(1)

---

### Problem 10 — Rotate String
**Difficulty:** Easy

**Idea:** `s` can be rotated to get `goal` if and only if `goal` is a substring of `s + s`.

```cpp
bool rotateString(string s, string goal) {
    if (s.size() != goal.size()) return false;
    return (s + s).find(goal) != string::npos;
}
```

**Why it works:** Every rotation of `s` appears as a substring of `s + s`.

**Complexity:** Time O(n²) with `find`, O(n) with KMP · Space O(n)

---

## 📌 Pattern 5 — String Simulation

### Core Idea
Process the string character by character, maintaining state (sign, overflow guard, current number, etc.). Faithfully simulate what the problem describes.

---

### Problem 11 — String to Integer (atoi)
**Difficulty:** Medium

**Steps:**
1. Skip leading whitespace
2. Read optional sign
3. Read digits until non-digit or end
4. Clamp to `[INT_MIN, INT_MAX]`

```cpp
int myAtoi(string s) {
    int i = 0, n = s.size();
    long long result = 0;
    int sign = 1;

    // Step 1: skip whitespace
    while (i < n && s[i] == ' ') i++;

    // Step 2: read sign
    if (i < n && (s[i] == '+' || s[i] == '-')) {
        sign = (s[i] == '-') ? -1 : 1;
        i++;
    }

    // Step 3: read digits
    while (i < n && isdigit(s[i])) {
        result = result * 10 + (s[i] - '0');
        i++;

        // Step 4: clamp early
        if (result * sign >= INT_MAX) return INT_MAX;
        if (result * sign <= INT_MIN) return INT_MIN;
    }

    return (int)(result * sign);
}
```

**Edge Cases:**
```
"  -42"     → -42
"4193 words"→  4193
"words 987" →  0
"-91283472332" → INT_MIN
```

**Complexity:** Time O(n) · Space O(1)

---

### Problem 12 — Roman to Integer
**Difficulty:** Medium

**Idea:** Process right to left. If current value < previous value, subtract it (IV, IX, etc.); otherwise add it.

```cpp
int romanToInt(string s) {
    unordered_map<char, int> val = {
        {'I', 1}, {'V', 5},  {'X', 10}, {'L', 50},
        {'C', 100}, {'D', 500}, {'M', 1000}
    };

    int result = val[s.back()];

    for (int i = s.size() - 2; i >= 0; i--) {
        if (val[s[i]] < val[s[i + 1]])
            result -= val[s[i]];    // subtraction case (IV, IX, XL...)
        else
            result += val[s[i]];
    }

    return result;
}
```

**Subtraction rules:**
```
IV = 4    IX = 9
XL = 40   XC = 90
CD = 400  CM = 900
```

**Complexity:** Time O(n) · Space O(1)

---

## 📌 Pattern 6 — Greedy on Strings

### Core Idea
Make a locally optimal choice at each character without backtracking. Usually involves scanning from a specific end or maintaining a running condition.

---

### Problem 13 — Largest Odd Number in a String
**Difficulty:** Easy

**Idea:** A number is odd if its last digit is odd. Scan from right — the longest prefix ending at the first odd digit is the answer.

```cpp
string largestOddNumber(string num) {
    for (int i = num.size() - 1; i >= 0; i--) {
        if ((num[i] - '0') % 2 == 1)
            return num.substr(0, i + 1);
    }
    return "";
}
```

**Example:**
```
"52"   → scan from right: 2 (even), 5 (odd) → return "5"
"4206" → 6,0,2,4 all even → return ""
"35427"→ 7 is odd → return "35427"
```

**Complexity:** Time O(n) · Space O(1)

---

## 📌 Pattern 7 — Palindrome Patterns

### Core Idea
Every palindrome has a center (single character or gap between two characters). Expand outward from each possible center and track the longest valid window.

---

### Expand Around Center Template
```cpp
// Returns [left, right] of the longest palindrome centered at (l, r)
pair<int,int> expand(string& s, int l, int r) {
    while (l >= 0 && r < s.size() && s[l] == s[r]) {
        l--; r++;
    }
    return {l + 1, r - 1};  // last valid positions
}
```

---

### Problem 14 — Longest Palindromic Substring
**Difficulty:** Medium

**Idea:** Try all 2n−1 centers (n odd-length, n−1 even-length). Expand and keep track of the longest.

```cpp
string longestPalindrome(string s) {
    int n = s.size();
    int start = 0, maxLen = 1;

    auto expand = [&](int l, int r) {
        while (l >= 0 && r < n && s[l] == s[r]) { l--; r++; }
        return make_pair(l + 1, r - 1);
    };

    for (int i = 0; i < n; i++) {
        // Odd-length palindrome (center at i)
        auto [l1, r1] = expand(i, i);
        if (r1 - l1 + 1 > maxLen) {
            maxLen = r1 - l1 + 1;
            start = l1;
        }

        // Even-length palindrome (center between i and i+1)
        if (i + 1 < n) {
            auto [l2, r2] = expand(i, i + 1);
            if (r2 - l2 + 1 > maxLen) {
                maxLen = r2 - l2 + 1;
                start = l2;
            }
        }
    }

    return s.substr(start, maxLen);
}
```

**Complexity:** Time O(n²) · Space O(1)

> **Manacher's Algorithm** solves this in O(n) but is rarely needed in interviews — O(n²) is accepted.

---

## 📌 Pattern 8 — Substring Counting

### Core Idea
Count substrings satisfying a condition. Two approaches:
- **Enumerate all O(n²) substrings** — feasible for small constraints
- **Contribution counting** — count how many substrings each position contributes to

---

### Problem 15 — Count Number of Substrings
**Difficulty:** Easy

**Idea:** Count substrings with exactly `k` distinct characters using:
`exactly(k) = atMost(k) - atMost(k-1)`

```cpp
int atMostK(string& s, int k) {
    unordered_map<char, int> freq;
    int left = 0, count = 0;

    for (int right = 0; right < s.size(); right++) {
        freq[s[right]]++;

        while (freq.size() > k) {
            freq[s[left]]--;
            if (freq[s[left]] == 0) freq.erase(s[left]);
            left++;
        }

        count += right - left + 1;  // all substrings ending at right
    }

    return count;
}

int countSubstrings(string s, int k) {
    return atMostK(s, k) - atMostK(s, k - 1);
}
```

**Complexity:** Time O(n) · Space O(k)

---

### Problem 16 — Sum of Beauty of All Substrings
**Difficulty:** Medium

**Beauty** = (max frequency character) − (min frequency character) in a substring.

**Idea:** Enumerate all O(n²) substrings. For each, maintain a frequency array and track max/min on the fly.

```cpp
int beautySum(string s) {
    int n = s.size(), total = 0;

    for (int i = 0; i < n; i++) {
        int freq[26] = {0};

        for (int j = i; j < n; j++) {
            freq[s[j] - 'a']++;

            int maxF = 0, minF = INT_MAX;
            for (int f : freq) {
                if (f > 0) {
                    maxF = max(maxF, f);
                    minF = min(minF, f);
                }
            }

            total += maxF - minF;
        }
    }

    return total;
}
```

**Complexity:** Time O(n² · 26) = O(n²) · Space O(26) = O(1)

---

### Problem 17 — Count Palindromic Substrings
**Difficulty:** Medium

**Idea:** Expand around each center. Count every valid expansion as one palindrome.

```cpp
int countPalindromicSubstrings(string s) {
    int n = s.size(), count = 0;

    auto expand = [&](int l, int r) {
        while (l >= 0 && r < n && s[l] == s[r]) {
            count++;
            l--; r++;
        }
    };

    for (int i = 0; i < n; i++) {
        expand(i, i);       // odd-length
        expand(i, i + 1);   // even-length
    }

    return count;
}
```

**Complexity:** Time O(n²) · Space O(1)

---

## 🧩 Additional Important String Problems

---

### Problem 18 — Valid Parentheses
**Difficulty:** Easy

```cpp
bool isValid(string s) {
    stack<char> st;
    unordered_map<char, char> match = {{')', '('}, {']', '['}, {'}', '{'}};

    for (char c : s) {
        if (c == '(' || c == '[' || c == '{') {
            st.push(c);
        } else {
            if (st.empty() || st.top() != match[c]) return false;
            st.pop();
        }
    }

    return st.empty();
}
```

**Complexity:** Time O(n) · Space O(n)

---

### Problem 19 — Longest Substring Without Repeating Characters
**Difficulty:** Medium

```cpp
int lengthOfLongestSubstring(string s) {
    unordered_map<char, int> last;  // char → last seen index
    int left = 0, result = 0;

    for (int right = 0; right < s.size(); right++) {
        if (last.count(s[right]) && last[s[right]] >= left)
            left = last[s[right]] + 1;  // shrink window past duplicate

        last[s[right]] = right;
        result = max(result, right - left + 1);
    }

    return result;
}
```

**Complexity:** Time O(n) · Space O(min(n, charset))

---

### Problem 20 — Minimum Window Substring
**Difficulty:** Hard

```cpp
string minWindow(string s, string t) {
    unordered_map<char, int> need, window;
    for (char c : t) need[c]++;

    int left = 0, formed = 0, required = need.size();
    int minLen = INT_MAX, minStart = 0;

    for (int right = 0; right < s.size(); right++) {
        window[s[right]]++;

        if (need.count(s[right]) && window[s[right]] == need[s[right]])
            formed++;

        while (formed == required) {
            if (right - left + 1 < minLen) {
                minLen = right - left + 1;
                minStart = left;
            }
            window[s[left]]--;
            if (need.count(s[left]) && window[s[left]] < need[s[left]])
                formed--;
            left++;
        }
    }

    return minLen == INT_MAX ? "" : s.substr(minStart, minLen);
}
```

**Complexity:** Time O(n + m) · Space O(charset)

---

### Problem 21 — Group Anagrams
**Difficulty:** Medium

```cpp
vector<vector<string>> groupAnagrams(vector<string>& strs) {
    unordered_map<string, vector<string>> groups;

    for (string& s : strs) {
        string key = s;
        sort(key.begin(), key.end());   // sorted string = canonical key
        groups[key].push_back(s);
    }

    vector<vector<string>> result;
    for (auto& [key, group] : groups)
        result.push_back(group);

    return result;
}
```

**Complexity:** Time O(n · k log k) where k = max string length · Space O(n·k)

---

### Problem 22 — Largest Number (from array)
**Difficulty:** Medium

```cpp
string largestNumber(vector<int>& nums) {
    vector<string> strs;
    for (int n : nums) strs.push_back(to_string(n));

    sort(strs.begin(), strs.end(), [](const string& a, const string& b) {
        return a + b > b + a;  // compare concatenation orders
    });

    if (strs[0] == "0") return "0";  // all zeros

    string result = "";
    for (string& s : strs) result += s;
    return result;
}
```

**Complexity:** Time O(n log n · k) · Space O(n)

---

### Problem 23 — Zigzag Conversion
**Difficulty:** Medium

```cpp
string convert(string s, int numRows) {
    if (numRows == 1 || numRows >= s.size()) return s;

    vector<string> rows(numRows, "");
    int row = 0, dir = -1;

    for (char c : s) {
        rows[row] += c;
        if (row == 0 || row == numRows - 1) dir = -dir;
        row += dir;
    }

    string result = "";
    for (string& r : rows) result += r;
    return result;
}
```

**Complexity:** Time O(n) · Space O(n)

---

## 🧠 Pattern Decision Guide

```
Input is a single string — what are you asked?

Reverse / rearrange words?
└── Split by delimiter + reverse + rejoin (Two Pointer)

Check if palindrome?
├── Whole string         → Two Pointer from both ends
└── Longest substring    → Expand Around Center

Find/count substrings with constraint?
├── Exact k distinct     → atMost(k) - atMost(k-1) (Sliding Window)
├── Sum of property      → O(n²) enumeration + frequency array
└── Palindromic count    → Expand Around Center × all centers

Match/compare two strings?
├── Anagram check        → Frequency array diff
├── Isomorphic           → Bidirectional char mapping
└── Rotation check       → find(goal) in s+s

Parse a string as a number?
└── Simulate: skip spaces → read sign → read digits → clamp (atoi)

Convert a representation?
└── Simulate with a lookup map (Roman to Integer)

Parentheses / bracket problems?
├── Validity             → Stack push/pop
├── Depth                → Counter increment/decrement
└── Remove outer         → Depth-based include/exclude

Find minimum window containing all chars?
└── Sliding Window with need/formed counters (Minimum Window)

Group strings by equivalence?
└── Sort each string as canonical key → HashMap grouping
```

---

## 📋 Complete Problem Reference

| # | Problem | Difficulty | Pattern | Time | Space |
|---|---------|-----------|---------|------|-------|
| 1 | Reverse Words in a String | Medium | Split + Reverse | O(n) | O(n) |
| 2 | Reverse Every Word in a String | Easy | Split + Reverse each | O(n) | O(n) |
| 3 | Palindrome Check | Medium | Two Pointer | O(n) | O(1) |
| 4 | Remove Outermost Parentheses | Medium | Depth Counter | O(n) | O(n) |
| 5 | Maximum Nesting Depth | Medium | Depth Counter | O(n) | O(1) |
| 6 | Check Anagram | Easy | Frequency Array | O(n) | O(1) |
| 7 | Sort Characters by Frequency | Easy | Bucket Sort | O(n) | O(n) |
| 8 | Isomorphic Strings | Easy | Bidirectional Map | O(n) | O(1) |
| 9 | Longest Common Prefix | Easy | Column-by-Column | O(S) | O(1) |
| 10 | Rotate String | Easy | s+s contains goal | O(n²) | O(n) |
| 11 | String to Integer (atoi) | Medium | Simulation | O(n) | O(1) |
| 12 | Roman to Integer | Medium | Right-to-left scan | O(n) | O(1) |
| 13 | Largest Odd Number in String | Easy | Greedy (right scan) | O(n) | O(1) |
| 14 | Longest Palindromic Substring | Medium | Expand Around Center | O(n²) | O(1) |
| 15 | Count Number of Substrings | Easy | Sliding Window | O(n) | O(k) |
| 16 | Sum of Beauty of All Substrings | Medium | O(n²) Enumeration | O(n²) | O(1) |
| 17 | Count Palindromic Substrings | Medium | Expand Around Center | O(n²) | O(1) |
| 18 | Valid Parentheses | Easy | Stack | O(n) | O(n) |
| 19 | Longest Substring Without Repeat | Medium | Sliding Window | O(n) | O(k) |
| 20 | Minimum Window Substring | Hard | Sliding Window | O(n+m) | O(k) |
| 21 | Group Anagrams | Medium | Sort + HashMap | O(nk log k) | O(nk) |
| 22 | Largest Number | Medium | Custom Sort | O(n log n) | O(n) |
| 23 | Zigzag Conversion | Medium | Simulation | O(n) | O(n) |

---

## 🐛 Debugging Guide

### 1. Off-by-one in Substring
```cpp
// ❌ Wrong: includes one extra character
s.substr(start, end);

// ✅ Correct: length = end - start + 1
s.substr(start, end - start + 1);
```

### 2. Shrinking Sliding Window Too Late
Always shrink the window **before** reading the result at that step, not after.

### 3. Isomorphic — One Map Isn't Enough
```
s = "ab", t = "aa"
One map: a→a, b→a — appears valid
Two maps: a→a, a→b — contradiction caught ✓
```

### 4. Palindrome Expansion Boundary
```cpp
while (l >= 0 && r < n && s[l] == s[r])
//     ^^^^^^              ^^^^^^^^
//     don't go negative   don't go out of bounds
```

### 5. atoi Overflow
Use `long long` for accumulation, then clamp to `INT_MIN / INT_MAX` before returning.

### 6. Always Test These Edge Cases

| Edge Case | Affects |
|-----------|---------|
| Empty string `""` | All problems |
| Single character | Palindrome, sliding window |
| All same characters `"aaaa"` | Anagram, isomorphic |
| Mixed case `"AbBa"` | Palindrome with case folding |
| Leading/trailing spaces | atoi, reverse words |
| Multiple spaces between words | Reverse words |
| All open brackets `"((("` | Valid parentheses |
| Large numbers | atoi overflow |

---

## 🏆 Golden Rules of String Problems

1. **Frequency array beats HashMap** for lowercase-only inputs — 26 integers, cache-friendly, O(1) space.

2. **Sort to canonicalize** — group anagrams, check anagram equivalence by sorting both strings.

3. **s + s trick** — any rotation of `s` is a substring of `s + s`. Use this before writing custom rotation logic.

4. **Expand around center** — for palindromes, there are exactly `2n − 1` centers. Always try both odd and even expansions.

5. **atMost(k) − atMost(k−1)** — converts "exactly k" substring problems into two "at most" sliding windows.

6. **Simulate explicitly** — for parsing problems (atoi, Roman numerals), code exactly what the problem says step by step. Don't be clever.

---

## 🎯 Final Advice

Most string interview problems are combinations of:

```
Two Pointer  +  Sliding Window  +  Frequency Map  +  Stack  +  Simulation
```

The tricky part isn't the algorithm — it's handling **edge cases** cleanly:
- Empty strings
- Whitespace
- Integer overflow in parsing
- Case sensitivity
- Single characters

Once you internalize these 8 patterns, every string problem becomes a familiar structure with new surface details.

---

*Generated for DSA Interview Preparation · String Patterns Reference*