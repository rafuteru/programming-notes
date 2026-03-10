# Data Structures & Java Collections

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

## Common Data Structures

| Structure | Description |
|--------|-------------|
| Array | Fixed-size sequential memory storage |
| Linked List | Nodes connected with pointers |
| Stack | LIFO structure |
| Queue | FIFO structure |
| Hash Table | Key-value structure using hashing |
| Tree | Hierarchical structure |
| Graph | Network of connected nodes |

---

# 2. Java Collections Framework (JCF)

## Definition

The **Java Collections Framework** is a set of interfaces, implementations, and algorithms used to store and manipulate groups of objects.

It provides:

- Standard interfaces
- Reusable data structures
- Utility algorithms

## Core Interfaces

```
Iterable
   |
Collection
   |---- List
   |---- Set
   |---- Queue

Separate hierarchy:
Map
```

---

# 3. Collection vs Collections

## Collection

Interface representing a **group of objects**.

Example:

```java
Collection<Integer> numbers;
```

## Collections

Utility class providing **static helper methods** for collections.

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

## Implementations

| Class | Description |
|------|-------------|
| ArrayList | Dynamic array |
| LinkedList | Doubly linked list |
| Vector | Synchronized legacy class |
| Stack | Legacy LIFO structure |

---

# 5. ArrayList

## Definition

`ArrayList` is a **resizable array implementation of the List interface**.

Official description (Oracle):

> "Resizable-array implementation of the List interface."

---

## Internal Structure

Internally stores elements in:

```java
Object[] elementData
```

This is a **dynamic array**.

---

## Default Capacity

When created with:

```java
new ArrayList<>();
```

Default capacity becomes:

```
10
```

(after the first element insertion)

---

## Growth Strategy

When capacity is exceeded:

```
newCapacity = oldCapacity + (oldCapacity >> 1)
```

Which is approximately:

```
1.5 × oldCapacity
```

Example growth:

```
10 → 15 → 22 → 33 → 49
```

---

## Time Complexity

| Operation | Complexity |
|----------|-----------|
| get(index) | O(1) |
| set(index) | O(1) |
| add(element) | Amortized O(1) |
| add(index) | O(n) |
| remove(index) | O(n) |

---

## Advantages

- Fast random access
- Memory efficient
- Good for read-heavy workloads

---

## Android Use Case

Very common for UI lists.

Example:

```java
List<User> users = new ArrayList<>();
```

Used in:

- RecyclerView adapters
- Data caching
- ViewModels

---

# 6. LinkedList

## Definition

`LinkedList` is a **doubly linked list implementation** of `List` and `Deque`.

---

## Internal Node Structure

Each node contains:

```
prev | element | next
```

Structure example:

```
Node1 <-> Node2 <-> Node3
```

---

## Time Complexity

| Operation | Complexity |
|----------|-----------|
| get(index) | O(n) |
| addFirst() | O(1) |
| addLast() | O(1) |
| remove(node) | O(1) |

---

## Characteristics

### Advantages

- Fast insertion/removal
- No array resizing

### Disadvantages

- Slow random access
- More memory overhead (node objects)

---

## Android Reality

Rarely used for UI lists because:

`RecyclerView` requires **fast index access**, so **ArrayList performs better**.

---

# 7. ArrayList vs LinkedList

| Feature | ArrayList | LinkedList |
|-------|-----------|-----------|
| Structure | Dynamic Array | Doubly Linked List |
| Access time | O(1) | O(n) |
| Insert middle | O(n) | O(1) after traversal |
| Memory usage | Lower | Higher |
| Android usage | Very common | Rare |

---

# 8. Set Interface

## Definition

A **Set** is a collection that **does not allow duplicate elements**.

---

## Implementations

| Implementation | Property |
|--------------|----------|
| HashSet | No order |
| LinkedHashSet | Maintains insertion order |
| TreeSet | Sorted set |

---

# 9. HashSet

## Definition

`HashSet` is a **Set implementation backed by a HashMap**.

From Java docs:

> "This class implements the Set interface, backed by a HashMap instance."

---

## Internal Structure

Internally uses:

```java
HashMap<E, Object>
```

Each element becomes a **key in the HashMap**.

Conceptually:

```
value → stored as key
dummyValue → map value
```

---

## Time Complexity

| Operation | Complexity |
|----------|-----------|
| add | O(1) average |
| remove | O(1) average |
| contains | O(1) average |

---

# 10. Map Interface

## Definition

A **Map** stores **key-value pairs**.

Example:

```java
Map<String, User>
```

Structure:

```
key → value
```

---

## Map Implementations

| Implementation | Feature |
|--------------|--------|
| HashMap | Unordered |
| LinkedHashMap | Maintains insertion order |
| TreeMap | Sorted by key |
| ConcurrentHashMap | Thread-safe |

---

# 11. HashMap

## Definition

`HashMap` is a **hash-table-based implementation of the Map interface**.

---

## Internal Structure (Java 8+)

```
Array (table)
     |
     |---- Bucket
            |
            |---- Node (Linked List)
            |---- TreeNode (Red-Black Tree)
```

---

## Hashing Process

Steps:

1. `hashCode()` of key
2. Hash function spreads bits
3. Index calculated

```
index = (n - 1) & hash
```

Where:

```
n = table length
```

---

## Default Values

| Property | Value |
|--------|------|
| Default capacity | 16 |
| Load factor | 0.75 |

---

## Resize Condition

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

## Collision Handling

If multiple keys map to the same bucket:

1. Linked list initially
2. Converted to **Red-Black Tree** when:

```
bucket size ≥ 8
AND
table size ≥ 64
```

---

## Time Complexity

| Operation | Average |
|----------|---------|
| put | O(1) |
| get | O(1) |
| remove | O(1) |

Worst case:

```
O(log n)
```

(with tree buckets)

---

# 12. HashMap vs HashSet

| Feature | HashMap | HashSet |
|-------|--------|--------|
| Data stored | Key + Value | Only element |
| Duplicates | Keys unique | Elements unique |
| Internal implementation | Hash table | Uses HashMap internally |

---

# 13. Queue

## Definition

A **FIFO (First In First Out)** data structure.

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

## Implementations

| Implementation | Notes |
|--------------|------|
| LinkedList | Common |
| ArrayDeque | Faster than Stack |
| PriorityQueue | Sorted by priority |

---

# 14. Stack

## Definition

A **LIFO (Last In First Out)** structure.

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

## Modern Java Recommendation

Use **Deque instead of Stack**.

Example:

```java
Deque<Integer> stack = new ArrayDeque<>();
```

---

# 15. Android-Specific Collections

Android provides **memory-optimized alternatives** to standard Java collections.

---

## SparseArray

Recommended instead of:

```
HashMap<Integer, Object>
```

Reasons:

- Avoids Integer auto-boxing
- Saves memory
- Faster on Android

Example:

```java
SparseArray<User> users = new SparseArray<>();
```

---

## ArrayMap

Android optimized Map.

Best for:

```
Small maps (< 1000 items)
```

Uses **array-based storage internally**.

---

## ArraySet

Memory-efficient alternative to `HashSet`.

Optimized for Android memory constraints.

---

# 16. Key Android Guidelines

### Recommended usage

| Use Case | Structure |
|--------|-----------|
| UI lists | ArrayList |
| Lookup tables | HashMap |
| Unique collections | HashSet |
| Integer keys | SparseArray |
| Small maps | ArrayMap |
| Small sets | ArraySet |

---

### Avoid

- Vector
- Stack
- LinkedList (for UI lists)

---

# End of Notes