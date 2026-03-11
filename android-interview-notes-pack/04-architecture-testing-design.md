# 04 — Architecture, Design, and Testing Interview Notes

## Recommended Android architecture
A common modern Android answer is a layered architecture with:
- UI layer
- data layer
- optional domain layer

### UI layer
Responsibilities:
- render UI state
- collect user input
- forward events
- observe state from a state holder such as ViewModel

### Data layer
Responsibilities:
- repositories
- data sources (network, database, file, memory)
- business rules around data access
- mapping and caching

### Domain layer
Optional.
Useful when:
- business rules are complex
- logic is reused across multiple screens/features
- you want clearer use-case boundaries

Avoid adding a domain layer as ceremony if the app is simple.

---

## MVVM

## What it is
Model-View-ViewModel is a presentation architecture where:
- View renders state and forwards user actions
- ViewModel exposes state and coordinates logic
- Model is your domain/data representation

## Why it is popular on Android
It works well with:
- lifecycle-aware state holders
- Jetpack ViewModel
- Flow / LiveData
- Compose and View-based UIs

## Good MVVM interview answer
MVVM keeps UI controllers thin and pushes screen logic into ViewModel. That improves testability and lifecycle handling, especially when the ViewModel exposes immutable UI state and the UI only renders it.

## Common MVVM mistakes
- ViewModel directly calling Android UI widgets
- putting navigation, analytics, repository, and formatting logic all together with no boundaries
- exposing mutable state publicly
- treating ViewModel as global shared storage

---

## MVI / UDF

## What it is
MVI usually means:
- state is represented explicitly
- user actions are modeled as intents/events
- logic reduces events into new state
- state flows down, events flow up

This aligns naturally with Compose and unidirectional data flow.

### When MVI is strong
- complex state-heavy screens
- forms
- offline/retry flows
- many loading/error/content variants
- screens where state transitions need to be auditable

### Tradeoffs
- more boilerplate if done rigidly
- can be overkill for simple screens
- event modeling must be disciplined

### Recommended interview line
I use MVVM broadly, and for complex screens I often apply MVI-style unidirectional data flow inside the ViewModel so the UI consumes a single source of truth.

---

## Clean Architecture

## Practical meaning in Android
In interviews, “Clean Architecture” usually means:
- separation of concerns
- dependency direction inward
- framework details at the outer layers
- use cases depending on abstractions, not UI framework details

### Practical version, not dogmatic version
A strong answer is not “I always create 20 modules and 50 interfaces.”
A stronger answer is:
- keep business rules isolated from UI details
- keep dependencies pointing from UI toward domain/data abstractions
- add abstraction where it reduces coupling or improves testability

---

## State holders and UI state
Android guidance emphasizes state holders in the UI layer.

### Business logic state holder
Usually the ViewModel.
It should:
- process user events
- combine data sources
- expose UI state
- survive configuration changes

### UI logic state holder
Can be a smaller plain state holder closer to a widget or reusable UI component.

### Why this matters
It prevents Activities/Fragments/Composables from accumulating logic and becoming hard to test.

---

## Dependency injection
Interviewers usually care less about the framework name and more about the outcome:
- explicit dependencies
- testability
- clear ownership
- scope correctness

What to discuss:
- constructor injection first
- scoped objects
- easy test replacement with fakes
- avoiding service locator style hidden dependencies

---

## Repository pattern
A repository is a data-facing abstraction that hides where data comes from.

Good answer:
- repository coordinates local and remote data sources
- exposes clean APIs to the rest of the app
- can implement caching, merge policies, retry, and mapping

Bad answer:
- repository is just a pass-through class with no boundary value

---

## Testing

## Android testing pyramid
A scalable strategy usually prefers:
- many small unit tests
- fewer integration / component tests
- targeted UI / end-to-end tests

Reason:
- smaller tests are faster and more reliable
- broader tests provide fidelity but are slower and more brittle

## Local unit tests
Run on the local JVM.
Best for:
- pure Kotlin logic
- reducers
- mappers
- use cases
- ViewModels with mocked/fake dependencies
- repository logic when Android framework is not required

## Instrumented tests
Run on a device or emulator.
Best for:
- real Android integration
- navigation integration
- database integration
- UI behavior
- framework-dependent code

## What to test in ViewModel
- initial state
- loading state transitions
- success state
- error state
- retry behavior
- one-off events if applicable
- cancellation behavior for async work when relevant

## Testing Flows
Typical goals:
- assert emission order
- test fake upstream producers
- verify transformations
- verify loading/error/content transitions

## Fragment and UI tests
Key idea:
- test behavior, not internal implementation
- avoid brittle tests tied to exact layout internals unless necessary

## Senior-level testing answer
I bias toward unit tests for state holders, reducers, mappers, and business rules, and use integration/UI tests selectively for high-value user journeys, navigation, database wiring, and critical framework behavior.

---

## Common architecture interview questions

### MVVM vs MVI
MVVM is a broad presentation pattern centered around a ViewModel. MVI is more explicit about state, events, and reduction into a single source of truth. MVI often fits complex state-heavy screens better, while plain MVVM may be simpler for straightforward screens.

### Do you always need a domain layer?
No. Add it when business logic is complex or reused. Skip it when it only adds ceremony.

### Why immutable UI state?
It makes state transitions easier to reason about, improves predictability, and works very well with Compose and reactive streams.

### Why constructor injection?
It makes dependencies explicit, simplifies tests, and avoids hidden global lookups.

## Official references
- Guide to app architecture: https://developer.android.com/topic/architecture
- Architecture recommendations: https://developer.android.com/topic/architecture/recommendations
- UI layer: https://developer.android.com/topic/architecture/ui-layer
- State holders and UI state: https://developer.android.com/topic/architecture/ui-layer/stateholders
- UI state production: https://developer.android.com/topic/architecture/ui-layer/state-production
- Data layer: https://developer.android.com/topic/architecture/data-layer
- Fundamentals of testing Android apps: https://developer.android.com/training/testing/fundamentals
- Testing strategies and pyramid: https://developer.android.com/training/testing/fundamentals/strategies
- What to test: https://developer.android.com/training/testing/fundamentals/what-to-test
- Local unit tests: https://developer.android.com/training/testing/local-tests
- Fragment testing: https://developer.android.com/guide/fragments/test
- Testing Kotlin Flows on Android: https://developer.android.com/kotlin/flow/test
