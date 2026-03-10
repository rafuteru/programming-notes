# Android / DSA Interview Notes
## Anagram, Two Sum, Subarray Sum, and Best Time to Buy/Sell Stock
### Interview Preparation Notes (Bolt-Oriented)

---

# 1. Why These Problems Matter in Interviews

These problems are not asked just to test whether you can write code.

Interviewers usually check whether you can:

- identify the core pattern quickly
- explain brute force vs optimized approach
- justify time and space complexity
- choose the right data structure
- discuss constraints and edge cases
- write clean, bug-free code
- explain trade-offs like readability vs performance

For companies like **Bolt**, even basic DSA questions often become deeper discussion points.

They may ask follow-ups like:

- Why is this approach optimal?
- Can you do it in one pass?
- What are the edge cases?
- Can this break with duplicates?
- What if input is huge?
- Can this be adapted to real product scenarios?

So your preparation should include:

1. problem understanding
2. multiple approaches
3. optimal solution
4. edge cases
5. interview explanation
6. product/system thinking follow-up

---

# 2. Valid Anagram

## Problem

Given two strings `s` and `t`, return `true` if `t` is an anagram of `s`, otherwise return `false`.

An anagram means both strings contain the same characters with the same frequencies, only the order differs.

Example:

```text
s = "anagram", t = "nagaram" → true
s = "rat", t = "car" → false
```

---

## Core Idea

If two strings are anagrams:

- they must have the same length
- each character count must match exactly

---

## Approach 1 — Sorting

### Idea

Sort both strings and compare them.

### Code

```java
public boolean isAnagramSorting(String s, String t) {
    if (s.length() != t.length()) return false;

    char[] a = s.toCharArray();
    char[] b = t.toCharArray();

    java.util.Arrays.sort(a);
    java.util.Arrays.sort(b);

    return java.util.Arrays.equals(a, b);
}
```

### Time Complexity

```text
O(n log n)
```

Sorting dominates.

### Space Complexity

```text
O(n)
```

Depends on sorting internals and char arrays.

### Interview Notes

Good first solution if you want to quickly communicate correctness, but not optimal.

---

## Approach 2 — Frequency Array (Optimal for lowercase English letters)

### Idea

Use an array of size 26.

- increment count for each char in `s`
- decrement count for each char in `t`
- if all counts end at zero, the strings are anagrams

### Code

```java
public boolean isAnagram(String s, String t) {
    if (s.length() != t.length()) {
        return false;
    }

    int[] freq = new int[26];

    for (char c : s.toCharArray()) {
        freq[c - 'a']++;
    }

    for (char c : t.toCharArray()) {
        freq[c - 'a']--;
    }

    for (int count : freq) {
        if (count != 0) {
            return false;
        }
    }

    return true;
}
```

---

## Step-by-Step Explanation

Take:

```text
s = "listen"
t = "silent"
```

### Step 1: Length check

Both lengths are 6, so continue.

### Step 2: Build frequency from `s`

```text
l -> +1
i -> +1
s -> +1
t -> +1
e -> +1
n -> +1
```

### Step 3: Subtract frequency using `t`

```text
s -> -1
i -> -1
l -> -1
e -> -1
n -> -1
t -> -1
```

Since every increment is matched by one decrement, final array becomes all zeros.

So result is:

```text
true
```

---

## Why `c - 'a'` Works

Example:

```text
'a' - 'a' = 0
'b' - 'a' = 1
'c' - 'a' = 2
...
'z' - 'a' = 25
```

That means each lowercase letter maps to one index in the array.

So:

```java
freq[c - 'a']++;
```

means:

- find the correct slot for that character
- increase its count

Example:

```text
c = 'd'
'd' - 'a' = 3
freq[3]++
```

---

## Time Complexity

```text
O(n)
```

We scan each string once.

## Space Complexity

```text
O(1)
```

The array size is always 26, so it is constant space.

---

## Important Interview Clarification

This `O(1)` space is correct only because:

- input is restricted to lowercase English letters
- array size is fixed at 26

If characters can be arbitrary Unicode, then you would typically use a `HashMap`, and space would no longer be constant.

---

## Approach 3 — HashMap (Generalized Character Set)

### Code

```java
public boolean isAnagramMap(String s, String t) {
    if (s.length() != t.length()) return false;

    java.util.Map<Character, Integer> map = new java.util.HashMap<>();

    for (char c : s.toCharArray()) {
        map.put(c, map.getOrDefault(c, 0) + 1);
    }

    for (char c : t.toCharArray()) {
        if (!map.containsKey(c)) return false;

        map.put(c, map.get(c) - 1);
        if (map.get(c) == 0) {
            map.remove(c);
        }
    }

    return map.isEmpty();
}
```

### Time Complexity

```text
O(n)
```

### Space Complexity

```text
O(k)
```

Where `k` is number of distinct characters.

---

## Bolt-Style Interview Follow-Ups

### Q1: Why prefer array over HashMap here?

Because array is:

- faster
- lower memory overhead
- simple indexing
- ideal when character range is fixed

### Q2: When would HashMap be better?

When input may contain:

- uppercase
- Unicode
- accented characters
- arbitrary symbols

### Q3: What if strings are very large?

Still linear time with the array approach, which is efficient.

### Q4: What if case-insensitive anagram is needed?

Normalize first:

```java
s = s.toLowerCase();
t = t.toLowerCase();
```

---

## Best Interview Answer

For lowercase English letters, the optimal approach is to use a frequency array of size 26. First check length equality. Then increment counts for the first string and decrement for the second. If all values are zero at the end, the strings are anagrams. This runs in `O(n)` time and `O(1)` space because the array size is constant.

---

# 3. Two Sum

## Problem

Given an integer array `nums` and an integer `target`, return indices of the two numbers such that they add up to `target`.

Example:

```text
nums = [2, 7, 11, 15], target = 9
Output: [0, 1]
```

---

## Approach 1 — Brute Force

### Idea

Try every pair.

### Code

```java
public int[] twoSumBruteForce(int[] nums, int target) {
    for (int i = 0; i < nums.length; i++) {
        for (int j = i + 1; j < nums.length; j++) {
            if (nums[i] + nums[j] == target) {
                return new int[]{i, j};
            }
        }
    }
    return new int[]{-1, -1};
}
```

### Time Complexity

```text
O(n^2)
```

### Space Complexity

```text
O(1)
```

---

## Approach 2 — HashMap (Optimal)

### Idea

For every element `x`, look for `target - x`.

If that complement was seen before, return indices immediately.

### Code

```java
public int[] twoSum(int[] nums, int target) {
    java.util.Map<Integer, Integer> map = new java.util.HashMap<>();

    for (int i = 0; i < nums.length; i++) {
        int complement = target - nums[i];

        if (map.containsKey(complement)) {
            return new int[]{map.get(complement), i};
        }

        map.put(nums[i], i);
    }

    return new int[]{-1, -1};
}
```

---

## Step-by-Step Example

```text
nums = [2, 7, 11, 15], target = 9
```

### i = 0

```text
nums[0] = 2
complement = 7
map = {}
7 not found
store 2 -> 0
```

### i = 1

```text
nums[1] = 7
complement = 2
map contains 2
return [0, 1]
```

---

## Time Complexity

```text
O(n)
```

## Space Complexity

```text
O(n)
```

---

## Bolt-Style Interview Follow-Ups

### Q1: Why insert after checking?

Because you should not reuse the same element twice.

### Q2: What if array is sorted?

Then two-pointer may be used.

---

## Approach 3 — Two Pointer (Only for Sorted Array)

### Code

```java
public int[] twoSumSorted(int[] nums, int target) {
    int left = 0;
    int right = nums.length - 1;

    while (left < right) {
        int sum = nums[left] + nums[right];

        if (sum == target) {
            return new int[]{left, right};
        } else if (sum < target) {
            left++;
        } else {
            right--;
        }
    }

    return new int[]{-1, -1};
}
```

### Time Complexity

```text
O(n)
```

### Space Complexity

```text
O(1)
```

---

## Best Interview Answer

The optimal solution uses a HashMap. As we iterate through the array, for each number we compute its complement `target - nums[i]`. If that complement is already present in the map, we return the stored index and current index. Otherwise, we store the current value and index. This gives `O(n)` time and `O(n)` space.

---

# 4. Subarray Sum Equals K

## Problem

Given an integer array `nums` and an integer `k`, return the total number of continuous subarrays whose sum equals `k`.

Example:

```text
nums = [1, 1, 1], k = 2
Output: 2
```

Subarrays:

```text
[1,1] at indices [0..1]
[1,1] at indices [1..2]
```

---

## Why This Problem Is Important

This is a very common interview problem because it tests:

- prefix sum
- hashing
- continuous subarray logic
- handling negative numbers

It is much more meaningful than a simple array sum question.

---

## Approach 1 — Brute Force

### Idea

Start from every index and keep building sum.

### Code

```java
public int subarraySumBruteForce(int[] nums, int k) {
    int count = 0;

    for (int i = 0; i < nums.length; i++) {
        int sum = 0;

        for (int j = i; j < nums.length; j++) {
            sum += nums[j];

            if (sum == k) {
                count++;
            }
        }
    }

    return count;
}
```

### Time Complexity

```text
O(n^2)
```

### Space Complexity

```text
O(1)
```

---

## Approach 2 — Prefix Sum + HashMap (Optimal)

### Core Idea

If:

```text
currentPrefixSum - previousPrefixSum = k
```

then:

```text
previousPrefixSum = currentPrefixSum - k
```

So while scanning the array:

- maintain running prefix sum
- check how many times `prefixSum - k` has appeared before
- add that count to answer

### Code

```java
public int subarraySum(int[] nums, int k) {
    java.util.Map<Integer, Integer> prefixCount = new java.util.HashMap<>();
    prefixCount.put(0, 1);

    int sum = 0;
    int count = 0;

    for (int num : nums) {
        sum += num;

        if (prefixCount.containsKey(sum - k)) {
            count += prefixCount.get(sum - k);
        }

        prefixCount.put(sum, prefixCount.getOrDefault(sum, 0) + 1);
    }

    return count;
}
```

---

## Step-by-Step Example

```text
nums = [1, 1, 1], k = 2
```

Initial:

```text
prefixCount = {0:1}
sum = 0
count = 0
```

### First element = 1

```text
sum = 1
sum - k = -1
not found
store 1 -> 1
```

### Second element = 1

```text
sum = 2
sum - k = 0
found prefixCount[0] = 1
count = 1
store 2 -> 1
```

### Third element = 1

```text
sum = 3
sum - k = 1
found prefixCount[1] = 1
count = 2
store 3 -> 1
```

Final answer:

```text
2
```

---

## Time Complexity

```text
O(n)
```

## Space Complexity

```text
O(n)
```

---

## Important Interview Insight

This works even when the array contains negative numbers.

That is exactly why sliding window does **not** always work here.

---

## Bolt-Style Follow-Ups

### Q1: Why not use sliding window?

Because sliding window works reliably only when numbers are non-negative and the sum changes monotonically.

With negative numbers:

- expanding window may reduce sum
- shrinking window may increase sum

So sliding window breaks.

### Q2: Why initialize `prefixCount.put(0, 1)`?

To handle subarrays starting from index 0.

Example:

If current sum itself equals `k`, then `sum - k = 0`, and we need one prior zero prefix.

---

## Best Interview Answer

The optimal approach uses prefix sum with a HashMap. As we scan the array, we maintain a running sum. If `sum - k` has appeared before, then every occurrence of that prefix sum represents a valid subarray ending at the current index. This gives `O(n)` time and `O(n)` space, and works even with negative numbers.

---

# 5. Best Time to Buy and Sell Stock

## Problem

You are given an array where `prices[i]` is the stock price on day `i`.

You want to maximize profit by choosing:

- one day to buy
- a later day to sell

Return the maximum profit.

Example:

```text
prices = [7,1,5,3,6,4]
Output = 5
```

Buy at 1 and sell at 6.

---

## Approach 1 — Brute Force

### Idea

Try every buy day with every sell day after it.

### Code

```java
public int maxProfitBruteForce(int[] prices) {
    int maxProfit = 0;

    for (int i = 0; i < prices.length; i++) {
        for (int j = i + 1; j < prices.length; j++) {
            maxProfit = Math.max(maxProfit, prices[j] - prices[i]);
        }
    }

    return maxProfit;
}
```

### Time Complexity

```text
O(n^2)
```

### Space Complexity

```text
O(1)
```

---

## Approach 2 — Track Minimum Price So Far (Optimal)

### Core Idea

At every day:

- keep track of the lowest price seen so far
- calculate profit if selling today
- update maximum profit

### Code

```java
public int maxProfit(int[] prices) {
    int minPrice = Integer.MAX_VALUE;
    int maxProfit = 0;

    for (int price : prices) {
        if (price < minPrice) {
            minPrice = price;
        } else {
            maxProfit = Math.max(maxProfit, price - minPrice);
        }
    }

    return maxProfit;
}
```

---

## Step-by-Step Example

```text
prices = [7,1,5,3,6,4]
```

### Day 1: 7

```text
minPrice = 7
maxProfit = 0
```

### Day 2: 1

```text
minPrice = 1
maxProfit = 0
```

### Day 3: 5

```text
profit = 5 - 1 = 4
maxProfit = 4
```

### Day 4: 3

```text
profit = 3 - 1 = 2
maxProfit = 4
```

### Day 5: 6

```text
profit = 6 - 1 = 5
maxProfit = 5
```

### Day 6: 4

```text
profit = 4 - 1 = 3
maxProfit = 5
```

Final answer:

```text
5
```

---

## Time Complexity

```text
O(n)
```

## Space Complexity

```text
O(1)
```

---

## Bolt-Style Interview Insight

This problem tests whether you can convert a naive pair-comparison problem into a single-pass state-tracking solution.

This is a common engineering skill:

- track best state seen so far
- compute answer incrementally
- avoid recomputation

This pattern appears in product code too.

Example analogy for ride-hailing:

- keep the best cheapest route estimate so far
- update best opportunity as new data arrives

---

## Follow-Up Variants

### Variant 1: Multiple transactions allowed

That becomes a different problem.

### Variant 2: Cooldown period

Requires dynamic programming.

### Variant 3: Transaction fee

Also DP-based.

A strong interview answer mentions that current solution is only for:

```text
exactly one buy and one sell
```

---

## Best Interview Answer

The optimal approach is to scan the array once while tracking the minimum price seen so far. For each day, compute the profit if selling on that day, and update the maximum profit. This gives `O(n)` time and `O(1)` space.

---

# 6. Comparison Summary

| Problem | Brute Force | Optimal Approach | Time | Space |
|---|---|---|---|---|
| Valid Anagram | Sort or compare counts repeatedly | Frequency array / HashMap | O(n) | O(1) or O(k) |
| Two Sum | Check all pairs | HashMap | O(n) | O(n) |
| Subarray Sum = K | Check all subarrays | Prefix Sum + HashMap | O(n) | O(n) |
| Best Time to Buy/Sell Stock | Check all buy/sell pairs | Track minimum so far | O(n) | O(1) |

---

# 7. Common Interview Mistakes

## Valid Anagram

- forgetting length check
- assuming only lowercase without stating it
- not explaining why space is `O(1)`

## Two Sum

- inserting before checking complement incorrectly in some variants
- confusing sorted and unsorted versions
- returning values instead of indices

## Subarray Sum

- trying sliding window with negative numbers
- forgetting `prefixCount.put(0, 1)`
- confusing subarray with subsequence

## Best Time to Buy/Sell Stock

- allowing sell before buy
- missing case where no profit is possible
- overcomplicating with DP for single transaction

---

# 8. Bolt-Oriented Interview Preparation Notes

At Bolt-like interviews, even for these problems, discussion may extend into engineering judgment.

They may ask:

- Which solution would you choose in production?
- What are the hidden assumptions?
- What if input format changes?
- Can the same idea be generalized?
- How would you explain this to a junior engineer?
- How do you make this robust and readable?

Good answers usually include:

- brute force first
- optimized version second
- constraints awareness
- edge cases
- clean explanation

---

# 9. Short Interview-Ready Answers

## Valid Anagram

Use a frequency array if the input is lowercase English letters. Increment counts for the first string and decrement for the second. If all values are zero, the strings are anagrams. This is `O(n)` time and `O(1)` space.

## Two Sum

Use a HashMap to store previously seen values and their indices. For each number, compute the complement `target - current`. If the complement already exists in the map, return the pair of indices. This runs in `O(n)` time.

## Subarray Sum Equals K

Use prefix sum and a HashMap of prefix sum frequencies. If `sum - k` has appeared before, then those occurrences correspond to valid subarrays ending at the current index. This is `O(n)` time and works even with negative numbers.

## Best Time to Buy and Sell Stock

Track the minimum price seen so far while scanning the array. At each day, compute possible profit by selling today and update the maximum profit. This gives `O(n)` time and `O(1)` space.

---

# 10. Final Revision Sheet

## Patterns Used

- **Frequency counting** → Anagram
- **HashMap lookup** → Two Sum
- **Prefix sum + HashMap** → Subarray Sum
- **State tracking / running minimum** → Stock Profit

## What Interviewer Really Checks

- can you identify the pattern quickly
- can you explain why it works
- can you compare brute force vs optimal
- can you handle edge cases
- can you communicate cleanly under pressure

---