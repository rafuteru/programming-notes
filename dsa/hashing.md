# Hashing

Hashing is a technique used to **store and retrieve data quickly**.

It maps a **key** to an **index in an array** using a hash function.

Goal:

Fast lookup, insert, and delete.

Average time complexity:

```
O(1)
```

---

# Why Hashing is Used

Without hashing:

Search in array

```
O(n)
```

Example:

```
[4, 8, 2, 9, 5]
```

To find `9` we may need to check many elements.

With hashing:

```
O(1)
```

Direct access using index.

---

# Common Hashing Data Structures

Java examples:

```
HashMap
HashSet
Hashtable
```

Example:

```java
HashMap<String, Integer> map = new HashMap<>();
map.put("apple", 3);
map.put("banana", 5);
```

Access:

```java
map.get("apple");
```

Time complexity:

```
O(1)
```

---

# Hashing with Arrays

Sometimes instead of HashMap we use **arrays as hash tables**.

Example:

Counting characters in a string.

Alphabet size:

```
26
```

We create array:

```java
int[] freq = new int[26];
```

---

# Understanding `c - 'a'`

This is a very common interview trick.

Example:

```
'a' -> ASCII 97
'b' -> ASCII 98
'c' -> ASCII 99
```

If we subtract `'a'`:

```
'a' - 'a' = 0
'b' - 'a' = 1
'c' - 'a' = 2
'z' - 'a' = 25
```

So characters become **array indexes**.

---

# Example Code

```java
int[] freq = new int[26];

for(char c : s.toCharArray()){
    freq[c - 'a']++;
}
```

Explanation:

```
c - 'a' -> converts character to index
```

Example:

```
s = "abc"
```

Steps:

```
'a' -> index 0
'b' -> index 1
'c' -> index 2
```

Result:

```
freq = [1,1,1,0,0,0...]
```

---

# Example Problem — Valid Anagram

Two strings are anagrams if they contain the **same characters with same frequency**.

Example:

```
listen
silent
```

---

# Solution Using Frequency Array

```java
public boolean isAnagram(String s, String t) {

    if(s.length() != t.length())
        return false;

    int[] freq = new int[26];

    for(char c : s.toCharArray())
        freq[c - 'a']++;

    for(char c : t.toCharArray())
        freq[c - 'a']--;

    for(int f : freq)
        if(f != 0)
            return false;

    return true;
}
```

---

# Time and Space Complexity

Time complexity:

```
O(n)
```

We traverse string once.

Space complexity:

```
O(1)
```

Why?

Because array size is fixed:

```
26
```

It does not grow with input.

---

# Example Walkthrough

```
s = "eat"
t = "tea"
```

Step 1:

Process `eat`

```
e -> freq[4]++
a -> freq[0]++
t -> freq[19]++
```

Array:

```
[1,0,0,0,1,...,1]
```

Step 2:

Process `tea`

```
t -> freq[19]--
e -> freq[4]--
a -> freq[0]--
```

Final array:

```
all zeros
```

Strings are anagrams.

---

# What if String Contains Uppercase or Symbols?

Array size must increase.

Example:

```
ASCII characters
```

```
int[] freq = new int[128];
```

Or:

Use `HashMap`.

---

# HashMap Approach

```java
HashMap<Character, Integer> map = new HashMap<>();

for(char c : s.toCharArray()){
    map.put(c, map.getOrDefault(c, 0) + 1);
}
```

---

# When to Use Hashing

Hashing is used in many problems:

- Character frequency
- Two Sum
- Anagram detection
- Duplicate detection
- Counting elements
- Fast lookups

---

# Common Interview Questions

1. Two Sum
2. Valid Anagram
3. Group Anagrams
4. First Non-Repeating Character
5. Top K Frequent Elements

---

# Optimization Pattern

Brute Force:

```
Nested loops
O(n²)
```

Optimized:

```
HashMap
O(n)
```

Example:

Two Sum.

---

# Key Takeaways

- Hashing provides fast lookup.
- `c - 'a'` converts characters into indexes.
- Frequency arrays are faster than HashMap when size is fixed.
- HashMap is used when data range is large.