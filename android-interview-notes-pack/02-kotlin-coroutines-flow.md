# 02 — Kotlin, Coroutines, and Flow Interview Notes

## Kotlin essentials that matter in Android interviews

### `val` vs `var`
- `val` means read-only reference.
- `var` means mutable reference.
- Prefer `val` by default because immutability reduces accidental state changes.

### Null safety
Key tools:
- nullable types: `String?`
- safe call: `?.`
- Elvis operator: `?:`
- not-null assertion: `!!` (avoid unless truly guaranteed)
- smart casts after null checks

**Interview point:** Kotlin reduces an entire class of null-related runtime bugs, but platform types from Java interop can still be unsafe.

### Data classes
Use them for immutable state and value objects.
They automatically provide:
- `equals()`
- `hashCode()`
- `toString()`
- `copy()`
- destructuring support

This makes them ideal for:
- UI state
- DTOs
- Room models (carefully, depending on your modeling style)

### Sealed classes / sealed interfaces
Best for:
- modeling UI state
- result wrappers
- one-off events
- reducer-style MVI state transitions

Example:
```kotlin
sealed interface UiState {
    data object Loading : UiState
    data class Success(val items: List<String>) : UiState
    data class Error(val message: String) : UiState
}
```

Why interviewers like this:
- exhaustive `when`
- closed set of states
- clearer than flag combinations like `isLoading`, `isError`, `data`

### Scope functions
Know the intent, not just syntax:
- `let`: transform or use nullable value
- `run`: configure + return result
- `with`: operate on receiver
- `apply`: configure object and return object
- `also`: side effects / logging and return object

**Rule:** choose the one whose return type and readability fit the use case.

---

## Coroutines

## What is a coroutine?
A coroutine is a suspendable computation. It lets you write asynchronous code in a sequential style.

### Why Android uses coroutines
On Android, long-running work on the main thread can freeze the UI and lead to ANRs. Coroutines help move work off the main thread while keeping code readable.

## Core terms

### `suspend`
A `suspend` function can pause without blocking the underlying thread and resume later.

Important interview nuance:
- `suspend` does **not** automatically mean background thread.
- Thread selection depends on the coroutine context / dispatcher.

### `launch` vs `async`
`launch`
- fire-and-forget
- returns `Job`
- use when you do not need a result

`async`
- concurrent computation with a result
- returns `Deferred<T>`
- call `await()` to get the result

**Common answer:**  
Use `launch` for actions and `async` when you truly need a returned value from concurrent work.

### `withContext`
- switches coroutine context for a block
- commonly used for main-safe APIs
- preferred when you want a result from another dispatcher in the current coroutine

Example:
```kotlin
suspend fun loadUser(repo: UserRepository): User =
    withContext(Dispatchers.IO) {
        repo.fetchUser()
    }
```

### Dispatchers
- `Dispatchers.Main`: UI work
- `Dispatchers.IO`: blocking I/O such as DB/network/file access
- `Dispatchers.Default`: CPU-intensive work
- `Dispatchers.Unconfined`: special behavior; usually not for normal app code

### Structured concurrency
Child coroutines are tied to a parent scope. This improves:
- cancellation
- error propagation
- lifecycle correctness
- resource cleanup

This is why `viewModelScope` and `lifecycleScope` are preferred to ad-hoc scopes.

### `coroutineScope` vs `supervisorScope`
`coroutineScope`
- child failure cancels siblings and the parent scope block

`supervisorScope`
- one child can fail without cancelling siblings

Use `supervisorScope` when sibling tasks are independent.

### Cancellation
Coroutines are cooperative.
This means cancellation works well when code:
- calls suspending functions
- checks `isActive`
- avoids blocking threads directly

### Exception handling
- `launch`: uncaught exceptions are thrown immediately to the parent / exception handler
- `async`: exception is stored and rethrown on `await()`

Use `CoroutineExceptionHandler` only for top-level handling, not as a replacement for normal `try/catch`.

### `runBlocking`
- blocks the current thread until completion
- useful mainly in tests and bridging code
- avoid in Android production UI code

---

## Android coroutine best practices

### Use lifecycle-aware scopes
- `viewModelScope` for screen business logic
- `lifecycleScope` for UI-bound work
- in Views/Fragments, prefer `repeatOnLifecycle` when collecting streams

### Expose suspend or Flow from lower layers
A clean pattern is:
- repositories expose `suspend` functions for one-shot work
- repositories expose `Flow<T>` for streams
- ViewModel transforms these into UI state

### Make suspend functions main-safe
If a repository does blocking I/O, it should move that work internally with the right dispatcher so callers can use it safely from the main thread.

### Avoid `GlobalScope`
Why it is bad:
- not lifecycle-aware
- hard to cancel
- easy to leak work
- bad for tests

---

## Flow

## What is Flow?
`Flow<T>` is an asynchronous stream that emits values sequentially and completes normally or with an exception.

### When to use Flow
Use Flow when data changes over time:
- Room query updates
- DataStore preferences
- polling / refresh streams
- search queries
- UI state pipelines

### Cold flow
By default, Flow is cold:
- code inside the flow builder does not run until collected
- each collector triggers the upstream again unless shared

### Common operators
- `map`
- `filter`
- `combine`
- `flatMapLatest`
- `debounce`
- `distinctUntilChanged`
- `catch`
- `onStart`

### `StateFlow`
Use for observable state.
Properties:
- always has a current value
- replays the latest value to new collectors
- good for screen UI state

Typical ViewModel pattern:
```kotlin
private val _uiState = MutableStateFlow<MyUiState>(MyUiState.Loading)
val uiState: StateFlow<MyUiState> = _uiState
```

### `SharedFlow`
Use for shared emissions to multiple collectors.
Typical uses:
- one-time events
- tick streams
- app-wide signals
- fan-out events

Interview nuance:
- SharedFlow is more general than StateFlow.
- StateFlow is a specialized state-holder flow with one current value.

### StateFlow vs LiveData
StateFlow:
- coroutine-based
- requires initial value
- works naturally with Flow operators
- lifecycle handling is explicit on Android UI side

LiveData:
- lifecycle-aware out of the box for Views/Fragments
- simpler in older View-based apps
- less powerful for stream transformations compared to Flow ecosystem

Modern recommended answer:
- prefer Flow / StateFlow for new coroutine-based code
- use LiveData mainly for legacy/interoperability reasons

### Collecting Flow safely in Android UI
For Views/Fragments:
```kotlin
viewLifecycleOwner.lifecycleScope.launch {
    viewLifecycleOwner.repeatOnLifecycle(Lifecycle.State.STARTED) {
        viewModel.uiState.collect { state ->
            render(state)
        }
    }
}
```

Why this matters:
- collection stops when UI is not visible
- avoids wasted work and lifecycle leaks

---

## Common interview questions and strong answers

### What is the difference between suspending and blocking?
A suspending function can pause the coroutine without blocking the underlying thread, allowing the thread to do other work. Blocking holds the thread itself until the operation finishes.

### `launch` vs `async` vs `withContext`
- `launch`: start coroutine, no result needed
- `async`: concurrent result needed later
- `withContext`: switch context and return result in the same coroutine

### Why is `GlobalScope` discouraged?
Because it is not tied to any lifecycle or structured parent. That makes cancellation, error handling, and testing harder.

### What happens if an exception occurs inside `async` and `await()` is never called?
The exception remains in the `Deferred`; it is normally observed when `await()` is called. In interview terms: do not use `async` unless you intend to consume its result.

### When would you choose `StateFlow` over `SharedFlow`?
Choose `StateFlow` when the stream represents current state that always has a latest value. Choose `SharedFlow` for broadcast-style events or multiple emissions that are not “the current state”.

---

## Pitfalls interviewers expect you to know
- `suspend` is not equal to background thread
- collecting Flow directly in Fragment without lifecycle handling
- using `GlobalScope`
- exposing mutable flow types publicly
- doing blocking I/O in `Dispatchers.Main`
- using one-time events incorrectly with plain `StateFlow`

## Official references
- Kotlin coroutines on Android: https://developer.android.com/kotlin/coroutines
- Coroutine best practices on Android: https://developer.android.com/kotlin/coroutines/coroutines-best-practices
- Lifecycle-aware coroutines: https://developer.android.com/topic/libraries/architecture/coroutines
- Kotlin coroutines basics: https://kotlinlang.org/docs/coroutines-basics.html
- Kotlin Flow on Android: https://developer.android.com/kotlin/flow
- StateFlow and SharedFlow: https://developer.android.com/kotlin/flow/stateflow-and-sharedflow
- Flow API reference: https://kotlinlang.org/api/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines.flow/-flow/
