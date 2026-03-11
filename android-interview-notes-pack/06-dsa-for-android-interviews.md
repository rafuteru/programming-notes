# 06 — DSA for Android Interviews

## Why DSA still matters for Android interviews
Even if the role is Android-focused, many companies still use a coding round to test:
- problem solving
- complexity analysis
- clean code under pressure
- edge case handling
- communication

The most common Android-interview coding patterns are usually not advanced graph theory. They are often:
- arrays and strings
- hashing
- sliding window
- two pointers
- stack/queue
- intervals
- binary search basics
- recursion / tree traversal basics

---

## Complexity basics

### Time complexity
How runtime grows as input size grows.

### Space complexity
How extra memory usage grows as input size grows.

### Common values
- `O(1)` constant
- `O(log n)` logarithmic
- `O(n)` linear
- `O(n log n)` sorting-like
- `O(n^2)` nested loops

In interviews, always state:
1. brute force idea
2. optimized idea
3. time complexity
4. space complexity
5. key tradeoff

---

## 1. Valid Anagram

### Problem
Check whether two strings are anagrams.

### Best interview approach for lowercase English letters
Use frequency counting.

```kotlin
fun isAnagram(s: String, t: String): Boolean {
    if (s.length != t.length) return false
    val freq = IntArray(26)
    for (c in s) freq[c - 'a']++
    for (c in t) freq[c - 'a']--
    return freq.all { it == 0 }
}
```

### Why `c - 'a'` works
Characters are represented by numeric codes.
If `c` is `'c'`, then:
- `'c' - 'a' == 2`
So index 2 represents letter `c`.

### Complexity
- Time: `O(n)`
- Space: `O(1)` because the array size is fixed at 26

### Follow-up
If input is full Unicode or mixed characters, use a `HashMap<Char, Int>` instead of fixed 26-size array.

---

## 2. Two Sum

### Problem
Find two indices whose values add to target.

### Optimal approach
Use hash map from value to index.

```kotlin
fun twoSum(nums: IntArray, target: Int): IntArray {
    val seen = HashMap<Int, Int>()
    for (i in nums.indices) {
        val need = target - nums[i]
        if (seen.containsKey(need)) {
            return intArrayOf(seen[need]!!, i)
        }
        seen[nums[i]] = i
    }
    return intArrayOf()
}
```

### Complexity
- Time: `O(n)`
- Space: `O(n)`

### Interview explanation
We trade memory for speed. The hash map lets us check in constant average time whether the complement has already appeared.

---

## 3. Best Time to Buy and Sell Stock

### Problem
Find max profit from one buy and one sell.

### Optimal approach
Track the minimum price seen so far and best profit so far.

```kotlin
fun maxProfit(prices: IntArray): Int {
    var minPrice = Int.MAX_VALUE
    var best = 0
    for (price in prices) {
        if (price < minPrice) minPrice = price
        best = maxOf(best, price - minPrice)
    }
    return best
}
```

### Complexity
- Time: `O(n)`
- Space: `O(1)`

### Interview line
At each day, ask: if I sell today, what is the best profit using the cheapest price seen before today?

---

## 4. Maximum Subarray

### Problem
Find contiguous subarray with maximum sum.

### Optimal approach
Kadane’s algorithm.

```kotlin
fun maxSubArray(nums: IntArray): Int {
    var current = nums[0]
    var best = nums[0]
    for (i in 1 until nums.size) {
        current = maxOf(nums[i], current + nums[i])
        best = maxOf(best, current)
    }
    return best
}
```

### Complexity
- Time: `O(n)`
- Space: `O(1)`

### Interview intuition
At each position, either:
- start a new subarray here
- extend the previous one

---

## 5. Longest Substring Without Repeating Characters

### Pattern
Sliding window + hash set / map.

### Complexity target
- Time: `O(n)`

Interview point:
Be ready to explain left/right pointers and how duplicates are removed from the active window.

---

## 6. Merge Intervals

### Pattern
Sort by start, then merge greedily.

### Complexity
- Time: `O(n log n)` because sorting dominates

Interview point:
State clearly that without sorting, overlapping ranges are not easy to process in one pass.

---

## 7. Valid Parentheses

### Pattern
Stack

### Why stack?
Because the most recent unmatched opening bracket must be matched first.

---

## 8. Binary Search

### When to use it
When the search space is sorted or the answer space is monotonic.

### Common mistakes
- off-by-one errors
- overflow in midpoint in some languages
- not defining the invariant clearly

Kotlin safer midpoint:
```kotlin
val mid = left + (right - left) / 2
```

---

## How to explain solutions in interview-friendly way

### Good structure
1. clarify assumptions
2. state brute force
3. optimize
4. dry run with example
5. code cleanly
6. summarize complexity
7. mention edge cases

### Edge cases to mention
- empty input
- one-element input
- duplicates
- negative numbers
- overflow boundaries when relevant
- invalid input if contract is unclear

---

## Android-specific coding round advice
Even in DSA rounds, write code like a production engineer:
- meaningful variable names
- early returns where helpful
- avoid clever one-liners that reduce clarity
- explain complexity out loud
- test with small examples

## Revision checklist
- Can I explain why hashing helps in Two Sum?
- Can I explain why an anagram array of size 26 is `O(1)` space?
- Can I explain sliding window without hand-waving?
- Can I explain why sorting changes complexity to `O(n log n)`?
- Can I dry run my own code?

## Official / trustworthy references
- Kotlin language docs: https://kotlinlang.org/docs/home.html
- Kotlin collections overview: https://kotlinlang.org/docs/collections-overview.html
- Kotlin arrays: https://kotlinlang.org/docs/arrays.html

## Note
This DSA file is intentionally framework-agnostic. The interview repo lists DSA as a preparation bucket, but problem statements themselves are usually from standard interview patterns rather than Android-specific platform APIs.
