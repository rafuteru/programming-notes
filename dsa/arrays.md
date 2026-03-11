# Arrays

An array is a **linear data structure** that stores elements in **contiguous memory locations**.

Each element can be accessed using an **index**.

Example:

```
Index: 0  1  2  3  4
Value: 5  8  2  9  1
```

Access element:

```java
arr[2] // 2
```

---

# Time Complexity

| Operation | Complexity |
|-----------|------------|
Access | O(1)
Search | O(n)
Insert (end) | O(1)
Insert (middle) | O(n)
Delete | O(n)

---

# Why Arrays Are Important

Arrays are used in:

- Searching problems
- Sorting algorithms
- Sliding window problems
- Two pointer techniques
- Dynamic programming

---

# Example Problem — Find Maximum

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

---

# Example Problem — Reverse Array

Input:

```
[1,2,3,4]
```

Output:

```
[4,3,2,1]
```

Solution:

```java
int left = 0;
int right = arr.length - 1;

while(left < right){
    int temp = arr[left];
    arr[left] = arr[right];
    arr[right] = temp;

    left++;
    right--;
}
```

Time complexity:

```
O(n)
```

Space complexity:

```
O(1)
```

---

# Common Array Problems

Very common interview questions:

- Two Sum
- Best Time to Buy and Sell Stock
- Maximum Subarray
- Move Zeroes
- Product of Array Except Self

---

# Interview Tip

Whenever you see:

- contiguous elements
- subarrays
- sum of elements

Think about:

```
Sliding Window
Prefix Sum
Two Pointer
```

Arrays are the **foundation of many algorithms**.