# Bolt Android Interview Master Notes
Consolidated technical notes compiled from interview reports, preparation materials, and system design discussions related to Bolt Android interviews.  
All duplicate information has been merged and organized into structured sections for efficient study.

---

# 1. Bolt Interview Overview

Typical Bolt Android interview process:

1. Recruiter screening
2. Android technical interview
3. Algorithm / coding round
4. System design round
5. Live coding task
6. Behavioral discussion

Common focus areas:

- Android fundamentals
- Kotlin / Java internals
- Architecture patterns
- Concurrency
- Real-world system design
- Performance and debugging
- Product thinking

Senior interviewers (example: Uladzimir Akulovich) usually test:

- Real production thinking
- System failures
- Battery optimization
- Scalability
- Ownership mindset

---

# 2. Java / Kotlin Fundamentals

## Java Collections Framework

The Java Collections Framework provides reusable data structures for storing and manipulating objects.

### Main hierarchy

```
Iterable
   |
Collection
 ├── List
 ├── Set
 └── Queue

Map (separate hierarchy)
```

### Common implementations

| Interface | Implementations |
|--------|----------------|
| List | ArrayList, LinkedList |
| Set | HashSet, TreeSet |
| Queue | PriorityQueue |
| Map | HashMap, TreeMap |

Key features:

- Generic types
- Standardized APIs
- Built-in algorithms (sorting, searching)

---

## ArrayList vs LinkedList

| Feature | ArrayList | LinkedList |
|-------|-----------|-----------|
| Structure | Dynamic array | Doubly linked list |
| Random access | O(1) | O(n) |
| Insert middle | O(n) | O(1) after traversal |
| Memory | Lower | Higher |
| Android usage | Very common | Rare |

Android applications generally prefer **ArrayList** for UI lists.

---

## HashMap Internals

HashMap stores key-value pairs using hashing.

Steps:

1. Hash of key computed
2. Bucket index determined
3. Entry inserted into bucket
4. Collisions handled

### Collision handling

Java uses:

- Linked list
- Converted to Red-Black Tree when threshold exceeded

---

## HashMap vs ConcurrentHashMap

| Feature | HashMap | ConcurrentHashMap |
|------|------|------|
| Thread safety | No | Yes |
| Locking | None | Bucket/segment locking |
| Performance | Faster single thread | Better multi-threading |

---

## Kotlin Basics

### val vs var

| Keyword | Meaning |
|------|------|
| val | Immutable variable |
| var | Mutable variable |

Example:

```kotlin
val name = "Bolt"
var ridesCompleted = 15
```

---

### lateinit vs lazy

| Feature | lateinit | lazy |
|------|------|------|
| Type | Mutable | Immutable |
| Initialization | Later manually | First access |
| Thread safety | No | Yes (default) |

Example:

```kotlin
lateinit var repository: RideRepository

val config by lazy {
    loadConfig()
}
```

---

## Reflection

Reflection allows runtime inspection of classes.

Example:

```java
Field field = MyClass.class.getDeclaredField("privateField");
field.setAccessible(true);
```

Why reflection is discouraged:

- Slower performance
- Breaks encapsulation
- Harder debugging
- Security concerns

Use cases:

- Frameworks
- Dependency injection
- Testing libraries

---

# 3. Android Core Fundamentals

## Four Android Components

Defined in AndroidManifest.

| Component | Purpose |
|------|------|
| Activity | UI screen |
| Service | Background tasks |
| BroadcastReceiver | Listen to system events |
| ContentProvider | Data sharing between apps |

---

## Activity Lifecycle

```
onCreate
onStart
onResume

App running

onPause
onStop
onDestroy
```

---

## Fragment Lifecycle

```
onAttach
onCreate
onCreateView
onViewCreated
onStart
onResume
```

Fragments depend on Activity lifecycle.

---

## Activity vs Fragment

| Feature | Activity | Fragment |
|------|------|------|
| UI scope | Full screen | Partial UI |
| Lifecycle | Independent | Depends on Activity |
| Reusability | Low | High |
| Modular UI | No | Yes |

Fragments allow dynamic UI composition.

---

## Fragment–Activity Communication

Recommended approach:

Shared ViewModel.

```
Fragment → ViewModel → Activity
```

---

# 4. Android Architecture

Architecture questions appear frequently.

## MVVM Architecture

```
View
 ↓
ViewModel
 ↓
Repository
 ↓
DataSource (API / DB)
```

Benefits:

- Lifecycle aware
- Separation of concerns
- Testable
- Survives configuration changes

---

## Repository Pattern

Purpose: abstract data layer.

```
UI
 ↓
ViewModel
 ↓
Repository
 ↓
API / Database
```

Benefits:

- Single source of truth
- Easy testing
- Replaceable data sources

---

# 5. Concurrency & Background Processing

## Preserving long-running tasks during rotation

Use:

- ViewModel
- SavedStateHandle
- WorkManager
- Foreground Service

ViewModel retains data during configuration change.

---

## Android Services

| Service Type | Purpose |
|------|------|
| Started Service | Long-running background task |
| Foreground Service | Visible ongoing task |
| Bound Service | Client-server communication |

---

## AIDL

Android Interface Definition Language.

Used for IPC between processes.

Example uses:

- System services
- Inter-app communication

---

# 6. Android Debugging & Performance

## OutOfMemoryError

Causes:

- Large bitmaps
- Memory leaks
- Excessive caching

Tools:

- Memory profiler
- LeakCanary

---

## ANR (Application Not Responding)

Occurs when main thread blocked > 5 seconds.

Common causes:

- Network on main thread
- Heavy computation
- Disk I/O

---

## Profiling Tools

- Android Studio Profiler
- Memory profiler
- CPU profiler
- GPU rendering tools

---

# 7. Algorithms & Coding Questions

Bolt includes algorithm questions.

## Longest Substring Without Repeating Characters

Technique: Sliding window

Time complexity: O(n)

---

## Two Sum

Find two numbers that sum to target.

Example:

```java
Map<Integer, Integer> map = new HashMap<>();

for(int i=0;i<arr.length;i++){
    int diff = target - arr[i];

    if(map.containsKey(diff)){
        return new int[]{map.get(diff), i};
    }

    map.put(arr[i], i);
}
```

Time complexity: O(n)

---

## Reverse Words in String

Input:

```
hello world
```

Output:

```
world hello
```

---

## LRU Cache

Uses:

- HashMap
- Doubly linked list

Time complexity:

| Operation | Complexity |
|------|------|
| get | O(1) |
| put | O(1) |

---

# 8. Android Live Coding Tasks

Candidates report tasks such as:

### REST API Integration

Steps:

1. Fetch API
2. Parse JSON
3. Display in RecyclerView

Common tools:

- Retrofit
- Gson
- Kotlin Serialization

---

## Map Marker Task

Flow:

```
API
 ↓
Coordinates
 ↓
Convert to markers
 ↓
Display on Google Map
```

---

## Downloader Task

Requirements:

- Download multiple files
- Show progress
- Handle orientation changes
- Retry on failure

Tools:

- WorkManager
- Coroutines

---

# 9. Ride-Hailing System Design

Bolt focuses heavily on real-world architecture.

## High-Level Architecture

```
Passenger App
      |
API Gateway
      |
Ride Service
--------------------------------
|           |           |
Driver      Pricing     Matching
Service     Service     Service
      |
Location Service
```

---

## Ride Request Flow

```
Passenger requests ride
↓
Ride service creates request
↓
Matching service finds nearby drivers
↓
Ride request sent to drivers
↓
Driver accepts
↓
Ride confirmed
```

---

## Race Condition Problem

Two drivers accept same ride.

Solution:

Atomic update.

```
UPDATE ride
SET driver_id = ?
WHERE ride_id = ?
AND status = REQUESTED
```

Only one update succeeds.

---

# 10. Driver Location Tracking

Drivers continuously send GPS updates.

Typical interval:

```
Every 5 seconds
```

Flow:

```
Driver GPS
 ↓
Driver App
 ↓
Location API
 ↓
Location Store
 ↓
Matching Service
```

---

## Location Storage Options

- Redis GEO index
- GeoHash
- ElasticSearch
- PostGIS

---

# 11. Scaling Location System

Millions of drivers updating location.

Solution:

GeoHash partitioning.

World divided into grid regions.

Benefits:

- Horizontal scaling
- Faster geo queries

---

# 12. Battery Optimization (Driver Apps)

Drivers run apps for many hours.

Strategies:

- Adaptive location updates
- Batch network calls
- Stop updates when offline

Example:

```
Idle driver → update every 10 sec
Active ride → update every 3 sec
```

Recommended API:

```
FusedLocationProviderClient
```

---

# 13. Loyalty Program System Design

Goal:

Increase driver engagement.

Metrics:

- Completed rides
- Online hours
- Acceptance rate
- Driver rating

---

## Loyalty Tier Example

| Tier | Requirement |
|------|------|
| Bronze | 20 rides/week |
| Silver | 50 rides/week |
| Gold | 100 rides/week |

Rewards:

- Bonus payment
- Reduced commission
- Ride priority

---

## Loyalty Architecture

Event-driven architecture.

```
Ride Completed
 ↓
Event Queue
 ↓
Loyalty Service
 ↓
Update Driver Points
 ↓
Driver Dashboard API
```

Queue technologies:

- Kafka
- RabbitMQ

---

## Fraud Prevention

Possible fraud:

- Fake rides
- GPS spoofing
- Collusion

Solutions:

- Minimum ride distance
- Minimum ride duration
- GPS anomaly detection
- Suspicious pattern analysis

---

# 14. Handling Network Failures

Driver apps often operate with poor connectivity.

Strategy: Local action queue.

```
Accept ride
Cancel ride
Update location
```

Stored locally and synced later.

Recommended tool:

```
WorkManager
```

---

# 15. Global Scaling

For worldwide deployment:

- Region-based infrastructure
- Microservices
- Distributed cache
- CDN

Example regions:

```
Europe servers
Asia servers
America servers
```

Drivers connect to nearest region.

---

# 16. Behavioral Questions

Common Bolt questions:

- Why Bolt?
- Most exciting project
- Biggest mistake
- Conflict with teammate
- Handling pressure

---

# 17. Product Thinking Questions

Bolt values engineers with product awareness.

Examples:

- Explore new mobility product
- Define KPIs for ride-hailing
- Build MVP for new feature

---

# 18. Key Engineering Principles Bolt Values

## Idempotency

Prevent duplicate processing.

Example:

```
ride_id processed only once
```

---

## 60 FPS UI Performance

Frame budget:

```
16ms per frame
```

Tools:

- Profile GPU Rendering
- Choreographer

---

## Modularization

Large Android apps should use:

- Feature modules
- Feature flags
- Dynamic feature delivery

---

# Final Preparation Tips

Focus heavily on:

- Android architecture (MVVM)
- Location tracking systems
- Battery optimization
- System design
- Concurrency
- Algorithms
- Real-world failure scenarios