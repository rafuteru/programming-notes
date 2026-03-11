# 01 — Repo Topic Map and Interview Study Plan

## What this GitHub repository is useful for
The repository is best treated as a **topic map and question bank**, not as the only source of truth. It is valuable because it groups Android interview prep into practical buckets:

- Kotlin Coroutines
- Kotlin Flow API
- Kotlin
- Android
- Android Libraries
- Android Architecture
- Design Pattern
- Android System Design
- Android Unit Testing
- Android Tools and Technologies
- Jetpack Compose
- Java
- Other Topics
- Data Structures and Algorithms

That structure is a good mirror of what many senior Android interviews test:
1. language fundamentals
2. platform internals
3. architecture and tradeoffs
4. async/state management
5. persistence/network/background work
6. UI and Compose
7. testing
8. coding round / DSA

## Recommended study order

### Phase 1 — Must-have Android core
Revise these first because they appear often and connect to almost every other topic:
- Activity lifecycle
- Fragment lifecycle
- Process death vs configuration change
- ViewModel
- Saved state
- Intents, tasks, back stack
- Main thread and ANR basics
- Services / BroadcastReceiver / ContentProvider at a high level

### Phase 2 — Kotlin for production Android
Focus on what changes day-to-day code quality:
- `val` vs `var`
- null safety
- data classes
- sealed classes / sealed interfaces
- object / companion object
- extension functions
- higher-order functions
- inline / crossinline / noinline
- collections transformations
- delegation
- scope functions
- generics basics

### Phase 3 — Coroutines and Flow
This is one of the highest-signal areas for senior Android interviews:
- suspend functions
- structured concurrency
- `launch` vs `async`
- `withContext`
- dispatchers
- cancellation and exception handling
- `StateFlow` and `SharedFlow`
- collecting flows safely with lifecycle
- testing coroutine and flow code

### Phase 4 — Architecture
Know both the recommended answer and the tradeoff answer:
- recommended layered Android architecture
- MVVM
- MVI / UDF
- repository pattern
- use cases / domain layer
- state holders
- dependency injection
- modularization

### Phase 5 — Jetpack and persistence
Be ready to explain when and why:
- Room
- WorkManager
- Navigation
- Paging
- DataStore
- ViewModel / SavedStateHandle
- Retrofit / OkHttp at practical level

### Phase 6 — UI
For modern interviews, this usually means Compose first:
- declarative UI
- state and recomposition
- side effects
- lifecycle in Compose
- state hoisting
- interoperability with Views
- performance basics

### Phase 7 — Testing
Expected for senior roles:
- local unit tests
- instrumented tests
- ViewModel tests
- Flow tests
- repository tests with fakes
- UI tests
- testing pyramid and tradeoffs

### Phase 8 — Coding rounds
For Android roles, the common pattern is:
- arrays / strings / hash maps
- stacks / queues
- sliding window
- two pointers
- intervals
- recursion basics
- time and space complexity explanation
- writing readable production-style code

## How to answer in an interview

### Good answer structure
Use this 4-part format:
1. one-line definition
2. how Android uses it in practice
3. a common pitfall
4. when to choose one option over another

### Example
**Question:** What is a ViewModel?  
**Strong answer:**  
A ViewModel is a screen-level state holder that exposes UI state and handles presentation/business logic for a screen. Its main benefit is that it survives configuration changes, so the UI does not need to reload data after every rotation. In practice I keep screen state and UI events in the ViewModel and keep Activities/Fragments/Composables thin. One important caveat is that ViewModel does not protect you from process death by itself, so for essential state I combine it with persisted data or saved state.

## Senior interview lens
At senior level, interviewers usually look for:
- correct fundamentals
- explicit tradeoffs
- awareness of lifecycle and threading
- maintainability and testability
- ability to reason about failures, not just happy path
- awareness of process death, back pressure, retries, cancellation, and offline support

## What to avoid
- Memorized definitions with no production example
- Saying “MVVM because everyone uses it”
- Treating LiveData, StateFlow, SharedFlow, and Compose state as interchangeable
- Using `GlobalScope`
- Long background tasks on the main thread
- Giving architecture answers without testing strategy

## Quick revision checklist
- Can I explain configuration change vs process death?
- Can I explain `launch` vs `async` vs `withContext`?
- Can I explain `StateFlow` vs `SharedFlow` vs LiveData?
- Can I explain why ViewModel exists and what it should not do?
- Can I explain Room, WorkManager, and Navigation at practical level?
- Can I explain recomposition and state hoisting in Compose?
- Can I explain how I would test a ViewModel and a repository?
- Can I explain time and space complexity for common coding-round problems?

## Official references
- Android app architecture: https://developer.android.com/topic/architecture
- Android architecture recommendations: https://developer.android.com/topic/architecture/recommendations
- Android fundamentals and app components: https://developer.android.com/guide/components/fundamentals
- Kotlin docs home: https://kotlinlang.org/docs/home.html
