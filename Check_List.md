# 1️⃣ Authentic Questions Asked in Bolt Interviews (Verified)

These come from real candidate reports and interview preparation platforms.

---

# Android / Java Questions

## Q1 Describe Java Collection types

### Answer

Java Collections Framework provides interfaces and implementations for storing and manipulating groups of objects.

Main interfaces:

Collection
├── List
├── Set
└── Queue

Map (separate hierarchy)

### Examples

| Interface | Implementation |
|----------|---------------|
| List | ArrayList, LinkedList |
| Set | HashSet, TreeSet |
| Queue | PriorityQueue |
| Map | HashMap, TreeMap |

### Key idea

- Standard API
- Generic
- Reusable data structures

---

## Q2 Difference between ArrayList and LinkedList

### Answer

| Feature | ArrayList | LinkedList |
|--------|-----------|-----------|
| Structure | Dynamic Array | Doubly Linked List |
| Access | O(1) | O(n) |
| Insert middle | O(n) | O(1) after traversal |
| Memory | Lower | Higher |
| Android usage | Common | Rare |

Android apps usually prefer **ArrayList for UI lists**.

---

## Q3 Two design patterns used in Android

### Example Answer

### 1️⃣ MVVM

Purpose:  
Separate UI and business logic.

Structure

View → ViewModel → Repository → DataSource

Benefits

- Lifecycle aware
- Testable
- Clean architecture

### 2️⃣ Repository Pattern

Purpose:  
Abstract data source.

Example

ViewModel  
↓  
Repository  
↓  
API + Database

Benefits

- Decoupling
- Easier testing
- Multiple data sources

---

## Q4 Android components in AndroidManifest

Four major components:

1️⃣ Activity – UI screen  
2️⃣ Service – Background tasks  
3️⃣ BroadcastReceiver – System event listener  
4️⃣ ContentProvider – Data sharing between apps

---

## Q5 Difference between Activity and Fragment

| Feature | Activity | Fragment |
|--------|----------|----------|
| Lifecycle owner | Yes | Attached to Activity |
| UI container | Entire screen | Part of screen |
| Reusability | Low | High |
| Modular UI | No | Yes |

Fragments allow dynamic UI composition.

---

## Q6 When to use Services?

### Types

Started Service  
Long running background tasks

Example

Upload logs

Bound Service  
Client binds to interact.

Example

Music playback service

---

## Q7 How to preserve long-running operations on screen rotation?

Options:

1️⃣ ViewModel (Recommended)  
2️⃣ SavedStateHandle  
3️⃣ Service  
4️⃣ WorkManager for background work

---

## Q8 Design an API for ride-hailing system

This is a real system design question reported for Bolt interviews.

Example APIs

POST /ride/request  
GET /ride/status  
POST /ride/accept  
POST /ride/cancel  
GET /driver/location

### Key components

Passenger App  
Driver App  
Matching Service  
Location Service  
Pricing Service  
Payment Service

---

# 2️⃣ Generated Questions Based on YOUR ROLE

Now I generated questions specifically based on your situation.

### Factors considered

- Cab booking product
- Driver app
- Loyalty system for drivers
- Android mobile client
- Real-time data

These are very likely questions.

---

# 3️⃣ Driver App Architecture Questions

## Q1 How would you design a Driver App for a ride-hailing system?

### Answer

Main modules:

Driver App  
├── Authentication  
├── Online / Offline status  
├── Location tracking  
├── Ride request handling  
├── Navigation  
├── Earnings dashboard  
└── Loyalty system

### Backend services

Driver Service  
Ride Matching Service  
Pricing Service  
Payment Service  
Loyalty Service

### Important Android considerations

- Background location updates
- Battery optimization
- Network resilience

---

## Q2 How would driver location updates work?

Driver app periodically sends location.

Driver App → Location API → Backend

### Tech choices

Update frequency:

5–10 seconds

### Data flow

GPS → App → Backend → Matching engine

### Optimization

- Batch updates
- Throttle frequency
- Background service

---

# 4️⃣ Loyalty System Questions

This is extremely likely since you mentioned driver loyalty program.

## Q3 How would you design a Driver Loyalty Program?

### Example logic

Driver completes rides → earns points  
Points → rewards / bonuses

### System design

Ride Completed  
↓  
Event Queue  
↓  
Loyalty Service  
↓  
Update Driver Points  
↓  
Driver Dashboard

### Example reward logic

10 rides → bonus  
50 rides → higher incentive  
Gold / Silver tiers

---

## Q4 How would you prevent fraud in a driver loyalty system?

### Possible fraud

- Fake rides
- Colluding passengers
- GPS spoofing

### Solutions

1️⃣ Ride validation – Check trip distance + time  
2️⃣ GPS anomaly detection  
3️⃣ Minimum trip duration  
4️⃣ Fraud detection service

---

# 5️⃣ Real-Time System Questions

## Q5 How does the driver receive ride requests in real time?

### Option 1 – WebSockets

Driver App ← WebSocket → Backend

Advantages

- Real-time
- Low latency

### Option 2 – Firebase Cloud Messaging

Used when driver offline.

Flow

Ride request  
→ Backend  
→ Push notification  
→ Driver App

---

# 6️⃣ Android Engineering Questions

## Q6 How would you handle poor network conditions?

Techniques:

1️⃣ Retry mechanism  
2️⃣ Local caching  
3️⃣ WorkManager  
4️⃣ Queue unsent requests

Example

Driver accepts ride  
↓  
Save locally  
↓  
Sync when network returns

---

## Q7 How would you optimize battery usage in driver app?

Important for navigation apps.

Techniques

- Adaptive location updates
- Use FusedLocationProvider
- Stop updates when offline
- Background service optimization

---

# 7️⃣ Practical Coding Question

### Problem

Find duplicate drivers in a list.

Example

Input

[101,102,103,101,104]

Output

101

### Solution

```java
Set<Integer> seen = new HashSet<>();
Set<Integer> duplicates = new HashSet<>();

for(int id : drivers){
    if(!seen.add(id)){
        duplicates.add(id);
    }
}