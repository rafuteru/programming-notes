# Senior Android Developer Study Guide
## Bolt Driver App Interview Preparation

Focus Areas:
- Performance
- Reliability
- Concurrency
- Android internals
- Scalable architecture

Target role: **Senior Android Engineer (Ride Hailing Platform)**

---

# 1. Data Structures & Collections (Android + JVM Internals)

Senior engineers must understand **internal implementation and performance trade-offs**, not just APIs.

---

# HashMap Internals

## Structure

```
HashMap
 ├── Array (table[])
 │     ├── Node (Linked List)
 │     └── TreeNode (Red-Black Tree)
```

### Key Concepts

| Concept | Description |
|------|-------------|
| Hashing | Converts key → index |
| Collision | Multiple keys map to same bucket |
| Load Factor | Default = 0.75 |
| Resize | Table doubles when threshold reached |
| Treeification | Linked list converts to Red-Black Tree when chain > 8 |

### Treeification Conditions

```
chain length >= 8
table size >= 64
```

### Time Complexity

| Operation | Complexity |
|----------|-----------|
| get() | O(1) average |
| put() | O(1) average |
| worst collision | O(log n) |

---

## Senior Concept

Rehash operation:

```
O(n)
```

All entries must be redistributed.

This can cause **UI frame drops if done on main thread**.

### Optimization

```
HashMap(expectedSize)
```

---

## Bolt Context

Driver app may maintain maps like:

```
driverId -> driverState
rideId -> rideStatus
```

Frequent updates from **GPS tracking** require:

- Pre-sized HashMaps
- Background processing
- Immutable state flows

---

## Edge Cases

| Problem | Result |
|-------|-------|
| Bad hashCode() | All keys collide |
| Concurrent modification | Data corruption |
| Large map resizing | CPU spike |

Solutions:

```
ConcurrentHashMap
Immutable data flow
```

---

# ArrayList Internals

## Structure

```
ArrayList
 └── Object[] elementData
```

### Memory Locality

```
[element0][element1][element2][element3]
```

CPU cache friendly.

Compared to:

```
LinkedList
Node -> Node -> Node
```

---

### Growth Strategy

```
newCapacity = oldCapacity + (oldCapacity >> 1)
≈ 1.5x growth
```

Example:

```
10 → 15 → 22 → 33
```

---

### Time Complexity

| Operation | Complexity |
|----------|-----------|
| get(index) | O(1) |
| add() | Amortized O(1) |
| insert middle | O(n) |
| remove | O(n) |

---

## Senior Concept

Resizing triggers:

```
System.arraycopy()
```

Large lists in UI thread → **frame drops**

---

## Bolt Context

Common lists:

```
Nearby drivers
Map markers
Ride events
```

Use:

```
ArrayList
```

Avoid:

```
LinkedList
```

---

## Edge Cases

Frequent list mutations cause:

```
RecyclerView layout thrashing
```

Solutions:

```
DiffUtil
Paging
Immutable lists
```

---

# SparseArray

Optimized for:

```
Int -> Object
```

Avoids **autoboxing overhead**.

---

## HashMap Problem

```
HashMap<Int, Object>
```

Causes:

```
int -> Integer boxing
Extra allocations
More GC
```

---

## SparseArray Implementation

```
int[] keys
Object[] values
```

Keys are **sorted**.

Lookup uses:

```
Binary Search
```

---

### Time Complexity

| Operation | Complexity |
|---------|-----------|
| get() | O(log n) |
| put() | O(n) |
| memory | very efficient |

---

## Senior Concept

Tradeoff:

```
Slightly slower lookup
Much lower memory
```

---

## Bolt Context

Useful for:

```
markerId(int) -> MapMarker
driverId(int) -> DriverObject
```

---

## Edge Cases

Frequent insertions:

```
O(n) array shifts
```

Avoid for large dynamic datasets.

---

# ArrayMap

Used inside Android framework.

Example:

```
Bundle
View tags
Intent extras
```

---

## Structure

```
int[] hashes
Object[] keyValues
```

Layout:

```
[key0,value0,key1,value1]
```

---

## Performance

| Feature | ArrayMap | HashMap |
|------|------|------|
| Memory | Very low | Higher |
| Lookup | O(log n) | O(1) |
| Insert | O(n) | O(1) |

---

## Bolt Context

Use for:

```
Small configuration maps
UI metadata
```

Not suitable for:

```
large caches
```

---

# SparseArray vs HashMap vs ArrayMap

| Feature | HashMap | SparseArray | ArrayMap |
|------|------|------|------|
| Key type | Any | Int | Any |
| Lookup | O(1) | O(log n) | O(log n) |
| Memory | High | Low | Very Low |
| Autoboxing | Yes | No | No |

---

# 2. Big O Notation in Android

Android devices have **limited CPU and memory**.

Understanding complexity prevents **UI jank**.

---

## Real Impact

| Scenario | Complexity | Result |
|------|------|------|
| HashMap resize | O(n) | UI freeze |
| RecyclerView diff | O(n) | layout cost |
| SparseArray lookup | O(log n) | minimal |

---

## Frame Budget

```
60 FPS = 16.67ms per frame
```

Operations exceeding this cause:

```
Jank
Dropped frames
```

---

## Bolt Context

Driver updates every:

```
1 second or faster
```

Operations must be efficient:

```
map updates
driver location refresh
route polyline updates
```

---

# 3. Concurrency & Threading

Android uses **message-driven architecture**.

---

# Threading Architecture

```
Thread
 └── Looper
      └── MessageQueue
           └── Handler
```

---

# Looper

Runs infinite message loop.

```
while(true){
  msg = queue.next()
  handler.dispatch(msg)
}
```

Each thread can have **only one Looper**.

Main thread already has one.

---

# MessageQueue

Stores tasks:

```
post()
postDelayed()
sendMessage()
```

Ordered by execution time.

---

# Handler

Posts tasks to Looper.

```kotlin
val handler = Handler(Looper.getMainLooper())

handler.post {
    updateUI()
}
```

---

# Structured Concurrency (Modern Approach)

Instead of:

```
Thread + Handler
```

Use:

```
Kotlin Coroutines
```

Example:

```kotlin
viewModelScope.launch {
    repository.driverLocationFlow()
        .collect { location ->
            updateMap(location)
        }
}
```

Benefits:

- Lifecycle awareness
- No orphan threads
- Automatic cancellation

---

# Java Memory Model

Important for thread safety.

---

## Volatile

Guarantees **visibility**.

```kotlin
@Volatile
var isRunning = true
```

---

## Synchronized

Mutual exclusion.

```kotlin
@Synchronized
fun updateState() {
    state++
}
```

---

## Atomic Variables

Lock-free concurrency.

```kotlin
val counter = AtomicInteger()

counter.incrementAndGet()
```

---

# Bolt Context

Concurrent events:

```
GPS updates
Network responses
Ride acceptance
```

Recommended approach:

```
StateFlow
Structured concurrency
Single source of truth
```

---

# 4. Modern Architecture

---

# Architecture Comparison

| Pattern | Flow | Pros | Cons |
|------|------|------|------|
| MVP | View → Presenter | Simple | Hard to scale |
| MVVM | View ↔ ViewModel | Lifecycle aware | State duplication |
| MVI | Intent → Reducer → State | Predictable | Boilerplate |

---

# MVVM Example

```kotlin
class DriverViewModel : ViewModel() {

    private val _state = MutableStateFlow<DriverState>()
    val state: StateFlow<DriverState> = _state

    fun updateLocation(location: Location) {
        _state.update { it.copy(location = location) }
    }
}
```

---

# MVI Example

```kotlin
sealed class DriverIntent {
    data class LocationUpdate(val location: Location) : DriverIntent()
}

fun reduce(state: DriverState, intent: DriverIntent): DriverState {
    return when(intent) {
        is DriverIntent.LocationUpdate ->
            state.copy(location = intent.location)
    }
}
```

---

# Clean Architecture

Layers:

```
Presentation
Domain
Data
```

---

# Domain Layer

Contains **UseCases**.

```kotlin
class AcceptRideUseCase(
    private val repository: RideRepository
) {
    suspend operator fun invoke(rideId: String) {
        repository.acceptRide(rideId)
    }
}
```

Rules:

```
Pure Kotlin
Framework independent
Highly testable
```

---

# Bolt Context

Example use cases:

```
AcceptRideUseCase
UpdateDriverLocationUseCase
CalculateDriverEarningsUseCase
```

---

# Unidirectional Data Flow

```
UI Event
 ↓
Intent
 ↓
Reducer
 ↓
State
 ↓
UI
```

Advantages:

```
Predictable state
Debuggable
Testable
```

---

# 5. Android Memory Management

Android uses **generational garbage collection**.

---

# Heap Structure

```
Young Generation
Old Generation
Large Object Space
```

Most objects die young.

---

# Senior Concept

Frequent allocations cause:

```
GC pauses
Frame drops
```

Avoid:

```
temporary objects in UI thread
```

---

# Common Memory Leaks

## Inner Class Leak

```kotlin
class MyActivity {

    inner class MyHandler : Handler() {
        override fun handleMessage(msg: Message) {}
    }
}
```

Inner class holds **Activity reference**.

Fix:

```
static class + WeakReference
```

---

## Static Context Leak

```
static Context
```

Activity never garbage collected.

---

# onTrimMemory()

Triggered when system under memory pressure.

```kotlin
override fun onTrimMemory(level: Int) {
    if(level >= TRIM_MEMORY_BACKGROUND) {
        cache.clear()
    }
}
```

---

# Bolt Context

Driver app caches:

```
Map tiles
Driver avatars
Route polylines
```

Release when backgrounded.

---

# 6. Android Rendering Pipeline

```
Measure → Layout → Draw
```

---

# Measure

Determines view size.

```
measure(widthSpec, heightSpec)
```

---

# Layout

Positions views.

```
layout(left, top, right, bottom)
```

---

# Draw

Rendering stage.

```
draw(Canvas)
```

---

# VSYNC

Display refresh synchronization.

```
60Hz → 16.67ms frame
```

Managed by:

```
Choreographer
```

---

# SurfaceFlinger

Android compositor.

Pipeline:

```
App → GPU → SurfaceFlinger → Display
```

Combines surfaces from multiple apps.

---

# Overdraw

Occurs when pixels drawn multiple times.

Debug using:

```
Developer Options → Debug GPU Overdraw
```

---

# Custom View Example

```kotlin
class DriverMarkerView(context: Context) : View(context) {

    private val paint = Paint()

    override fun onDraw(canvas: Canvas) {
        canvas.drawCircle(50f, 50f, 20f, paint)
    }
}
```

Avoid allocating objects inside `onDraw()`.

---

# Bolt Interview Q&A

### 1
Explain HashMap treeification.

### 2
Why SparseArray uses binary search?

### 3
How Looper and MessageQueue interact?

### 4
What causes ANR?

### 5
Explain VSYNC.

### 6
How GC affects UI rendering?

### 7
When should you use ArrayMap?

### 8
Explain structured concurrency.

### 9
Benefits of Unidirectional Data Flow?

### 10
How to handle two drivers accepting ride simultaneously?

Expected answer:

```
Server side atomic transaction
Idempotent APIs
Backend authority
```

### 11
Design driver location updates.

### 12
Debug frame drops.

Tools:

```
Systrace
GPU profiler
Layout Inspector
```

### 13
Explain memory leaks.

### 14
Handle process death in driver app.

### 15
How Flow improves state management.

---

# Final Advice for Bolt Interview

Interviewers evaluate:

```
System thinking
Performance awareness
Concurrency correctness
Architecture clarity
```

Always connect answers to real scenarios:

```
Driver tracking
Ride acceptance
Network latency
Real-time updates
```
