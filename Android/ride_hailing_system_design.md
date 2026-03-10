# Ride Hailing System Design Notes
(Bolt / Uber Style System Design – Interview Preparation)

This document contains core concepts required to design a **large-scale ride-hailing platform** such as Bolt or Uber.

Topics covered:

• Real-time driver location updates  
• Ride request handling  
• Driver matching algorithms  
• Race condition handling  
• Surge pricing  
• ETA prediction  
• WebSocket connectivity  
• Network failure handling  
• Scaling architecture  
• Fraud detection  
• Event-driven architecture

---

# 1. Real-Time Driver Location Updates

## Problem

Passengers must see **live driver locations** on the map.

### Requirements

• Low latency updates  
• Scalable to thousands or millions of drivers  
• Battery efficient  
• Real-time UI updates

---

## High-Level Architecture

```
Driver App
   ↓
GPS / Location API
   ↓
Backend Location Service
   ↓
Message Broker / Stream
   ↓
Passenger App (Map Updates)
```

Flow:

```
Driver phone → GPS → Backend → Stream → Rider app
```

---

# 2. Driver Location Collection (Android)

Driver app collects GPS data using:

• Android Location Services  
• Fused Location Provider

Example:

```kotlin
FusedLocationProviderClient.requestLocationUpdates()
```

### Update Frequency

Typically:

```
every 3–5 seconds
```

Trade-off:

| Factor | Impact |
|------|------|
| Higher frequency | More accurate location |
| Lower frequency | Better battery life |

---

# 3. Sending Location to Backend

Driver sends periodic location updates.

Example payload:

```json
{
  "driverId": "123",
  "lat": 12.9716,
  "lng": 77.5946,
  "timestamp": 123456789
}
```

### Transport Methods

| Method | Use Case |
|------|------|
REST | simple but slower |
WebSocket | real-time |
gRPC streaming | high-scale systems |

Best production systems use:

```
WebSockets or gRPC streaming
```

---

# 4. Backend Location Processing

Backend temporarily stores driver location.

Typical architecture:

```
API Gateway
   ↓
Location Service
   ↓
Redis (in-memory store)
```

### Why Redis?

• Extremely fast  
• Low latency  
• Supports geospatial queries

Example key format:

```
driver_location:{driver_id}
```

---

# 5. Broadcasting Updates to Riders

When a rider opens the map:

```
Passenger App
     ↓
WebSocket Connection
     ↓
Receive driver updates
```

Technologies used:

• WebSockets  
• Kafka / PubSub  
• Streaming pipelines

Example pipeline:

```
Driver update
   ↓
Kafka Topic
   ↓
Rider subscribed
   ↓
UI updates
```

---

# 6. Android Map Updates

Passenger app moves driver marker.

Example:

```kotlin
marker.position = LatLng(newLat, newLng)
```

Better UX uses:

• smooth animation  
• interpolation

---

# 7. Location Update Optimizations

## Location Throttling

Send updates only if driver moved significantly.

Example rule:

```
Send update only if moved > 10 meters
```

---

## Batch Updates

Instead of sending every GPS tick:

```
send updates every few seconds
```

---

## Delta Updates

Send **only changed coordinates** instead of full state.

---

# 8. Handling Thousands of Ride Requests

## Problem

Thousands of riders request rides simultaneously.

System must:

• match riders with nearby drivers  
• respond quickly  
• scale horizontally

---

## High-Level Architecture

```
Rider App
   ↓
API Gateway
   ↓
Ride Request Service
   ↓
Matching Engine
   ↓
Driver Notification Service
```

---

# 9. Ride Request Creation

Rider sends request.

Example:

```json
{
  "riderId": "r123",
  "pickup": {"lat": 12.9716, "lng": 77.5946},
  "destination": {"lat": 12.9352, "lng": 77.6245}
}
```

Stored in database.

---

# 10. Driver Discovery

System finds nearby drivers.

Using **geospatial queries**.

Example (Redis GEO):

```
GEOSEARCH drivers within 2km
```

---

# 11. Matching Engine

Matching engine selects the best driver.

Factors considered:

• distance  
• driver rating  
• driver availability  
• estimated arrival time

Example scoring formula:

```
score =
0.5 * distance +
0.3 * rating +
0.2 * idle_time
```

Lowest score wins.

---

# 12. Sending Ride Requests to Drivers

Multiple drivers are notified.

Example:

```
Driver 1
Driver 2
Driver 3
```

Often **5–10 nearby drivers** receive the request.

---

# 13. Driver Accepts Ride

First driver who accepts wins.

Other drivers receive cancellation.

Flow:

```
Ride Request
 ↓
Multiple drivers notified
 ↓
First accept wins
 ↓
Others cancelled
```

---

# 14. Handling Race Conditions

## Problem

Two drivers accept ride simultaneously.

Example:

```
Driver A clicks accept
Driver B clicks accept
```

Both requests reach backend almost together.

---

## Solution 1 — Atomic Database Update

Example query:

```sql
UPDATE rides
SET driver_id = ?
WHERE ride_id = ?
AND driver_id IS NULL
```

Only first update succeeds.

---

## Solution 2 — Redis Distributed Lock

```
SETNX ride_lock_123
```

If lock acquired → assign ride.

---

## Solution 3 — Optimistic Locking

Use version numbers.

```
version = 1
```

Update succeeds only if version matches.

---

## Solution 4 — Queue Ordering

Requests processed sequentially.

Example:

```
Kafka topic → accept events
```

First event wins.

---

# 15. Surge Pricing

## Goal

Balance supply and demand.

If many riders request rides but few drivers exist:

```
increase price
```

---

## Surge Formula (Simplified)

```
surge_multiplier =
ride_requests / available_drivers
```

Example:

| Requests | Drivers | Surge |
|---|---|---|
100 | 50 | 2x |

Example fare:

```
Base price = ₹200
Surge = 1.8x
Final price = ₹360
```

---

# 16. ETA Prediction

ETA = Estimated Time of Arrival.

Basic formula:

```
ETA = distance / average speed
```

Example:

| Distance | Speed | ETA |
|---|---|---|
3 km | 30 km/h | 6 minutes |

---

### Real Systems Use

• traffic data  
• historical routes  
• road types  
• driver behavior

Often using:

```
Google Maps API
or internal routing systems
```

ETA recalculated every **10 seconds**.

---

# 17. WebSocket Connection (Driver App)

Ride apps typically use **WebSockets**.

Flow:

```
Driver App
   ↓
WebSocket Connection
   ↓
Backend pushes ride requests
```

---

# 18. Handling WebSocket Disconnections

Solutions:

### Heartbeat Mechanism

```
ping every 20 seconds
```

---

### Automatic Reconnection

Retry pattern:

```
1 sec
2 sec
5 sec
10 sec
```

Using **exponential backoff**.

---

# 19. Handling Poor Network

Driver may accept ride but lose network.

Solution:

Use **local action queue**.

Example queue:

```
accept ride
cancel ride
start trip
```

Stored locally.

Android tool:

```
WorkManager
```

Sync when network returns.

---

# 20. Scaling Location Updates

Example scale:

| Drivers | Update Frequency | Updates/sec |
|---|---|---|
2 million | every 5 sec | 400,000/sec |

---

## Scalable Architecture

```
Load Balancers
     ↓
Distributed Location Servers
     ↓
Kafka Streaming
     ↓
Geo-indexing
```

---

# 21. Geo Partitioning

Divide world into regions.

Example:

```
Region A → Europe
Region B → Asia
Region C → America
```

Each server handles drivers in its region.

Benefits:

• better scaling  
• lower latency

---

# 22. Preventing GPS Spoofing

Drivers may fake location.

Example:

Driver pretends to be near airport.

Detection methods:

### Speed anomaly

Example:

```
Driver moves 10 km in 5 seconds
```

Impossible → flagged.

---

### Location verification

Compare:

• GPS location  
• Network location

---

### Pattern detection

Repeated abnormal behavior → investigation.

---

# 23. Driver Offline Scenario

Driver may lose internet during trip.

Solution:

Driver app maintains **local trip state**.

Example:

```
ride_started
distance_travelled
timestamps
```

Synced when connection returns.

---

# 24. Loyalty Program Scaling

Millions of rides generate reward events.

Solution:

Use **event-driven architecture**.

Flow:

```
Ride Completed
     ↓
Kafka Event
     ↓
Loyalty Service
     ↓
Reward Points Update
```

Benefits:

• asynchronous  
• scalable  
• reliable

---

## Prevent Duplicate Rewards

Use **idempotent processing**.

Example:

```
ride_id processed only once
```

---

# 25. Driver App State Machine

Driver states:

```
OFFLINE
AVAILABLE
ON_TRIP
```

Rules:

| State | Behavior |
|---|---|
OFFLINE | cannot receive rides |
AVAILABLE | can receive rides |
ON_TRIP | cannot accept new ride |

This prevents **multiple ride acceptance**.

---

# 26. Trade-Off Thinking (Interview Tip)

Example: Location update frequency.

### High frequency updates

Pros:

• accurate matching

Cons:

• battery drain  
• higher server load

---

### Low frequency updates

Pros:

• battery efficient

Cons:

• inaccurate location

---

# 27. 30-Second Senior Interview Answer

If asked:

**"How would you design a ride-hailing backend?"**

Example answer:

A ride-hailing system uses driver devices that periodically send GPS location updates to backend servers. These locations are stored in fast in-memory databases such as Redis and indexed using geospatial queries. When a rider requests a ride, a matching engine finds nearby drivers and sends ride requests through a message queue or WebSocket connections. To prevent multiple drivers from accepting the same ride, the system uses atomic database updates or distributed locking mechanisms. The architecture scales horizontally using microservices, streaming systems like Kafka, and geo-partitioned location services.

---

# End of Notes