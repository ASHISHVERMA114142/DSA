# Prefix Sum Algorithm - Complete Guide

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

Prefix Sum (also called Cumulative Sum) is a preprocessing technique that allows us to answer range sum queries in O(1) time after O(n) preprocessing. It's one of the most fundamental techniques in competitive programming and technical interviews.

### When to Use Prefix Sum?
- Range sum queries on static arrays
- Subarray sum problems
- Finding subarrays with specific properties
- 2D matrix sum queries
- Problems involving cumulative frequencies
- Optimization of brute force O(n²) solutions to O(n)

### Key Insight
Instead of calculating sum repeatedly, precompute cumulative sums once and use them for instant queries.

---

## Core Concepts

### 1. **Basic Prefix Sum (1D Array)**

**Definition:**
```
prefix[i] = sum of elements from index 0 to i
prefix[i] = arr[0] + arr[1] + ... + arr[i]
```

**Visual Representation:**
```
Array:     [3,  1,  4,  1,  5,  9,  2]
Index:      0   1   2   3   4   5   6

Prefix:    [3,  4,  8,  9, 14, 23, 25]
            ↑   ↑   ↑   ↑   ↑   ↑   ↑
            3  3+1 4+4 8+1 9+5 14+9 23+2
```

**Range Sum Formula:**
```
sum(L, R) = prefix[R] - prefix[L-1]
```

**Example:** Sum from index 2 to 5:
```
sum(2, 5) = prefix[5] - prefix[1] = 23 - 4 = 19
Verify: 4 + 1 + 5 + 9 = 19 ✓
```

### 2. **Prefix Sum with Hash Map**

Used to find subarrays with specific sum properties.

**Key Technique:**
```
If prefix[j] - prefix[i] = target
Then prefix[j] - target = prefix[i]
```

Store prefix sums in hash map to find matching pairs in O(1).

### 3. **2D Prefix Sum (Matrix)**

**Definition:**
```
prefix[i][j] = sum of all elements in rectangle from (0,0) to (i,j)
```

**Visual Representation:**
```
Matrix:
1  2  3
4  5  6
7  8  9

Prefix Sum Matrix:
1   3   6
5  12  21
12 27  45
```

**Range Sum Formula:**
```
sum(r1, c1, r2, c2) = prefix[r2][c2] 
                    - prefix[r1-1][c2] 
                    - prefix[r2][c1-1] 
                    + prefix[r1-1][c1-1]
```

### 4. **Difference Array (Inverse of Prefix Sum)**

Used for range update operations.

**Concept:**
```
To add 'val' to range [L, R]:
diff[L] += val
diff[R+1] -= val

Then apply prefix sum to get final array
```

---

## Pattern Categories

### Pattern 1: Basic Range Sum Query

**Template:**
```java
// Build prefix sum
int[] prefix = new int[n + 1];
for (int i = 0; i < n; i++) {
    prefix[i + 1] = prefix[i] + arr[i];
}

// Query sum from L to R (0-indexed)
int rangeSum = prefix[R + 1] - prefix[L];
```

### Pattern 2: Subarray Sum with Hash Map

**Template:**
```java
Map<Integer, Integer> map = new HashMap<>();
map.put(0, 1); // Base case: empty prefix
int prefixSum = 0, count = 0;

for (int i = 0; i < n; i++) {
    prefixSum += arr[i];
    
    // Check if (prefixSum - target) exists
    if (map.containsKey(prefixSum - target)) {
        count += map.get(prefixSum - target);
    }
    
    map.put(prefixSum, map.getOrDefault(prefixSum, 0) + 1);
}
```

### Pattern 3: 2D Prefix Sum

**Template:**
```java
// Build 2D prefix sum
int[][] prefix = new int[m + 1][n + 1];
for (int i = 1; i <= m; i++) {
    for (int j = 1; j <= n; j++) {
        prefix[i][j] = matrix[i-1][j-1] 
                     + prefix[i-1][j] 
                     + prefix[i][j-1] 
                     - prefix[i-1][j-1];
    }
}

// Query rectangle sum
int sum = prefix[r2+1][c2+1] 
        - prefix[r1][c2+1] 
        - prefix[r2+1][c1] 
        + prefix[r1][c1];
```

### Pattern 4: Difference Array

**Template:**
```java
int[] diff = new int[n + 1];

// Apply range updates
for (int[] update : updates) {
    int L = update[0], R = update[1], val = update[2];
    diff[L] += val;
    diff[R + 1] -= val;
}

// Convert to final array using prefix sum
int[] result = new int[n];
result[0] = diff[0];
for (int i = 1; i < n; i++) {
    result[i] = result[i - 1] + diff[i];
}
```

---

## Problem-Solving Framework

### Step 1: Identify Prefix Sum Pattern
Ask yourself:
- Do I need to calculate range sums multiple times?
- Am I looking for subarrays with specific sum properties?
- Can I convert the problem to cumulative values?
- Is there a "sum from start to i" relationship?

### Step 2: Choose the Right Variant
- **Basic prefix sum:** Simple range queries
- **Hash map variant:** Finding subarrays with target sum
- **2D prefix sum:** Matrix range queries
- **Difference array:** Multiple range updates

### Step 3: Handle Edge Cases
- Empty array
- Single element
- Negative numbers
- Integer overflow
- Out of bounds indices

### Step 4: Optimize Space
- Can you use the input array itself?
- Do you need to store all prefix sums?
- Can you use running sum instead?

---

## Curated Problem List

### 🟢 Level 1: Fundamentals (Master These First)

#### 1. **Range Sum Query - Immutable**
- **Platform:** LeetCode #303
- **Difficulty:** Easy
- **Pattern:** Basic Prefix Sum
- **Key Learning:** Understanding prefix sum construction and query
- **Link:** https://leetcode.com/problems/range-sum-query-immutable/
- **Must Read Discussion:** https://leetcode.com/problems/range-sum-query-immutable/solutions/75184/java-simple-o-n-init-and-o-1-query-solution/

#### 2. **Running Sum of 1d Array**
- **Platform:** LeetCode #1480
- **Difficulty:** Easy
- **Pattern:** Basic Prefix Sum
- **Key Learning:** Building prefix sum array
- **Link:** https://leetcode.com/problems/running-sum-of-1d-array/

#### 3. **Find Pivot Index**
- **Platform:** LeetCode #724
- **Difficulty:** Easy
- **Pattern:** Prefix + Suffix Sum
- **Key Learning:** Using prefix sum to check balance condition
- **Link:** https://leetcode.com/problems/find-pivot-index/

#### 4. **Find the Middle Index in Array**
- **Platform:** LeetCode #1991
- **Difficulty:** Easy
- **Pattern:** Prefix Sum
- **Key Learning:** Similar to pivot index, reinforces concept
- **Link:** https://leetcode.com/problems/find-the-middle-index-in-array/

#### 5. **Sum of All Odd Length Subarrays**
- **Platform:** LeetCode #1588
- **Difficulty:** Easy
- **Pattern:** Prefix Sum + Math
- **Key Learning:** Contribution technique with prefix sum
- **Link:** https://leetcode.com/problems/sum-of-all-odd-length-subarrays/

---

### 🟡 Level 2: Intermediate Techniques

#### 6. **Subarray Sum Equals K**
- **Platform:** LeetCode #560
- **Difficulty:** Medium
- **Pattern:** Prefix Sum + Hash Map
- **Key Learning:** Classic problem - finding subarrays with target sum
- **Link:** https://leetcode.com/problems/subarray-sum-equals-k/
- **Must Read Discussion:** https://leetcode.com/problems/subarray-sum-equals-k/solutions/102106/java-solution-presum-hashmap/

#### 7. **Contiguous Array**
- **Platform:** LeetCode #525
- **Difficulty:** Medium
- **Pattern:** Prefix Sum + Hash Map (with transformation)
- **Key Learning:** Transform 0→-1, then find subarray with sum=0
- **Link:** https://leetcode.com/problems/contiguous-array/
- **Must Read Discussion:** https://leetcode.com/problems/contiguous-array/solutions/99655/python-o-n-solution-with-visual-explanation/

#### 8. **Product of Array Except Self**
- **Platform:** LeetCode #238
- **Difficulty:** Medium
- **Pattern:** Prefix Product + Suffix Product
- **Key Learning:** Prefix sum concept applied to products
- **Link:** https://leetcode.com/problems/product-of-array-except-self/
- **Must Read Discussion:** https://leetcode.com/problems/product-of-array-except-self/solutions/65622/simple-java-solution-in-o-n-without-extra-space/

#### 9. **Range Sum Query 2D - Immutable**
- **Platform:** LeetCode #304
- **Difficulty:** Medium
- **Pattern:** 2D Prefix Sum
- **Key Learning:** Extending prefix sum to 2D matrices
- **Link:** https://leetcode.com/problems/range-sum-query-2d-immutable/
- **Must Read Discussion:** https://leetcode.com/problems/range-sum-query-2d-immutable/solutions/75350/clean-c-solution-and-explaination-o-mn-space-with-o-1-time/

#### 10. **Continuous Subarray Sum**
- **Platform:** LeetCode #523
- **Difficulty:** Medium
- **Pattern:** Prefix Sum + Hash Map + Modulo
- **Key Learning:** Using modulo arithmetic with prefix sum
- **Link:** https://leetcode.com/problems/continuous-subarray-sum/
- **Must Read Discussion:** https://leetcode.com/problems/continuous-subarray-sum/solutions/99499/java-o-n-time-o-k-space/

#### 11. **Subarray Sums Divisible by K**
- **Platform:** LeetCode #974
- **Difficulty:** Medium
- **Pattern:** Prefix Sum + Hash Map + Modulo
- **Key Learning:** Counting subarrays with divisibility condition
- **Link:** https://leetcode.com/problems/subarray-sums-divisible-by-k/
- **Must Read Discussion:** https://leetcode.com/problems/subarray-sums-divisible-by-k/solutions/217980/java-o-n-with-hashmap-and-prefix-sum/

#### 12. **Make Sum Divisible by P**
- **Platform:** LeetCode #1590
- **Difficulty:** Medium
- **Pattern:** Prefix Sum + Hash Map + Modulo
- **Key Learning:** Finding minimum subarray to remove
- **Link:** https://leetcode.com/problems/make-sum-divisible-by-p/

---

### 🔴 Level 3: Advanced Applications

#### 13. **Maximum Size Subarray Sum Equals k**
- **Platform:** LeetCode #325 (Premium)
- **Alternative:** GeeksforGeeks - "Largest subarray with sum k"
- **Difficulty:** Medium
- **Pattern:** Prefix Sum + Hash Map
- **Key Learning:** Finding longest subarray with target sum
- **GFG Link:** https://www.geeksforgeeks.org/problems/longest-sub-array-with-sum-k0809/1

#### 14. **Binary Subarrays With Sum**
- **Platform:** LeetCode #930
- **Difficulty:** Medium
- **Pattern:** Prefix Sum + Hash Map
- **Key Learning:** Counting binary subarrays with target sum
- **Link:** https://leetcode.com/problems/binary-subarrays-with-sum/

#### 15. **Count Number of Nice Subarrays**
- **Platform:** LeetCode #1248
- **Difficulty:** Medium
- **Pattern:** Prefix Sum + Hash Map (transform even→0, odd→1)
- **Key Learning:** Similar to binary subarrays, transformation technique
- **Link:** https://leetcode.com/problems/count-number-of-nice-subarrays/

#### 16. **Maximum Subarray**
- **Platform:** LeetCode #53
- **Difficulty:** Medium
- **Pattern:** Kadane's Algorithm (related to prefix sum)
- **Key Learning:** Finding maximum sum subarray
- **Link:** https://leetcode.com/problems/maximum-subarray/
- **Must Read Discussion:** https://leetcode.com/problems/maximum-subarray/solutions/1595195/c-python-7-simple-solutions-w-explanation-brute-force-dp-kadane-divide-conquer/

#### 17. **Maximum Sum of Two Non-Overlapping Subarrays**
- **Platform:** LeetCode #1031
- **Difficulty:** Medium
- **Pattern:** Prefix Sum + DP
- **Key Learning:** Combining prefix sum with dynamic programming
- **Link:** https://leetcode.com/problems/maximum-sum-of-two-non-overlapping-subarrays/

#### 18. **Minimum Operations to Reduce X to Zero**
- **Platform:** LeetCode #1658
- **Difficulty:** Medium
- **Pattern:** Prefix Sum (inverse thinking - find max middle subarray)
- **Key Learning:** Converting problem to finding maximum subarray
- **Link:** https://leetcode.com/problems/minimum-operations-to-reduce-x-to-zero/
- **Must Read Discussion:** https://leetcode.com/problems/minimum-operations-to-reduce-x-to-zero/solutions/935935/java-detailed-explanation-o-n-prefix-sum-hashmap-sliding-window/

#### 19. **Subarray Sum Equals K II (Count of Range Sum)**
- **Platform:** LeetCode #327
- **Difficulty:** Hard
- **Pattern:** Prefix Sum + Merge Sort / Segment Tree
- **Key Learning:** Advanced counting with range constraints
- **Link:** https://leetcode.com/problems/count-of-range-sum/

#### 20. **Maximum Sum Rectangle in a 2D Matrix**
- **Platform:** GeeksforGeeks
- **Difficulty:** Hard
- **Pattern:** 2D Prefix Sum + Kadane's Algorithm
- **Key Learning:** Combining 2D prefix sum with 1D max subarray
- **Link:** https://www.geeksforgeeks.org/problems/maximum-sum-rectangle2948/1

---

### 🏆 Level 4: Expert Challenges

#### 21. **Corporate Flight Bookings**
- **Platform:** LeetCode #1109
- **Difficulty:** Medium
- **Pattern:** Difference Array
- **Key Learning:** Range update technique using difference array
- **Link:** https://leetcode.com/problems/corporate-flight-bookings/
- **Must Read Discussion:** https://leetcode.com/problems/corporate-flight-bookings/solutions/328871/c-java-python-sweep-line/

#### 22. **Car Pooling**
- **Platform:** LeetCode #1094
- **Difficulty:** Medium
- **Pattern:** Difference Array / Sweep Line
- **Key Learning:** Range updates with capacity constraint
- **Link:** https://leetcode.com/problems/car-pooling/

#### 23. **Range Addition**
- **Platform:** LeetCode #370 (Premium)
- **Alternative:** Practice on similar problems
- **Difficulty:** Medium
- **Pattern:** Difference Array
- **Key Learning:** Multiple range updates efficiently

#### 24. **Maximum Sum of 3 Non-Overlapping Subarrays**
- **Platform:** LeetCode #689
- **Difficulty:** Hard
- **Pattern:** Prefix Sum + DP
- **Key Learning:** Complex optimization with multiple subarrays
- **Link:** https://leetcode.com/problems/maximum-sum-of-3-non-overlapping-subarrays/

#### 25. **Number of Submatrices That Sum to Target**
- **Platform:** LeetCode #1074
- **Difficulty:** Hard
- **Pattern:** 2D Prefix Sum + Hash Map
- **Key Learning:** Combining 2D prefix sum with subarray sum technique
- **Link:** https://leetcode.com/problems/number-of-submatrices-that-sum-to-target/
- **Must Read Discussion:** https://leetcode.com/problems/number-of-submatrices-that-sum-to-target/solutions/303750/java-c-python-find-the-subarray-with-target-sum/

#### 26. **Maximum Sum BST in Binary Tree**
- **Platform:** LeetCode #1373
- **Difficulty:** Hard
- **Pattern:** Tree + Prefix Sum concept
- **Key Learning:** Applying cumulative sum in tree structures
- **Link:** https://leetcode.com/problems/maximum-sum-bst-in-binary-tree/

#### 27. **Longest Well-Performing Interval**
- **Platform:** LeetCode #1124
- **Difficulty:** Medium
- **Pattern:** Prefix Sum + Hash Map (transform >8→1, ≤8→-1)
- **Key Learning:** Transformation + finding subarray with sum > 0
- **Link:** https://leetcode.com/problems/longest-well-performing-interval/

#### 28. **K Inverse Pairs Array**
- **Platform:** LeetCode #629
- **Difficulty:** Hard
- **Pattern:** Prefix Sum + DP optimization
- **Key Learning:** Using prefix sum to optimize DP transitions
- **Link:** https://leetcode.com/problems/k-inverse-pairs-array/

#### 29. **Maximum Score of a Good Subarray**
- **Platform:** LeetCode #1793
- **Difficulty:** Hard
- **Pattern:** Prefix Min + Two Pointers
- **Key Learning:** Combining prefix operations with other techniques
- **Link:** https://leetcode.com/problems/maximum-score-of-a-good-subarray/

#### 30. **Stamping The Sequence**
- **Platform:** LeetCode #936
- **Difficulty:** Hard
- **Pattern:** Difference Array concept (reverse thinking)
- **Key Learning:** Advanced application of range operations
- **Link:** https://leetcode.com/problems/stamping-the-sequence/

---

## Advanced Techniques

### 1. **Prefix Sum with Modulo Arithmetic**

Used for divisibility problems.

```java
Map<Integer, Integer> modCount = new HashMap<>();
modCount.put(0, 1); // Empty prefix has remainder 0
int prefixSum = 0, count = 0;

for (int num : arr) {
    prefixSum = ((prefixSum + num) % k + k) % k; // Handle negatives
    count += modCount.getOrDefault(prefixSum, 0);
    modCount.put(prefixSum, modCount.getOrDefault(prefixSum, 0) + 1);
}
```

**Key Insight:** If `(prefix[j] - prefix[i]) % k == 0`, then `prefix[j] % k == prefix[i] % k`

### 2. **2D Prefix Sum - Optimized Construction**

```java
// Space-optimized: modify input matrix
for (int i = 0; i < m; i++) {
    for (int j = 0; j < n; j++) {
        if (i > 0) matrix[i][j] += matrix[i-1][j];
        if (j > 0) matrix[i][j] += matrix[i][j-1];
        if (i > 0 && j > 0) matrix[i][j] -= matrix[i-1][j-1];
    }
}
```

### 3. **Prefix Sum with Transformation**

Transform array elements to solve different problems:

```java
// Example: Contiguous Array (equal 0s and 1s)
// Transform: 0 → -1, 1 → 1
// Then find subarray with sum = 0

for (int i = 0; i < n; i++) {
    arr[i] = (arr[i] == 0) ? -1 : 1;
}
// Now apply prefix sum + hash map for sum = 0
```

### 4. **Prefix + Suffix Sum**

Used in problems requiring left and right information.

```java
int[] prefix = new int[n];
int[] suffix = new int[n];

prefix[0] = arr[0];
for (int i = 1; i < n; i++) {
    prefix[i] = prefix[i-1] + arr[i];
}

suffix[n-1] = arr[n-1];
for (int i = n-2; i >= 0; i--) {
    suffix[i] = suffix[i+1] + arr[i];
}

// Check condition: prefix[i-1] == suffix[i+1]
```

### 5. **Prefix Sum on Frequency Array**

```java
// Count elements ≤ x in range [L, R]
int[] freq = new int[MAX_VAL + 1];
for (int num : arr) {
    freq[num]++;
}

// Build prefix sum on frequency
for (int i = 1; i <= MAX_VAL; i++) {
    freq[i] += freq[i-1];
}

// Query: count of elements ≤ x
int count = freq[x];
```

### 6. **Difference Array for 2D Range Updates**

```java
// Add 'val' to rectangle (r1,c1) to (r2,c2)
diff[r1][c1] += val;
diff[r1][c2+1] -= val;
diff[r2+1][c1] -= val;
diff[r2+1][c2+1] += val;

// Apply 2D prefix sum to get final matrix
```

---

## Common Pitfalls

### 1. **Off-by-One Errors in Range Queries**

```java
// ❌ Wrong: missing +1
int sum = prefix[R] - prefix[L];

// ✅ Correct: for 0-indexed array with 1-indexed prefix
int sum = prefix[R + 1] - prefix[L];
```

### 2. **Forgetting Base Case in Hash Map**

```java
// ❌ Wrong: missing base case
Map<Integer, Integer> map = new HashMap<>();

// ✅ Correct: handle empty prefix
Map<Integer, Integer> map = new HashMap<>();
map.put(0, 1); // or map.put(0, -1) for index problems
```

### 3. **Integer Overflow**

```java
// ❌ Wrong: can overflow
int prefixSum = 0;

// ✅ Correct: use long for large sums
long prefixSum = 0;
```

### 4. **Incorrect Modulo for Negative Numbers**

```java
// ❌ Wrong: negative remainders
int mod = prefixSum % k;

// ✅ Correct: handle negatives
int mod = ((prefixSum % k) + k) % k;
```

### 5. **2D Prefix Sum Index Confusion**

```java
// ❌ Wrong: easy to mix up indices
sum = prefix[r2][c2] - prefix[r1][c2] - prefix[r2][c1] + prefix[r1][c1];

// ✅ Correct: use 1-indexed prefix array to avoid confusion
// Build with padding: prefix[m+1][n+1]
```

### 6. **Not Considering Empty Subarray**

```java
// For "subarray sum equals k" starting from index 0
// Must initialize: map.put(0, 1)
```

---

## Practice Strategy

### Week 1: Build Foundation
- Solve problems 1-5
- Master basic prefix sum construction
- Practice range sum queries
- Understand prefix + suffix sum

### Week 2: Hash Map Mastery
- Solve problems 6-12
- Focus on prefix sum + hash map pattern
- Practice modulo arithmetic
- Learn transformation techniques

### Week 3: 2D and Advanced
- Solve problems 13-20
- Master 2D prefix sum
- Combine with other algorithms
- Practice on hard problems

### Week 4: Difference Array & Expert
- Solve problems 21-30
- Learn difference array technique
- Tackle expert-level challenges
- Optimize solutions

### Week 5: Mixed Practice & Review
- Revisit difficult problems
- Time yourself
- Read discussions
- Identify patterns quickly

---

## Must-Read LeetCode Discussions

### 🌟 **Foundational Concepts**

1. **Prefix Sum Basics**
   - https://leetcode.com/discuss/study-guide/1682594/prefix-sum-problems-patterns
   - Comprehensive guide covering all prefix sum patterns

2. **Subarray Sum Problems Pattern**
   - https://leetcode.com/problems/subarray-sum-equals-k/solutions/102106/java-solution-presum-hashmap/
   - Master template for subarray sum with hash map

3. **Prefix Sum + Hash Map Technique**
   - https://leetcode.com/discuss/general-discussion/1189865/prefix-sum-hash-map-technique
   - Detailed explanation with multiple examples

### 🎯 **Specific Problem Discussions**

4. **Contiguous Array - Visual Explanation**
   - https://leetcode.com/problems/contiguous-array/solutions/99655/python-o-n-solution-with-visual-explanation/
   - Best visual explanation of transformation technique

5. **2D Prefix Sum - Clean Explanation**
   - https://leetcode.com/problems/range-sum-query-2d-immutable/solutions/75350/clean-c-solution-and-explaination-o-mn-space-with-o-1-time/
   - Clear explanation of 2D prefix sum construction

6. **Subarray Sums Divisible by K**
   - https://leetcode.com/problems/subarray-sums-divisible-by-k/solutions/217980/java-o-n-with-hashmap-and-prefix-sum/
   - Modulo arithmetic with prefix sum

7. **Product of Array Except Self**
   - https://leetcode.com/problems/product-of-array-except-self/solutions/65622/simple-java-solution-in-o-n-without-extra-space/
   - Prefix product technique

8. **Difference Array Technique**
   - https://leetcode.com/problems/corporate-flight-bookings/solutions/328871/c-java-python-sweep-line/
   - Best explanation of difference array

9. **2D Submatrix Sum**
   - https://leetcode.com/problems/number-of-submatrices-that-sum-to-target/solutions/303750/java-c-python-find-the-subarray-with-target-sum/
   - Combining 2D prefix sum with 1D technique

10. **Minimum Operations to Reduce X**
    - https://leetcode.com/problems/minimum-operations-to-reduce-x-to-zero/solutions/935935/java-detailed-explanation-o-n-prefix-sum-hashmap-sliding-window/
    - Inverse thinking with prefix sum

### 📚 **Study Guides & Patterns**

11. **Complete Prefix Sum Study Guide**
    - https://leetcode.com/discuss/study-guide/2166045/prefix-sum-study-guide
    - Comprehensive collection of problems

12. **Kadane's Algorithm & Variations**
    - https://leetcode.com/problems/maximum-subarray/solutions/1595195/c-python-7-simple-solutions-w-explanation-brute-force-dp-kadane-divide-conquer/
    - Multiple approaches to maximum subarray

13. **Subarray Problems Master List**
    - https://leetcode.com/discuss/study-guide/1128615/subarray-problems-master-list
    - All subarray problems categorized

14. **Hash Map + Prefix Sum Pattern**
    - https://leetcode.com/discuss/general-discussion/1189865/prefix-sum-hash-map-technique
    - Template for all hash map problems

15. **Range Query Techniques**
    - https://leetcode.com/discuss/study-guide/1731911/range-query-techniques
    - Prefix sum, segment tree, BIT comparison

---

## Time & Space Complexity

### Typical Complexities:

| Operation | Time | Space |
|-----------|------|-------|
| Build prefix sum (1D) | O(n) | O(n) |
| Range query (1D) | O(1) | - |
| Build prefix sum (2D) | O(m×n) | O(m×n) |
| Range query (2D) | O(1) | - |
| Subarray sum with hash map | O(n) | O(n) |
| Difference array update | O(1) per update | O(n) |

### Comparison with Brute Force:

| Problem Type | Brute Force | Prefix Sum | Improvement |
|--------------|-------------|------------|-------------|
| Q range queries | O(Q×n) | O(n + Q) | Massive for large Q |
| Subarray sum | O(n²) | O(n) | Quadratic to linear |
| 2D range queries | O(Q×m×n) | O(m×n + Q) | Huge improvement |

---

## Key Takeaways

1. **Prefix sum trades space for time** - O(n) preprocessing for O(1) queries
2. **Hash map unlocks subarray problems** - store prefix sums to find matching pairs
3. **Transformation is powerful** - convert problems to prefix sum form
4. **2D extends naturally** - same principles apply to matrices
5. **Difference array is inverse** - use for range updates
6. **Modulo arithmetic** - essential for divisibility problems
7. **Always consider edge cases** - empty subarrays, negative numbers, overflow

---

## Quick Reference Card

| Problem Type | Technique | Key Data Structure |
|--------------|-----------|-------------------|
| Range sum query | Basic prefix sum | Array |
| Subarray sum = k | Prefix + hash map | HashMap<sum, count> |
| Subarray sum divisible by k | Prefix + modulo | HashMap<remainder, count> |
| Equal 0s and 1s | Transform + prefix | HashMap (0→-1, 1→1) |
| 2D range query | 2D prefix sum | 2D array |
| Multiple range updates | Difference array | Array + prefix sum |
| Longest subarray sum = k | Prefix + hash map | HashMap<sum, index> |
| Maximum subarray | Kadane's algorithm | Running sum |

---

## Visual Debugging Tips

### 1. **Draw the Prefix Array**
```
Array:    [2, -1, 3, -2, 4]
Prefix:   [2,  1, 4,  2, 6]
          
Query sum(1, 3) = prefix[3] - prefix[0] = 2 - 2 = 0
Verify: -1 + 3 + (-2) = 0 ✓
```

### 2. **Trace Hash Map State**
```
Finding subarrays with sum = 5
Array: [1, 2, 3, 2, 1]

i=0: sum=1, map={0:1, 1:1}
i=1: sum=3, map={0:1, 1:1, 3:1}
i=2: sum=6, map={0:1, 1:1, 3:1, 6:1}, found! (6-5=1 exists)
i=3: sum=8, map={0:1, 1:1, 3:1, 6:1, 8:1}, found! (8-5=3 exists)
i=4: sum=9, map={0:1, 1:1, 3:1, 6:1, 8:1, 9:1}
```

### 3. **Visualize 2D Prefix Sum**
```
Matrix:        Prefix:
1  2  3        1  3  6
4  5  6   →    5 12 21
7  8  9       12 27 45

Query (1,1) to (2,2):
= 45 - 3 - 12 + 1 = 31
Verify: 5+6+8+9 = 28... wait, let me recalculate!
= 45 - 6 - 12 + 1 = 28 ✓
```

---

## Additional Resources

### Online Judges
- **LeetCode:** Best for interview preparation
- **Codeforces:** Competitive programming problems
- **AtCoder:** High-quality prefix sum problems
- **GeeksforGeeks:** Good explanations and practice

### Visualization Tools
- Use pen and paper for small examples
- Draw prefix arrays for debugging
- Trace hash map states step by step

### Practice Tips
- Start with brute force, then optimize
- Always verify with small examples
- Read discussions after solving
- Time yourself on medium problems
- Revisit hard problems after a week

---

## Common Interview Questions

### Questions Interviewers Ask:
1. "Find all subarrays with sum equal to k"
2. "What's the sum of elements from index i to j?" (multiple queries)
3. "Find the longest subarray with equal 0s and 1s"
4. "Count subarrays with sum divisible by k"
5. "Find maximum sum rectangle in 2D matrix"

### How to Approach:
1. Clarify if array can have negatives
2. Ask about multiple queries
3. Discuss time/space tradeoffs
4. Mention hash map optimization
5. Code cleanly with proper variable names

---

**Happy Coding! 🚀**

*Remember: Prefix sum is about preprocessing to answer queries efficiently. Master the hash map variant - it's the most frequently asked in interviews!*
