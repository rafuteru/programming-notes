# Android Advanced System Internals – Interview Notes

---

# 1. Android Memory Management

## Overview
Android applications run on **Android Runtime (ART)**.

Memory management is handled through:

- Heap memory allocation
- Garbage Collection (GC)
- Object lifecycle management

Developers **do not manually free memory**, but must avoid:

- Memory leaks
- Excessive allocations
- OutOfMemoryError
- UI performance issues

### Goals

- Efficient memory usage
- Avoid crashes
- Prevent memory leaks
- Maintain smooth UI performance

---

# 2. Android Runtime (ART)

Android apps run inside the **Android Runtime**.

Responsibilities:

- Executes application bytecode
- Manages memory allocation
- Performs Garbage Collection
- Optimizes runtime performance

### Improvements over Dalvik

| Dalvik | ART |
|------|------|
| JIT compilation | AOT + JIT |
| Slower startup | Faster startup |
| Less efficient GC | Improved GC |
| Higher CPU usage | Better optimization |

---

# 3. Android Memory Areas

Android memory mainly consists of:

## Stack Memory

Stores:

- Method calls
- Local variables
- Primitive values

Characteristics:

- Managed automatically
- Very fast allocation
- Exists per thread

Example:

```
functionA()
   ↓
functionB()
   ↓
functionC()
```

Each function call creates a **stack frame**.

---

## Heap Memory

Stores:

- Objects
- Class instances
- Arrays

Characteristics:

- Shared across threads
- Managed by Garbage Collector

Example:

```java
User user = new User();
```

The **User object is stored in the heap**.

Stack contains a reference.

---

# 4. Object Allocation

Example:

```java
User user = new User();
```

Steps:

1. Memory allocated in heap
2. Reference stored in stack
3. Object becomes eligible for GC when no references remain

Example:

```
Stack: user → reference
Heap: User Object
```

---

# 5. Garbage Collection (GC)

Garbage Collection automatically frees memory of unused objects.

Object becomes eligible when:

- No references exist
- It is unreachable

Example:

```java
User user = new User();
user = null;
```

Now the object becomes **eligible for GC**.

---

# 6. Garbage Collection Algorithm

Android uses **Tracing Garbage Collection**.

Steps:

1. Identify GC roots
2. Traverse reachable objects
3. Mark reachable objects
4. Remove unreferenced objects

This process is called **Mark and Sweep Algorithm**.

Steps:

1. Mark reachable objects
2. Sweep unreachable objects
3. Reclaim memory

---

# 7. GC Roots

Garbage Collector starts scanning from **GC roots**.

Examples:

- Active threads
- Static variables
- Local variables
- JNI references

If an object is reachable from GC roots → **not collected**.

---

# 8. Common Causes of Memory Leaks

Memory leak occurs when an object is **no longer needed but still referenced**.

---

## Activity Context Leak

Bad example:

```java
static Context context;
```

Flow:

```
Activity destroyed
↓
Static reference exists
↓
GC cannot free Activity
```

Fix:

Use **Application Context**

```java
context.getApplicationContext()
```

---

## Handler Memory Leak

Problem:

Handler holds reference to Activity.

```
MessageQueue
↓
Handler
↓
Activity
```

Activity cannot be garbage collected.

Fix:

- Static Handler
- WeakReference
- Remove callbacks

Example:

```java
handler.removeCallbacksAndMessages(null);
```

---

## Inner Class Leak

Non-static inner classes hold implicit reference to outer class.

Example:

```java
class MainActivity {

    class WorkerThread extends Thread {

    }

}
```

Fix:

Use **static inner class**.

---

## Static References

Static variables live as long as the **application process**.

Example:

```java
static List<Activity> activities;
```

Activities stored here **will never be garbage collected**.

---

# 9. OutOfMemoryError (OOM)

Occurs when app tries to allocate memory but heap is full.

Common reasons:

- Large bitmap loading
- Memory leaks
- Large object allocations
- Too many objects created

Example:

```java
Bitmap bitmap = BitmapFactory.decodeFile("huge_image.png");
```

Large image → **OOM crash**.

---

# 10. Bitmap Memory Issues

Bitmaps consume large memory.

Example:

```
Image size: 4000 × 3000
Color format: ARGB_8888
Bytes per pixel: 4
```

Memory usage:

```
4000 × 3000 × 4
= 48 MB
```

### Solution

Use sampling:

```java
BitmapFactory.Options options = new BitmapFactory.Options();
options.inSampleSize = 4;
```

Libraries:

- Glide
- Picasso

---

# 11. Memory Monitoring Tools

## Android Studio Memory Profiler

Features:

- Heap dump
- Allocation tracking
- Leak detection

---

## LeakCanary

Detects:

- Activity leaks
- Fragment leaks
- View leaks

---

# 12. Best Practices

Avoid context leaks.

Bad:

```
static Context context
```

Good:

```
context.getApplicationContext()
```

Use WeakReference:

```
WeakReference<Activity>
```

Avoid large object allocations.

Use image libraries:

- Glide
- Picasso

Remove callbacks:

```java
handler.removeCallbacksAndMessages(null);
```

---

# 13. Interview Questions

### What is a memory leak?

A memory leak occurs when an object is **no longer needed but still referenced**, preventing garbage collection.

---

### What causes Activity leaks?

Common causes:

- Static references
- Handlers
- Inner classes
- Long-running threads

---

### How do you detect memory leaks?

Tools:

- Android Studio Memory Profiler
- LeakCanary

---

### Stack vs Heap

| Stack | Heap |
|------|------|
| Stores method calls | Stores objects |
| Thread specific | Shared across threads |
| Faster | Slower |
| Auto managed | GC managed |

---

# Android Garbage Collection Internals

## What is GC?

Garbage Collection reclaims memory used by **unreachable objects**.

Android Runtime manages:

- Memory allocation
- Garbage collection
- Object lifecycle

---

# Why GC is Needed

Without GC:

- Memory usage keeps increasing
- Eventually leads to **OutOfMemoryError**

GC ensures:

- Memory reuse
- Stable performance
- Reduced crashes

---

# How GC Works

### Step 1 – Identify GC Roots

Examples:

- Active threads
- Static fields
- Local variables
- JNI references

---

### Step 2 – Mark Phase

Objects reachable from GC roots are marked.

Example:

```
GC Root
↓
Object A
↓
Object B
```

A and B are reachable → not collected.

---

### Step 3 – Sweep Phase

Unreachable objects are removed.

Example:

```
Object C
(no references)
```

Object C becomes garbage.

---

# Stop-The-World Problem

During GC, application threads may pause.

```
App running
↓
GC triggered
↓
Threads paused
↓
GC finishes
↓
Threads resume
```

Frequent pauses cause **UI jank**.

---

# Generational Garbage Collection

Observation:

Most objects die young.

Memory divided into:

### Young Generation

- Stores new objects
- Frequent GC
- Fast collection

### Old Generation

- Stores long-lived objects
- Less frequent GC

---

# Types of GC

## Minor GC

- Collects young generation
- Fast
- Frequent

## Major GC

- Collects entire heap
- Slower
- Longer pause

## Concurrent GC

Runs alongside application threads.

Benefits:

- Reduced UI pauses
- Better responsiveness

---

# ANR (Application Not Responding)

ANR occurs when **main thread is blocked**.

### Timeout Limits

| Scenario | Timeout |
|------|------|
| Input event | 5 seconds |
| BroadcastReceiver | 10 seconds |
| Service start | 20 seconds |

---

### ANR Flow

```
User input
↓
Event sent to main thread
↓
Main thread blocked
↓
5 seconds pass
↓
ANR triggered
```

---

# Main Thread Architecture

Main thread runs:

```
Looper.loop()
   ↓
MessageQueue
   ↓
Process messages
```

Processes:

- UI updates
- Input events
- Lifecycle callbacks
- Broadcast receivers

If loop blocked → **ANR**.

---

# Common Causes of ANR

## Network on Main Thread

Bad example:

```java
HttpURLConnection conn = url.openConnection();
```

---

## Heavy Computation

Examples:

- Sorting large dataset
- Image processing
- JSON parsing

---

## Disk I/O

Examples:

- Reading large files
- Database queries

---

## Lock Contention

Main thread waiting for lock held by background thread.

---

# ANR Debugging Tools

- adb shell traces.txt
- Android Studio Profiler
- StrictMode

Example:

```java
StrictMode.setThreadPolicy(
 new StrictMode.ThreadPolicy.Builder()
 .detectAll()
 .penaltyLog()
 .build()
);
```

---

# Binder IPC Architecture

Android components communicate using **Binder IPC**.

Used between:

- Apps
- System services
- Framework components

Examples:

- LocationManager
- ActivityManager
- PackageManager

---

### Binder Flow

```
App (Client)
↓
Binder Proxy
↓
Binder Driver (Kernel)
↓
Binder Stub
↓
System Service
```

---

# AIDL

AIDL defines IPC interfaces.

Example:

```java
interface IRemoteService {
    int getData();
}
```

Android automatically generates:

- Proxy
- Stub

---

# Android App Startup

Three types of startup:

---

## Cold Start

App process not running.

Flow:

```
Process start
↓
Application.onCreate()
↓
Activity.onCreate()
↓
First frame drawn
```

Slowest startup.

---

## Warm Start

Process exists but Activity recreated.

Example:

User reopens app.

---

## Hot Start

App already in memory.

Activity resumed immediately.

Fastest startup.

---

# Startup Optimization

Reduce work in **Application.onCreate**.

Bad:

- Database initialization
- Network calls
- Heavy libraries

---

## Lazy Initialization

Initialize objects **only when needed**.

---

## Avoid Main Thread Blocking

Move work to:

- Background thread
- Coroutines

---

# RecyclerView Internals

RecyclerView efficiently displays large lists.

Key idea:

**Reuse views instead of creating new ones.**

---

### Components

```
RecyclerView
↓
LayoutManager
↓
Adapter
↓
ViewHolder
```

---

### View Recycling

Example:

```
List = 1000 items
Screen shows = 10 items
```

RecyclerView creates **~10 ViewHolders**.

Scrolling:

```
Top item leaves screen
↓
View reused for new item
```

Benefits:

- Reduced memory usage
- Faster scrolling

---

### ViewHolder Pattern

```java
class UserViewHolder extends RecyclerView.ViewHolder {

    TextView name;

    UserViewHolder(View view) {
        super(view);
        name = view.findViewById(R.id.name);
    }

}
```

Avoids repeated `findViewById`.

---

### RecyclerView Caches

RecyclerView uses multiple caches:

- Attached Scrap
- Cached Views
- Recycled View Pool

These reduce view inflation cost.

---

# Coroutines vs Threads vs Executors

## Threads

Basic Java concurrency.

Example:

```java
Thread thread = new Thread(() -> doWork());
thread.start();
```

Problems:

- Heavy
- Hard lifecycle management
- Manual synchronization

---

## Executors

Thread pool abstraction.

Example:

```java
ExecutorService executor =
Executors.newFixedThreadPool(4);

executor.execute(() -> doWork());
```

Advantages:

- Thread reuse
- Better task scheduling

---

## Kotlin Coroutines

Modern concurrency approach.

Example:

```kotlin
lifecycleScope.launch {

    val data = withContext(Dispatchers.IO) {
        api.getData()
    }

    updateUI(data)

}
```

Advantages:

- Lightweight
- Lifecycle aware
- Structured concurrency
- Cleaner async code

---

### Coroutine Dispatchers

| Dispatcher | Usage |
|------|------|
| Main | UI work |
| IO | Network / Disk |
| Default | CPU work |
| Unconfined | Special cases |

---

# Senior Interview Quick Answers

### Why does ANR occur?

ANR occurs when the **main thread is blocked** and cannot process input events within the timeout.

---

### Why does Android use Binder?

Binder provides **high-performance IPC communication** between apps and system services.

---

### How does RecyclerView improve performance?

RecyclerView **reuses ViewHolders instead of creating new views**, reducing memory usage and improving scrolling performance.

---

### Why prefer Coroutines?

Coroutines provide:

- Lightweight concurrency
- Lifecycle-aware execution
- Structured concurrency
- Cleaner asynchronous code