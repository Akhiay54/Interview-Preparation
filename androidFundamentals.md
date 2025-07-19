Android Senior Developer Study Guide (Complete)
This guide summarizes the key concepts covered for a developer with 3+ years of experience, focusing on interview-critical topics for Kotlin, Coroutines, and Jetpack Compose.

Part 1: Advanced Kotlin Language Features
(This section covers advanced language features, including core concepts and asynchronous programming.)

Lesson 1: Core Language Features
Beyond coroutines, a senior developer must have a deep understanding of Kotlin's powerful language constructs.

Generics (in and out):

Covariance (out): Used for producers. A List<Dog> can be treated as a List<Animal> if Dog is a subtype of Animal. List<out T> means the list produces Ts.

Contravariance (in): Used for consumers. A Comparator<Animal> can be used where a Comparator<Dog> is needed. Comparator<in T> means the comparator consumes Ts.

reified: A special keyword used with inline functions. It allows you to access the actual type T of a generic parameter at runtime, which is normally erased. This is famously used in functions like filterIsInstance<T>().

Delegation: Kotlin provides native support for the delegation pattern, reducing boilerplate code.

Property Delegation: Delegate the getter/setter logic of a property to another object. Common examples include:

by lazy { ... }: The property's value is computed only on its first access.

by Delegates.observable(...): Get notified whenever the property's value changes.

by viewModels(): A common pattern in Android for delegating ViewModel creation.

Class Delegation: A class can delegate the implementation of an interface to another object. class MyClass(b: Base) : Base by b.

Higher-Order Functions & inline:

Higher-Order Function: A function that takes another function as a parameter or returns a function (e.g., collection.filter { ... }).

inline Keyword: When used on a higher-order function, the compiler copies the code of the function and the passed lambda directly to the call site. This avoids the overhead of creating a Function object for the lambda, improving performance. It also enables non-local returns and reified type parameters.

Interview Q&A:
Q: "What does the inline keyword do in Kotlin?"
A: "The inline keyword tells the compiler to copy the function's bytecode and its lambda parameters directly to the call site. This primarily improves performance by avoiding the creation of Function objects for lambdas. It also enables two powerful features: non-local returns from within a lambda and using reified generic types to access a type at runtime."

Lesson 2: Structured Concurrency
Structured Concurrency imposes a parent-child relationship on coroutines, ensuring they have a managed lifecycle. Every coroutine lives within a CoroutineScope, which provides crucial guarantees.

Cancellation is Propagated Downwards: Cancelling the parent scope automatically cancels all its children. This is how viewModelScope prevents leaks.

Parent Waits for Children: A parent scope or coroutine will not complete until all its child coroutines have completed.

Errors are Propagated Upwards: An uncaught exception in a child will cancel its parent and, consequently, all its siblings.

Lesson 3: Coroutine Dispatchers & withContext
A CoroutineDispatcher determines the thread or thread pool a coroutine runs on.

Dispatchers.Main: The single UI thread. Used for any UI-related tasks. viewModelScope defaults to this.

Dispatchers.IO: A shared pool of background threads for I/O-bound work like networking (Retrofit) or database access (Room).

Dispatchers.Default: A thread pool sized to the number of CPU cores, for CPU-intensive work like sorting large lists or complex parsing.

withContext(Dispatcher): The idiomatic way to switch threads for a block of code. It suspends until the block is done and resumes on the original dispatcher.

Lesson 4: Coroutine Exception Handling
try-catch Block: The primary tool for handling expected, local errors (e.g., IOException from a network call).

CoroutineExceptionHandler: A context element for a global, "last-resort" catch block. Ideal for logging unexpected crashes to services like Crashlytics.

Job vs. SupervisorJob:

Job (Default): Failure of one child cancels the parent and all other children.

SupervisorJob: Failure of a child is isolated and does not cancel the parent or siblings. viewModelScope uses a SupervisorJob by default.

Lesson 5: Introduction to Kotlin Flow
A Flow is an asynchronous stream of data built on coroutines, ideal for values that arrive over time.

Flows are Cold: The producer code inside flow { ... } is lazy. It only executes when a terminal operator like .collect() is called. Each new collector gets its own, new stream.

flowOn() Operator: The idiomatic way to change the Dispatcher for the producer (upstream). It moves the work of the producer to a background thread while the consumer (downstream) remains on its original thread.

Lesson 6: Hot Flows - StateFlow & SharedFlow
Hot flows are active and emitting values regardless of whether anyone is listening.

StateFlow: A hot flow designed to hold and observe state.

Always has an initial value. Only emits the latest value to collectors (conflated).

The modern replacement for LiveData for managing UI state in a ViewModel.

SharedFlow: A hot flow designed to broadcast one-time events.

Highly configurable (e.g., replay cache).

Ideal for commands like showing a Toast, a dialog, or triggering navigation.

Part 2: Jetpack Compose
Lesson 7: The Recomposition Process
Recomposition is the process of Compose re-running @Composable functions when their input state changes to update the UI.

Three Phases: Composition (What to show?), Layout (Where to place?), Drawing (How to render?).

Smart Recomposition: Compose intelligently skips recomposing composables whose inputs have not changed.

Stability: This optimization relies on stable inputs. The best way to ensure this is to use an immutable data class with val properties for state.

Lesson 8: State Management & Hoisting
remember: Stores an object in memory to survive recompositions. It is lost if the Activity is recreated.

rememberSaveable: Stores an object and also saves it to the Bundle, so it survives Activity recreation (e.g., screen rotation).

State Hoisting: The core pattern of making a composable stateless by moving its state up to its caller. This creates a Unidirectional Data Flow (UDF).

Lesson 9: Side Effects in Compose
A side effect is any work that happens outside the scope of a composable. Compose provides handlers to run them in a lifecycle-aware way.

LaunchedEffect: Launches a coroutine when the composable enters the composition. It's cancelled on exit. Perfect for initial data fetching.

rememberCoroutineScope: Provides a CoroutineScope that can be used to manually launch coroutines from event handlers like onClick.

DisposableEffect: Used for effects that need cleanup. Its mandatory onDispose block is guaranteed to run on exit, making it perfect for registering/unregistering listeners or observers.

Lesson 10: Compose Navigation & Animation
Navigation: Jetpack Compose Navigation provides a framework for navigating between composables.

NavHost: A composable that defines a navigation graph. It hosts the composables that represent the screens in your app.

NavController: The central API for navigation. You use it to trigger navigation actions with navController.navigate("route"). It's typically created with rememberNavController().

Routes: Routes are simple String paths that uniquely identify a destination (e.g., "profile/{userId}"). You can pass arguments as part of the route.

Animation: Compose has a powerful, built-in animation system.

State-based animations: The simplest form. Use functions like animateColorAsState, animateDpAsState to animate a value when its target state changes.

AnimatedVisibility: A composable that animates the appearance and disappearance of its content (e.g., fade in, slide out).

animateContentSize: A modifier that smoothly animates a composable's size when its content changes.

Lesson 11: Customization & Interoperability
Customization: The primary way to customize UI in Compose is by creating small, reusable, and often stateless composables. By following the State Hoisting pattern (Lesson 8), you can build a library of custom components.

Interoperability (Interop):

Using XML Views in Compose: Use the AndroidView composable to embed a traditional Android View (like a MapView) inside your Compose UI.

Using Compose in XML Layouts: Use the ComposeView class in your XML file. You can then call setContent on it from your Activity/Fragment.

Part 3: Core Android Fundamentals
Lesson 12: Activity & Fragment Lifecycles
Understanding component lifecycles is non-negotiable. It's how you manage resources, save state, and avoid crashes.

Activity Lifecycle: onCreate() -> onStart() -> onResume() -> onPause() -> onStop() -> onDestroy().

Fragment Lifecycle: More complex, with a separate View lifecycle: onAttach() -> onCreate() -> onCreateView() -> onViewCreated() -> onStart() -> onResume() -> onPause() -> onStop() -> onDestroyView() -> onDestroy() -> onDetach().

ViewModel & Lifecycles: A ViewModel survives configuration changes (like screen rotation) that destroy and recreate an Activity, making it the ideal place to store screen state.

Lesson 13: Android App Components
Service: For long-running background tasks without a UI.

Foreground Service: Has a persistent notification. For user-aware tasks like music playback.

Bound Service: Offers a client-server interface for components to interact with it.

WorkManager: The modern, recommended solution for most deferrable, guaranteed background work.

BroadcastReceiver: For handling system-wide or app-specific events.

ContentProvider: For sharing a specific set of application data with other applications securely.

Lesson 14: Memory Management, Leaks, and Context
Understanding Context:

Application Context: Tied to the application lifecycle. Safe for long-lived objects.

Activity Context: Tied to the Activity lifecycle. Leaks if held by a long-lived object.

Common Causes of Leaks: Static references to Activity context, unregistered listeners, and non-static inner classes.

Tools: Use the Android Studio Profiler and LeakCanary to detect and analyze memory leaks.

Part 4: System Design & Architecture
Lesson 15: The Data Layer: Networking with Retrofit
Retrofit: A type-safe HTTP client that turns your HTTP API into a Kotlin interface.

Interceptors: A powerful mechanism from OkHttp to modify requests and responses. Used for logging (HttpLoggingInterceptor), adding headers (auth tokens), and global error handling.

Lesson 16: The Data Layer: Persistence with Room
Room: An abstraction layer over SQLite that provides compile-time verification of SQL queries.

Core Components: @Entity (table), @Dao (queries), @Database (the main database class).

Reactive Updates: DAOs can return a Flow to automatically emit new data when the underlying table changes.

Lesson 17: Architectural Patterns & DI
MVVM (Model-View-ViewModel): The View observes data from the ViewModel. The ViewModel handles logic and gets data from the Model (Repository).

MVI (Model-View-Intent): A reactive, cyclical pattern. The View emits Intents (user actions). The ViewModel processes these, updates its State, and the View renders the new State.

Dependency Injection (DI):

Why use DI?: Decouples components, making them easier to test and reuse.

Hilt: Google's compile-time, annotation-based DI framework for Android.

Koin: A popular runtime DI framework that is often simpler to set up.

Lesson 18: SOLID Principles in Android
S - Single Responsibility Principle: A class should have only one reason to change. (e.g., A UserAuthRepository should only handle auth, not user profiles).

O - Open/Closed Principle: Software entities should be open for extension, but closed for modification. (e.g., Use interfaces for a Repository so you can provide a fake implementation for tests without changing the original).

L - Liskov Substitution Principle: Subtypes must be substitutable for their base types. (e.g., If you have a PremiumUser that extends User, it must not throw an exception when a method that works on User is called).

I - Interface Segregation Principle: Clients should not be forced to depend on interfaces they do not use. (e.g., Create separate DataReader and DataWriter interfaces instead of one large DataAccess interface).

D - Dependency Inversion Principle: High-level modules should not depend on low-level modules. Both should depend on abstractions. (e.g., A ViewModel should depend on a UserRepository interface, not the concrete UserRepositoryImpl class).

Lesson 19: Modularization
Why Modularize?: Faster build times (Gradle only rebuilds changed modules), better code ownership, enforced separation of concerns, and potential for dynamic feature delivery.

Strategies:

By Layer: :app, :data, :domain, :ui. Enforces architectural boundaries.

By Feature: :app, :feature_login, :feature_profile, :core_networking. Scales well for large teams and apps.

Part 5: Kotlin Multiplatform (KMP)
Lesson 20: Core KMP Concepts
expect/actual: The core mechanism for defining common APIs in shared code and providing platform-specific implementations.

expect: Declared in commonMain. It's a declaration without an implementation, like an abstract method.

actual: The platform-specific implementation of the expect declaration, provided in androidMain and iosMain.

Source Sets: The directory structure that enables code sharing. commonMain contains shared Kotlin code. androidMain and iosMain contain platform-specific actual implementations and code that uses native SDKs.

Concurrency: While Coroutines work out-of-the-box in KMP, the main challenge is ensuring UI updates happen on the main thread on both platforms. Libraries like Ktor handle this for networking, but for your own logic, you often need an expect/actual dispatcher provider.

Key Libraries: Ktor for multiplatform networking and SQLDelight for multiplatform databases are the de-facto standards.

Part 6: Testing
Lesson 21: The Testing Pyramid
A strategy for balancing different types of tests.

Unit Tests (70%): Small, fast tests for individual classes or functions (e.g., a ViewModel's logic, a utility function). They run on the local JVM.

Integration Tests (20%): Test how multiple components work together (e.g., writing to a Room database and reading the result, navigating from one screen to another). Run on an emulator/device.

End-to-End (E2E) Tests (10%): Test a full user flow through the UI. They are slow and brittle but valuable for critical paths. Run on an emulator/device.

Lesson 22: Testing Tools & Techniques
Unit Testing:

Tools: JUnit4/5, Truth (for assertions), MockK/Mockito (for creating mocks).

Testing Coroutines: Use runTest from kotlinx-coroutines-test. It provides a special dispatcher that gives you control over time for testing delay and Flow emissions.

UI Testing:

Jetpack Compose: Use createComposeRule. You can find nodes (onNodeWithText), perform actions (performClick), and make assertions (assertIsDisplayed).

View System (XML): Use Espresso. It provides APIs to find Views (onView(withId(...))), perform actions (perform(click())), and check states (check(matches(isDisplayed()))).

Part 7: Build & Performance
Lesson 23: Build Optimization
Gradle:

Build Cache: Reuses outputs from previous builds. Enable it in gradle.properties.

Configuration Cache: Speeds up the configuration phase by caching the result of the task graph.

Parallel Execution: Allows Gradle to run tasks in different modules concurrently.

App Size:

R8/ProGuard: Shrinks, obfuscates, and optimizes your code by removing unused classes/methods and renaming remaining ones.

App Bundles (.aab): The modern publishing format. You upload a bundle to Google Play, and it generates optimized APKs for each user's specific device configuration (ABI, screen density, language).

Lesson 24: Runtime Performance
Profiling: Use the Android Studio Profiler to find and fix performance bottlenecks.

CPU Profiler: To find slow methods and UI jank (dropped frames). Use System Trace to see what's happening across all threads.

Memory Profiler: To find memory leaks and analyze memory churn.

Baseline Profiles: A file included in your app that tells the Android Runtime which classes and methods to pre-compile ahead-of-time (AOT). This significantly improves performance for critical user journeys, reducing app startup time and jank.

Part 8: Final Topics
Lesson 25: Clean Code, DS&A, and Continuous Learning
Clean Code: Refers to writing code that is readable, maintainable, and easy to understand. Follow principles like meaningful naming, small functions, and clear comments (when necessary).

Data Structures & Algorithms: While not an everyday task, understanding core concepts (Big O notation, HashMap vs. Array, basic sorting algorithms) is crucial for passing interviews at product-based companies and for writing efficient code.

Continuous Learning: The Android ecosystem evolves rapidly. Stay updated by following the official Android Developers blog, watching talks from conferences like Google I/O and Droidcon, and reading community publications.






This document provides detailed answers to common and scenario-based interview questions covering Kotlin, Jetpack Compose, and Core Android Fundamentals.

Part 1: Kotlin (Core Language & Coroutines/Flow)
Beginner / Intermediate Questions
1. Q: What is the difference between val, var, const val, and a property with a get() method?

A:

var (variable): A mutable property. Its value can be reassigned after initialization.

val (value): An immutable property. It must be assigned a value when it is declared, and it cannot be reassigned.

const val (compile-time constant): A more restrictive version of val. The value must be known at compile time and is inlined by the compiler.

Property with get(): This defines a property whose value is computed every time it is accessed. It doesn't have a backing field.

2. Q: Explain Kotlin's null safety system. What are the ?. (safe call) and ?: (elvis) operators?

A:
Kotlin's type system distinguishes between nullable (String?) and non-nullable (String) types to prevent NullPointerExceptions at compile time.

?. (Safe Call Operator): Executes a call only if the object is not null; otherwise, it returns null.

?: (Elvis Operator): Provides a default value if the expression on its left side is null.

3. Q: What is the purpose of the inline keyword? What are two major benefits it provides besides performance?

A:
The inline The keyword tells the compiler to copy the function's bytecode and its lambda parameters directly to the call site. Its two most powerful benefits are:

Non-local returns: Allows a return statement inside a lambda to exit the enclosing function.

reified type parameters: Allows a generic type parameter's actual type to be accessed at runtime (e.g., for is T checks).

4. Q: What is property delegation? Give a practical example using by lazy.

A:
Property delegation is a pattern where the getter/setter logic for a property is delegated to another object. by lazy { ... } is a standard delegate that ensures a property's value is computed only on its first access and cached for future use. It's ideal for expensive, deferred initialization.

5. Q: What is a coroutine? How is it different from a standard Thread?

A:
A coroutine is a "lightweight thread" that can be suspended and resumed. Unlike threads, which are heavyweight and managed by the OS, coroutines are managed by the Kotlin runtime and are very cheap to create. Their key feature is suspension, which allows them to pause without blocking the underlying thread.

6. Q: What is Structured Concurrency? What three guarantees does it provide?

A:
Structured Concurrency is a model that treats coroutines as a hierarchy within a CoroutineScope. It guarantees:

Cancellation is Propagated Downwards: Cancelling the scope cancels all children.

Parent Waits for Children: A parent scope won't complete until all its children do.

Errors are Propagated Upwards: A child's failure cancels its parent and siblings.

7. Q: Explain the difference between Dispatchers.IO, Dispatchers.Main, and Dispatchers.Default.

A:

Dispatchers.Main: The main UI thread. For all UI-related tasks.

Dispatchers.IO: A background thread pool for I/O-bound operations (networking, disk access).

Dispatchers.Default: A background thread pool for CPU-intensive work (complex calculations, sorting large lists).

8. Q: What is the difference between launch and async?

A:

launch: "Fire and forget." Starts a coroutine that doesn't return a result. Returns a Job.

async: Starts a coroutine that computes a result. Returns a Deferred<T>, from which you get the result by calling .await(). Use it for parallel decomposition.

9. Q: What does it mean for a Flow to be "cold"? How is this different from a "hot" stream?

A:

Cold Flow (Flow): Is lazy. The producer code only runs when a terminal operator like .collect() is called. Each new collector gets a new, independent stream.

Hot Flow (StateFlow, SharedFlow): Is active regardless of collectors. It broadcasts values to all current listeners simultaneously.

10. Q: Compare and contrast StateFlow and SharedFlow.

A:

StateFlow: Represents state. It always has a value, replays the latest value to new subscribers, and is conflated. Ideal for ViewModel UI state.

SharedFlow: Represents events. It's a general-purpose broadcast stream with no initial value by default. Ideal for one-time events like showing a Snackbar or navigation.

Advanced / Scenario-Based Questions
(Answers for this section are in Part 4)

Part 2: Jetpack Compose
Beginner / Intermediate Questions
17. Q: What is recomposition? What triggers it?

A:
Recomposition is the process of Jetpack Compose re-running your @Composable functions to update the UI. It is triggered whenever a state object that a composable reads from, changes.

18. Q: What is the most important thing you can do to prevent unnecessary recompositions?

A:
Use stable data types for state and parameters, with immutability being the ideal goal. The best practice is to model UI state using Kotlin's data class with val properties for all its fields.

19. Q: Explain state hoisting and why it is the recommended pattern in Compose.

A:
State hoisting is a pattern where you make a composable stateless by moving its state up to its caller. This creates a Unidirectional Data Flow (UDF) and makes components reusable, testable, and establishes a single source of truth.

20. Q: What is the difference between remember and rememberSaveable?

A:

remember: Survives recompositions but is lost if the Activity is recreated.

rememberSaveable: Survives both recomposition and Activity/process recreation by saving the state to the Bundle.

21. Q: What are the three phases of rendering a Compose frame?

A:

Composition: Running @Composable functions to build a UI description.

Layout: Measuring and placing UI elements.

Drawing: Rendering the elements onto the canvas.

22. Q: Explain the difference between LaunchedEffect, rememberCoroutineScope, and DisposableEffect.

A:

LaunchedEffect: Runs a suspend block tied to the composable's lifecycle. For "on-enter" logic like fetching data.

rememberCoroutineScope: Provides a CoroutineScope to launch coroutines manually from event handlers like onClick.

DisposableEffect: For side effects that require cleanup. Its onDispose block is guaranteed to run on exit. For registering/unregistering listeners.

23. Q: How do you use a traditional Android View inside a composable function?

A:
You use the AndroidView composable, which acts as a bridge. You provide a factory lambda to create the View and an update lambda to update it based on state changes.

Advanced / Scenario-Based Questions
(Answers for this section are in Part 4)

Part 3: Core Android Fundamentals
Beginner / Intermediate Questions
28. Q: Describe the Activity lifecycle. What happens when a user rotates the screen?

A:
The standard lifecycle is onCreate() -> onStart() -> onResume() -> onPause() -> onStop() -> onDestroy(). On screen rotation, the Activity is destroyed and recreated: onPause() -> onSaveInstanceState() -> onStop() -> onDestroy(), followed by onCreate() -> onStart() -> onRestoreInstanceState() -> onResume() for the new instance.

29. Q: What is the difference between onCreateView() and onViewCreated() in a Fragment?

A:

onCreateView(): You must inflate and return the Fragment's layout here.

onViewCreated(): Called immediately after onCreateView(). This is the safe place to interact with the created view hierarchy.

30. Q: What is a memory leak? Give a classic example of how an Activity can be leaked.

A:
A memory leak occurs when an object is no longer needed but cannot be garbage collected because another object holds a reference to it. A classic example is a static field holding a reference to an Activity context.

31. Q: What is the difference between an Application Context and an Activity Context?

A:

Activity Context: Tied to the Activity lifecycle. Use for UI operations.

Application Context: Tied to the application lifecycle. Use for long-lived objects or singletons to avoid leaking Activities.

32. Q: Compare and contrast Service, Foreground Service, and WorkManager.

A:

Service: Base component for background work. Heavily restricted in modern Android.

Foreground Service: A Service for user-aware tasks (e.g., music). Must show a persistent notification.

WorkManager: The modern, recommended library for deferrable and guaranteed background work. It's constraint-aware and battery-efficient.

33. Q: What is an OkHttp Interceptor and what are two common use cases for it?

A:
An Interceptor is a mechanism to observe, modify, or short-circuit requests and responses. Common uses are:

Logging: Using HttpLoggingInterceptor for debugging.

Adding Headers: Adding an Authorization token to every request.

34. Q: What are the three main annotations used in the Room persistence library?

A:

@Entity: Marks a data class as a database table.

@Dao: Marks an interface as a Data Access Object for defining queries.

@Database: Marks an abstract class as the main database holder.

Advanced / Scenario-Based Questions
(Answers for this section are in Part 4)

Part 4: Advanced / Tough Interview Questions & Answers
Advanced Kotlin: Coroutines & Flow
1. Q: Imagine a CoroutineScope where you launch a parent Job (jobA). Inside jobA, you launch two child jobs, jobB and jobC. If jobC is launched within a supervisorScope { ... } block and it fails with an exception, what is the final state of jobA, jobB, and jobC?

A:

jobC: Fails. The exception is thrown.

jobB: Continues running and completes normally.

jobA: Continues running and completes normally.

Explanation: The supervisorScope creates an exception boundary. The failure of jobC is caught by the supervisorScope's SupervisorJob, which handles the exception but does not propagate it upwards to its parent (jobA). Therefore, jobA and its other child, jobB, are completely unaffected by jobC's failure.

2. Q: Why can't a CoroutineExceptionHandler catch a CancellationException?

A:
A CoroutineExceptionHandler is designed to handle unexpected, terminal failures. CancellationException is not considered an error; it's a normal, expected signal that a coroutine was cancelled. The coroutine machinery uses it for cleanup and to stop execution. If the handler caught it, it would interfere with the fundamental mechanism of structured concurrency and cancellation.

3. Q: What were the fundamental problems with BroadcastChannel that SharedFlow was designed to solve?

A:
BroadcastChannel had two major issues:

Terminal Failure: If the producer coroutine failed or closed the channel with an exception, all consumers would immediately fail with the same exception. There was no way to recover. SharedFlow is not tied to a specific scope and does not have a "terminal" state.

No Backpressure Support: It was a hot stream that would either suspend the sender or drop values if the buffer was full, but it had no mechanism for the collector to signal that it was overwhelmed. SharedFlow is integrated with the Flow ecosystem and respects backpressure from downstream collectors.

4. Q: How would you implement a custom Flow operator called debounceUntilChanged(timeout: Long)?

A:
This is a complex operator. A good approach would be to use transformLatest. This operator cancels the previous block's execution as soon as a new value arrives. We can use this to our advantage.

fun <T> Flow<T>.debounceUntilChanged(timeout: Long): Flow<T> = transformLatest { value ->
    emit(value) // Emit the first value immediately
    delay(timeout) // Ignore subsequent values for the timeout duration
}

When a new value arrives in the upstream flow, transformLatest cancels the previous delay and starts a new block, emitting the new value and starting a new delay.

5. Q: Explain backpressure in Flow and the strategies offered by buffer(), conflate(), and collectLatest().

A:
Backpressure is when a consumer is slower than the producer. Flow is sequential by default, so the producer waits for the consumer. These operators change that:

buffer(): Runs the producer concurrently in a separate coroutine, storing emitted values in a buffer (channel) so the producer doesn't have to wait.

conflate(): A form of buffering that only stores the most recent value, dropping intermediate ones if the consumer is slow.

collectLatest(): If a new value arrives while the previous one is still being processed by the collector's block, it cancels the processing of the old value and restarts the block with the new value.

6. Q: You need to wrap a callback-based API into a Flow. What is callbackFlow and how does awaitClose work?

A:
callbackFlow is a flow builder specifically designed for this purpose. It allows you to emit values from outside the coroutine (from the callback).

awaitClose is a mandatory suspending block that you put at the end of your callbackFlow builder. The code inside awaitClose is executed when the flow's consumer is cancelled. This is the perfect place to unregister the listener or callback to prevent memory leaks.

7. Q: Describe a scenario for a Mutex vs. a Semaphore.

A:

Mutex (Mutual Exclusion): Use when you need to protect a critical section of code to ensure only one coroutine can execute it at a time. Scenario: A function that updates a shared counter variable. mutex.withLock { counter++ } prevents race conditions.

Semaphore: Use when you want to limit the number of concurrent coroutines accessing a resource. Scenario: You are downloading images from a server that only allows 3 concurrent connections. You would create a Semaphore(permits = 3) and have each download coroutine call semaphore.withPermit { ... }.

8. Q: What happens if you call two terminal operators on the same Flow instance?

A:
Because Flow is cold, each terminal operator starts a new, independent execution of the producer code. If you have myFlow.first() followed by myFlow.toList(), the producer will run once to get the first element, and then it will run again from the beginning to collect all elements for the list.

9. Q: A StateFlow is updated with A, B, and C. A slow collector is processing A. What values will it receive?

A:
The collector is only guaranteed to receive the final value, C. StateFlow is conflated, meaning it only cares about delivering the most recent state. While the collector is busy with A, values B and C will update the state, but by the time the collector is ready for the next value, only the latest one (C) is relevant.

10. Q: How would you use the select expression to react to the first of three Flows?

A:
The select expression allows you to await multiple suspending operations and choose the first one that completes. You can use it with the onEach operator on each flow and the first() terminal operator.

select<Unit> {
    flow1.onEach { value -> println("Flow 1 wins with $value") }.first()
    flow2.onEach { value -> println("Flow 2 wins with $value") }.first()
    flow3.onEach { value -> println("Flow 3 wins with $value") }.first()
}

This will launch all three, and as soon as one of them emits a value, its onEach block will execute, the select will complete, and the other two flow collections will be cancelled.

Advanced Jetpack Compose
1. Q: What is a CompositionLocal and when is it a better solution than parameter passing?

A:
A CompositionLocal is a tool for passing data implicitly down through the composition tree without having to pass it as an explicit parameter at every level. It's best used for broad, tree-wide concerns. Use Case: Material Theming. MaterialTheme.colors, MaterialTheme.typography, etc., are provided via CompositionLocal. It would be impractical to pass the Colors object to every single composable. The downside of overusing it is that it creates implicit dependencies, making code harder to reason about.

2. Q: What is SubcomposeLayout and what problem does it solve?

A:
SubcomposeLayout is a low-level layout primitive that allows you to measure and lay out children during the layout phase of a parent, not just during the composition phase. This is useful when the measurement of one child depends on the measurement of another. Example: Scaffold. It needs to measure the content of the screen first to know where to place the FloatingActionButton (e.g., to avoid the BottomAppBar).

3. Q: Is data class User(val id: String, val friends: List<User>) stable for Compose? Why or why not?

A:
No, it is not considered stable. While id is a stable String, the standard Kotlin List is a mutable interface. The Compose compiler cannot guarantee that the contents of the list won't change, so it marks the User class as unstable to be safe. To fix this, you should use an explicitly immutable collection, like ImmutableList from the kotlinx.collections.immutable library.

4. Q: How does the Modifier.Node API improve performance over older stateful modifier approaches?

A:
The Modifier.Node system creates a dedicated tree of Modifier.Node instances that parallels the layout node tree. This is more performant because:

It avoids excessive object allocations. The nodes can be reused and have their state changed directly.

It avoids re-running complex recomposition scopes. Changes can be localized to the specific node, bypassing the need for recomposition of the composable that applied the modifier.

5. Q: When a state variable read by a Column's content lambda changes, does the Column function itself re-execute, or only its content lambda?

A:
Only the content lambda re-executes. Compose is highly optimized. It doesn't re-run the entire Column function. It identifies the smallest "recomposition scope" that reads the changed state—in this case, the content block—and only re-invokes that specific part of the code.

6. Q: derivedStateOf vs. remember(key). Which is more efficient for showing a "Scroll to Top" button after scrolling past 5 items, and why?

A:
derivedStateOf is more efficient.

remember(lazyListState.firstVisibleItemIndex) would re-calculate its value on every single scroll event where the index changes.

derivedStateOf { lazyListState.firstVisibleItemIndex > 5 } is smarter. It only triggers a recomposition of its readers when the result of the calculation changes (i.e., when the value flips from false to true at item 6, and from true to false at item 5). It avoids recomposition for all the intermediate scroll events.

7. Q: What are intrinsic measurements in the Compose Layout phase?

A:
Intrinsic measurements allow a parent to measure its children before committing to a final size. It's a way for a child to tell its parent "here's how big I'd like to be." minIntrinsicWidth, for example, asks the child for the minimum width at which it can correctly display its content. This is useful for components like Row where one child might need to match the height of another, taller child.

8. Q: How does LazyColumn improve scroll performance?

A:
LazyColumn improves performance by being "lazy." It only composes, measures, and draws the items that are currently visible (or nearly visible) in the viewport. As the user scrolls, it recycles the composables for items that scroll off-screen and uses them for new items that scroll on-screen, avoiding the massive cost of composing an entire list of thousands of items at once.

9. Q: Walk through the steps of creating a simple custom Layout composable.

A:

Define the Layout composable, which takes a content lambda.

Inside the Layout's measure lambda, you receive a list of measurables (the children) and constraints (from the parent).

You iterate through the measurables and call .measure(constraints) on each one to get a placeable. This tells you how big each child wants to be.

You then determine the size of your custom layout itself based on the sizes of the placeables.

Finally, you call the layout(width, height) function. Inside its lambda, you iterate through your placeables and call .placeRelative(x, y) on each one to position them within your layout.

10. Q: What is the difference between the main Composition and a Subcomposition?

A:
The main Composition is the primary UI tree of your app. A Subcomposition is a separate, nested composition tree that is created and managed independently. It's a powerful tool for building UI that is logically separate from the main hierarchy, even if it's displayed on top of it. Components like Dialog and Popup use subcomposition to render their content in a new window without interfering with the main UI's recomposition scopes.

Advanced Core Android & Architecture
1. Q: Explain the four Activity launch modes.

A:

standard: Default. A new instance of the Activity is created in the task every time.

singleTop: If an instance of the Activity already exists at the top of the back stack, the system routes the intent to that instance through onNewIntent() instead of creating a new one.

singleTask: The system creates the activity at the root of a new task or brings an existing task with it to the foreground. If an instance already exists anywhere in the system, that instance is brought to the front and any activities on top of it are cleared. Ideal for "Home" or launcher screens.

singleInstance: Same as singleTask, but the activity is the only activity in its task. Any activities it launches are created in a separate task. Ideal for highly isolated screens like an incoming call UI.

2. Q: Explain how Android's main thread works using Looper, Handler, and HandlerThread.

A:

Looper: A class that continuously runs a message loop for a thread. The main UI thread has a Looper by default.

MessageQueue: The Looper manages a MessageQueue that holds tasks (as Message or Runnable objects).

Handler: An object that allows you to post Messages or Runnables to a specific Looper's MessageQueue. A Handler created on the main thread will post tasks to the main thread's queue.

HandlerThread: A helper class that creates a background thread that has its own Looper, allowing it to process a queue of messages sequentially in the background.

3. Q: What is memory churn and how would you identify it?

A:
Memory churn is the rapid creation and garbage collection of a large number of short-lived objects. While GC is normal, excessive churn can cause the GC to run frequently, pausing the app for short periods and leading to UI stutter or "jank." You can identify it using the Android Studio Memory Profiler. Look for a "sawtooth" pattern in the memory allocation graph, where memory usage rapidly increases and then drops sharply during GC events.

4. Q: What is a scenario where you must still use onSaveInstanceState instead of a ViewModel?

A:
You must use onSaveInstanceState to survive process death. While a ViewModel survives configuration changes, it lives in the app's process. If the Android system kills your app's process while it's in the background to reclaim memory, the ViewModel is destroyed along with it. The Bundle from onSaveInstanceState, however, is preserved by the system and will be passed back to your Activity's onCreate method when the user navigates back to your app.

5. Q: Explain Hilt DI Scopes. Can an @ActivityScoped object depend on a @Singleton? What about the reverse?

A:
DI Scopes define the lifecycle of a provided dependency.

@Singleton: One instance for the entire application lifecycle.

@ActivityScoped: One instance for the lifecycle of an Activity.

@ViewModelScoped: One instance for the lifecycle of a ViewModel.

A component can only depend on components with an equal or longer lifetime. Therefore, an @ActivityScoped object can depend on a @Singleton because the singleton will always outlive the activity. The reverse is not valid. A @Singleton cannot depend on an @ActivityScoped object, because the singleton lives forever, but the activity (and its scoped dependency) can be destroyed, which would lead to a dangling reference and a crash.

6. Q: You need to add a non-null creation_date column to a User table in Room. What happens if you just ship the update, and how do you fix it?

A:
If you just change the @Entity and ship the update, the app will crash on launch for existing users with an IllegalStateException. This is because the database schema on their device doesn't match the schema expected by the Room code.

Fix: You must provide a Migration. You would increment the database version number in your @Database annotation and then add a Migration object to the Room.databaseBuilder call.

val MIGRATION_1_2 = object : Migration(1, 2) {
    override fun migrate(database: SupportSQLiteDatabase) {
        database.execSQL("ALTER TABLE User ADD COLUMN creation_date INTEGER NOT NULL DEFAULT 0")
    }
}
// In builder: .addMigrations(MIGRATION_1_2)

7. Q: What is the fundamental difference between an OkHttp Authenticator and an Interceptor for refreshing an expired auth token?

A:

An Interceptor is proactive. It runs on every request and response. You could build logic to check for a 401 response, but it's complex and can interfere with other interceptors.

An Authenticator is reactive. It is specifically designed to be called by OkHttp only after a 401 Unauthorized response is received from the server. Its job is to attempt to refresh the token and return a new request with the updated token. OkHttp will then automatically retry that request. For handling token refreshes, the Authenticator is the correct and cleaner tool for the job.

8. Q: You are asked to design a social media feed app that works seamlessly offline. Describe the architecture.

A:
I would implement an offline-first architecture using the Single Source of Truth (SSOT) pattern with the database.

Database (SSOT): Use Room as the single source of truth for all UI-facing data. The UI will only ever read data from the database.

Repository: The Repository layer is responsible for orchestrating data. When data is requested, it first returns the cached data from Room (as a Flow). Then, it triggers a network request using Retrofit to fetch fresh data.

Data Flow: When the network request succeeds, the Repository saves the new data into the Room database. Because the UI is observing a Flow from Room, Room automatically detects the change and pushes the new list to the UI, which updates reactively.

Offline Actions: For actions like posting a comment while offline, the app would save the comment to a separate "pending_actions" table in Room and use WorkManager to schedule a sync task that will execute it once network connectivity is restored.

9. Q: How does a ContentProvider enforce security?

A:
A ContentProvider enforces security primarily through the AndroidManifest.xml.

You can specify permissions on the provider tag itself using android:readPermission and android:writePermission. Another app must have these permissions in its manifest to access the provider.

For more granular control, you can use URI permissions. By setting android:grantUriPermissions="true", you can grant temporary, one-time access to a specific piece of data to another app that doesn't have the global read/write permissions. This is often used with Intent.FLAG_GRANT_READ_URI_PERMISSION.

10. Q: How does WorkManager handle coroutines and threading?

A:
WorkManager has excellent built-in support for coroutines. Instead of extending Worker, you extend CoroutineWorker.

The doWork() method in CoroutineWorker is a suspend function.

WorkManager provides a default background dispatcher to run this function, so you don't need to manually use withContext(Dispatchers.IO).

Because doWork() is a suspend function, you can directly call other suspend functions (like those from Retrofit or Room) inside it without any extra boilerplate, making the code clean and sequential.
