# In-Depth Interview Q&A (Deduplicated from All PDFs)

This file consolidates all 12 PDFs into a single interview-focused Q&A set.
Each item includes:
- Interview purpose (why this is asked)
- What a strong answer should include
- Cross-check links to official documentation

## 1) Interview Process and Intent

### Q1. What does the typical Bolt-style Android interview funnel test?
**Interview purpose:** Evaluate breadth first, then depth under constraints similar to production.

**Strong answer should include:**
- Recruiter/fit screening: motivation, communication, role fit.
- Android fundamentals: lifecycle, threading, architecture, memory.
- DSA/coding round: problem solving speed and correctness.
- System design: scalability, fault tolerance, tradeoffs.
- Live coding/debugging: practical implementation quality.

**Official references:**
- Android app fundamentals: https://developer.android.com/guide/components/fundamentals
- Android architecture guidance: https://developer.android.com/topic/architecture

### Q2. Why do interviewers ask âwhat happens if this service failsâ questions?
**Interview purpose:** Test operational thinking, not just ideal-path design.

**Strong answer should include:**
- Failure modes: timeout, partial failure, stale data, retries.
- Resilience patterns: idempotency, circuit breaker, backoff, fallbacks.
- Observability: metrics, logs, tracing, SLO/SLA thinking.
- Recovery and data consistency implications.

**Official references:**
- Google SRE book (official): https://sre.google/sre-book/table-of-contents/
- WorkManager retries/backoff: https://developer.android.com/topic/libraries/architecture/workmanager/how-to/define-work

## 2) Big-O and Algorithmic Thinking

### Q3. What is Big-O in interview context?
**Interview purpose:** Ensure you can reason about scale before writing code.

**Strong answer should include:**
- Growth trend as input `n` increases.
- Time and space complexity both matter.
- Compare feasible vs non-feasible approaches for large datasets.

**Official references:**
- Python wiki time complexity (widely used reference): https://wiki.python.org/moin/TimeComplexity
- Java Collections performance notes: https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/util/package-summary.html

### Q4. Which complexity classes matter most in backend/mobile interviews?
**Interview purpose:** Validate practical prioritization.

**Strong answer should include:**
- `O(1)`: direct lookup.
- `O(log n)`: binary-search-like pruning.
- `O(n)`: single pass scan.
- `O(n log n)`: efficient sorting/heap use.
- `O(n^2)` and worse: acceptable only for small bounded inputs.

**Official references:**
- Java `Collections.sort` and complexity context: https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/util/Collections.html

### Q5. How do you explain tradeoffs when interviewer asks for optimization?
**Interview purpose:** Check decision quality, not memorization.

**Strong answer should include:**
- Baseline approach first.
- Bottleneck identification.
- Improved approach and measurable complexity gain.
- Memory vs latency tradeoff.

**Official references:**
- Java performance-related collection docs: https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/util/HashMap.html

## 3) Java/Kotlin Core Questions

### Q6. What is Java Collections Framework and why does it matter in Android interviews?
**Interview purpose:** Validate data-structure selection under real constraints.

**Strong answer should include:**
- Main interfaces: `List`, `Set`, `Queue`, `Map`.
- Typical implementations and when to use each.
- Time complexity awareness for common operations.

**Official references:**
- Java Collections overview: https://docs.oracle.com/javase/tutorial/collections/intro/index.html
- `java.util` package docs: https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/util/package-summary.html

### Q7. ArrayList vs LinkedList: what should a senior Android answer look like?
**Interview purpose:** Check practical data-structure choices, not textbook repetition.

**Strong answer should include:**
- `ArrayList`: contiguous memory, fast random access, usually preferred for UI/data lists.
- `LinkedList`: pointer-based, costly random access, niche usage.
- Real-world note: Android apps usually benefit more from `ArrayList`.

**Official references:**
- ArrayList docs: https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/util/ArrayList.html
- LinkedList docs: https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/util/LinkedList.html

### Q8. HashMap internals and collisions: what is expected?
**Interview purpose:** Test understanding of hash-based lookup guarantees and failure cases.

**Strong answer should include:**
- Hash -> bucket mapping.
- Collision handling (bucket structures).
- Why bad hash distribution hurts performance.

**Official references:**
- HashMap docs: https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/util/HashMap.html

### Q9. HashMap vs ConcurrentHashMap?
**Interview purpose:** Assess thread-safety decisions.

**Strong answer should include:**
- `HashMap` is not thread-safe.
- `ConcurrentHashMap` supports concurrent reads/writes.
- Choose based on contention and concurrency requirements.

**Official references:**
- ConcurrentHashMap docs: https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/util/concurrent/ConcurrentHashMap.html

### Q10. Kotlin `val` vs `var` and `lateinit` vs `lazy`?
**Interview purpose:** Evaluate Kotlin fluency used daily in Android codebases.

**Strong answer should include:**
- `val`: read-only reference; `var`: mutable reference.
- `lateinit`: deferred init for non-null mutable properties.
- `lazy`: computed on first access, typically immutable property usage.

**Official references:**
- Kotlin properties: https://kotlinlang.org/docs/properties.html
- Kotlin delegated properties (`lazy`): https://kotlinlang.org/docs/delegated-properties.html#lazy-properties

### Q11. What is a Kotlin data class and when should you use it?
**Interview purpose:** Check clean model design and immutability habits.

**Strong answer should include:**
- Auto-generated `equals`, `hashCode`, `toString`, `copy`, `componentN`.
- Use for immutable UI/domain state and DTO-style models.

**Official references:**
- Kotlin data classes: https://kotlinlang.org/docs/data-classes.html

## 4) Android Fundamentals

### Q12. What are the four core Android components?
**Interview purpose:** Confirm baseline platform competency.

**Strong answer should include:**
- `Activity`, `Service`, `BroadcastReceiver`, `ContentProvider`.
- Typical responsibilities for each.

**Official references:**
- App component overview: https://developer.android.com/guide/components/fundamentals

### Q13. Activity vs Fragment in interview framing?
**Interview purpose:** Validate architecture decisions in modular UI.

**Strong answer should include:**
- Activity as host/container and entry point.
- Fragment as reusable UI behavior unit managed by FragmentManager.
- Lifecycle implications and state handling.

**Official references:**
- Activity lifecycle: https://developer.android.com/guide/components/activities/activity-lifecycle
- Fragments: https://developer.android.com/guide/fragments

### Q14. What does AndroidManifest communicate to the system?
**Interview purpose:** Ensure deployment/runtime configuration awareness.

**Strong answer should include:**
- Component declarations.
- Permissions.
- Intent filters.
- App-level metadata and process behavior.

**Official references:**
- App manifest overview: https://developer.android.com/guide/topics/manifest/manifest-intro

## 5) Architecture (MVVM/MVI/Clean)

### Q15. Why is modern architecture mandatory for large apps like ride-hailing?
**Interview purpose:** Check if you optimize for maintainability and scale.

**Strong answer should include:**
- Predictable state.
- Separation of concerns.
- Testability.
- Lifecycle-safe async handling.

**Official references:**
- Android architecture recommendations: https://developer.android.com/topic/architecture/recommendations

### Q16. What is the core MVVM flow expected in interviews?
**Interview purpose:** Validate practical app-layer design.

**Strong answer should include:**
- View emits UI actions.
- ViewModel transforms actions and calls use cases/repositories.
- View observes immutable UI state.

**Official references:**
- ViewModel: https://developer.android.com/topic/libraries/architecture/viewmodel
- StateFlow (Kotlin): https://kotlinlang.org/api/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines.flow/-state-flow/

### Q17. MVVM vs MVI: how do you answer without being dogmatic?
**Interview purpose:** Test tradeoff reasoning.

**Strong answer should include:**
- MVVM: pragmatic, widely adopted, fast to implement.
- MVI: strict unidirectional flow, immutable state, easier reasoning for complex UI states.
- Decision by team skill, complexity, and debugging needs.

**Official references:**
- Unidirectional data flow in Compose: https://developer.android.com/develop/ui/compose/architecture

### Q18. How does ViewModel survive configuration changes?
**Interview purpose:** Confirm lifecycle correctness.

**Strong answer should include:**
- ViewModel scoped to Activity/Fragment lifecycle owner.
- Retained across configuration changes.
- Do not store direct View/context references.

**Official references:**
- ViewModel lifecycle behavior: https://developer.android.com/topic/libraries/architecture/viewmodel#lifecycle

### Q19. Repository pattern and Clean Architecture in interview terms?
**Interview purpose:** Evaluate boundary design and dependency direction.

**Strong answer should include:**
- Presentation layer independent of data source details.
- Domain use cases encapsulate business rules.
- Data layer handles API/DB/cache.

**Official references:**
- Guide to app architecture: https://developer.android.com/topic/architecture

## 6) Threading, Looper, Handler, Work

### Q20. What is Androidâs single-threaded UI model?
**Interview purpose:** Check ANR prevention and correctness.

**Strong answer should include:**
- Main thread must remain responsive.
- Heavy work moves to background execution.
- Post results safely back to UI state.

**Official references:**
- Processes and threads: https://developer.android.com/guide/components/processes-and-threads

### Q21. Explain Looper, MessageQueue, Handler clearly.
**Interview purpose:** Validate platform internals understanding.

**Strong answer should include:**
- `Looper`: event loop for a thread.
- `MessageQueue`: pending messages/runnables in time order.
- `Handler`: enqueues work and handles callbacks.

**Official references:**
- Looper API: https://developer.android.com/reference/android/os/Looper
- Handler API: https://developer.android.com/reference/android/os/Handler
- MessageQueue API: https://developer.android.com/reference/android/os/MessageQueue

### Q22. How do you preserve long-running work across rotation/app restarts?
**Interview purpose:** Evaluate lifecycle-safe background execution.

**Strong answer should include:**
- UI-tied work in ViewModel/coroutines.
- Guaranteed deferrable work in WorkManager.
- Foreground service only for user-noticeable ongoing tasks.

**Official references:**
- WorkManager overview: https://developer.android.com/topic/libraries/architecture/workmanager
- Foreground services: https://developer.android.com/develop/background-work/services/fgs

### Q23. What should you say about Service types and AIDL?
**Interview purpose:** Test IPC and background execution understanding.

**Strong answer should include:**
- Started vs bound services.
- Foreground service constraints.
- AIDL for cross-process interfaces when Binder IPC is needed.

**Official references:**
- Services overview: https://developer.android.com/guide/components/services
- AIDL: https://developer.android.com/guide/components/aidl

## 7) View Rendering and UI Performance

### Q24. What is Measure -> Layout -> Draw pipeline?
**Interview purpose:** Confirm UI performance fundamentals.

**Strong answer should include:**
- `onMeasure`: compute size.
- `onLayout`: assign bounds.
- `onDraw`: render pixels.
- Unnecessary passes hurt frame time.

**Official references:**
- Custom view drawing: https://developer.android.com/develop/ui/views/layout/custom-views/custom-drawing
- View class docs: https://developer.android.com/reference/android/view/View

### Q25. What are MeasureSpec modes and why do they matter?
**Interview purpose:** Ensure custom view correctness.

**Strong answer should include:**
- `EXACTLY`, `AT_MOST`, `UNSPECIFIED` semantics.
- Correct measured dimensions avoid layout bugs and overdraw-related issues.

**Official references:**
- View.MeasureSpec: https://developer.android.com/reference/android/view/View.MeasureSpec

### Q26. What UI performance signals should be discussed in interview?
**Interview purpose:** Check whether you can ship smooth, battery-friendly UI.

**Strong answer should include:**
- Jank/frame drops, overdraw, excessive layout passes.
- Profiling and frame timing tools.

**Official references:**
- Render performance: https://developer.android.com/topic/performance/rendering
- Android Studio profiler docs: https://developer.android.com/studio/profile

## 8) Memory Management and Leaks

### Q27. How is memory managed in Android (ART)?
**Interview purpose:** Validate runtime-level understanding.

**Strong answer should include:**
- ART handles allocation and GC.
- Stack vs heap distinction.
- Developer responsibility is leak prevention and allocation discipline.

**Official references:**
- ART and runtime basics: https://source.android.com/docs/core/runtime
- Memory overview: https://developer.android.com/topic/performance/memory

### Q28. What makes an object eligible for garbage collection?
**Interview purpose:** Test correctness around object lifetime.

**Strong answer should include:**
- Unreachable from GC roots.
- Retained references via static/singleton/callback can block GC.

**Official references:**
- Java GC fundamentals: https://docs.oracle.com/en/java/javase/21/gctuning/garbage-collector-implementation.html

### Q29. Most common Android memory leak patterns?
**Interview purpose:** Identify practical debugging maturity.

**Strong answer should include:**
- Activity/Fragment reference leaked through static fields.
- Non-static inner classes and handlers retaining outer class.
- Long-lived callbacks/listeners not removed.

**Official references:**
- Android memory leaks guidance: https://developer.android.com/topic/performance/memory
- LeakCanary (official project docs): https://square.github.io/leakcanary/

### Q30. Why do bitmaps cause OOM and how do you mitigate?
**Interview purpose:** Evaluate media-heavy app readiness.

**Strong answer should include:**
- Bitmap memory footprint math (`width * height * bytesPerPixel`).
- Use sampling, image pipelines, and caches.
- Prefer mature libraries for lifecycle-aware loading.

**Official references:**
- Loading large bitmaps efficiently: https://developer.android.com/topic/performance/graphics/load-bitmap
- BitmapFactory.Options: https://developer.android.com/reference/android/graphics/BitmapFactory.Options

### Q31. Which tools are expected for memory analysis?
**Interview purpose:** Verify you can diagnose and not just theorize.

**Strong answer should include:**
- Android Studio Memory Profiler.
- Heap dumps and allocation tracking.
- LeakCanary for leak detection in debug builds.

**Official references:**
- Memory profiler: https://developer.android.com/studio/profile/memory-profiler

## 9) Ride-Hailing System Design (Core)

### Q32. What is the baseline architecture for ride-hailing?
**Interview purpose:** Confirm service decomposition skills.

**Strong answer should include:**
- API gateway, ride service, matching service, location service, pricing service.
- Passenger app and driver app as independent clients.
- Eventing and data stores chosen by access pattern.

**Official references:**
- Google Cloud architecture center (microservices patterns): https://cloud.google.com/architecture

### Q33. What is the expected ride request lifecycle?
**Interview purpose:** Test sequence clarity and state machine thinking.

**Strong answer should include:**
- `REQUESTED -> OFFERED -> ACCEPTED -> IN_TRIP -> COMPLETED/CANCELLED`.
- Timeouts and retries at each stage.
- Auditable state transitions.

**Official references:**
- Event-driven architecture basics (AWS docs): https://docs.aws.amazon.com/eventbridge/latest/userguide/eb-what-is.html

### Q34. How do you prevent double-accept race condition?
**Interview purpose:** Validate concurrency correctness.

**Strong answer should include:**
- Atomic conditional update (`WHERE status = REQUESTED`).
- Exactly one winner semantics.
- Idempotent retry handling.

**Official references:**
- PostgreSQL transaction isolation: https://www.postgresql.org/docs/current/transaction-iso.html
- SQL transaction docs (MySQL/InnoDB): https://dev.mysql.com/doc/refman/8.0/en/innodb-transaction-model.html

### Q35. How should surge pricing be explained?
**Interview purpose:** Check ability to formalize business logic safely.

**Strong answer should include:**
- Supply-demand ratio based multiplier.
- Guardrails: floor/ceiling, smoothing, anti-volatility windows.
- Recompute cadence and user transparency.

**Official references:**
- N/A single standard spec; use internal policy + experimentation docs.

### Q36. How do you design ETA prediction answer?
**Interview purpose:** Evaluate practical ML/routing integration reasoning.

**Strong answer should include:**
- Inputs: route distance, traffic, historical speed, road class.
- Real-time recalculation strategy.
- Confidence intervals and UX implications.

**Official references:**
- Google Routes API docs: https://developers.google.com/maps/documentation/routes

### Q37. What factors should drive matching algorithm decisions?
**Interview purpose:** Check fairness + efficiency tradeoff reasoning.

**Strong answer should include:**
- Distance/ETA.
- Acceptance and cancellation history.
- Driver idle time and rating.
- Anti-starvation strategy.

**Official references:**
- N/A product-specific; for geo indexing patterns:
- Redis geospatial docs: https://redis.io/docs/latest/develop/data-types/geospatial/

## 10) Real-Time Location Design

### Q38. What are key requirements for driver location updates?
**Interview purpose:** Ensure requirements clarity before architecture.

**Strong answer should include:**
- Low latency.
- Massive concurrent scale.
- Battery/network efficiency.
- Correctness under intermittent connectivity.

**Official references:**
- Fused Location Provider: https://developers.google.com/location-context/fused-location-provider
- Android location permissions/background limits: https://developer.android.com/develop/sensors-and-location/location/permissions

### Q39. What update frequency is realistic and why?
**Interview purpose:** Test tradeoff balancing.

**Strong answer should include:**
- Typical 3-5 second cadence when active trip states need precision.
- Adaptive intervals by speed, trip phase, battery, and foreground/background state.

**Official references:**
- Request location updates on Android: https://developer.android.com/develop/sensors-and-location/location/request-updates

### Q40. REST vs WebSocket vs gRPC streaming for live location?
**Interview purpose:** Check transport-level decision logic.

**Strong answer should include:**
- REST: easy but chatty for high-frequency real-time.
- WebSocket: full-duplex low-overhead push.
- gRPC streaming: high-performance typed streaming, strong for service-to-service and mobile if infra supports it.

**Official references:**
- WebSocket protocol RFC 6455: https://datatracker.ietf.org/doc/html/rfc6455
- gRPC core concepts: https://grpc.io/docs/what-is-grpc/core-concepts/

### Q41. Why use Redis for driver location state?
**Interview purpose:** Validate datastore choice by access pattern.

**Strong answer should include:**
- In-memory low latency.
- Native geo queries.
- Fast ephemeral state updates.

**Official references:**
- Redis geospatial commands: https://redis.io/docs/latest/develop/data-types/geospatial/

### Q42. How do you scale to millions of location updates?
**Interview purpose:** Evaluate horizontal scaling strategy.

**Strong answer should include:**
- Partition by geography.
- Message broker ingestion buffer.
- Stateless processing nodes behind load balancer.
- Backpressure and rate controls.

**Official references:**
- Apache Kafka intro: https://kafka.apache.org/intro
- Kubernetes autoscaling concepts: https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/

## 11) Reliability, Offline, and Security

### Q43. How do you handle poor network when driver accepts/cancels trip?
**Interview purpose:** Verify offline-first robustness.

**Strong answer should include:**
- Durable local action queue.
- Retry with exponential backoff.
- Idempotent server APIs to prevent duplicate side effects.

**Official references:**
- WorkManager reliable background sync: https://developer.android.com/topic/libraries/architecture/workmanager
- HTTP idempotent semantics: https://www.rfc-editor.org/rfc/rfc9110

### Q44. How do you design WebSocket reconnect behavior?
**Interview purpose:** Test real-time reliability handling.

**Strong answer should include:**
- Heartbeats/pings.
- Exponential backoff with jitter.
- Session resubscription and missed-event recovery strategy.

**Official references:**
- WebSocket RFC: https://datatracker.ietf.org/doc/html/rfc6455

### Q45. How do you detect GPS spoofing/fraud signals?
**Interview purpose:** Assess abuse resistance in production systems.

**Strong answer should include:**
- Impossible speed/acceleration checks.
- GPS vs network/provider consistency checks.
- Repeated anomaly scoring and risk pipelines.

**Official references:**
- Android location provider docs: https://developer.android.com/develop/sensors-and-location/location

### Q46. What should happen if driver goes offline mid-trip?
**Interview purpose:** Validate state durability and reconciliation strategy.

**Strong answer should include:**
- Persist local trip timeline/events.
- Reconcile with backend when connectivity resumes.
- Conflict resolution rules and auditability.

**Official references:**
- Offline-first architecture: https://developer.android.com/topic/architecture/data-layer/offline-first

## 12) Event-Driven Loyalty and Idempotency

### Q47. How do you scale loyalty points for millions of completed rides?
**Interview purpose:** Check asynchronous architecture skills.

**Strong answer should include:**
- Publish `RideCompleted` events.
- Dedicated loyalty consumer service.
- Exactly-once business effect via idempotent keys.

**Official references:**
- Kafka design and delivery semantics: https://kafka.apache.org/documentation/#semantics

### Q48. How do you avoid duplicate rewards processing?
**Interview purpose:** Validate correctness under retries and duplicate events.

**Strong answer should include:**
- Unique idempotency key (e.g., `ride_id + reward_type`).
- Dedup store / unique DB constraint.
- Safe reprocessing behavior.

**Official references:**
- Idempotency semantics in HTTP: https://www.rfc-editor.org/rfc/rfc9110

## 13) Coding Round Patterns

### Q49. Which coding patterns are repeatedly reported in these PDFs?
**Interview purpose:** Basic algorithmic readiness.

**Strong answer should include:**
- String frequency/counting.
- Sliding window.
- Hash-based lookups.
- Interval merging.
- LRU-cache style design.

**Official references:**
- Java `Map` API: https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/util/Map.html
- Kotlin collections: https://kotlinlang.org/docs/collections-overview.html

### Q50. Example: âValid Anagramâ expected interview-grade answer?
**Interview purpose:** Check linear-time thinking and clean implementation.

**Strong answer should include:**
- Quick length mismatch check.
- Fixed-size frequency array/map.
- Single pass increment/decrement.
- Complexity: `O(n)` time, `O(1)` extra space for fixed alphabet.

**Official references:**
- Java character and arrays docs: https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/lang/package-summary.html

## 14) Behavioral and Product Questions

### Q51. Why âmost exciting projectâ and âmistake you madeâ questions?
**Interview purpose:** Assess ownership, reflection, and execution maturity.

**Strong answer should include:**
- Situation, constraints, action, measurable result.
- Explicit mistake and correction loop.
- Preventive mechanism introduced afterward.

**Official references:**
- STAR method (official careers guidance example): https://nationalcareers.service.gov.uk/careers-advice/interview-advice/the-star-method

### Q52. How to answer âWhy Bolt?â effectively?
**Interview purpose:** Evaluate motivation depth and role alignment.

**Strong answer should include:**
- Product and mission alignment.
- Specific technical fit (mobility scale, real-time systems).
- Why your prior work maps to team needs.

**Official references:**
- Bolt company engineering/product pages (for role-specific prep).

### Q53. How do product-thinking questions differ from system design questions?
**Interview purpose:** Distinguish business impact thinking from technical architecture.

**Strong answer should include:**
- Problem framing with user segments.
- KPI definition and success metrics.
- MVP scope and experiment plan.
- Then architecture sized to MVP risk.

**Official references:**
- Google HEART framework (UX metrics): https://research.google/pubs/the-heart-framework-for-evaluating-ux/

## 15) Senior-Level âPerfect Answerâ Checklist

### Q54. What elevates an answer from mid-level to senior in these topics?
**Interview purpose:** Evaluate engineering judgment under ambiguity.

**Strong answer should include:**
- Explicit assumptions and constraints.
- Failure mode handling.
- Data model and state transitions.
- Latency/scale/cost tradeoffs.
- Monitoring, alerting, and rollout strategy.
- Security and abuse considerations.

**Official references:**
- Android quality/performance guidance: https://developer.android.com/topic/performance
- SRE principles: https://sre.google/

### Q55. What mistakes should be avoided in interviews based on this content set?
**Interview purpose:** Identify risk signals quickly.

**Strong answer should include avoiding:**
- Only happy-path design.
- No complexity discussion.
- Ignoring lifecycle/memory/threading details.
- No idempotency plan for retries.
- No observability plan.

**Official references:**
- Android app quality best practices: https://developer.android.com/docs/quality-guidelines

## Quick Revision Grid (One-Page)

### Q56. If you have 10 minutes before interview, what should you revise?
**Interview purpose:** Fast signal extraction under time pressure.

**Strong answer should include:**
- Android lifecycle + ViewModel + WorkManager.
- Looper/Handler basics.
- Memory leaks + bitmap/OOM mitigation.
- Ride matching race condition solution.
- Real-time location pipeline and reconnect strategy.
- 2 coding templates: hash-frequency and sliding window.

**Official references:**
- Android architecture: https://developer.android.com/topic/architecture
- WorkManager: https://developer.android.com/topic/libraries/architecture/workmanager
- Memory: https://developer.android.com/topic/performance/memory
- Location: https://developer.android.com/develop/sensors-and-location/location

---

## Notes on Deduplication
- Repeated questions from multiple PDFs were merged into one canonical Q&A.
- Similar system-design variants (ride matching, real-time location, offline sync) were normalized to avoid overlap.
- Duplicate Android fundamentals (MVVM, lifecycle, collections, threading, memory leaks) were consolidated.
