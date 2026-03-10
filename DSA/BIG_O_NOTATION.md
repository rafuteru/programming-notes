# Big O Notation

Big O Notation is used to measure the **time complexity** or **space complexity** of an algorithm as the input size increases.

It helps us understand **how fast or slow an algorithm grows** when the data becomes large.

Example question interviewers ask:

- What is the time complexity?
- Can we optimize this algorithm?
- Why is this solution better?

---

# Why Big O is Important

Big O helps to:

- Compare algorithms
- Predict performance for large input
- Choose efficient solutions
- Avoid slow programs

Example:

Searching in an array

```
Linear Search -> O(n)
Binary Search -> O(log n)
```

Binary search is faster for large datasets.

---

# Common Time Complexities

## O(1) — Constant Time

Execution time **does not depend on input size**.

Example:

```java
int first = arr[0];
```

Even if array size is 10 or 1,000,000, the operation takes the same time.

---

## O(log n) — Logarithmic Time

The algorithm **reduces the input size by half each step**.

Example:

Binary Search

```java
while(left <= right){
    int mid = (left + right) / 2;

    if(arr[mid] == target)
        return mid;
}
```

Every iteration halves the search space.

Example growth:

```
n = 8 -> 3 steps
n = 16 -> 4 steps
n = 32 -> 5 steps
```

---

## O(n) — Linear Time

Time grows **proportionally with input size**.

Example:

```java
for(int i = 0; i < n; i++){
    System.out.println(arr[i]);
}
```

If `n` doubles, the time also doubles.

---

## O(n log n)

Common in **efficient sorting algorithms**.

Examples:

- Merge Sort
- Quick Sort (average case)

Example pattern:

```
Divide problem -> log n
Process elements -> n
```

Total:

```
n * log n
```

---

## O(n²) — Quadratic Time

Usually happens with **nested loops**.

Example:

```java
for(int i = 0; i < n; i++){
    for(int j = 0; j < n; j++){
        System.out.println(i + " " + j);
    }
}
```

If `n = 1000`

Operations:

```
1000 * 1000 = 1,000,000
```

Very slow for large data.

---

# Visual Growth Comparison

```
n = 10

O(1)     -> 1
O(log n) -> 3
O(n)     -> 10
O(n log n) -> 30
O(n²) -> 100
```

As `n` grows, inefficient algorithms become extremely slow.

---

# Space Complexity

Big O can also measure **memory usage**.

Example:

```java
int[] arr = new int[n];
```

Space complexity:

```
O(n)
```

Example constant memory:

```java
int sum = 0;
```

Space complexity:

```
O(1)
```

---

# How to Calculate Time Complexity

## Rule 1 — Drop Constants

```
O(2n) -> O(n)
```

Example:

```java
for(int i = 0; i < n; i++)
for(int j = 0; j < n; j++)
```

Operations:

```
2n
```

Final complexity:

```
O(n)
```

---

## Rule 2 — Drop Smaller Terms

```
O(n² + n) -> O(n²)
```

Because `n²` dominates for large input.

---

# Example Interview Question

### Find maximum element

```java
int max = arr[0];

for(int i = 1; i < arr.length; i++){
    if(arr[i] > max){
        max = arr[i];
    }
}
```

Time complexity:

```
O(n)
```

Because every element must be checked once.

---

# Example Optimization

Bad approach:

Nested loops

```
O(n²)
```

Better approach:

Use HashMap

```
O(n)
```

This is a **common optimization pattern in interviews**.

---

# Common Interview Follow-Up Questions

1. What is the time complexity of this code?
2. Can you optimize this solution?
3. What is the space complexity?
4. What happens when input size becomes very large?
5. Why is `O(log n)` faster than `O(n)`?

---

# Real World Example

Search contact in phone.

Bad approach:

```
Check every contact
O(n)
```

Better approach:

```
Binary search on sorted contacts
O(log n)
```

---

# Key Takeaways

- Big O measures algorithm efficiency
- Focus on **worst-case complexity**
- Avoid nested loops when possible
- Use better data structures when needed

Efficiency ranking:

```
O(1)      Best
O(log n)
O(n)
O(n log n)
O(n²)
O(2ⁿ)     Worst
```

---

# Interview Tip

When solving problems always say:

1️⃣ Brute force solution  
2️⃣ Time complexity  
3️⃣ Optimized solution  

This is what interviewers expect.