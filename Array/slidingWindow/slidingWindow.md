# Sliding Window Algorithm - Complete Guide

## Table of Contents
1. [Introduction](#introduction)
2. [Core Concepts](#core-concepts)
3. [Pattern Categories](#pattern-categories)
4. [Problem-Solving Framework](#problem-solving-framework)
5. [Curated Problem List](#curated-problem-list)
6. [Advanced Techniques](#advanced-techniques)
7. [Common Pitfalls](#common-pitfalls)
8. [Must-Read LeetCode Discussions](#must-read-leetcode-discussions)

---

## Introduction

Sliding Window is an optimization technique that transforms nested loops (O(n²)) into a single loop (O(n)) by maintaining a window that slides through the array/string. It's essentially a specialized application of the two-pointer technique.

### When to Use Sliding Window?
- Finding subarrays/substrings with specific properties
- Maximum/minimum in a contiguous sequence
- Problems with "contiguous" or "consecutive" keywords
- Optimization of brute force solutions involving subarrays
- Problems asking for "longest", "shortest", "maximum", "minimum" subarray/substring

### Key Insight
Instead of recalculating from scratch for each position, maintain a window and update incrementally by adding new elements and removing old ones.

---

## Core Concepts

### 1. **Fixed Size Window**

Window size is constant throughout the problem.

**Visual Representation:**
```
Array: [1, 3, 2, 6, -1, 4, 1, 8, 2]
Window size k=3

Step 1: [1, 3, 2] 6, -1, 4, 1, 8, 2  → sum = 6
Step 2: 1, [3, 2, 6] -1, 4, 1, 8, 2  → sum = 11
Step 3: 1, 3, [2, 6, -1] 4, 1, 8, 2  → sum = 7
...
```

**Update Formula:**
```
newSum = oldSum - arr[left] + arr[right]
```

### 2. **Variable Size Window (Expandable)**

Window size changes based on conditions.

**Visual Representation:**
```
Find longest substring with at most 2 distinct characters
String: "eceba"

Step 1: [e]           → valid (1 distinct)
Step 2: [e,c]         → valid (2 distinct)
Step 3: [e,c,e]       → valid (2 distinct)
Step 4: [e,c,e,b]     → invalid (3 distinct) → shrink
Step 5: [c,e,b]       → valid (2 distinct)
Step 6: [c,e,b,a]     → invalid (3 distinct) → shrink
```

### 3. **Dynamic Window (Shrinkable)**

Window shrinks when condition is violated.

**Pattern:**
```
Expand window → Check condition → Shrink if needed → Update result
```

### 4. **Window with Auxiliary Data Structures**

Use hash maps, sets, or deques to track window state.

```
HashMap: Track character frequencies
Set: Track unique elements
Deque: Track min/max in window
```

---

## Pattern Categories

### Pattern 1: Fixed Size Window

**Template:**
```java
int windowSum = 0;
int maxSum = Integer.MIN_VALUE;

// Build first window
for (int i = 0; i < k; i++) {
    windowSum += arr[i];
}
maxSum = windowSum;

// Slide window
for (int i = k; i < arr.length; i++) {
    windowSum = windowSum - arr[i - k] + arr[i];
    maxSum = Math.max(maxSum, windowSum);
}
```

**Use Cases:**
- Maximum sum of k consecutive elements
- Average of subarrays of size k
- First negative in every window of size k

---

### Pattern 2: Variable Size Window (Find Maximum)

**Template:**
```java
int left = 0, right = 0;
int maxLength = 0;
// Auxiliary data structure (map, set, etc.)

while (right < arr.length) {
    // Expand window - add arr[right]
    addToWindow(arr[right]);
    
    // Shrink window while invalid
    while (windowInvalid()) {
        removeFromWindow(arr[left]);
        left++;
    }
    
    // Update result
    maxLength = Math.max(maxLength, right - left + 1);
    right++;
}
```

**Use Cases:**
- Longest substring with k distinct characters
- Longest substring without repeating characters
- Maximum consecutive ones with k flips

---

### Pattern 3: Variable Size Window (Find Minimum)

**Template:**
```java
int left = 0, right = 0;
int minLength = Integer.MAX_VALUE;

while (right < arr.length) {
    // Expand window
    addToWindow(arr[right]);
    
    // Shrink window while valid (to find minimum)
    while (windowValid()) {
        minLength = Math.min(minLength, right - left + 1);
        removeFromWindow(arr[left]);
        left++;
    }
    
    right++;
}
```

**Use Cases:**
- Minimum window substring
- Minimum size subarray sum
- Smallest subarray with sum ≥ target

---

### Pattern 4: Variable Size Window (Count Subarrays)

**Template:**
```java
int left = 0, count = 0;

for (int right = 0; right < arr.length; right++) {
    // Expand window
    addToWindow(arr[right]);
    
    // Shrink window while invalid
    while (windowInvalid()) {
        removeFromWindow(arr[left]);
        left++;
    }
    
    // All subarrays ending at right are valid
    count += (right - left + 1);
}
```

**Use Cases:**
- Count subarrays with at most k distinct elements
- Count subarrays with product less than k
- Count nice subarrays

---

### Pattern 5: Window with Deque (Min/Max in Window)

**Template:**
```java
Deque<Integer> deque = new LinkedList<>();
List<Integer> result = new ArrayList<>();

for (int i = 0; i < arr.length; i++) {
    // Remove elements outside window
    while (!deque.isEmpty() && deque.peekFirst() < i - k + 1) {
        deque.pollFirst();
    }
    
    // Maintain monotonic property
    while (!deque.isEmpty() && arr[deque.peekLast()] < arr[i]) {
        deque.pollLast();
    }
    
    deque.offerLast(i);
    
    // Add result when window is full
    if (i >= k - 1) {
        result.add(arr[deque.peekFirst()]);
    }
}
```

**Use Cases:**
- Sliding window maximum
- Sliding window minimum
- Longest continuous subarray with absolute diff ≤ limit

---

## Problem-Solving Framework

### Step 1: Identify Sliding Window Pattern

**Keywords to look for:**
- "contiguous", "consecutive", "subarray", "substring"
- "longest", "shortest", "maximum", "minimum"
- "window", "k elements"
- "at most k", "exactly k", "at least k"

**Questions to ask:**
- Is the window size fixed or variable?
- Am I finding maximum or minimum?
- Do I need to count valid windows?
- What makes a window valid/invalid?

### Step 2: Choose Window Type

| Problem Type | Window Type | Template |
|--------------|-------------|----------|
| Fixed size k | Fixed | Slide by 1 each time |
| Longest/Maximum | Variable (expand) | Expand + shrink when invalid |
| Shortest/Minimum | Variable (shrink) | Shrink while valid |
| Count subarrays | Variable (count) | Count += right - left + 1 |

### Step 3: Define Window State

**What to track:**
- Window boundaries (left, right)
- Window sum/product
- Character frequencies (HashMap)
- Unique elements (Set)
- Min/Max in window (Deque)

### Step 4: Define Valid/Invalid Conditions

**Examples:**
- "At most k distinct" → `map.size() <= k` is valid
- "Sum ≥ target" → `sum >= target` is valid
- "No repeating characters" → `set.size() == windowSize` is valid

### Step 5: Update Result

**When to update:**
- **Maximum:** After shrinking (window is valid)
- **Minimum:** While shrinking (find smallest valid)
- **Count:** After each expansion

---

## Curated Problem List

### 🟢 Level 1: Fundamentals (Master These First)

#### 1. **Maximum Average Subarray I**
- **Platform:** LeetCode #643
- **Difficulty:** Easy
- **Pattern:** Fixed Size Window
- **Key Learning:** Basic sliding window with fixed size k
- **Link:** https://leetcode.com/problems/maximum-average-subarray-i/

#### 2. **Contains Duplicate II**
- **Platform:** LeetCode #219
- **Difficulty:** Easy
- **Pattern:** Fixed Size Window + Set
- **Key Learning:** Using set to track window elements
- **Link:** https://leetcode.com/problems/contains-duplicate-ii/

#### 3. **Maximum Sum of Distinct Subarrays With Length K**
- **Platform:** LeetCode #2461
- **Difficulty:** Medium (but good for practice)
- **Pattern:** Fixed Size Window + Set
- **Key Learning:** Combining fixed window with uniqueness check
- **Link:** https://leetcode.com/problems/maximum-sum-of-distinct-subarrays-with-length-k/

#### 4. **Minimum Difference Between Highest and Lowest of K Scores**
- **Platform:** LeetCode #1984
- **Difficulty:** Easy
- **Pattern:** Fixed Size Window (after sorting)
- **Key Learning:** Preprocessing + sliding window
- **Link:** https://leetcode.com/problems/minimum-difference-between-highest-and-lowest-of-k-scores/

#### 5. **Defuse the Bomb**
- **Platform:** LeetCode #1652
- **Difficulty:** Easy
- **Pattern:** Fixed Size Circular Window
- **Key Learning:** Handling circular arrays
- **Link:** https://leetcode.com/problems/defuse-the-bomb/

---

### 🟡 Level 2: Variable Window Basics

#### 6. **Longest Substring Without Repeating Characters**
- **Platform:** LeetCode #3
- **Difficulty:** Medium
- **Pattern:** Variable Window + Set/HashMap
- **Key Learning:** Classic variable window problem - finding maximum
- **Link:** https://leetcode.com/problems/longest-substring-without-repeating-characters/
- **Must Read:** https://leetcode.com/problems/longest-substring-without-repeating-characters/solutions/1729/11-line-simple-java-solution-o-n-with-explanation/

#### 7. **Minimum Size Subarray Sum**
- **Platform:** LeetCode #209
- **Difficulty:** Medium
- **Pattern:** Variable Window - Finding Minimum
- **Key Learning:** Shrinking window to find minimum length
- **Link:** https://leetcode.com/problems/minimum-size-subarray-sum/

#### 8. **Longest Repeating Character Replacement**
- **Platform:** LeetCode #424
- **Difficulty:** Medium
- **Pattern:** Variable Window + Frequency Map
- **Key Learning:** Window with replacement constraint
- **Link:** https://leetcode.com/problems/longest-repeating-character-replacement/
- **Must Read:** https://leetcode.com/problems/longest-repeating-character-replacement/solutions/91271/java-12-lines-o-n-sliding-window-solution-with-explanation/

#### 9. **Max Consecutive Ones III**
- **Platform:** LeetCode #1004
- **Difficulty:** Medium
- **Pattern:** Variable Window with k flips
- **Key Learning:** Counting zeros in window, shrink when > k
- **Link:** https://leetcode.com/problems/max-consecutive-ones-iii/

#### 10. **Fruit Into Baskets**
- **Platform:** LeetCode #904
- **Difficulty:** Medium
- **Pattern:** Variable Window - At Most K Distinct
- **Key Learning:** At most 2 distinct elements (types of fruits)
- **Link:** https://leetcode.com/problems/fruit-into-baskets/

#### 11. **Get Equal Substrings Within Budget**
- **Platform:** LeetCode #1208
- **Difficulty:** Medium
- **Pattern:** Variable Window with Cost
- **Key Learning:** Window with cumulative cost constraint
- **Link:** https://leetcode.com/problems/get-equal-substrings-within-budget/

#### 12. **Grumpy Bookstore Owner**
- **Platform:** LeetCode #1052
- **Difficulty:** Medium
- **Pattern:** Fixed Window + Optimization
- **Key Learning:** Finding best window to apply operation
- **Link:** https://leetcode.com/problems/grumpy-bookstore-owner/

---

### 🔴 Level 3: Advanced Patterns

#### 13. **Minimum Window Substring**
- **Platform:** LeetCode #76
- **Difficulty:** Hard
- **Pattern:** Variable Window + Two HashMaps
- **Key Learning:** Most important sliding window problem - template for many others
- **Link:** https://leetcode.com/problems/minimum-window-substring/
- **Must Read:** https://leetcode.com/problems/minimum-window-substring/solutions/26808/here-is-a-10-line-template-that-can-solve-most-substring-problems/

#### 14. **Longest Substring with At Most K Distinct Characters**
- **Platform:** LeetCode #340 (Premium) / LintCode #386
- **Difficulty:** Medium
- **Pattern:** Variable Window + HashMap
- **Key Learning:** Classic "at most k" pattern
- **Alternative:** https://www.lintcode.com/problem/386/

#### 15. **Longest Substring with At Least K Repeating Characters**
- **Platform:** LeetCode #395
- **Difficulty:** Medium
- **Pattern:** Sliding Window + Divide & Conquer
- **Key Learning:** Multiple approaches to same problem
- **Link:** https://leetcode.com/problems/longest-substring-with-at-least-k-repeating-characters/

#### 16. **Permutation in String**
- **Platform:** LeetCode #567
- **Difficulty:** Medium
- **Pattern:** Fixed Window + Frequency Match
- **Key Learning:** Checking if window is permutation
- **Link:** https://leetcode.com/problems/permutation-in-string/

#### 17. **Find All Anagrams in a String**
- **Platform:** LeetCode #438
- **Difficulty:** Medium
- **Pattern:** Fixed Window + Frequency Match
- **Key Learning:** Similar to permutation, find all occurrences
- **Link:** https://leetcode.com/problems/find-all-anagrams-in-a-string/

#### 18. **Substring with Concatenation of All Words**
- **Platform:** LeetCode #30
- **Difficulty:** Hard
- **Pattern:** Fixed Window + Word Boundaries
- **Key Learning:** Complex window validation with multiple words
- **Link:** https://leetcode.com/problems/substring-with-concatenation-of-all-words/

#### 19. **Longest Turbulent Subarray**
- **Platform:** LeetCode #978
- **Difficulty:** Medium
- **Pattern:** Variable Window with Pattern
- **Key Learning:** Maintaining alternating comparison pattern
- **Link:** https://leetcode.com/problems/longest-turbulent-subarray/

#### 20. **Number of Substrings Containing All Three Characters**
- **Platform:** LeetCode #1358
- **Difficulty:** Medium
- **Pattern:** Variable Window - Counting
- **Key Learning:** Count valid windows with all required elements
- **Link:** https://leetcode.com/problems/number-of-substrings-containing-all-three-characters/

---

### 🏆 Level 4: Expert Challenges

#### 21. **Sliding Window Maximum**
- **Platform:** LeetCode #239
- **Difficulty:** Hard
- **Pattern:** Fixed Window + Monotonic Deque
- **Key Learning:** Using deque to track maximum in O(1)
- **Link:** https://leetcode.com/problems/sliding-window-maximum/
- **Must Read:** https://leetcode.com/problems/sliding-window-maximum/solutions/65884/java-o-n-solution-using-deque-with-explanation/

#### 22. **Subarrays with K Different Integers**
- **Platform:** LeetCode #992
- **Difficulty:** Hard
- **Pattern:** Variable Window - "Exactly K" = "At Most K" - "At Most K-1"
- **Key Learning:** Converting "exactly k" to two "at most" problems
- **Link:** https://leetcode.com/problems/subarrays-with-k-different-integers/
- **Must Read:** https://leetcode.com/problems/subarrays-with-k-different-integers/solutions/523136/java-c-python-sliding-window/

#### 23. **Minimum Window Subsequence**
- **Platform:** LeetCode #727 (Premium)
- **Difficulty:** Hard
- **Pattern:** Two Pointers + Sliding Window
- **Key Learning:** Subsequence vs substring
- **Alternative:** Practice on similar problems

#### 24. **Shortest Subarray with Sum at Least K**
- **Platform:** LeetCode #862
- **Difficulty:** Hard
- **Pattern:** Monotonic Deque + Prefix Sum
- **Key Learning:** Handling negative numbers with deque
- **Link:** https://leetcode.com/problems/shortest-subarray-with-sum-at-least-k/

#### 25. **Longest Continuous Subarray With Absolute Diff Less Than or Equal to Limit**
- **Platform:** LeetCode #1438
- **Difficulty:** Medium
- **Pattern:** Variable Window + Two Deques (min & max)
- **Key Learning:** Tracking both min and max in window
- **Link:** https://leetcode.com/problems/longest-continuous-subarray-with-absolute-diff-less-than-or-equal-to-limit/

#### 26. **Count Subarrays With Score Less Than K**
- **Platform:** LeetCode #2302
- **Difficulty:** Hard
- **Pattern:** Variable Window - Counting
- **Key Learning:** Score = sum * length, complex counting
- **Link:** https://leetcode.com/problems/count-subarrays-with-score-less-than-k/

#### 27. **Frequency of the Most Frequent Element**
- **Platform:** LeetCode #1838
- **Difficulty:** Medium
- **Pattern:** Variable Window + Greedy (after sorting)
- **Key Learning:** Making all elements equal with k operations
- **Link:** https://leetcode.com/problems/frequency-of-the-most-frequent-element/

#### 28. **Maximum Number of Vowels in a Substring of Given Length**
- **Platform:** LeetCode #1456
- **Difficulty:** Medium
- **Pattern:** Fixed Window + Counting
- **Key Learning:** Simple fixed window with character counting
- **Link:** https://leetcode.com/problems/maximum-number-of-vowels-in-a-substring-of-given-length/

#### 29. **Maximum Points You Can Obtain from Cards**
- **Platform:** LeetCode #1423
- **Difficulty:** Medium
- **Pattern:** Fixed Window (inverse thinking)
- **Key Learning:** Find minimum window in middle = maximum from ends
- **Link:** https://leetcode.com/problems/maximum-points-you-can-obtain-from-cards/

#### 30. **Minimum Swaps to Group All 1's Together II**
- **Platform:** LeetCode #2134
- **Difficulty:** Medium
- **Pattern:** Fixed Window + Circular Array
- **Key Learning:** Circular sliding window with optimization
- **Link:** https://leetcode.com/problems/minimum-swaps-to-group-all-1s-together-ii/

---

### 🎯 Bonus: Important Variations

#### 31. **Subarray Product Less Than K**
- **Platform:** LeetCode #713
- **Difficulty:** Medium
- **Pattern:** Variable Window - Counting with Product
- **Key Learning:** Using product instead of sum
- **Link:** https://leetcode.com/problems/subarray-product-less-than-k/

#### 32. **Count Number of Nice Subarrays**
- **Platform:** LeetCode #1248
- **Difficulty:** Medium
- **Pattern:** Variable Window - "Exactly K" technique
- **Key Learning:** Transform to binary (odd=1, even=0)
- **Link:** https://leetcode.com/problems/count-number-of-nice-subarrays/

#### 33. **Replace the Substring for Balanced String**
- **Platform:** LeetCode #1234
- **Difficulty:** Medium
- **Pattern:** Variable Window - Finding Minimum
- **Key Learning:** Complex balance condition
- **Link:** https://leetcode.com/problems/replace-the-substring-for-balanced-string/

#### 34. **Max Consecutive Ones II**
- **Platform:** LeetCode #487 (Premium)
- **Difficulty:** Medium
- **Pattern:** Variable Window with 1 flip
- **Key Learning:** Simpler version of Max Consecutive Ones III
- **Alternative:** Practice with k=1

#### 35. **Longest Nice Subarray**
- **Platform:** LeetCode #2401
- **Difficulty:** Medium
- **Pattern:** Variable Window + Bitwise AND
- **Key Learning:** Window with bitwise constraints
- **Link:** https://leetcode.com/problems/longest-nice-subarray/

---

## Advanced Techniques

### 1. **"At Most K" to "Exactly K" Conversion**

**Key Insight:** `Exactly K = At Most K - At Most (K-1)`

```java
public int exactlyK(int[] arr, int k) {
    return atMostK(arr, k) - atMostK(arr, k - 1);
}

private int atMostK(int[] arr, int k) {
    Map<Integer, Integer> map = new HashMap<>();
    int left = 0, count = 0;
    
    for (int right = 0; right < arr.length; right++) {
        map.put(arr[right], map.getOrDefault(arr[right], 0) + 1);
        
        while (map.size() > k) {
            map.put(arr[left], map.get(arr[left]) - 1);
            if (map.get(arr[left]) == 0) {
                map.remove(arr[left]);
            }
            left++;
        }
        
        count += right - left + 1;
    }
    
    return count;
}
```

### 2. **Monotonic Deque for Window Min/Max**

**Maintains elements in increasing/decreasing order**

```java
// For maximum in window
Deque<Integer> deque = new LinkedList<>();

for (int i = 0; i < arr.length; i++) {
    // Remove elements outside window
    while (!deque.isEmpty() && deque.peekFirst() < i - k + 1) {
        deque.pollFirst();
    }
    
    // Remove smaller elements (they won't be max)
    while (!deque.isEmpty() && arr[deque.peekLast()] < arr[i]) {
        deque.pollLast();
    }
    
    deque.offerLast(i);
    
    if (i >= k - 1) {
        result.add(arr[deque.peekFirst()]); // Front is maximum
    }
}
```

### 3. **Frequency Map Optimization**

**Track valid characters count instead of checking entire map**

```java
Map<Character, Integer> required = new HashMap<>();
Map<Character, Integer> window = new HashMap<>();
int formed = 0; // Count of characters that meet requirement

// Expand
char c = s.charAt(right);
window.put(c, window.getOrDefault(c, 0) + 1);
if (required.containsKey(c) && 
    window.get(c).intValue() == required.get(c).intValue()) {
    formed++;
}

// Check if window is valid
if (formed == required.size()) {
    // All characters meet requirement
}
```

### 4. **Two Deques for Min and Max**

**Track both minimum and maximum simultaneously**

```java
Deque<Integer> minDeque = new LinkedList<>(); // Increasing
Deque<Integer> maxDeque = new LinkedList<>(); // Decreasing

// Add to window
while (!maxDeque.isEmpty() && arr[maxDeque.peekLast()] < arr[right]) {
    maxDeque.pollLast();
}
maxDeque.offerLast(right);

while (!minDeque.isEmpty() && arr[minDeque.peekLast()] > arr[right]) {
    minDeque.pollLast();
}
minDeque.offerLast(right);

// Check condition
while (arr[maxDeque.peekFirst()] - arr[minDeque.peekFirst()] > limit) {
    left++;
    if (maxDeque.peekFirst() < left) maxDeque.pollFirst();
    if (minDeque.peekFirst() < left) minDeque.pollFirst();
}
```

### 5. **Circular Array Window**

**Handle wraparound using modulo**

```java
int n = arr.length;
for (int i = 0; i < n; i++) {
    int windowSum = 0;
    for (int j = 0; j < k; j++) {
        windowSum += arr[(i + j) % n]; // Circular indexing
    }
    // Process window
}

// Or extend array: [arr, arr]
int[] extended = new int[2 * n];
for (int i = 0; i < n; i++) {
    extended[i] = arr[i];
    extended[i + n] = arr[i];
}
```

### 6. **Window with Multiple Constraints**

**Combine multiple conditions**

```java
while (right < n) {
    // Expand
    addToWindow(arr[right]);
    
    // Shrink if ANY constraint violated
    while (constraint1Violated() || 
           constraint2Violated() || 
           constraint3Violated()) {
        removeFromWindow(arr[left]);
        left++;
    }
    
    updateResult();
    right++;
}
```

---

## Common Pitfalls

### 1. **Forgetting to Update Window State**

```java
// ❌ Wrong: forgot to update map
while (map.size() > k) {
    left++;
}

// ✅ Correct: update map when shrinking
while (map.size() > k) {
    char c = s.charAt(left);
    map.put(c, map.get(c) - 1);
    if (map.get(c) == 0) {
        map.remove(c);
    }
    left++;
}
```

### 2. **Wrong Window Length Calculation**

```java
// ❌ Wrong: off by one
int length = right - left;

// ✅ Correct: inclusive of both ends
int length = right - left + 1;
```

### 3. **Updating Result at Wrong Time**

```java
// For MAXIMUM length:
// ✅ Update AFTER shrinking (window is valid)
while (invalid()) {
    shrink();
}
maxLen = Math.max(maxLen, right - left + 1);

// For MINIMUM length:
// ✅ Update WHILE shrinking (find smallest valid)
while (valid()) {
    minLen = Math.min(minLen, right - left + 1);
    shrink();
}
```

### 4. **Not Handling Empty Window**

```java
// ❌ Wrong: might return 0 when no valid window exists
int minLen = 0;

// ✅ Correct: use sentinel value
int minLen = Integer.MAX_VALUE;
// At end: return minLen == Integer.MAX_VALUE ? 0 : minLen;
```

### 5. **Incorrect Deque Maintenance**

```java
// ❌ Wrong: comparing values instead of indices
while (!deque.isEmpty() && deque.peekFirst() < i - k + 1) {
    deque.pollFirst();
}

// ✅ Correct: store indices, compare indices
while (!deque.isEmpty() && deque.peekFirst() < i - k + 1) {
    deque.pollFirst();
}
```

### 6. **Counting Subarrays Incorrectly**

```java
// For counting ALL valid subarrays ending at right:
// ✅ Correct: all subarrays from left to right are valid
count += (right - left + 1);

// NOT: count++ (only counts one subarray)
```

---

## Must-Read LeetCode Discussions

### 🌟 **Master Templates & Study Guides**

1. **The Ultimate Sliding Window Template**
   - https://leetcode.com/problems/minimum-window-substring/solutions/26808/here-is-a-10-line-template-that-can-solve-most-substring-problems/
   - **Must Read!** Universal template for substring problems

2. **Sliding Window Algorithm Template**
   - https://leetcode.com/discuss/study-guide/1773891/sliding-window-technique-and-question-bank
   - Comprehensive guide with problem bank

3. **Sliding Window Patterns**
   - https://leetcode.com/discuss/study-guide/1688903/solved-all-two-pointers-problems-in-100-days
   - Two pointers + sliding window combined

4. **Complete Sliding Window Guide**
   - https://leetcode.com/discuss/study-guide/1773891/Sliding-Window-Technique-and-Question-Bank
   - Categorized problem list with explanations

### 🎯 **Specific Problem Discussions**

5. **Longest Substring Without Repeating Characters**
   - https://leetcode.com/problems/longest-substring-without-repeating-characters/solutions/1729/11-line-simple-java-solution-o-n-with-explanation/
   - Clean implementation of basic variable window

6. **Longest Repeating Character Replacement**
   - https://leetcode.com/problems/longest-repeating-character-replacement/solutions/91271/java-12-lines-o-n-sliding-window-solution-with-explanation/
   - Excellent explanation of maxFreq technique

7. **Minimum Window Substring - Detailed**
   - https://leetcode.com/problems/minimum-window-substring/solutions/26808/here-is-a-10-line-template-that-can-solve-most-substring-problems/
   - Template that solves 10+ problems

8. **Sliding Window Maximum - Deque Approach**
   - https://leetcode.com/problems/sliding-window-maximum/solutions/65884/java-o-n-solution-using-deque-with-explanation/
   - Best explanation of monotonic deque

9. **Subarrays with K Different Integers**
   - https://leetcode.com/problems/subarrays-with-k-different-integers/solutions/523136/java-c-python-sliding-window/
   - "Exactly K" = "At Most K" - "At Most K-1" technique

10. **Permutation in String**
    - https://leetcode.com/problems/permutation-in-string/solutions/102588/java-solution-sliding-window/
    - Fixed window with frequency matching

### 📚 **Pattern-Specific Guides**

11. **Fixed vs Variable Window**
    - https://leetcode.com/discuss/study-guide/1122776/summary-of-sliding-window-patterns-for-subarray-substring
    - Clear distinction between patterns

12. **Counting Subarrays Pattern**
    - https://leetcode.com/problems/subarray-product-less-than-k/solutions/108861/java-c-clean-solution-with-explanation/
    - How to count valid subarrays correctly

13. **At Most K Pattern**
    - https://leetcode.com/problems/subarrays-with-k-different-integers/solutions/523136/java-c-python-sliding-window/
    - Converting "exactly k" problems

14. **Monotonic Deque Pattern**
    - https://leetcode.com/problems/shortest-subarray-with-sum-at-least-k/solutions/143726/c-java-python-o-n-using-deque/
    - Advanced deque techniques

15. **Two Pointers vs Sliding Window**
    - https://leetcode.com/discuss/study-guide/1688903/solved-all-two-pointers-problems-in-100-days
    - When to use which technique

---

## Practice Strategy

### Week 1: Fixed Window Mastery
- **Problems:** 1-5, 28
- **Focus:** Understanding window mechanics
- **Goal:** Write bug-free fixed window code

### Week 2: Variable Window Basics
- **Problems:** 6-12
- **Focus:** Expand and shrink logic
- **Goal:** Identify when to shrink window

### Week 3: Advanced Patterns
- **Problems:** 13-20
- **Focus:** Hash map optimization, frequency matching
- **Goal:** Solve medium problems in < 20 minutes

### Week 4: Expert Techniques
- **Problems:** 21-30
- **Focus:** Monotonic deque, complex counting
- **Goal:** Tackle hard problems confidently

### Week 5: Mixed Practice
- **Problems:** 31-35 + revisit difficult ones
- **Focus:** Pattern recognition, speed
- **Goal:** Identify pattern in < 2 minutes

---

## Time & Space Complexity

### Typical Complexities:

| Pattern | Time | Space | Explanation |
|---------|------|-------|-------------|
| Fixed window | O(n) | O(1) or O(k) | Single pass, constant extra space |
| Variable window | O(n) | O(k) | Each element visited at most twice |
| Window + HashMap | O(n) | O(k) | k = distinct elements in window |
| Window + Deque | O(n) | O(k) | Each element added/removed once |
| Nested window | O(n²) | O(k) | Avoid this! Optimize to O(n) |

### Comparison with Brute Force:

| Problem Type | Brute Force | Sliding Window | Improvement |
|--------------|-------------|----------------|-------------|
| All subarrays | O(n²) or O(n³) | O(n) | Quadratic to linear |
| Window max/min | O(n*k) | O(n) | k times faster |
| Substring search | O(n*m) | O(n+m) | Significant for large m |

---

## Quick Reference Card

### Pattern Selection Guide

| Problem Description | Pattern | Key Indicator |
|---------------------|---------|---------------|
| "k consecutive elements" | Fixed Window | Size is given |
| "longest substring with..." | Variable (max) | Find maximum |
| "shortest subarray with..." | Variable (min) | Find minimum |
| "count subarrays where..." | Variable (count) | Counting valid windows |
| "maximum in each window" | Deque | Need min/max tracking |
| "at most k distinct" | Variable + Map | Distinct elements |
| "exactly k distinct" | At Most K technique | Exactly = At Most K - At Most K-1 |
| "permutation/anagram" | Fixed + Frequency | Match character counts |

### Template Selection

```
Fixed Size:
- Build first window
- Slide: remove left, add right
- Update result each step

Variable (Maximum):
- Expand right
- Shrink while invalid
- Update result after shrinking

Variable (Minimum):
- Expand right
- Shrink while valid
- Update result during shrinking

Counting:
- Expand right
- Shrink while invalid
- Add (right - left + 1) to count
```

---

## Visual Debugging Tips

### 1. **Trace Window Boundaries**
```
String: "abcabcbb"
Finding longest without repeating

i=0: [a] left=0, right=0, len=1, set={a}
i=1: [ab] left=0, right=1, len=2, set={a,b}
i=2: [abc] left=0, right=2, len=3, set={a,b,c}
i=3: [abca] → duplicate! shrink → [bca] left=1, right=3, len=3
i=4: [bcab] → duplicate! shrink → [cab] left=2, right=4, len=3
...
```

### 2. **Track Window State**
```
Print at each step:
- Window boundaries: [left, right]
- Window content: arr[left...right]
- Auxiliary structure state: map/set contents
- Current result: max/min/count
```

### 3. **Verify Window Validity**
```
After each operation, check:
✓ Is window valid according to constraints?
✓ Are left and right pointers correct?
✓ Is auxiliary data structure consistent?
✓ Is result updated correctly?
```

---

## Common Interview Questions

### Questions Interviewers Ask:
1. "Find longest substring with at most k distinct characters"
2. "Find minimum window containing all characters of pattern"
3. "Find maximum sum of k consecutive elements"
4. "Count subarrays with product less than k"
5. "Find longest subarray with absolute difference ≤ limit"

### How to Approach:
1. **Identify pattern:** Fixed or variable? Max or min?
2. **Define window state:** What to track?
3. **Define valid condition:** When is window valid/invalid?
4. **Choose template:** Fixed, variable (max), variable (min), or counting?
5. **Code step by step:** Expand → Check → Shrink → Update
6. **Test edge cases:** Empty array, k=0, k>n, all same elements

### Follow-up Questions to Expect:
- "What if array has negative numbers?" (affects product/sum logic)
- "Can you optimize space?" (use input array, single pass)
- "What if k is very large?" (handle k > array length)
- "How would you handle infinite stream?" (maintain fixed window)

---

## Key Takeaways

1. **Sliding window optimizes O(n²) to O(n)** by avoiding redundant calculations
2. **Two main types:** Fixed size and variable size
3. **Variable window has two subtypes:** Finding maximum (shrink when invalid) and finding minimum (shrink while valid)
4. **"Exactly K" = "At Most K" - "At Most K-1"** is a powerful transformation
5. **Monotonic deque** enables O(1) min/max queries in window
6. **Hash map tracks window state** for complex conditions
7. **Count valid subarrays:** Add `(right - left + 1)` for each position
8. **Always update result at the right time:** After shrinking for max, during shrinking for min

---

## Additional Resources

### Practice Platforms
- **LeetCode:** Best for interview prep (tag: sliding-window)
- **Codeforces:** Competitive programming problems
- **HackerRank:** Good for beginners
- **GeeksforGeeks:** Detailed explanations

### Visualization
- Draw window movement on paper
- Use debugger to step through code
- Print window state at each iteration
- Visualize deque operations

### Learning Path
1. Master fixed window (1 week)
2. Learn variable window for maximum (1 week)
3. Practice variable window for minimum (1 week)
4. Study monotonic deque (1 week)
5. Solve mixed problems (ongoing)

---

**Happy Coding! 🚀**

*Remember: Sliding window is all about maintaining state efficiently. Master the expand-shrink pattern, and you'll solve most problems with ease!*

---

## Appendix: Problem Difficulty Distribution

- **Easy (Fixed Window):** 5 problems
- **Medium (Variable Window):** 20 problems
- **Hard (Advanced Techniques):** 10 problems
- **Total:** 35 carefully curated problems

**Recommended order:** Follow the levels sequentially for optimal learning curve.
