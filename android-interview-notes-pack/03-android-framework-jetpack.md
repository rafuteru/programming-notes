# 03 — Android Framework and Jetpack Interview Notes

## Android application fundamentals

Android apps are built from app components. The four major component types are:
- Activity
- Service
- BroadcastReceiver
- ContentProvider

**Interview framing:** each component is an entry point through which the system or the user can enter the app.

## Activity

### What is an Activity?
An Activity usually acts as a host for UI and navigation entry points.

### Core lifecycle callbacks
- `onCreate()`
- `onStart()`
- `onResume()`
- `onPause()`
- `onStop()`
- `onDestroy()`

### Practical lifecycle explanation
- `onCreate`: initialize state, inflate UI, wire dependencies
- `onStart`: UI becomes visible
- `onResume`: user can interact
- `onPause`: losing focus; keep it short
- `onStop`: no longer visible
- `onDestroy`: final cleanup, but do not rely on it always being called for critical persistence

### Configuration change vs process death
**Configuration change**
- Activity is recreated, often due to rotation, locale, UI mode, etc.
- ViewModel survives, Activity instance does not.

**Process death**
- whole app process is killed
- ViewModel is gone
- restore from disk, database, network cache, or saved state as needed

This is one of the most important senior-level distinctions.

## Fragment

### What is a Fragment?
A Fragment is a reusable portion of UI and behavior that must be hosted by an Activity or another Fragment.

### Important lifecycle nuance
A Fragment has:
- its own lifecycle
- a separate **view lifecycle**

That means view references should be cleared in `onDestroyView`, not `onDestroy`, in a View-based Fragment.

### Why default constructor and arguments bundle are preferred
The system may recreate fragments. Constructor parameters outside the default recreation path can break restoration, so stable arguments should go through fragment arguments / navigation arguments.

## FragmentManager and back stack
`FragmentManager` performs fragment transactions such as add, replace, remove, and back stack operations.

**Interview answer tip:**  
Explain both what it does and why incorrect use leads to state loss bugs.

---

## Intents
Intents are messaging objects used to request an action from another app component.

### Explicit intent
Targets a specific class.
Use for:
- opening a screen inside your app
- starting a known service

### Implicit intent
Describes an action to perform.
Use for:
- share text
- open URL
- capture image via another capable app

---

## Tasks and back stack
A task is a collection of activities the user interacts with when performing a job.
The back stack determines what happens when the user presses Back.

Typical interview areas:
- launch modes
- `singleTop`, `singleTask` at concept level
- deep links
- task affinity
- why back stack bugs feel “random” when lifecycle is misunderstood

---

## Services and background execution
At high level:
- Services are for work the system should know is ongoing, especially foreground work with user awareness.
- For deferrable persistent background tasks, modern Android guidance usually points to WorkManager instead of raw background services.

---

## BroadcastReceiver
Used to respond to system-wide or app-wide broadcast events.
Examples:
- boot completed
- connectivity-related events (subject to platform limits)
- custom app broadcasts

Be ready to mention that modern Android imposes restrictions on many implicit broadcasts for performance and privacy reasons.

---

## ContentProvider
A structured interface for sharing app data across processes.
Interview answer:
- not every app needs to implement one
- useful when exposing data to other apps or for framework integrations
- supports CRUD through URIs

---

## Main thread, ANR, and responsiveness
Android uses a single main thread model for UI operations.
If you block the main thread with long work:
- frames are missed
- UI feels janky
- eventually you can get ANR

Strong answer:
- UI work on Main
- blocking I/O on IO
- CPU-heavy work on Default
- use profiling and tracing rather than guessing

---

## Jetpack essentials

## ViewModel
A ViewModel is a screen-level state holder that exposes UI state and handles related logic.
Key benefit:
- survives configuration changes

What it should do:
- hold UI state
- coordinate use cases / repositories
- expose observable state
- handle user actions

What it should not do:
- hold direct references to Views
- do rendering work
- become a god object

## LiveData
Lifecycle-aware observable holder.
Still common in legacy apps, but many new apps prefer Flow / StateFlow.

## SavedStateHandle
Use for small UI-related state that should survive process recreation for that screen, such as:
- current query
- selected tab
- filter arguments

It is not a replacement for database persistence.

## Room
Room is an abstraction layer over SQLite.

### Main pieces
- `@Entity`
- `@Dao`
- `@Database`

### Why use it
- compile-time query validation
- easier mapping than raw SQLite
- good integration with coroutines and Flow
- clean local persistence for offline-first patterns

### Good interview answer
Use Room when the app has non-trivial structured local data, especially for caching and offline use.

## WorkManager
WorkManager is for deferrable asynchronous work that must run reliably, even across app exits or device restarts.

### Typical use cases
- sync
- uploads
- cleanup
- log submission
- periodic refresh within platform constraints

### Why interviewers ask it
They want to know you can choose the right background API:
- immediate UI-bound work -> coroutine in lifecycle scope
- user-visible long-running work -> often foreground service
- reliable deferred work -> WorkManager

## Navigation
Jetpack Navigation gives structured in-app navigation.
Know these concepts:
- navigation graph
- destinations
- safe args / typed arguments patterns
- deep links
- back stack integration

## DataStore
Modern key-value or typed storage replacement for many SharedPreferences use cases.
Good answer:
- prefer DataStore for async, consistent, coroutine-friendly preference storage in modern apps

## Modularization
Multi-module Android apps improve:
- build isolation
- ownership boundaries
- scalability
- testability
- feature separation

Tradeoff:
- too many modules too early can slow teams down

---

## Strong interview questions and answers

### Why does ViewModel survive rotation but not process death?
Because ViewModel is retained across configuration-driven Activity recreation inside the same process. If the process is killed, its memory is gone.

### When would you choose Room?
When you need structured local persistence, queryable data, offline support, and compile-time checked SQL access patterns.

### Why WorkManager instead of a Service?
Because WorkManager is designed for deferrable, guaranteed background work and handles scheduling across app restarts and system constraints more reliably for those use cases.

### Why is `onPause()` not the right place for heavy work?
Because it should execute quickly; blocking it slows transitions and hurts responsiveness.

## Official references
- App components fundamentals: https://developer.android.com/guide/components/fundamentals
- Activity lifecycle: https://developer.android.com/guide/components/activities/activity-lifecycle
- Fragments guide: https://developer.android.com/guide/fragments
- Fragment lifecycle: https://developer.android.com/guide/fragments/lifecycle
- FragmentManager: https://developer.android.com/guide/fragments/fragmentmanager
- Processes and app lifecycle: https://developer.android.com/guide/components/activities/process-lifecycle
- ViewModel overview: https://developer.android.com/topic/libraries/architecture/viewmodel
- Create ViewModels with dependencies: https://developer.android.com/topic/libraries/architecture/viewmodel/viewmodel-factories
- Room training guide: https://developer.android.com/training/data-storage/room
- Room entities: https://developer.android.com/training/data-storage/room/defining-data
- Room DAOs: https://developer.android.com/training/data-storage/room/accessing-data
- WorkManager getting started: https://developer.android.com/develop/background-work/background-tasks/persistent/getting-started
- Task scheduling with WorkManager: https://developer.android.com/develop/background-work/background-tasks/persistent
- Android modularization guide: https://developer.android.com/topic/modularization
