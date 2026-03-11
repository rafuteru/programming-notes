# 05 — Jetpack Compose Interview Notes

## What is Jetpack Compose?
Jetpack Compose is Android’s recommended modern toolkit for building native UI. It uses a declarative approach.

## Declarative UI vs imperative UI
**Imperative UI**
- tell the UI how to change step by step

**Declarative UI**
- describe what UI should look like for a given state
- framework updates the rendered result when state changes

Interview line:
Compose fits naturally with state-driven UIs because the UI becomes a function of state.

---

## Composable functions
Composable functions describe UI.
They are annotated with `@Composable`.

Example:
```kotlin
@Composable
fun Greeting(name: String) {
    Text(text = "Hello $name")
}
```

---

## State in Compose
Compose UI updates are driven by state.

### Core idea
When state read by a composable changes, Compose can schedule recomposition for the affected parts.

### `remember`
Stores an object across recompositions in the current composition.

### `rememberSaveable`
Like `remember`, but also saves/restores eligible state across configuration changes and process recreation through saved instance mechanisms when possible.

### State hoisting
Move state up to the lowest common parent that needs to own it.

Why it matters:
- makes child composables reusable
- improves testability
- supports unidirectional data flow

Typical stateless API shape:
```kotlin
@Composable
fun SearchBox(
    query: String,
    onQueryChange: (String) -> Unit
) { /* ... */ }
```

---

## Recomposition

## What is recomposition?
Recomposition is the process of re-executing composables when observed state changes so the UI can reflect the latest state.

### Important nuance
Compose tries to recompose only what is needed, not the entire UI tree.

### Performance interview points
- keep state narrow and local where appropriate
- avoid unnecessary object recreation in hot paths
- avoid unstable parameters when possible
- use lazy lists for large collections
- measure with tools instead of guessing

---

## Side effects
Composable functions should ideally be side-effect free with respect to rendering.

Use effect APIs when you need work tied to composition.

### `LaunchedEffect`
Launches a coroutine tied to the composition lifecycle.

Use for:
- one-off load on entering composition
- collecting suspend work tied to a key
- showing snackbar from state transition

### `DisposableEffect`
Use when setup needs cleanup.
Examples:
- register / unregister listeners
- attach lifecycle observer

### `rememberCoroutineScope`
Use when you need to launch a coroutine in response to events from the UI layer, such as button clicks, while staying tied to the composition lifecycle.

### `rememberUpdatedState`
Useful when an effect should keep the latest lambda/value without restarting the effect.

### `derivedStateOf`
Creates derived state to avoid unnecessary recomputation / recomposition for expensive or frequently changing derived values.

---

## Lifecycle and Compose
Compose itself is not a replacement for Android lifecycle knowledge.

Good interview answer:
- screen business logic usually stays in ViewModel
- UI collects state using lifecycle-aware APIs where appropriate
- Compose state handles UI rendering concerns
- screen data/state often comes from Flow/StateFlow exposed by ViewModel

---

## Unidirectional data flow in Compose
State flows down.
Events flow up.

This matches Compose especially well because composables accept state and emit callbacks.

Typical setup:
- ViewModel exposes `uiState`
- Screen composable renders `uiState`
- user actions call event lambdas
- ViewModel updates state
- UI recomposes

---

## Compose and View interoperability
Yes, both systems can exist in one app.
Common migration options:
- embed Compose in View-based screens
- embed Android Views inside Compose
- migrate feature by feature

That is the realistic answer many teams need.

---

## Modifiers
Modifiers decorate or add behavior to composables.
Common uses:
- sizing
- padding
- click handling
- background
- semantics
- layout behavior

Interview point:
Modifier order matters because each modifier wraps the next.

---

## Semantics
Semantics provide meaning about UI for accessibility, testing, and tooling.

Why interviewers ask:
- they want to know you understand accessibility and testability in Compose, not just visual rendering

---

## Common interview questions and answers

### What is recomposition?
It is the re-execution of composables affected by state changes so the UI can be updated to reflect the latest state.

### `remember` vs `rememberSaveable`
`remember` survives recomposition only. `rememberSaveable` also persists eligible state through recreation using saved state support.

### Why state hoisting?
It centralizes ownership, improves reuse, makes UI stateless where appropriate, and supports unidirectional data flow.

### Can Compose and Views be used together?
Yes. Compose supports interoperability, which makes incremental migration practical.

### Why does Compose pair well with MVI/UDF?
Because state-driven declarative rendering and event callbacks map naturally to state-down/events-up architecture.

## Performance checklist
- keep composables small and focused
- hoist state correctly
- prefer immutable UI models
- use `LazyColumn` / `LazyRow` for large lists
- avoid unnecessary heavy work in composition
- use memoization only when it solves a real measured issue
- profile with tooling before “optimizing”

## Official references
- Compose overview: https://developer.android.com/compose
- Get started with Compose: https://developer.android.com/develop/ui/compose/documentation
- Thinking in Compose: https://developer.android.com/develop/ui/compose/mental-model
- State in Compose: https://developer.android.com/develop/ui/compose/state
- Compose side effects: https://developer.android.com/develop/ui/compose/side-effects
- Compose phases: https://developer.android.com/develop/ui/compose/phases
- Compose lifecycle: https://developer.android.com/develop/ui/compose/lifecycle
- Compose modifiers: https://developer.android.com/develop/ui/compose/modifiers
- Semantics in Compose: https://developer.android.com/develop/ui/compose/accessibility/semantics
- Compose UI architecture / UDF: https://developer.android.com/develop/ui/compose/architecture
