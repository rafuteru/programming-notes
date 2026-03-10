# Programming Notes System

This repository contains my programming knowledge and learning journey.

The goal of these notes is to:
- Organize technical knowledge
- Improve understanding of concepts
- Prepare for interviews
- Build a personal knowledge base

---

# Principles for Writing Notes

## 1. Keep Notes Simple

Notes should be easy to scan.

Avoid long paragraphs.

Good example:

```
HashMap

Purpose
Fast lookup of key-value pairs

Time Complexity
Insert -> O(1)
Search -> O(1)
Delete -> O(1)
```

Bad example:

```
HashMap is a data structure used in Java that stores key-value pairs and it is commonly used in many applications because it allows faster lookup...
```

---

# Note Structure

Each topic should follow this structure.

```
Concept
Definition
Why it is used
Example
Code
Edge Cases
Interview Questions
```

Example:

```
Binary Search

Definition
Search algorithm for sorted arrays.

Time Complexity
O(log n)

Steps
1. Find middle
2. Compare value
3. Search left or right
```

---

# Code Formatting

Always use code blocks.

Example:

```java
int[] freq = new int[26];

for(char c : s.toCharArray()){
    freq[c - 'a']++;
}
```

Inline code example:

Use `HashMap` to store frequencies.

---

# Folder Structure

Notes are organized by domain.

```
Programming-Notes
│
├── README.md
│
├── DSA
│   ├── Big-O.md
│   ├── Arrays.md
│   ├── Hashing.md
│
├── Android
│   ├── MVVM.md
│   ├── MVI.md
│   ├── Room.md
│
├── Backend
│   ├── NodeJS.md
│   ├── API-Design.md
│
├── Cloud
│   ├── AWS-Basics.md
│
└── Insights
    ├── 2026-03-10.md
```

---

# Naming Convention

Each file should represent **one concept only**.

Good:

```
Hashing.md
Binary-Search.md
MVVM.md
Room-Database.md
```

Bad:

```
Java-Concepts.md (contains many topics)
```

---

# Headers

Use Markdown headers to structure content.

```
# Title
## Section
### Subsection
```

Example:

```
# Hashing
## Definition
## Example
## Code
## Interview Questions
```

---

# Bullet Points

Use bullet points for readability.

Example:

```
Advantages of HashMap

- Fast lookup
- Flexible key types
- Widely used in real systems
```

---

# Learning Insights

Sometimes insights don't belong to a specific topic.

Create a dated file.

Example:

```
Insights/2026-03-10.md
```

Example content:

```
# Learning Insights

Today I learned:

- Hashing reduces lookup time to O(1)
- Array indexing works like hashing
- Character mapping: c - 'a'
```

---

# Best Practices

✔ One concept per file  
✔ Use bullet points  
✔ Always include examples  
✔ Always include time complexity for algorithms  
✔ Write interview questions when possible  

---

# Tools Used

Editor  
Visual Studio Code

Format  
Markdown (.md)

Storage  
GitHub repository

Recommended VS Code Extensions

- Markdown All in One
- Markdown Preview Enhanced
- Code Spell Checker

---

# Goal

This repository should grow into a **personal programming knowledge base** covering:

- Data Structures & Algorithms
- Android Development
- Backend Development
- System Design
- Cloud (AWS)