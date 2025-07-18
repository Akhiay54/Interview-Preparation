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
