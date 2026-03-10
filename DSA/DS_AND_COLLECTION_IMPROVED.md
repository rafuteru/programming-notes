# Data Structures & Java Collections – Android Developer Study Notes

Verified concepts based on:

- Oracle Java Documentation
- OpenJDK source design
- Android Developer documentation

---

# 1. Data Structure

## Definition

A **data structure** is a way of organizing and storing data so that it can be accessed and modified efficiently.

## Purpose

- Efficient data storage
- Faster search
- Faster insert/delete
- Efficient memory usage

---

## Common Data Structures

| Structure | Description |
|--------|-------------|
| Array | Fixed-size sequential memory storage |
| Linked List | Nodes connected using pointers |
| Stack | Last-In-First-Out structure |
| Queue | First-In-First-Out structure |
| Hash Table | Key-value structure using hashing |
| Tree | Hierarchical data structure |
| Graph | Network of connected nodes |

---

# 2. Java Collections Framework (JCF)

## Definition

The **Java Collections Framework** is a set of interfaces, implementations, and algorithms used to store and manipulate groups of objects.

It provides:

- Standard interfaces
- Reusable data structures
- Utility algorithms

---

## Core Hierarchy

```
Iterable
   |
Collection
   |---- List
   |---- Set
   |---- Queue

Separate hierarchy
Map
```

Important point:

```
Map is NOT part of Collection
```

---

# 3. Collection vs Collections

## Collection

Interface representing a **group of objects**.

Example:

```java
Collection<Integer> numbers;
```

---

## Collections

Utility class containing **static helper methods**.

Examples:

```java
Collections.sort(list);
Collections.reverse(list);
Collections.shuffle(list);
Collections.min(list);
```

---

# 4. List Interface

## Definition

A **List** is an ordered collection that allows duplicate elements.

## Properties

- Maintains insertion order
- Allows duplicates
- Supports index-based access

---

## List Implementations

| Class | Description |
|------|-------------|
| ArrayList | Dynamic array |
| LinkedList | Doubly linked list |
| Vector | Legacy synchronized list |
| Stack | Legacy LIFO structure |

---

# 5. ArrayList

## Definition

`ArrayList` is a **resizable array implementation of List**.

Official description (Oracle):

> "Resizable-array implementation of the List interface."

---

# Internal Structure

ArrayList internally uses:

```java
Object[] elementData;
```

Diagram:

```
Index:   0   1   2   3   4
        -------------------
Array → | A | B | C | D | E |
        -------------------
```

---

# Default Capacity

```
new ArrayList<>();
```

Default capacity becomes:

```
10
```

(after first insertion)

---

# Growth Strategy

When array becomes full, ArrayList resizes.

Formula used in OpenJDK:

```
newCapacity = oldCapacity + (oldCapacity >> 1)
```

Meaning:

```
newCapacity = 1.5 × oldCapacity
```

Example growth:

```
10 → 15 → 22 → 33 → 49
```

Why 1.5?

Trade-off between:

- memory usage
- resize frequency

---

# Time Complexity

| Operation | Complexity |
|----------|-----------|
| get(index) | O(1) |
| set(index) | O(1) |
| add(element) | Amortized O(1) |
| add(index) | O(n) |
| remove(index) | O(n) |

---

# Android Usage

Common uses:

- RecyclerView data
- Adapter datasets
- ViewModel state
- Data caching

Example:

```java
List<User> users = new ArrayList<>();
```

---

# 6. LinkedList

## Definition

`LinkedList` is a **doubly linked list implementation** of List and Deque.

---

# Internal Structure

Node contains:

```
prev | element | next
```

Diagram:

```
Node1 <-> Node2 <-> Node3
```

Each node points to previous and next node.

---

# Time Complexity

| Operation | Complexity |
|----------|-----------|
| get(index) | O(n) |
| addFirst() | O(1) |
| addLast() | O(1) |
| remove(node) | O(1) |

---

# Advantages

- Fast insertions
- Fast deletions
- No resizing

---

# Disadvantages

- Slow random access
- Higher memory usage
- Poor CPU cache locality

---

# Android Reality

Rarely used for UI lists.

Reason:

```
RecyclerView needs fast index access
```

ArrayList performs better.

---

# 7. ArrayList vs LinkedList

| Feature | ArrayList | LinkedList |
|-------|-----------|-----------|
| Structure | Dynamic Array | Doubly Linked List |
| Access | O(1) | O(n) |
| Insert middle | O(n) | O(1) after traversal |
| Memory | Lower | Higher |
| Android usage | Very common | Rare |

---

# 8. Set Interface

## Definition

A **Set** is a collection that **does not allow duplicate elements**.

---

## Implementations

| Implementation | Property |
|--------------|----------|
| HashSet | No ordering |
| LinkedHashSet | Maintains insertion order |
| TreeSet | Sorted set |

---

# 9. HashSet

## Definition

HashSet is implemented using **HashMap internally**.

From Java documentation:

> "This class implements the Set interface, backed by a HashMap instance."

---

# Internal Concept

HashSet internally stores:

```
HashMap<E, Object>
```

Conceptually:

```
Element → Key
Dummy Object → Value
```

Example:

```
HashSet
   |
HashMap
 key = element
 value = dummy
```

---

# Time Complexity

| Operation | Complexity |
|----------|-----------|
| add | O(1) average |
| remove | O(1) average |
| contains | O(1) average |

---

# 10. Map Interface

## Definition

A **Map** stores key-value pairs.

Example:

```java
Map<String, User>
```

Structure:

```
key → value
```

---

# Map Implementations

| Implementation | Feature |
|--------------|--------|
| HashMap | Unordered |
| LinkedHashMap | Maintains insertion order |
| TreeMap | Sorted |
| ConcurrentHashMap | Thread-safe |

---

# 11. HashMap

## Definition

HashMap is a **hash-table-based implementation of Map**.

---

# Internal Structure (Java 8+)

```
Array (table)
     |
     |---- Bucket
            |
            |---- Node (Linked List)
            |---- TreeNode (Red Black Tree)
```

---

# Hashing Process

Steps:

1. key.hashCode()
2. hash spread function
3. calculate index

```
index = (n - 1) & hash
```

Where:

```
n = table size
```

---

# hashCode() vs equals()

Very important interview concept.

Rule:

```
If equals() is true
hashCode() must be same
```

Example:

```java
@Override
public int hashCode() {
    return id;
}

@Override
public boolean equals(Object obj) {
    return this.id == ((User)obj).id;
}
```

---

# Default Values

| Property | Value |
|--------|------|
| Default capacity | 16 |
| Load factor | 0.75 |

---

# Resize Condition

Resize happens when:

```
size > capacity × loadFactor
```

Example:

```
16 × 0.75 = 12
```

After inserting **13th element**, resize occurs.

---

# Collision Handling

If multiple keys map to same bucket:

Step 1

```
Linked List
```

Step 2 (Java 8 optimization)

```
Red-Black Tree
```

When:

```
bucket size ≥ 8
AND
table size ≥ 64
```

---

# Time Complexity

| Operation | Average |
|----------|---------|
| put | O(1) |
| get | O(1) |
| remove | O(1) |

Worst case:

```
O(log n)
```

---

# 12. HashMap vs HashSet

| Feature | HashMap | HashSet |
|-------|--------|--------|
| Data stored | Key + Value | Only element |
| Duplicates | Keys unique | Elements unique |
| Internal implementation | Hash table | HashMap internally |

---

# 13. Queue

## Definition

Queue is a **FIFO (First In First Out)** structure.

Example order:

```
First inserted → first removed
```

---

## Queue Operations

```
offer() → insert
poll()  → remove
peek()  → view head
```

---

# Queue Implementations

| Implementation | Notes |
|--------------|------|
| LinkedList | Common |
| ArrayDeque | Faster |
| PriorityQueue | Sorted by priority |

---

# 14. Stack

## Definition

Stack is a **LIFO (Last In First Out)** structure.

Example:

```
Last inserted → first removed
```

---

## Stack Operations

```
push()
pop()
peek()
```

---

# Modern Java Recommendation

Use **Deque instead of Stack**.

Example:

```java
Deque<Integer> stack = new ArrayDeque<>();
```

---

# 15. Android Optimized Collections

Android provides **memory-optimized alternatives**.

---

# SparseArray

Better than:

```
HashMap<Integer, Object>
```

Benefits:

- avoids Integer boxing
- lower memory usage
- faster lookup

Example:

```java
SparseArray<User> users = new SparseArray<>();
```

---

# ArrayMap

Optimized Map for Android.

Best for:

```
Small maps (<1000 elements)
```

---

# ArraySet

Memory-efficient alternative to HashSet.

Optimized for Android memory constraints.

---

# 16. Android Best Practices

Recommended:

| Use Case | Structure |
|--------|-----------|
| UI lists | ArrayList |
| Lookup tables | HashMap |
| Unique values | HashSet |
| Integer keys | SparseArray |
| Small maps | ArrayMap |
| Small sets | ArraySet |

---

Avoid:

```
Vector
Stack
LinkedList (for UI lists)
```

---

# Common Android Interview Questions

### Q1

Why is **ArrayList preferred over LinkedList** in Android?

Answer:

- faster random access
- better CPU cache locality
- better RecyclerView performance

---

### Q2

Why use **SparseArray instead of HashMap<Integer, Object>?**

Answer:

- avoids boxing
- lower memory usage
- optimized for Android

---

### Q3

What happens when **HashMap collisions occur?**

Answer:

```
Linked List → Red Black Tree (Java 8+)
```

---

# End of Notes