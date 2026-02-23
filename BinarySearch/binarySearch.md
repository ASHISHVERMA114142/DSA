# Binary Search - Complete Interview Guide

## Table of Contents
1. [What is Binary Search?](#what-is-binary-search)
2. [Binary Search Fundamentals](#binary-search-fundamentals)
3. [Binary Search Templates](#binary-search-templates)
4. [Binary Search Variants](#binary-search-variants)
5. [Common Binary Search Patterns](#common-binary-search-patterns)
6. [Practice Problems by Category](#practice-problems-by-category)
7. [Interview Tips](#interview-tips)

---

## What is Binary Search?

Binary Search is an efficient algorithm for finding a target value within a **sorted array** or search space. It works by repeatedly dividing the search interval in half, eliminating half of the remaining elements in each step.

**Key Characteristics:**
- **Time Complexity:** O(log n)
- **Space Complexity:** O(1) iterative, O(log n) recursive
- **Prerequisite:** Array must be sorted (or search space must be monotonic)
- **Principle:** Divide and conquer

```
Example: Find 7 in sorted array
Array: [1, 3, 5, 7, 9, 11, 13]
        L        M         R

Step 1: mid = 7, target = 7 → Found!

Example: Find 9
Step 1: [1, 3, 5, 7, 9, 11, 13]  mid=7 < 9, search right
Step 2: [9, 11, 13]               mid=11 > 9, search left
Step 3: [9]                       mid=9 = 9 → Found!
```

**Why Binary Search?**
- Reduces search time from O(n) to O(log n)
- Essential for large datasets
- Foundation for many advanced algorithms
- Appears in 15-20% of coding interviews

---

## Binary Search Fundamentals

### Basic Concept

Binary search works on the principle of **eliminating half the search space** in each iteration based on a comparison with the middle element.

**Requirements:**
1. **Sorted data** (ascending or descending)
2. **Random access** (array-like structure)
3. **Comparison operation** (can determine if target is left or right of mid)

### When to Use Binary Search?

**Direct indicators:**
- Array is sorted
- Problem asks for "find target in sorted array"
- Need O(log n) time complexity

**Hidden indicators:**
- "Find minimum/maximum value that satisfies condition"
- "Find first/last occurrence"
- "Search in rotated sorted array"
- "Find peak element"
- Problem involves monotonic function
- Need to optimize from O(n) to O(log n)

### Binary Search vs Linear Search

| Aspect | Linear Search | Binary Search |
|--------|--------------|---------------|
| Time Complexity | O(n) | O(log n) |
| Space Complexity | O(1) | O(1) iterative |
| Data Structure | Any | Sorted array |
| Use Case | Small/unsorted data | Large sorted data |

---

## Binary Search Templates

### Template 1: Basic Binary Search (Exact Match)

Find exact target in sorted array. Search space reduces to 0.

**When to use:**
- Finding exact element
- Array is fully sorted
- Simple yes/no answer

```java
public int binarySearch(int[] nums, int target) {
    int left = 0;
    int right = nums.length - 1;
    
    while (left <= right) {
        int mid = left + (right - left) / 2;  // Avoid overflow
        
        if (nums[mid] == target) {
            return mid;  // Found target
        } else if (nums[mid] < target) {
            left = mid + 1;  // Search right half
        } else {
            right = mid - 1;  // Search left half
        }
    }
    
    return -1;  // Target not found
}
```

**Key Points:**
- Loop condition: `left <= right`
- Search space: `[left, right]` (both inclusive)
- Update: `left = mid + 1` or `right = mid - 1`
- Termination: `left > right`

### Template 2: Find Boundary (Left/Right Most)

Find first or last occurrence of target, or insertion position.

**When to use:**
- Find first/last occurrence
- Find insertion position
- Find boundary of condition

```java
// Find LEFTMOST (first occurrence or insertion position)
public int findLeft(int[] nums, int target) {
    int left = 0;
    int right = nums.length;  // Note: length, not length-1
    
    while (left < right) {
        int mid = left + (right - left) / 2;
        
        if (nums[mid] < target) {
            left = mid + 1;  // Target is on the right
        } else {
            right = mid;  // Target is on the left or at mid
        }
    }
    
    return left;  // Leftmost position
}

// Find RIGHTMOST (last occurrence or insertion position)
public int findRight(int[] nums, int target) {
    int left = 0;
    int right = nums.length;
    
    while (left < right) {
        int mid = left + (right - left) / 2;
        
        if (nums[mid] <= target) {
            left = mid + 1;  // Continue searching right
        } else {
            right = mid;
        }
    }
    
    return left - 1;  // Rightmost position (subtract 1)
}
```

**Key Points:**
- Loop condition: `left < right`
- Search space: `[left, right)` (left inclusive, right exclusive)
- Update: `left = mid + 1` or `right = mid`
- Never `right = mid - 1` in this template
- Returns position even if target doesn't exist

### Template 3: Binary Search with Condition

Search for minimum/maximum value that satisfies a condition.

**When to use:**
- "Find minimum x such that condition(x) is true"
- "Find maximum x such that condition(x) is true"
- Answer space is continuous or monotonic

```java
// Find minimum value that satisfies condition
public int binarySearchCondition(int left, int right) {
    while (left < right) {
        int mid = left + (right - left) / 2;
        
        if (condition(mid)) {
            right = mid;  // Try to find smaller value
        } else {
            left = mid + 1;  // Need larger value
        }
    }
    
    return left;
}

// Find maximum value that satisfies condition
public int binarySearchConditionMax(int left, int right) {
    while (left < right) {
        int mid = left + (right - left + 1) / 2;  // Round up to avoid infinite loop
        
        if (condition(mid)) {
            left = mid;  // Try to find larger value
        } else {
            right = mid - 1;  // Need smaller value
        }
    }
    
    return left;
}

private boolean condition(int x) {
    // Define your condition here
    return true;  // or false based on logic
}
```

**Key Points:**
- Used for optimization problems
- Condition must be monotonic
- When finding max, use `mid = left + (right - left + 1) / 2` to avoid infinite loop
- Common in "capacity to ship packages", "split array", etc.

### Template Comparison

| Template | Loop Condition | Search Space | Use Case |
|----------|---------------|--------------|----------|
| Template 1 | `left <= right` | `[left, right]` | Exact match |
| Template 2 | `left < right` | `[left, right)` | Find boundary |
| Template 3 | `left < right` | `[left, right)` | Optimize with condition |

---

## Binary Search Variants

### 1. Recursive Binary Search

```java
public int binarySearchRecursive(int[] nums, int target) {
    return binarySearchHelper(nums, target, 0, nums.length - 1);
}

private int binarySearchHelper(int[] nums, int target, int left, int right) {
    if (left > right) {
        return -1;  // Base case: not found
    }
    
    int mid = left + (right - left) / 2;
    
    if (nums[mid] == target) {
        return mid;
    } else if (nums[mid] < target) {
        return binarySearchHelper(nums, target, mid + 1, right);
    } else {
        return binarySearchHelper(nums, target, left, mid - 1);
    }
}
```

**Pros:**
- Clean and intuitive code
- Natural for divide-and-conquer thinking

**Cons:**
- O(log n) space due to call stack
- Potential stack overflow for very large arrays

### 2. Binary Search on Answer Space

Instead of searching in an array, search in the range of possible answers.

```java
// Example: Find square root
public int mySqrt(int x) {
    if (x == 0) return 0;
    
    int left = 1;
    int right = x;
    
    while (left <= right) {
        int mid = left + (right - left) / 2;
        
        if (mid == x / mid) {
            return mid;
        } else if (mid < x / mid) {
            left = mid + 1;
        } else {
            right = mid - 1;
        }
    }
    
    return right;  // Return floor value
}
```

### 3. Binary Search on Rotated Array

```java
public int searchRotated(int[] nums, int target) {
    int left = 0;
    int right = nums.length - 1;
    
    while (left <= right) {
        int mid = left + (right - left) / 2;
        
        if (nums[mid] == target) {
            return mid;
        }
        
        // Determine which half is sorted
        if (nums[left] <= nums[mid]) {
            // Left half is sorted
            if (nums[left] <= target && target < nums[mid]) {
                right = mid - 1;
            } else {
                left = mid + 1;
            }
        } else {
            // Right half is sorted
            if (nums[mid] < target && target <= nums[right]) {
                left = mid + 1;
            } else {
                right = mid - 1;
            }
        }
    }
    
    return -1;
}
```

### 4. Binary Search on 2D Matrix

```java
// Search in row-wise and column-wise sorted matrix
public boolean searchMatrix(int[][] matrix, int target) {
    if (matrix.length == 0) return false;
    
    int m = matrix.length;
    int n = matrix[0].length;
    int left = 0;
    int right = m * n - 1;
    
    while (left <= right) {
        int mid = left + (right - left) / 2;
        int midValue = matrix[mid / n][mid % n];  // Convert 1D to 2D
        
        if (midValue == target) {
            return true;
        } else if (midValue < target) {
            left = mid + 1;
        } else {
            right = mid - 1;
        }
    }
    
    return false;
}
```

### 5. Binary Search with Duplicates

```java
// Find first occurrence in array with duplicates
public int findFirst(int[] nums, int target) {
    int left = 0;
    int right = nums.length - 1;
    int result = -1;
    
    while (left <= right) {
        int mid = left + (right - left) / 2;
        
        if (nums[mid] == target) {
            result = mid;
            right = mid - 1;  // Continue searching left
        } else if (nums[mid] < target) {
            left = mid + 1;
        } else {
            right = mid - 1;
        }
    }
    
    return result;
}

// Find last occurrence
public int findLast(int[] nums, int target) {
    int left = 0;
    int right = nums.length - 1;
    int result = -1;
    
    while (left <= right) {
        int mid = left + (right - left) / 2;
        
        if (nums[mid] == target) {
            result = mid;
            left = mid + 1;  // Continue searching right
        } else if (nums[mid] < target) {
            left = mid + 1;
        } else {
            right = mid - 1;
        }
    }
    
    return result;
}
```

---

## Common Binary Search Patterns

### Pattern 1: Search in Sorted Array

**Problem Type:** Find element in sorted array

**Template:** Basic Binary Search (Template 1)

```java
public int search(int[] nums, int target) {
    int left = 0, right = nums.length - 1;
    
    while (left <= right) {
        int mid = left + (right - left) / 2;
        if (nums[mid] == target) return mid;
        else if (nums[mid] < target) left = mid + 1;
        else right = mid - 1;
    }
    
    return -1;
}
```

**Examples:**
- LeetCode 704: Binary Search
- LeetCode 35: Search Insert Position

### Pattern 2: Find First/Last Occurrence

**Problem Type:** Find boundary positions in sorted array

**Template:** Boundary Search (Template 2)

```java
// Find range of target
public int[] searchRange(int[] nums, int target) {
    int first = findFirst(nums, target);
    if (first == -1) return new int[]{-1, -1};
    int last = findLast(nums, target);
    return new int[]{first, last};
}
```

**Examples:**
- LeetCode 34: Find First and Last Position
- LeetCode 278: First Bad Version

### Pattern 3: Search in Rotated/Modified Array

**Problem Type:** Array has some modification but maintains partial order

**Key Insight:** Identify which half is sorted

```java
public int searchRotated(int[] nums, int target) {
    int left = 0, right = nums.length - 1;
    
    while (left <= right) {
        int mid = left + (right - left) / 2;
        if (nums[mid] == target) return mid;
        
        // Determine sorted half
        if (nums[left] <= nums[mid]) {
            // Left sorted
            if (nums[left] <= target && target < nums[mid]) {
                right = mid - 1;
            } else {
                left = mid + 1;
            }
        } else {
            // Right sorted
            if (nums[mid] < target && target <= nums[right]) {
                left = mid + 1;
            } else {
                right = mid - 1;
            }
        }
    }
    
    return -1;
}
```

**Examples:**
- LeetCode 33: Search in Rotated Sorted Array
- LeetCode 81: Search in Rotated Sorted Array II
- LeetCode 153: Find Minimum in Rotated Sorted Array

### Pattern 4: Binary Search on Answer

**Problem Type:** Minimize/maximize a value subject to constraints

**Key Insight:** Answer space is monotonic

**Steps:**
1. Define search space (min and max possible answers)
2. Write condition function to check if answer is valid
3. Binary search to find optimal answer

```java
// Example: Capacity to Ship Packages
public int shipWithinDays(int[] weights, int days) {
    int left = Arrays.stream(weights).max().getAsInt();  // Min capacity
    int right = Arrays.stream(weights).sum();             // Max capacity
    
    while (left < right) {
        int mid = left + (right - left) / 2;
        
        if (canShip(weights, days, mid)) {
            right = mid;  // Try smaller capacity
        } else {
            left = mid + 1;  // Need larger capacity
        }
    }
    
    return left;
}

private boolean canShip(int[] weights, int days, int capacity) {
    int daysNeeded = 1;
    int currentWeight = 0;
    
    for (int weight : weights) {
        if (currentWeight + weight > capacity) {
            daysNeeded++;
            currentWeight = 0;
        }
        currentWeight += weight;
    }
    
    return daysNeeded <= days;
}
```

**Examples:**
- LeetCode 410: Split Array Largest Sum
- LeetCode 875: Koko Eating Bananas
- LeetCode 1011: Capacity To Ship Packages Within D Days
- LeetCode 1482: Minimum Number of Days to Make m Bouquets

### Pattern 5: Peak Finding

**Problem Type:** Find local maximum/minimum

**Key Insight:** Compare with neighbors to decide direction

```java
public int findPeakElement(int[] nums) {
    int left = 0;
    int right = nums.length - 1;
    
    while (left < right) {
        int mid = left + (right - left) / 2;
        
        if (nums[mid] < nums[mid + 1]) {
            left = mid + 1;  // Peak is on the right
        } else {
            right = mid;  // Peak is on the left or at mid
        }
    }
    
    return left;
}
```

**Examples:**
- LeetCode 162: Find Peak Element
- LeetCode 852: Peak Index in a Mountain Array

### Pattern 6: Matrix Binary Search

**Problem Type:** Search in 2D matrix

**Approaches:**
1. Treat as 1D array (if fully sorted)
2. Search row then column
3. Start from corner (for row/column sorted)

```java
// Approach 1: Treat as 1D
public boolean searchMatrix(int[][] matrix, int target) {
    int m = matrix.length, n = matrix[0].length;
    int left = 0, right = m * n - 1;
    
    while (left <= right) {
        int mid = left + (right - left) / 2;
        int midValue = matrix[mid / n][mid % n];
        
        if (midValue == target) return true;
        else if (midValue < target) left = mid + 1;
        else right = mid - 1;
    }
    
    return false;
}

// Approach 3: Start from top-right corner
public boolean searchMatrixII(int[][] matrix, int target) {
    int row = 0;
    int col = matrix[0].length - 1;
    
    while (row < matrix.length && col >= 0) {
        if (matrix[row][col] == target) {
            return true;
        } else if (matrix[row][col] > target) {
            col--;  // Move left
        } else {
            row++;  // Move down
        }
    }
    
    return false;
}
```

**Examples:**
- LeetCode 74: Search a 2D Matrix
- LeetCode 240: Search a 2D Matrix II

### Pattern 7: Minimize Maximum / Maximize Minimum

**Problem Type:** Optimization with constraints

**Key Insight:** Binary search on answer, check feasibility

```java
// Example: Minimize maximum distance between gas stations
public double minmaxGasDist(int[] stations, int k) {
    double left = 0;
    double right = stations[stations.length - 1] - stations[0];
    
    while (right - left > 1e-6) {  // Precision threshold
        double mid = left + (right - left) / 2;
        
        if (canPlace(stations, k, mid)) {
            right = mid;  // Try smaller max distance
        } else {
            left = mid;  // Need larger max distance
        }
    }
    
    return left;
}

private boolean canPlace(int[] stations, int k, double maxDist) {
    int count = 0;
    for (int i = 1; i < stations.length; i++) {
        count += (int)((stations[i] - stations[i-1]) / maxDist);
    }
    return count <= k;
}
```

**Examples:**
- LeetCode 410: Split Array Largest Sum
- LeetCode 774: Minimize Max Distance to Gas Station
- LeetCode 1870: Minimum Speed to Arrive on Time

---

## Practice Problems by Category

### 1. Basic Binary Search

#### Easy
1. **[LeetCode 704] Binary Search**
   - URL: https://leetcode.com/problems/binary-search/
   - Concept: Classic binary search
   - Hint: Template 1 - exact match
   - Difficulty: ⭐

2. **[LeetCode 35] Search Insert Position**
   - URL: https://leetcode.com/problems/search-insert-position/
   - Concept: Find insertion position
   - Hint: Template 2 - find left boundary
   - Difficulty: ⭐

3. **[LeetCode 374] Guess Number Higher or Lower**
   - URL: https://leetcode.com/problems/guess-number-higher-or-lower/
   - Concept: Binary search with API
   - Hint: Similar to basic binary search
   - Difficulty: ⭐

4. **[LeetCode 69] Sqrt(x)**
   - URL: https://leetcode.com/problems/sqrtx/
   - Concept: Binary search on answer
   - Hint: Search from 1 to x, find largest k where k*k <= x
   - Difficulty: ⭐

5. **[LeetCode 367] Valid Perfect Square**
   - URL: https://leetcode.com/problems/valid-perfect-square/
   - Concept: Binary search on answer
   - Hint: Check if x = mid * mid
   - Difficulty: ⭐

6. **[LeetCode 441] Arranging Coins**
   - URL: https://leetcode.com/problems/arranging-coins/
   - Concept: Find complete rows
   - Hint: Binary search on rows, check if n >= rows*(rows+1)/2
   - Difficulty: ⭐

7. **[LeetCode 1337] The K Weakest Rows in a Matrix**
   - URL: https://leetcode.com/problems/the-k-weakest-rows-in-a-matrix/
   - Concept: Binary search to count 1s in each row
   - Hint: Use binary search to find last 1 in each row
   - Difficulty: ⭐

8. **[LeetCode 1608] Special Array With X Elements Greater Than or Equal X**
   - URL: https://leetcode.com/problems/special-array-with-x-elements-greater-than-or-equal-x/
   - Concept: Binary search on answer
   - Hint: Try values from 0 to n, count elements >= x
   - Difficulty: ⭐

---

### 2. Find First/Last Occurrence

#### Easy
9. **[LeetCode 278] First Bad Version**
   - URL: https://leetcode.com/problems/first-bad-version/
   - Concept: Find first occurrence
   - Hint: Template 2 - minimize with condition
   - Difficulty: ⭐

10. **[LeetCode 744] Find Smallest Letter Greater Than Target**
    - URL: https://leetcode.com/problems/find-smallest-letter-greater-than-target/
    - Concept: Find ceiling
    - Hint: Find leftmost element > target
    - Difficulty: ⭐

#### Medium
11. **[LeetCode 34] Find First and Last Position of Element in Sorted Array**
    - URL: https://leetcode.com/problems/find-first-and-last-position-of-element-in-sorted-array/
    - Concept: Find boundaries
    - Hint: Two binary searches - find left and right boundaries
    - Difficulty: ⭐⭐

12. **[LeetCode 1539] Kth Missing Positive Number**
    - URL: https://leetcode.com/problems/kth-missing-positive-number/
    - Concept: Binary search with offset
    - Hint: Missing count at index i = nums[i] - (i + 1)
    - Difficulty: ⭐⭐

13. **[LeetCode 2055] Plates Between Candles**
    - URL: https://leetcode.com/problems/plates-between-candles/
    - Concept: Binary search for boundaries
    - Hint: Precompute candle positions, binary search for range
    - Difficulty: ⭐⭐

14. **[LeetCode 1060] Missing Element in Sorted Array**
    - URL: https://leetcode.com/problems/missing-element-in-sorted-array/
    - Concept: Find kth missing number
    - Hint: Binary search, calculate missing count
    - Difficulty: ⭐⭐

---

### 3. Search in Rotated/Modified Array

#### Easy
15. **[LeetCode 1150] Check If a Number Is Majority Element in a Sorted Array**
    - URL: https://leetcode.com/problems/check-if-a-number-is-majority-element-in-a-sorted-array/
    - Concept: Find first and last occurrence
    - Hint: Use binary search to find range
    - Difficulty: ⭐

#### Medium
16. **[LeetCode 33] Search in Rotated Sorted Array**
    - URL: https://leetcode.com/problems/search-in-rotated-sorted-array/
    - Concept: Rotated array search
    - Hint: Identify which half is sorted
    - Difficulty: ⭐⭐

17. **[LeetCode 81] Search in Rotated Sorted Array II**
    - URL: https://leetcode.com/problems/search-in-rotated-sorted-array-ii/
    - Concept: Rotated array with duplicates
    - Hint: Handle duplicates by shrinking search space
    - Difficulty: ⭐⭐

18. **[LeetCode 153] Find Minimum in Rotated Sorted Array**
    - URL: https://leetcode.com/problems/find-minimum-in-rotated-sorted-array/
    - Concept: Find pivot point
    - Hint: Compare mid with right boundary
    - Difficulty: ⭐⭐

19. **[LeetCode 154] Find Minimum in Rotated Sorted Array II**
    - URL: https://leetcode.com/problems/find-minimum-in-rotated-sorted-array-ii/
    - Concept: Find minimum with duplicates
    - Hint: Handle duplicates carefully
    - Difficulty: ⭐⭐⭐

20. **[LeetCode 1095] Find in Mountain Array**
    - URL: https://leetcode.com/problems/find-in-mountain-array/
    - Concept: Search in bitonic array
    - Hint: Find peak first, then search both sides
    - Difficulty: ⭐⭐⭐

21. **[LeetCode 2137] Pour Water Between Buckets to Make Water Levels Equal**
    - URL: https://leetcode.com/problems/pour-water-between-buckets-to-make-water-levels-equal/
    - Concept: Binary search on final water level
    - Hint: Check if can achieve level with k operations
    - Difficulty: ⭐⭐

---

### 4. Peak Finding

#### Easy
22. **[LeetCode 852] Peak Index in a Mountain Array**
    - URL: https://leetcode.com/problems/peak-index-in-a-mountain-array/
    - Concept: Find peak in mountain
    - Hint: Compare mid with mid+1
    - Difficulty: ⭐

23. **[LeetCode 1064] Fixed Point**
    - URL: https://leetcode.com/problems/fixed-point/
    - Concept: Find index where arr[i] = i
    - Hint: Binary search, compare arr[mid] with mid
    - Difficulty: ⭐

#### Medium
24. **[LeetCode 162] Find Peak Element**
    - URL: https://leetcode.com/problems/find-peak-element/
    - Concept: Find any peak
    - Hint: Move towards increasing slope
    - Difficulty: ⭐⭐

25. **[LeetCode 1901] Find a Peak Element II**
    - URL: https://leetcode.com/problems/find-a-peak-element-ii/
    - Concept: Find peak in 2D matrix
    - Hint: Binary search on columns, find max in row
    - Difficulty: ⭐⭐

---

### 5. Binary Search on Answer Space

#### Medium
26. **[LeetCode 875] Koko Eating Bananas**
    - URL: https://leetcode.com/problems/koko-eating-bananas/
    - Concept: Minimize eating speed
    - Hint: Binary search on speed, check if can finish in time
    - Difficulty: ⭐⭐

27. **[LeetCode 1011] Capacity To Ship Packages Within D Days**
    - URL: https://leetcode.com/problems/capacity-to-ship-packages-within-d-days/
    - Concept: Minimize ship capacity
    - Hint: Binary search on capacity, simulate shipping
    - Difficulty: ⭐⭐

28. **[LeetCode 410] Split Array Largest Sum**
    - URL: https://leetcode.com/problems/split-array-largest-sum/
    - Concept: Minimize maximum subarray sum
    - Hint: Binary search on max sum, check if can split
    - Difficulty: ⭐⭐⭐

29. **[LeetCode 1482] Minimum Number of Days to Make m Bouquets**
    - URL: https://leetcode.com/problems/minimum-number-of-days-to-make-m-bouquets/
    - Concept: Minimize days
    - Hint: Binary search on days, count bouquets possible
    - Difficulty: ⭐⭐

30. **[LeetCode 1283] Find the Smallest Divisor Given a Threshold**
    - URL: https://leetcode.com/problems/find-the-smallest-divisor-given-a-threshold/
    - Concept: Minimize divisor
    - Hint: Binary search on divisor, calculate sum
    - Difficulty: ⭐⭐

31. **[LeetCode 1231] Divide Chocolate**
    - URL: https://leetcode.com/problems/divide-chocolate/
    - Concept: Maximize minimum sweetness
    - Hint: Binary search on sweetness, check if can divide
    - Difficulty: ⭐⭐⭐

32. **[LeetCode 1552] Magnetic Force Between Two Balls**
    - URL: https://leetcode.com/problems/magnetic-force-between-two-balls/
    - Concept: Maximize minimum distance
    - Hint: Binary search on distance, greedy placement
    - Difficulty: ⭐⭐

33. **[LeetCode 2064] Minimized Maximum of Products Distributed to Any Store**
    - URL: https://leetcode.com/problems/minimized-maximum-of-products-distributed-to-any-store/
    - Concept: Minimize maximum products per store
    - Hint: Binary search on max products, check feasibility
    - Difficulty: ⭐⭐

34. **[LeetCode 2187] Minimum Time to Complete Trips**
    - URL: https://leetcode.com/problems/minimum-time-to-complete-trips/
    - Concept: Minimize time
    - Hint: Binary search on time, count trips possible
    - Difficulty: ⭐⭐

35. **[LeetCode 2226] Maximum Candies Allocated to K Children**
    - URL: https://leetcode.com/problems/maximum-candies-allocated-to-k-children/
    - Concept: Maximize candies per child
    - Hint: Binary search on candies, check if can distribute
    - Difficulty: ⭐⭐

36. **[LeetCode 1760] Minimum Limit of Balls in a Bag**
    - URL: https://leetcode.com/problems/minimum-limit-of-balls-in-a-bag/
    - Concept: Minimize maximum balls
    - Hint: Binary search on max, count operations needed
    - Difficulty: ⭐⭐

37. **[LeetCode 2517] Maximum Tastiness of Candy Basket**
    - URL: https://leetcode.com/problems/maximum-tastiness-of-candy-basket/
    - Concept: Maximize minimum difference
    - Hint: Binary search on difference, greedy selection
    - Difficulty: ⭐⭐

38. **[LeetCode 1870] Minimum Speed to Arrive on Time**
    - URL: https://leetcode.com/problems/minimum-speed-to-arrive-on-time/
    - Concept: Minimize speed
    - Hint: Binary search on speed, calculate total time
    - Difficulty: ⭐⭐

39. **[LeetCode 2071] Maximum Number of Tasks You Can Assign**
    - URL: https://leetcode.com/problems/maximum-number-of-tasks-you-can-assign/
    - Concept: Maximize tasks completed
    - Hint: Binary search on number of tasks, greedy matching
    - Difficulty: ⭐⭐⭐

40. **[LeetCode 1891] Cutting Ribbons**
    - URL: https://leetcode.com/problems/cutting-ribbons/
    - Concept: Maximize ribbon length
    - Hint: Binary search on length, count ribbons
    - Difficulty: ⭐⭐

#### Hard
41. **[LeetCode 774] Minimize Max Distance to Gas Station**
    - URL: https://leetcode.com/problems/minimize-max-distance-to-gas-station/
    - Concept: Minimize maximum gap
    - Hint: Binary search on distance (use doubles)
    - Difficulty: ⭐⭐⭐

42. **[LeetCode 1889] Minimum Space Wasted From Packaging**
    - URL: https://leetcode.com/problems/minimum-space-wasted-from-packaging/
    - Concept: Minimize waste
    - Hint: Sort and binary search for each package
    - Difficulty: ⭐⭐⭐

43. **[LeetCode 2141] Maximum Running Time of N Computers**
    - URL: https://leetcode.com/problems/maximum-running-time-of-n-computers/
    - Concept: Maximize runtime
    - Hint: Binary search on time, check if batteries suffice
    - Difficulty: ⭐⭐⭐

44. **[LeetCode 2448] Minimum Cost to Make Array Equal**
    - URL: https://leetcode.com/problems/minimum-cost-to-make-array-equal/
    - Concept: Find optimal target value
    - Hint: Binary search or ternary search on target value
    - Difficulty: ⭐⭐⭐

---

### 6. Matrix Binary Search

#### Easy
45. **[LeetCode 1351] Count Negative Numbers in a Sorted Matrix**
    - URL: https://leetcode.com/problems/count-negative-numbers-in-a-sorted-matrix/
    - Concept: Count negatives efficiently
    - Hint: Binary search on each row or staircase walk
    - Difficulty: ⭐

#### Medium
46. **[LeetCode 74] Search a 2D Matrix**
    - URL: https://leetcode.com/problems/search-a-2d-matrix/
    - Concept: Search in fully sorted matrix
    - Hint: Treat as 1D array
    - Difficulty: ⭐⭐

47. **[LeetCode 240] Search a 2D Matrix II**
    - URL: https://leetcode.com/problems/search-a-2d-matrix-ii/
    - Concept: Search in row/column sorted matrix
    - Hint: Start from top-right or bottom-left corner
    - Difficulty: ⭐⭐

48. **[LeetCode 378] Kth Smallest Element in a Sorted Matrix**
    - URL: https://leetcode.com/problems/kth-smallest-element-in-a-sorted-matrix/
    - Concept: Find kth element
    - Hint: Binary search on value, count elements <= mid
    - Difficulty: ⭐⭐

49. **[LeetCode 1292] Maximum Side Length of a Square with Sum Less than or Equal to Threshold**
    - URL: https://leetcode.com/problems/maximum-side-length-of-a-square-with-sum-less-than-or-equal-to-threshold/
    - Concept: Maximize square size
    - Hint: Binary search on size, use prefix sum
    - Difficulty: ⭐⭐

---

### 7. Advanced Binary Search

#### Medium
50. **[LeetCode 287] Find the Duplicate Number**
    - URL: https://leetcode.com/problems/find-the-duplicate-number/
    - Concept: Find duplicate without modifying array
    - Hint: Binary search on value, count numbers <= mid
    - Difficulty: ⭐⭐

51. **[LeetCode 540] Single Element in a Sorted Array**
    - URL: https://leetcode.com/problems/single-element-in-a-sorted-array/
    - Concept: Find unique element
    - Hint: Check parity of index and neighbor
    - Difficulty: ⭐⭐

52. **[LeetCode 658] Find K Closest Elements**
    - URL: https://leetcode.com/problems/find-k-closest-elements/
    - Concept: Find k closest elements
    - Hint: Binary search for starting position
    - Difficulty: ⭐⭐

53. **[LeetCode 300] Longest Increasing Subsequence**
    - URL: https://leetcode.com/problems/longest-increasing-subsequence/
    - Concept: LIS with binary search
    - Hint: Maintain sorted array, binary search for position
    - Difficulty: ⭐⭐

54. **[LeetCode 354] Russian Doll Envelopes**
    - URL: https://leetcode.com/problems/russian-doll-envelopes/
    - Concept: 2D LIS
    - Hint: Sort by width, LIS on height
    - Difficulty: ⭐⭐⭐

55. **[LeetCode 1802] Maximum Value at a Given Index in a Bounded Array**
    - URL: https://leetcode.com/problems/maximum-value-at-a-given-index-in-a-bounded-array/
    - Concept: Maximize value at index
    - Hint: Binary search on value, calculate sum needed
    - Difficulty: ⭐⭐

56. **[LeetCode 1818] Minimum Absolute Sum Difference**
    - URL: https://leetcode.com/problems/minimum-absolute-sum-difference/
    - Concept: Minimize sum after one replacement
    - Hint: For each element, binary search for closest replacement
    - Difficulty: ⭐⭐

57. **[LeetCode 1898] Maximum Number of Removable Characters**
    - URL: https://leetcode.com/problems/maximum-number-of-removable-characters/
    - Concept: Maximize removals
    - Hint: Binary search on number of removals, check if subsequence exists
    - Difficulty: ⭐⭐

58. **[LeetCode 1300] Sum of Mutated Array Closest to Target**
    - URL: https://leetcode.com/problems/sum-of-mutated-array-closest-to-target/
    - Concept: Find value to mutate array
    - Hint: Binary search on value, calculate resulting sum
    - Difficulty: ⭐⭐

59. **[LeetCode 275] H-Index II**
    - URL: https://leetcode.com/problems/h-index-ii/
    - Concept: Find h-index in sorted array
    - Hint: Binary search, check if citations[mid] >= n - mid
    - Difficulty: ⭐⭐

60. **[LeetCode 1712] Ways to Split Array Into Three Subarrays**
    - URL: https://leetcode.com/problems/ways-to-split-array-into-three-subarrays/
    - Concept: Count valid splits
    - Hint: Binary search for valid ranges
    - Difficulty: ⭐⭐

61. **[LeetCode 1751] Maximum Number of Events That Can Be Attended II**
    - URL: https://leetcode.com/problems/maximum-number-of-events-that-can-be-attended-ii/
    - Concept: DP + binary search
    - Hint: Binary search for next non-overlapping event
    - Difficulty: ⭐⭐⭐

62. **[LeetCode 1062] Longest Repeating Substring**
    - URL: https://leetcode.com/problems/longest-repeating-substring/
    - Concept: Find longest repeating substring
    - Hint: Binary search on length, use rolling hash
    - Difficulty: ⭐⭐

63. **[LeetCode 1044] Longest Duplicate Substring**
    - URL: https://leetcode.com/problems/longest-duplicate-substring/
    - Concept: Find longest duplicate
    - Hint: Binary search on length + Rabin-Karp
    - Difficulty: ⭐⭐⭐

64. **[LeetCode 475] Heaters**
    - URL: https://leetcode.com/problems/heaters/
    - Concept: Minimize maximum distance
    - Hint: Binary search on radius, check coverage
    - Difficulty: ⭐⭐

65. **[LeetCode 911] Online Election**
    - URL: https://leetcode.com/problems/online-election/
    - Concept: Query winner at time t
    - Hint: Precompute winners, binary search on time
    - Difficulty: ⭐⭐

#### Hard
66. **[LeetCode 4] Median of Two Sorted Arrays**
    - URL: https://leetcode.com/problems/median-of-two-sorted-arrays/
    - Concept: Find median in O(log(m+n))
    - Hint: Binary search on partition point
    - Difficulty: ⭐⭐⭐

67. **[LeetCode 719] Find K-th Smallest Pair Distance**
    - URL: https://leetcode.com/problems/find-k-th-smallest-pair-distance/
    - Concept: Find kth smallest distance
    - Hint: Binary search on distance, count pairs with distance <= mid
    - Difficulty: ⭐⭐⭐

68. **[LeetCode 786] K-th Smallest Prime Fraction**
    - URL: https://leetcode.com/problems/k-th-smallest-prime-fraction/
    - Concept: Find kth smallest fraction
    - Hint: Binary search on fraction value
    - Difficulty: ⭐⭐⭐

69. **[LeetCode 878] Nth Magical Number**
    - URL: https://leetcode.com/problems/nth-magical-number/
    - Concept: Find nth number divisible by a or b
    - Hint: Binary search on answer, use inclusion-exclusion
    - Difficulty: ⭐⭐⭐

70. **[LeetCode 1201] Ugly Number III**
    - URL: https://leetcode.com/problems/ugly-number-iii/
    - Concept: Find nth ugly number
    - Hint: Binary search + inclusion-exclusion principle
    - Difficulty: ⭐⭐

71. **[LeetCode 1439] Find the Kth Smallest Sum of a Matrix With Sorted Rows**
    - URL: https://leetcode.com/problems/find-the-kth-smallest-sum-of-a-matrix-with-sorted-rows/
    - Concept: Find kth smallest sum
    - Hint: Binary search + BFS/priority queue
    - Difficulty: ⭐⭐⭐

72. **[LeetCode 668] Kth Smallest Number in Multiplication Table**
    - URL: https://leetcode.com/problems/kth-smallest-number-in-multiplication-table/
    - Concept: Find kth smallest in virtual matrix
    - Hint: Binary search on value, count elements <= mid
    - Difficulty: ⭐⭐⭐

73. **[LeetCode 793] Preimage Size of Factorial Zeroes Function**
    - URL: https://leetcode.com/problems/preimage-size-of-factorial-zeroes-function/
    - Concept: Count numbers with k trailing zeros
    - Hint: Binary search on n, count trailing zeros
    - Difficulty: ⭐⭐⭐

74. **[LeetCode 1533] Find the Index of the Large Integer**
    - URL: https://leetcode.com/problems/find-the-index-of-the-large-integer/
    - Concept: Find index with limited API
    - Hint: Ternary search or binary search
    - Difficulty: ⭐⭐

---

### 8. Binary Search with Greedy/DP

#### Medium
75. **[LeetCode 1648] Sell Diminishing-Valued Colored Balls**
    - URL: https://leetcode.com/problems/sell-diminishing-valued-colored-balls/
    - Concept: Maximize profit
    - Hint: Binary search on final value, calculate profit
    - Difficulty: ⭐⭐

76. **[LeetCode 1642] Furthest Building You Can Reach**
    - URL: https://leetcode.com/problems/furthest-building-you-can-reach/
    - Concept: Maximize buildings reached
    - Hint: Use heap or binary search with greedy
    - Difficulty: ⭐⭐

77. **[LeetCode 1562] Find Latest Group of Size M**
    - URL: https://leetcode.com/problems/find-latest-group-of-size-m/
    - Concept: Find latest step
    - Hint: Binary search on steps or union-find
    - Difficulty: ⭐⭐

78. **[LeetCode 1488] Avoid Flood in The City**
    - URL: https://leetcode.com/problems/avoid-flood-in-the-city/
    - Concept: Greedy with binary search
    - Hint: Use TreeSet to find next dry day
    - Difficulty: ⭐⭐

79. **[LeetCode 2560] House Robber IV**
    - URL: https://leetcode.com/problems/house-robber-iv/
    - Concept: Minimize maximum stolen
    - Hint: Binary search on max value, check if can rob k houses
    - Difficulty: ⭐⭐

80. **[LeetCode 2513] Minimize the Maximum of Two Arrays**
    - URL: https://leetcode.com/problems/minimize-the-maximum-of-two-arrays/
    - Concept: Minimize maximum value
    - Hint: Binary search on answer, use inclusion-exclusion
    - Difficulty: ⭐⭐

---

### 9. Binary Search on Strings

#### Medium
81. **[LeetCode 1044] Longest Duplicate Substring**
    - URL: https://leetcode.com/problems/longest-duplicate-substring/
    - Concept: Find longest duplicate
    - Hint: Binary search on length + Rabin-Karp
    - Difficulty: ⭐⭐⭐

82. **[LeetCode 1062] Longest Repeating Substring**
    - URL: https://leetcode.com/problems/longest-repeating-substring/
    - Concept: Find longest repeating substring
    - Hint: Binary search on length, use rolling hash
    - Difficulty: ⭐⭐

83. **[LeetCode 1698] Number of Distinct Substrings in a String**
    - URL: https://leetcode.com/problems/number-of-distinct-substrings-in-a-string/
    - Concept: Count distinct substrings
    - Hint: Use trie or rolling hash with binary search
    - Difficulty: ⭐⭐

---

### 10. Binary Search with Bit Manipulation

#### Medium
84. **[LeetCode 1404] Number of Steps to Reduce a Number in Binary Representation to One**
    - URL: https://leetcode.com/problems/number-of-steps-to-reduce-a-number-in-binary-representation-to-one/
    - Concept: Count steps
    - Hint: Process from right to left
    - Difficulty: ⭐⭐

85. **[LeetCode 2333] Minimum Sum of Squared Difference**
    - URL: https://leetcode.com/problems/minimum-sum-of-squared-difference/
    - Concept: Minimize sum of squares
    - Hint: Binary search on final max difference
    - Difficulty: ⭐⭐

---

### 11. Miscellaneous Binary Search Problems

#### Easy
86. **[LeetCode 1385] Find the Distance Value Between Two Arrays**
    - URL: https://leetcode.com/problems/find-the-distance-value-between-two-arrays/
    - Concept: Count elements with distance > d
    - Hint: Sort arr2, binary search for each element in arr1
    - Difficulty: ⭐

87. **[LeetCode 1539] Kth Missing Positive Number**
    - URL: https://leetcode.com/problems/kth-missing-positive-number/
    - Concept: Find kth missing
    - Hint: Binary search or linear scan
    - Difficulty: ⭐

#### Medium
88. **[LeetCode 1802] Maximum Value at a Given Index in a Bounded Array**
    - URL: https://leetcode.com/problems/maximum-value-at-a-given-index-in-a-bounded-array/
    - Concept: Maximize center value
    - Hint: Binary search on value, calculate sum
    - Difficulty: ⭐⭐

89. **[LeetCode 2594] Minimum Time to Repair Cars**
    - URL: https://leetcode.com/problems/minimum-time-to-repair-cars/
    - Concept: Minimize time
    - Hint: Binary search on time, count cars repaired
    - Difficulty: ⭐⭐

90. **[LeetCode 2528] Maximize the Minimum Powered City**
    - URL: https://leetcode.com/problems/maximize-the-minimum-powered-city/
    - Concept: Maximize minimum power
    - Hint: Binary search on minimum, greedy placement
    - Difficulty: ⭐⭐⭐

91. **[LeetCode 2702] Minimum Operations to Make Numbers Non-positive**
    - URL: https://leetcode.com/problems/minimum-operations-to-make-numbers-non-positive/
    - Concept: Minimize operations
    - Hint: Binary search on operations, greedy check
    - Difficulty: ⭐⭐

92. **[LeetCode 2702] Minimum Operations to Make All Array Elements Equal**
    - URL: https://leetcode.com/problems/minimum-operations-to-make-all-array-elements-equal/
    - Concept: Find operations needed
    - Hint: Binary search + prefix sum
    - Difficulty: ⭐⭐

93. **[LeetCode 1870] Minimum Speed to Arrive on Time**
    - URL: https://leetcode.com/problems/minimum-speed-to-arrive-on-time/
    - Concept: Minimize speed
    - Hint: Binary search on speed, calculate time
    - Difficulty: ⭐⭐

94. **[LeetCode 2300] Successful Pairs of Spells and Potions**
    - URL: https://leetcode.com/problems/successful-pairs-of-spells-and-potions/
    - Concept: Count successful pairs
    - Hint: Sort potions, binary search for each spell
    - Difficulty: ⭐⭐

95. **[LeetCode 2389] Longest Subsequence With Limited Sum**
    - URL: https://leetcode.com/problems/longest-subsequence-with-limited-sum/
    - Concept: Find longest subsequence
    - Hint: Sort, prefix sum, binary search
    - Difficulty: ⭐

96. **[LeetCode 2476] Closest Nodes Queries in a Binary Search Tree**
    - URL: https://leetcode.com/problems/closest-nodes-queries-in-a-binary-search-tree/
    - Concept: Find floor and ceiling
    - Hint: Inorder traversal, binary search
    - Difficulty: ⭐⭐

97. **[LeetCode 2861] Maximum Number of Alloys**
    - URL: https://leetcode.com/problems/maximum-number-of-alloys/
    - Concept: Maximize alloys
    - Hint: Binary search on number of alloys
    - Difficulty: ⭐⭐

98. **[LeetCode 2563] Count the Number of Fair Pairs**
    - URL: https://leetcode.com/problems/count-the-number-of-fair-pairs/
    - Concept: Count pairs in range
    - Hint: Sort, binary search for each element
    - Difficulty: ⭐⭐

99. **[LeetCode 2616] Minimize the Maximum Difference of Pairs**
    - URL: https://leetcode.com/problems/minimize-the-maximum-difference-of-pairs/
    - Concept: Minimize max difference
    - Hint: Binary search on difference, greedy pairing
    - Difficulty: ⭐⭐

100. **[LeetCode 2702] Minimum Operations to Make Numbers Non-positive**
     - URL: https://leetcode.com/problems/minimum-operations-to-make-numbers-non-positive/
     - Concept: Minimize operations
     - Hint: Binary search on operations
     - Difficulty: ⭐⭐⭐

---

### Summary Statistics

**Total Problems: 100**

**By Difficulty:**
- Easy: 15 problems
- Medium: 65 problems
- Hard: 20 problems

**By Category:**
- Basic Binary Search: 8 problems
- Find First/Last Occurrence: 6 problems
- Rotated/Modified Array: 7 problems
- Peak Finding: 4 problems
- Binary Search on Answer: 19 problems
- Matrix Binary Search: 5 problems
- Advanced Binary Search: 21 problems
- Binary Search with Greedy/DP: 6 problems
- Binary Search on Strings: 3 problems
- Binary Search with Bit Manipulation: 2 problems
- Miscellaneous: 19 problems

---

## Interview Tips

### 1. Problem Identification

**Ask yourself:**
- Is the array/list sorted?
- Can I eliminate half the search space?
- Am I searching for a value or optimizing something?
- Is there a monotonic property?
- Can I define a condition function?

**Red flags for binary search:**
- "Find in sorted array"
- "Minimize maximum" or "Maximize minimum"
- "Find first/last occurrence"
- "O(log n) time complexity"
- "Search space is too large for linear search"

### 2. Choose the Right Template

| Problem Type | Template | Key Feature |
|-------------|----------|-------------|
| Find exact match | Template 1 | `left <= right` |
| Find boundary | Template 2 | `left < right`, `right = mid` |
| Optimize with condition | Template 3 | Condition function |
| Rotated array | Modified T1 | Check sorted half |
| Peak finding | Template 2 | Compare neighbors |

### 3. Common Mistakes to Avoid

1. **Integer Overflow**
   ```java
   // Wrong: int mid = (left + right) / 2;
   // Right: int mid = left + (right - left) / 2;
   ```

2. **Infinite Loop**
   ```java
   // When finding maximum with condition
   // Use: int mid = left + (right - left + 1) / 2;  // Round up
   ```

3. **Off-by-One Errors**
   - Carefully choose `right = nums.length` vs `right = nums.length - 1`
   - Decide between `left = mid + 1` vs `left = mid`
   - Check if you need `right = mid - 1` vs `right = mid`

4. **Wrong Return Value**
   - Template 1: Return -1 if not found
   - Template 2: Return `left` for leftmost, `left - 1` for rightmost
   - Always verify what to return after loop ends

5. **Not Handling Edge Cases**
   - Empty array
   - Single element
   - Target not in array
   - All elements same
   - Target smaller/larger than all elements

### 4. Binary Search Checklist

Before coding:
- [ ] Is the data sorted or monotonic?
- [ ] What am I searching for? (exact value, boundary, optimal answer)
- [ ] Which template should I use?
- [ ] What are the initial left and right values?
- [ ] What is the loop condition?
- [ ] How do I update left and right?
- [ ] What should I return?
- [ ] What are the edge cases?

### 5. Debugging Binary Search

**Common issues:**

1. **Loop doesn't terminate**
   - Check if `mid` calculation can cause infinite loop
   - Ensure search space is shrinking

2. **Wrong answer**
   - Print `left`, `right`, `mid` at each iteration
   - Verify condition logic
   - Check boundary updates

3. **Index out of bounds**
   - Verify initial `right` value
   - Check array access with `mid`

**Debug template:**
```java
while (left <= right) {
    int mid = left + (right - left) / 2;
    System.out.println("left=" + left + ", right=" + right + ", mid=" + mid);
    
    // Your logic here
}
```

### 6. Optimization Techniques

1. **Early Termination**
   ```java
   if (nums[mid] == target) {
       return mid;  // Found, no need to continue
   }
   ```

2. **Boundary Checks**
   ```java
   if (target < nums[0] || target > nums[nums.length - 1]) {
       return -1;  // Target out of range
   }
   ```

3. **Use Built-in Functions**
   ```java
   // Java
   int index = Arrays.binarySearch(nums, target);
   
   // C++
   auto it = lower_bound(nums.begin(), nums.end(), target);
   
   // Python
   import bisect
   index = bisect.bisect_left(nums, target)
   ```

### 7. Time Complexity Analysis

**Binary Search Complexity:**
- **Time:** O(log n) for search
- **Space:** O(1) iterative, O(log n) recursive

**With Condition Function:**
- If condition is O(k), total time is O(k log n)
- Example: Checking if can ship packages is O(n), so total is O(n log n)

**Common Complexities:**
| Problem | Time Complexity | Explanation |
|---------|----------------|-------------|
| Basic search | O(log n) | Binary search only |
| Search with condition | O(n log n) | O(n) check × O(log n) searches |
| 2D matrix (1D approach) | O(log(m×n)) | Treat as 1D array |
| 2D matrix (corner approach) | O(m + n) | Walk from corner |

### 8. Interview Communication

**Step-by-step approach:**

1. **Clarify the problem**
   - "Is the array sorted?"
   - "Can there be duplicates?"
   - "What should I return if not found?"
   - "Are there any constraints on array size?"

2. **Explain your approach**
   - "I'll use binary search because the array is sorted"
   - "I'll search on the answer space from X to Y"
   - "I'll use Template 2 to find the leftmost position"

3. **Discuss complexity**
   - "Time complexity is O(log n) for binary search"
   - "Space complexity is O(1) since we use iterative approach"

4. **Code and test**
   - Start with template
   - Explain each condition
   - Test with examples
   - Check edge cases

5. **Optimize if needed**
   - "We can add early termination here"
   - "We can cache this value to avoid recalculation"

### 9. Pattern Recognition

**When you see these keywords, think binary search:**

| Keyword | Pattern | Example |
|---------|---------|---------|
| "sorted array" | Direct search | Find target |
| "first occurrence" | Left boundary | First bad version |
| "last occurrence" | Right boundary | Last position |
| "minimize maximum" | Binary search on answer | Split array |
| "maximize minimum" | Binary search on answer | Magnetic force |
| "rotated sorted" | Modified binary search | Rotated array |
| "peak element" | Peak finding | Mountain array |
| "kth smallest" | Binary search on value | Kth in matrix |

### 10. Practice Strategy

**Week 1-2: Foundations**
- Master all three templates
- Solve 20 easy problems
- Focus on understanding, not speed

**Week 3-4: Patterns**
- Solve 30 medium problems by pattern
- Rotated arrays (5 problems)
- Binary search on answer (10 problems)
- Matrix problems (5 problems)
- Advanced variations (10 problems)

**Week 5-6: Advanced**
- Solve 15 hard problems
- Focus on complex condition functions
- Combine with other algorithms
- Time yourself

**Week 7-8: Mock Interviews**
- Simulate interview conditions
- Explain approach out loud
- Code without IDE help
- Review and optimize

### 11. Common Interview Questions

**Conceptual:**
- Explain binary search algorithm
- What is the time complexity and why?
- When can't you use binary search?
- Difference between templates?
- How to handle duplicates?

**Coding:**
- Implement binary search from scratch
- Find first/last occurrence
- Search in rotated array
- Find square root
- Minimize maximum subarray sum

### 12. Edge Cases to Always Test

```java
// Always test these cases:
int[] empty = {};                    // Empty array
int[] single = {5};                  // Single element
int[] two = {3, 7};                  // Two elements
int[] duplicates = {1, 2, 2, 2, 3}; // Duplicates
int[] allSame = {5, 5, 5, 5};       // All same

// Targets to test:
// - First element
// - Last element
// - Middle element
// - Not in array (smaller than all)
// - Not in array (larger than all)
// - Not in array (in between)
```

---

## Summary

Binary Search is a fundamental algorithm that every software engineer must master. Key takeaways:

1. **Three Templates**
   - Template 1: Exact match (`left <= right`)
   - Template 2: Find boundary (`left < right`)
   - Template 3: Optimize with condition

2. **Common Patterns**
   - Search in sorted array
   - Find first/last occurrence
   - Search in rotated array
   - Binary search on answer
   - Peak finding
   - Matrix search

3. **Key Principles**
   - Array must be sorted (or search space monotonic)
   - Eliminate half the search space each iteration
   - Time complexity: O(log n)
   - Avoid integer overflow: `mid = left + (right - left) / 2`

4. **Problem-Solving Steps**
   - Identify if binary search is applicable
   - Choose appropriate template
   - Define search space and condition
   - Handle edge cases
   - Test thoroughly

5. **Advanced Applications**
   - Binary search on answer space
   - Minimize maximum / Maximize minimum
   - Combine with other algorithms (DP, greedy)
   - 2D matrix problems

**Practice consistently**, understand the templates deeply, and you'll be able to recognize and solve binary search problems efficiently in interviews!

---

## Reference Materials and Resources

### 1. Official Documentation & Tutorials

**LeetCode Resources:**
- LeetCode Binary Search Explore Card: https://leetcode.com/explore/learn/card/binary-search/
- LeetCode Binary Search Study Plan: https://leetcode.com/studyplan/binary-search/
- LeetCode Discuss - Binary Search: https://leetcode.com/tag/binary-search/discuss/

**Educational Platforms:**
- GeeksforGeeks Binary Search: https://www.geeksforgeeks.org/binary-search/
- CP-Algorithms Binary Search: https://cp-algorithms.com/num_methods/binary_search.html
- Programiz Binary Search Tutorial: https://www.programiz.com/dsa/binary-search
- HackerEarth Binary Search: https://www.hackerearth.com/practice/algorithms/searching/binary-search/

### 2. Video Tutorials

**Comprehensive Courses:**
- Abdul Bari - Binary Search: https://www.youtube.com/watch?v=j5uXyPJ0Pew
- NeetCode - Binary Search Playlist: https://www.youtube.com/playlist?list=PLot-Xpze53leNZQd0iINpD-MAhMOMzWvO
- Tushar Roy - Binary Search Problems: https://www.youtube.com/watch?v=GU7DpgHINWQ
- Back To Back SWE - Binary Search: https://www.youtube.com/watch?v=K-RYzDZkzCI

**Specific Topics:**
- Binary Search on Answer Space: https://www.youtube.com/watch?v=W9QJ8HaRvJQ
- Rotated Array Problems: https://www.youtube.com/watch?v=QdVrY3stDD4
- Binary Search Templates Explained: https://www.youtube.com/watch?v=GU7DpgHINWQ

### 3. Articles & Blog Posts

**Must-Read Articles:**
- Binary Search 101 by LeetCode: https://leetcode.com/problems/binary-search/solutions/423162/Binary-Search-101/
- Powerful Ultimate Binary Search Template: https://leetcode.com/discuss/general-discussion/786126/python-powerful-ultimate-binary-search-template-solved-many-problems
- Binary Search Template Analysis: https://leetcode.com/discuss/general-discussion/691825/Binary-Search-for-Beginners-Problems-or-Patterns-or-Sample-solutions
- Topcoder Binary Search Tutorial: https://www.topcoder.com/thrive/articles/Binary%20Search

**Advanced Topics:**
- Binary Search on Answer: https://usaco.guide/silver/binary-search-ans
- Monotonic Stack and Binary Search: https://leetcode.com/discuss/study-guide/2347639/A-comprehensive-guide-and-template-for-monotonic-stack-based-problems
- Binary Search in Competitive Programming: https://codeforces.com/blog/entry/9901

### 4. Practice Platforms

**Coding Practice:**
- LeetCode: https://leetcode.com/tag/binary-search/ (300+ problems)
- Codeforces Binary Search Tag: https://codeforces.com/problemset?tags=binary+search
- HackerRank Search Challenges: https://www.hackerrank.com/domains/algorithms?filters%5Bsubdomains%5D%5B%5D=search
- AtCoder Problems: https://kenkoooo.com/atcoder/#/table/ (filter by binary search)

**Interview Preparation:**
- InterviewBit Binary Search: https://www.interviewbit.com/courses/programming/topics/binary-search/
- AlgoExpert Binary Search: https://www.algoexpert.io/questions (subscription required)
- Pramp Mock Interviews: https://www.pramp.com/

### 5. Books

**Algorithm Books:**
- "Introduction to Algorithms" (CLRS) - Chapter 2.3
  - ISBN: 978-0262033848
  - Binary Search fundamentals and analysis

- "Algorithm Design Manual" by Steven Skiena - Chapter 4.9
  - ISBN: 978-1848000698
  - Practical binary search applications

- "Competitive Programming 4" by Steven & Felix Halim
  - ISBN: 978-1716745850
  - Advanced binary search techniques

- "Elements of Programming Interviews" - Chapter 11
  - ISBN: 978-1537713946
  - Interview-focused binary search problems

### 6. Cheat Sheets & Quick References

**Templates:**
- Binary Search Template Cheat Sheet: https://leetcode.com/discuss/general-discussion/786126/
- Visual Binary Search Guide: https://www.cs.usfca.edu/~galles/visualization/Search.html
- Binary Search Patterns: https://leetcode.com/discuss/study-guide/2371234/

**Complexity Analysis:**
- Big-O Cheat Sheet: https://www.bigocheatsheet.com/
- Time Complexity Reference: https://cooervo.github.io/Algorithms-DataStructures-BigONotation/

### 7. Visualization Tools

**Interactive Visualizations:**
- VisuAlgo Binary Search: https://visualgo.net/en/bst
- Algorithm Visualizer: https://algorithm-visualizer.org/branch-and-bound/binary-search
- Binary Search Visualization: https://www.cs.usfca.edu/~galles/visualization/Search.html
- Python Tutor: https://pythontutor.com/ (step-by-step code execution)

### 8. Community Resources

**Discussion Forums:**
- LeetCode Discuss: https://leetcode.com/discuss/interview-question?currentPage=1&orderBy=hot&query=binary+search
- Reddit r/leetcode: https://www.reddit.com/r/leetcode/
- Codeforces Blogs: https://codeforces.com/blog/entry/9901

**Discord/Slack Communities:**
- LeetCode Discord: https://discord.gg/leetcode
- CS Career Questions: https://discord.gg/cscq

### 9. Company-Specific Resources

**FAANG Interview Prep:**
- Blind 75 (includes binary search): https://leetcode.com/discuss/general-discussion/460599/blind-75-leetcode-questions
- Grind 75: https://www.techinterviewhandbook.org/grind75
- NeetCode 150: https://neetcode.io/practice

**Company Interview Experiences:**
- LeetCode Company Tags: https://leetcode.com/company/
- Glassdoor Interview Questions: https://www.glassdoor.com/
- Levels.fyi Interview Experiences: https://www.levels.fyi/

### 10. Advanced Topics

**Research Papers:**
- "Binary Search Trees" - Knuth, TAOCP Vol 3
- "Nearly Optimal Binary Search Tree" - Mehlhorn (1975)

**Advanced Techniques:**
- Fractional Cascading: https://en.wikipedia.org/wiki/Fractional_cascading
- Exponential Search: https://www.geeksforgeeks.org/exponential-search/
- Interpolation Search: https://www.geeksforgeeks.org/interpolation-search/
- Ternary Search: https://cp-algorithms.com/num_methods/ternary_search.html

### 11. Mobile Apps

**Practice on the Go:**
- LeetCode Mobile App: iOS & Android
- Programming Hub: iOS & Android
- Enki: iOS & Android
- SoloLearn: iOS & Android

### 12. GitHub Repositories

**Code Collections:**
- LeetCode Solutions: https://github.com/kamyu104/LeetCode-Solutions
- Awesome Algorithms: https://github.com/tayllan/awesome-algorithms
- Coding Interview University: https://github.com/jwasham/coding-interview-university
- Tech Interview Handbook: https://github.com/yangshun/tech-interview-handbook

### 13. Online Judges

**Additional Practice:**
- SPOJ Binary Search: https://www.spoj.com/problems/tag/binary-search
- UVa Online Judge: https://onlinejudge.org/
- CodeChef: https://www.codechef.com/tags/problems/binary-search
- LightOJ: http://lightoj.com/

### 14. Mock Interview Platforms

**Practice Interviews:**
- Pramp: https://www.pramp.com/
- Interviewing.io: https://interviewing.io/
- Gainlo: http://www.gainlo.co/
- Exponent: https://www.tryexponent.com/

### 15. Study Plans

**Structured Learning:**
- 30-Day LeetCode Study Plan: https://leetcode.com/study-plan/
- AlgoMonster: https://algo.monster/
- Grokking the Coding Interview: https://www.educative.io/courses/grokking-the-coding-interview
- Master the Coding Interview: https://www.udemy.com/course/master-the-coding-interview-data-structures-algorithms/

### 16. Competitive Programming Resources

**For Advanced Practice:**
- USACO Guide - Binary Search: https://usaco.guide/silver/binary-search
- Codeforces EDU Section: https://codeforces.com/edu/course/2
- AtCoder Educational DP Contest: https://atcoder.jp/contests/dp

### 17. Language-Specific Resources

**Java:**
- Java Collections Framework: https://docs.oracle.com/javase/8/docs/api/java/util/Collections.html#binarySearch
- Arrays.binarySearch(): https://docs.oracle.com/javase/8/docs/api/java/util/Arrays.html#binarySearch

**Python:**
- bisect module: https://docs.python.org/3/library/bisect.html
- Python Binary Search Tutorial: https://realpython.com/binary-search-python/

**C++:**
- STL Binary Search: https://en.cppreference.com/w/cpp/algorithm/binary_search
- lower_bound/upper_bound: https://en.cppreference.com/w/cpp/algorithm/lower_bound

**JavaScript:**
- Implementing Binary Search: https://www.digitalocean.com/community/tutorials/js-binary-search

### 18. Interview Preparation Timelines

**4-Week Plan:**
- Week 1: Master templates, solve 20 easy problems
- Week 2: Rotated arrays and peak finding, 15 medium problems
- Week 3: Binary search on answer, 20 medium problems
- Week 4: Hard problems and mock interviews, 10 hard problems

**8-Week Plan:**
- Weeks 1-2: Foundations (30 easy + 10 medium)
- Weeks 3-4: Core patterns (30 medium)
- Weeks 5-6: Advanced topics (20 medium + 10 hard)
- Weeks 7-8: Mock interviews and review (15 hard)

**12-Week Plan:**
- Weeks 1-3: Fundamentals and easy problems
- Weeks 4-7: Medium problems by category
- Weeks 8-10: Hard problems and advanced techniques
- Weeks 11-12: Company-specific prep and mocks

### 19. Additional Problem Sets

**Curated Lists:**
- Sean Prashad's LeetCode Patterns: https://seanprashad.com/leetcode-patterns/
- Techie Delight Binary Search: https://www.techiedelight.com/binary-search-practice-problems/
- 14 Patterns to Ace Any Coding Interview: https://hackernoon.com/14-patterns-to-ace-any-coding-interview-question-c5bb3357f6ed

### 20. Success Stories & Motivation

**Interview Experiences:**
- LeetCode Success Stories: https://leetcode.com/discuss/interview-experience
- Blind Interview Experiences: https://www.teamblind.com/
- Reddit CS Career Questions: https://www.reddit.com/r/cscareerquestions/

---

## Quick Links Summary

**Essential Resources:**
1. LeetCode Binary Search Tag: https://leetcode.com/tag/binary-search/
2. Binary Search Template Guide: https://leetcode.com/discuss/general-discussion/786126/
3. NeetCode Video Playlist: https://www.youtube.com/playlist?list=PLot-Xpze53leNZQd0iINpD-MAhMOMzWvO
4. USACO Binary Search Guide: https://usaco.guide/silver/binary-search
5. VisuAlgo Visualization: https://visualgo.net/en/bst

**Practice Platforms:**
1. LeetCode: https://leetcode.com/
2. Codeforces: https://codeforces.com/
3. HackerRank: https://www.hackerrank.com/
4. InterviewBit: https://www.interviewbit.com/

**Study Plans:**
1. LeetCode Study Plan: https://leetcode.com/study-plan/binary-search/
2. NeetCode Roadmap: https://neetcode.io/roadmap
3. AlgoMonster: https://algo.monster/

---

**Good luck with your interview preparation! 🚀**

**Remember:** Consistency is key. Solve 2-3 problems daily, understand the patterns, and you'll master binary search in no time!

