# Two Pointer Technique

Two pointer is a technique where **two indexes move through an array or string**.

Usually:

```
left pointer
right pointer
```

This helps solve problems in **O(n)** instead of **O(n²)**.

---

# Why Two Pointers Are Useful

Without two pointers:

```
Nested loops
O(n²)
```

With two pointers:

```
Single pass
O(n)
```

---

# Example — Reverse Array

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

---

# Example — Two Sum (Sorted Array)

Problem:

Find two numbers whose sum equals target.

Example:

```
[2,7,11,15]
target = 9
```

Solution:

```java
int left = 0;
int right = arr.length - 1;

while(left < right){

    int sum = arr[left] + arr[right];

    if(sum == target)
        return true;

    if(sum < target)
        left++;
    else
        right--;
}
```

Time complexity:

```
O(n)
```

---

# Types of Two Pointer Patterns

## Opposite Direction

```
left -> start
right -> end
```

Used in:

- reverse array
- pair sum
- palindrome

---

## Same Direction

Both pointers move forward.

Used in:

- remove duplicates
- partitioning problems

Example:

```
slow pointer
fast pointer
```

---

# Example — Remove Duplicates

```java
int slow = 0;

for(int fast = 1; fast < arr.length; fast++){

    if(arr[fast] != arr[slow]){
        slow++;
        arr[slow] = arr[fast];
    }
}
```

---

# Common Interview Problems

Two pointer technique is used in:

- Two Sum (sorted)
- Container With Most Water
- Trapping Rain Water
- Valid Palindrome
- Remove Duplicates

---

# Key Idea

Two pointers reduce:

```
O(n²)
```

into

```
O(n)
```

by avoiding repeated work.