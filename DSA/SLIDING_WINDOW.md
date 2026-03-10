# Sliding Window

Sliding window is used when working with **subarrays or substrings**.

Instead of recalculating everything repeatedly, we **expand and shrink a window**.

---

# Idea

Window moves across the array.

Example:

```
[1,3,2,5,4]
```

Window size = 3

```
[1,3,2]
 [3,2,5]
  [2,5,4]
```

---

# Brute Force Approach

Check every subarray.

```
O(n²)
```

---

# Sliding Window Optimization

Reuse previous results.

```
O(n)
```

---

# Example — Maximum Sum Subarray (Size K)

Input:

```
arr = [2,1,5,1,3,2]
k = 3
```

---

# Solution

```java
int windowSum = 0;
int maxSum = 0;

for(int i = 0; i < k; i++){
    windowSum += arr[i];
}

maxSum = windowSum;

for(int i = k; i < arr.length; i++){

    windowSum += arr[i];
    windowSum -= arr[i-k];

    maxSum = Math.max(maxSum, windowSum);
}
```

---

# Time Complexity

```
O(n)
```

Instead of:

```
O(n²)
```

---

# Types of Sliding Window

## Fixed Window

Window size stays constant.

Example:

```
max sum of subarray size k
```

---

## Dynamic Window

Window expands and shrinks.

Used in:

- longest substring without repeating characters
- minimum window substring

---

# Example — Longest Substring Without Repeating Characters

Uses:

```
HashSet
+
Sliding window
```

Idea:

Expand window until duplicate appears.

Then shrink window.

---

# Common Interview Questions

Sliding window is used in:

- Maximum Sum Subarray
- Longest Substring Without Repeating Characters
- Minimum Window Substring
- Permutation in String
- Fruit Into Baskets

---

# Key Insight

Sliding window helps convert:

```
O(n²)
```

into

```
O(n)
```

for many subarray problems.