# Android Architecture – MVVM vs MVI
Interview Revision Notes for Senior Android Engineers

This document explains modern Android architecture patterns used in large-scale applications such as ride-hailing platforms (Bolt, Uber, Airbnb).

---

# 1. Why Modern Architecture Is Needed

Large Android applications require:

• Predictable state  
• Separation of concerns  
• Testable code  
• Lifecycle safety  
• Scalable architecture

Modern Android apps typically use:

```
MVVM + Clean Architecture
```

or

```
MVI + Clean Architecture
```

Many large companies (Uber, Bolt, Airbnb) use **unidirectional data flow architectures** for reliability and scalability.

---

# 2. MVVM (Model – View – ViewModel)

MVVM is the **most widely used Android architecture pattern**.

It works well with:

• ViewModel  
• LiveData  
• StateFlow  
• Jetpack libraries

---

# 3. MVVM Components

## Model

Responsible for **data sources**.

Examples:

• Repository  
• API services  
• Local database

Example:

```
RideRepository
DriverRepository
LocationRepository
```

Responsibilities:

• Fetch data from network or database  
• Provide data to ViewModel

---

## View

The **UI layer**.

Examples:

```
Activity
Fragment
Jetpack Compose UI
```

Responsibilities:

• Render UI  
• Observe ViewModel state  
• Send user actions to ViewModel

---

## ViewModel

Holds **UI logic and UI state**.

Responsibilities:

• Handle business logic  
• Prepare UI data  
• Call repositories  
• Expose observable state

---

# 4. MVVM Data Flow

```
User Action
   ↓
View
   ↓
ViewModel
   ↓
Repository
   ↓
ViewModel updates state
   ↓
View observes state
```

---

# 5. MVVM Example

## ViewModel

```kotlin
class RideViewModel(
    private val rideRepository: RideRepository
) : ViewModel() {

    private val _rideState = MutableStateFlow<Ride?>(null)
    val rideState = _rideState.asStateFlow()

    fun loadRide() {
        viewModelScope.launch {
            val ride = rideRepository.getRide()
            _rideState.value = ride
        }
    }
}
```

---

## View (Fragment)

```kotlin
lifecycleScope.launchWhenStarted {
    viewModel.rideState.collect {
        renderRide(it)
    }
}
```

---

# 6. Advantages of MVVM

• Lifecycle aware  
• Works perfectly with Jetpack  
• Less boilerplate code  
• Easy ViewModel testing  
• Good separation of concerns

---

# 7. Problems with MVVM

In very complex applications:

• ViewModels can become very large  
• Multiple UI states become messy  
• Harder to track state transitions

Example states:

```
loading
error
success
empty
```

This is where **MVI architecture becomes useful**.

---

# 8. MVI (Model – View – Intent)

MVI focuses on:

```
Unidirectional data flow
Immutable state
Predictable UI behavior
```

It is inspired by **Redux architecture**.

---

# 9. MVI Core Concepts

MVI has three major components:

```
Intent
State
Reducer
```

---

# 10. MVI Architecture Flow

```
User Intent
   ↓
ViewModel processes intent
   ↓
Business logic executed
   ↓
Reducer generates new state
   ↓
UI renders new state
```

---

# 11. Unidirectional Data Flow

Data moves in one direction only.

```
View
  ↓
Intent
  ↓
ViewModel
  ↓
Reducer
  ↓
State
  ↓
View
```

Benefits:

• Predictable behavior  
• Easier debugging  
• Better state tracking

---

# 12. MVI Components

## Intent

Represents **user actions**.

Examples:

• SearchRide  
• AcceptRide  
• CancelRide  
• RefreshDrivers

Example:

```kotlin
sealed class RideIntent {
    object LoadRide : RideIntent()
    data class AcceptRide(val rideId: String) : RideIntent()
}
```

---

## State

The **single source of truth** for the UI.

Example:

```kotlin
data class RideState(
    val isLoading: Boolean = false,
    val ride: Ride? = null,
    val error: String? = null
)
```

UI renders **only from this state**.

---

## Reducer

Reducer generates **new state** from previous state.

Example:

```kotlin
fun reduce(
    oldState: RideState,
    result: RideResult
): RideState {

    return when(result) {

        is RideResult.Loading ->
            oldState.copy(isLoading = true)

        is RideResult.Success ->
            oldState.copy(
                isLoading = false,
                ride = result.ride
            )

        is RideResult.Error ->
            oldState.copy(
                isLoading = false,
                error = result.message
            )
    }
}
```

---

# 13. MVI ViewModel Example

```kotlin
class RideViewModel(
    private val repository: RideRepository
) : ViewModel() {

    private val _state = MutableStateFlow(RideState())
    val state = _state.asStateFlow()

    fun processIntent(intent: RideIntent) {

        when(intent) {

            is RideIntent.LoadRide -> loadRide()

            is RideIntent.AcceptRide -> acceptRide(intent.rideId)
        }
    }

    private fun loadRide() {

        viewModelScope.launch {

            _state.value = _state.value.copy(isLoading = true)

            val ride = repository.getRide()

            _state.value = RideState(
                ride = ride,
                isLoading = false
            )
        }
    }
}
```

---

# 14. MVI View (Fragment)

```kotlin
lifecycleScope.launchWhenStarted {

    viewModel.state.collect { state ->

        render(state)

    }

}
```

UI renders the **entire state object**.

---

# 15. MVVM vs MVI Comparison

| Feature | MVVM | MVI |
|------|------|------|
State Management | Multiple variables | Single immutable state |
Data Flow | Two-way possible | Strict unidirectional |
Complex UI Handling | Harder | Easier |
Debugging | Medium | Easy |
Boilerplate | Less | More |

---

# 16. Example in Ride-Hailing App

## Possible Intents

```
RequestRide
CancelRide
AcceptRide
UpdateDriverLocation
```

---

## Example State

```
RideState
   isLoading
   driverLocation
   rideStatus
   fare
   error
```

---

# 17. UI Rendering Example

UI changes depending on state:

| State | UI |
|-----|-----|
Loading | Show progress indicator |
Driver Assigned | Show driver details |
Ride Started | Show navigation |
Ride Completed | Show payment screen |

---

# 18. Why Large Apps Prefer MVI

Large apps prefer MVI because:

• Predictable state transitions  
• Easier debugging  
• Better state management  
• Ideal for real-time apps

Ride-hailing applications like **Bolt or Uber** often use **MVI-style state management**.

---

# 19. Senior Android Interview Questions

## Q1: Why use MVI over MVVM?

Answer:

For complex UI states MVI provides:

• Single source of truth  
• Predictable state transitions  
• Easier debugging  
• Better scalability for large apps

---

## Q2: What is Single Source of Truth?

The UI should be rendered from **one state object only**.

Example:

```
RideState
```

No UI state should exist outside it.

---

## Q3: Why immutable state?

Immutable state provides:

• Prevents accidental mutations  
• Easier debugging  
• Safer concurrency  
• Predictable UI updates

---

# 20. Real Production Architecture

Most modern Android apps combine:

```
Clean Architecture
+
MVVM or MVI
```

Example structure:

```
presentation/
   ui/
   viewmodel/
   state/

domain/
   usecase/
   model/

data/
   repository/
   api/
   database/
```

---

# 21. Interview Tip

If interviewer asks:

**"Which architecture would you choose for a ride-hailing app?"**

Strong answer:

```
MVVM + Clean Architecture
```

or

```
MVI + Clean Architecture for complex UI state management
```

Reason:

• Clear separation of layers  
• Testable code  
• Scalable architecture  
• Predictable state handling

---

# End of Notes