# Senior Android Developer - Advanced Questions Bank

A comprehensive collection of advanced interview questions covering all aspects of senior Android development. Each topic contains 10+ questions designed to test deep understanding, practical experience, and problem-solving skills.

---

## �� 1. Core Android Fundamentals

### Activity & Fragment Lifecycle - Advanced Concepts

1. **Explain the difference between `onSaveInstanceState()` and `SavedStateHandle`. In what scenarios would each fail to preserve data?**

2. **You have an Activity that launches a camera intent. The camera app kills your process due to memory pressure. Walk me through exactly what happens and how you'd ensure data preservation.**

3. **What's the difference between `onStop()` and `onPause()`? Give me a real-world scenario where this distinction matters for user experience.**

4. **Explain Fragment's dual lifecycle (Fragment lifecycle vs View lifecycle). Why does this exist and how can improper handling lead to memory leaks?**

5. **Your app crashes with "Can not perform this action after onSaveInstanceState". Explain why this happens and provide 3 different solutions.**

6. **How would you test process death in your app? Provide both manual testing steps and automated testing approaches.**

7. **Explain the relationship between `ViewModel`, `SavedStateHandle`, and process death. What are the limitations of each?**

8. **You have a Fragment that needs to survive both configuration changes and process death while maintaining complex UI state. Design the complete solution.**

9. **What happens to pending Fragment transactions during configuration changes? How do you handle this properly?**

10. **Explain the Fragment Result API. How is it better than the old `setTargetFragment()` approach and what problems does it solve?**

11. **Your Activity has multiple Fragments, and one Fragment's state depends on another's. How do you handle state restoration when the app is killed and recreated?**

12. **Describe the complete lifecycle flow when an Activity is launched with `FLAG_ACTIVITY_CLEAR_TOP` and the target Activity already exists in the back stack.**

### Tasks and Back Stack - Real-World Scenarios

1. **You're building a banking app. A user gets a payment notification, taps it, and should land on the payment details screen. However, if they press back, they should go to the main app flow, not the notification shade. How do you implement this?**

2. **Explain the difference between `singleTask` and `singleInstance`. Provide a real-world use case where you'd choose `singleInstance` over `singleTask`.**

3. **Your app has Activities A → B → C. From C, you want to go directly to A and clear B from the stack, but only if A was launched with specific data. How do you implement this?**

4. **Explain task affinity and provide a scenario where you'd need to modify it. What are the security implications?**

5. **You have a deep-link that should create a proper back stack even if the app wasn't running. How do you implement this with Navigation Component?**

6. **What happens when you launch an Activity with `FLAG_ACTIVITY_NEW_TASK` from a Service? How is this different from launching from an Activity?**

7. **Your app supports multiple windows (Android 7+). How do launch modes behave differently in multi-window mode?**

8. **Explain the interaction between `launchMode="singleTop"` and `onNewIntent()`. What data is available in `onNewIntent()` and what isn't?**

9. **You need to implement a "logout" feature that clears the entire task and starts fresh. Provide multiple approaches and their trade-offs.**

10. **How do you handle deep links that should behave differently based on whether the app is already running or being launched fresh?**

11. **Explain the relationship between `taskAffinity`, `allowTaskReparenting`, and `finishOnTaskLaunch`. Provide a use case for each.**

12. **Your app has a chat feature where tapping a notification should open the specific chat, but the back stack should be preserved. How do you handle this with different launch modes?**

### Services & Background Work - Post-Android 8 Reality

1. **Explain Doze mode and App Standby in detail. How do they interact with each other and what are the whitelisting criteria?**

2. **You need to sync data every 15 minutes, but only when the device has WiFi and is charging. Design the complete WorkManager solution including retry policies.**

3. **What's the difference between `CoroutineWorker` and `Worker`? When would you choose one over the other?**

4. **Your app needs to play music in the background. Walk me through the complete implementation including foreground service, notification, and media session.**

5. **Explain the difference between `OneTimeWorkRequest` and `PeriodicWorkRequest`. What are the minimum intervals and why do these restrictions exist?**

6. **How do you chain WorkManager tasks where the output of one becomes the input of another? Handle both success and failure scenarios.**

7. **Your background sync fails due to network issues. Design a robust retry mechanism with exponential backoff using WorkManager.**

8. **Explain the concept of "expedited work" in WorkManager. When would you use it and what are the limitations?**

9. **You have a long-running upload task that should survive app kills but needs to update UI progress. Design the complete solution.**

10. **How do you migrate from IntentService to WorkManager? What are the key differences in lifecycle and execution guarantees?**

11. **Explain WorkManager's internal database. What happens if this database gets corrupted and how do you handle it?**

12. **Your app needs to perform different background tasks based on user location, but only when they're at specific places. Design this using WorkManager constraints.**

13. **How do you test WorkManager tasks? Provide approaches for both unit testing and integration testing.**

### Context - Memory Leak Prevention

1. **Explain the different types of Context and their lifecycles. Provide scenarios where using the wrong Context type causes memory leaks.**

2. **You have a singleton that needs Context. Design it to avoid memory leaks while still being functional.**

3. **Analyze this code and identify all potential memory leaks:**
   ```kotlin
   class MyActivity : AppCompatActivity() {
       companion object {
           var callback: (() -> Unit)? = null
       }
       
       private val handler = Handler()
       
       override fun onCreate(savedInstanceState: Bundle?) {
           super.onCreate(savedInstanceState)
           callback = { finish() }
           handler.postDelayed({ /* some work */ }, 60000)
       }
   }
   ```

4. **How do you safely pass Context to background threads? Provide multiple approaches and their trade-offs.**

5. **Explain the relationship between Context, Resources, and Configuration. How do configuration changes affect Context validity?**

6. **You need to show a dialog from a background thread. What are all the ways this can go wrong and how do you handle each?**

7. **How do you detect Context leaks in production? Provide both automated and manual approaches.**

8. **Explain the difference between `getApplicationContext()` and `getBaseContext()`. When would you use each?**

9. **Your library needs Context but shouldn't hold references to Activities. Design a Context management strategy.**

10. **How do WeakReferences help with Context leaks? Provide a practical example and explain the limitations.**

11. **Explain how Hilt/Dagger helps prevent Context-related memory leaks. What scoping strategies should you use?**

12. **You're reviewing code that stores Context in a static field. Under what circumstances (if any) would this be acceptable?**

---

## 🏗️ 2. Modern UI Development

### Jetpack Compose - Advanced State Management

1. **Explain the difference between `remember`, `rememberSaveable`, and `derivedStateOf`. Provide scenarios where each is most appropriate.**

2. **What triggers recomposition in Compose? Explain the role of `@Stable` and `@Immutable` annotations with practical examples.**

3. **You have a list of 10,000 items in Compose. The UI is janky during scrolling. Diagnose and fix the performance issues.**

4. **Explain `CompositionLocal`. How is it different from passing parameters down the tree? When would you use each approach?**

5. **Design a complex form with interdependent fields using Compose. Handle validation, error states, and state hoisting properly.**

6. **What's the difference between `LaunchedEffect`, `SideEffect`, and `DisposableEffect`? Provide use cases for each.**

7. **You need to integrate a complex custom View into Compose. Walk me through the complete implementation using `AndroidView`.**

8. **Explain Compose's phase system (Composition, Layout, Drawing). How do you optimize for each phase?**

9. **How do you handle deep linking in a Compose-only app? Design the navigation structure and state management.**

10. **Your Compose screen needs to communicate with a Fragment in the same Activity. Design the communication pattern.**

11. **Explain slot APIs in Compose. Design a reusable component that uses slots effectively.**

12. **How do you test Compose UIs? Provide examples for testing state changes, user interactions, and animations.**

13. **You have a Compose screen that needs to survive process death with complex nested state. Design the complete solution.**

14. **Explain the `key()` function in Compose. When is it necessary and what problems does it solve?**

### Traditional View System - Advanced Techniques

1. **Walk me through the complete View drawing process: measure, layout, and draw phases. How do you optimize each phase?**

2. **You need to create a custom ViewGroup that arranges children in a circular pattern. Implement `onMeasure()` and `onLayout()`.**

3. **Explain the difference between `invalidate()` and `requestLayout()`. When would you call each?**

4. **Your RecyclerView has performance issues with complex item layouts. Provide 5 different optimization techniques.**

5. **How do you implement a custom touch handling system that supports multi-touch gestures? Handle edge cases like touch slop and velocity tracking.**

6. **Explain ViewTreeObserver and its various listeners. Provide use cases for each listener type.**

7. **You need to create a View that can be used both in XML and programmatically with different styling. Design the complete solution.**

8. **How do you handle RTL (Right-to-Left) layouts in custom Views? What methods do you need to override?**

9. **Explain the difference between `View.GONE`, `View.INVISIBLE`, and `View.VISIBLE`. How does each affect layout and drawing?**

10. **Your app needs to support different screen densities and sizes. How do you handle this in custom Views?**

11. **Implement a custom View that efficiently handles large datasets with recycling (similar to RecyclerView concept).**

12. **How do you create accessible custom Views? What accessibility services do you need to support?**

13. **Explain ConstraintLayout's constraint system. How do you create complex layouts with barriers, guidelines, and chains?**

---

## 🏛️ 3. Architecture & Design Patterns

### Architectural Patterns Comparison

1. **Compare MVVM and MVI architectures. In what scenarios would you choose MVI over MVVM and why?**

2. **You're architecting a complex e-commerce app with multiple user types, real-time features, and offline support. Design the complete architecture.**

3. **Explain the problems with MVP pattern that MVVM solves. Why isn't MVP recommended for Android anymore?**

4. **How do you handle navigation in MVVM vs MVI? Design navigation patterns for both architectures.**

5. **Your app needs to support multiple themes, languages, and user preferences. How do you architect this with MVVM?**

6. **Explain Unidirectional Data Flow (UDF). How do you implement it in practice and what benefits does it provide?**

7. **You have a screen with multiple data sources (network, database, user input). Design the state management for both MVVM and MVI.**

8. **How do you handle complex business logic that spans multiple screens? Design the architecture for a multi-step checkout process.**

9. **Explain the role of ViewModels in different architectures. How do you test ViewModels effectively?**

10. **Your app needs to support both phone and tablet layouts with different navigation patterns. How do you architect this?**

11. **Design an architecture that supports A/B testing, feature flags, and gradual rollouts.**

12. **How do you handle error states and loading states consistently across your app architecture?**

### Clean Architecture - The Dependency Rule

1. **Explain the Dependency Rule in detail. How do you enforce it in a real Android project?**

2. **Design the complete package structure for a social media app using Clean Architecture principles.**

3. **You need to switch from REST API to GraphQL. How does Clean Architecture make this easier? Show the code changes required.**

4. **Explain the role of Use Cases/Interactors. When do you create a Use Case vs putting logic directly in the Repository?**

5. **How do you handle cross-cutting concerns (logging, analytics, crash reporting) in Clean Architecture?**

6. **Your domain layer needs to perform network calls. How do you handle this without violating Clean Architecture?**

7. **Design error handling strategy that works across all Clean Architecture layers.**

8. **You have business logic that depends on Android-specific features (like location services). How do you handle this in Clean Architecture?**

9. **Explain how to implement Clean Architecture with multiple data sources (network, database, cache).**

10. **How do you test each layer of Clean Architecture? Provide testing strategies for domain, data, and presentation layers.**

11. **Your app needs to support multiple build flavors with different business rules. How do you structure this with Clean Architecture?**

12. **Design a Clean Architecture solution for offline-first functionality with conflict resolution.**

### SOLID Principles with Android Examples

1. **Provide an Android example that violates each SOLID principle and show how to fix it.**

2. **You have a class that handles user authentication, caching, and analytics. Refactor it to follow Single Responsibility Principle.**

3. **Design an extensible notification system using Open/Closed Principle. It should support different notification types without modifying existing code.**

4. **Explain Liskov Substitution Principle with Android examples. How can violating it break your app?**

5. **You have a large interface with 20 methods, but most implementations only need 3-4 methods. Apply Interface Segregation Principle.**

6. **Design a dependency injection system that follows Dependency Inversion Principle. Compare it with direct instantiation.**

7. **Your Repository directly creates network and database instances. Refactor it to follow SOLID principles.**

8. **How do SOLID principles apply to Android's built-in classes like Activity and Fragment? What violations exist?**

9. **Design a plugin system for your app that allows third-party developers to extend functionality while following SOLID principles.**

10. **You're building a payment processing system. Apply all SOLID principles to make it maintainable and extensible.**

11. **Explain how following SOLID principles improves testability. Provide before/after examples.**

12. **Your team is arguing about whether to create many small classes vs fewer large classes. How do SOLID principles guide this decision?**

### Dependency Injection - Hilt vs Koin

1. **Compare Hilt and Koin in terms of performance, compile-time safety, and ease of use. When would you choose each?**

2. **You need to provide different implementations of a Repository based on build flavor. Show how to do this in both Hilt and Koin.**

3. **Explain Hilt's component hierarchy and scoping. How do you create custom scopes?**

4. **Your app needs to inject dependencies into classes you don't own (like third-party libraries). How do you handle this in Hilt?**

5. **Design a DI setup that supports multiple database configurations (dev, staging, prod) using Hilt.**

6. **Explain Assisted Injection in Hilt. Provide a real-world use case and implementation.**

7. **How do you test code that uses Hilt? Provide strategies for unit tests and integration tests.**

8. **Your app has modules that should only be available in debug builds. How do you structure this with Hilt?**

9. **Explain the difference between `@Binds` and `@Provides` in Hilt. When do you use each?**

10. **You need to inject different implementations based on runtime conditions (not compile-time). How do you handle this?**

11. **Design a DI setup for a modularized app where each feature module has its own dependencies.**

12. **How do you migrate from Dagger to Hilt? What are the main changes required?**

13. **Explain Hilt's code generation. What files are generated and how do they work?**

---

## ⚡ 4. Concurrency & Asynchronous Programming

### Kotlin Coroutines - Advanced Concepts

1. **Explain structured concurrency in detail. How does it prevent resource leaks and what happens when a parent coroutine is cancelled?**

2. **You have a coroutine that needs to make 10 parallel network calls, but only proceed when all succeed. If any fails, cancel the others. Implement this.**

3. **Explain the difference between `supervisorScope` and `coroutineScope`. Provide scenarios where each is appropriate.**

4. **Your app needs to perform a long-running operation that should survive configuration changes but be cancellable. Design the complete solution.**

5. **Explain CoroutineContext and its elements. How do you create custom CoroutineContext elements?**

6. **You have a suspend function that might take a long time. How do you make it cancellation-cooperative and handle cleanup?**

7. **Design a retry mechanism using coroutines with exponential backoff and jitter.**

8. **Explain the difference between `Dispatchers.Main` and `Dispatchers.Main.immediate`. When would you use each?**

9. **You need to coordinate multiple coroutines where some depend on others' results. Design this using channels and flows.**

10. **How do you handle exceptions in coroutines? Explain the exception propagation rules and provide handling strategies.**

11. **Implement a coroutine-based rate limiter that ensures no more than N operations per second.**

12. **Your suspend function needs to call a callback-based API. How do you convert it properly using `suspendCoroutine`?**

13. **Explain coroutine debugging. How do you debug deadlocks, race conditions, and performance issues?**

### Flow - Cold vs Hot Streams

1. **Explain the difference between cold and hot flows with practical examples. When would you convert between them?**

2. **You need to implement a real-time chat feature. Design the Flow architecture for message sending, receiving, and state management.**

3. **Implement a Flow that combines data from multiple sources (network, database, user input) and handles conflicts.**

4. **Explain backpressure in Flows. How do `buffer()`, `conflate()`, and `collectLatest()` handle it differently?**

5. **You have a Flow that emits too frequently and causes UI jank. Provide multiple solutions to throttle emissions.**

6. **Design a caching mechanism using StateFlow that supports cache invalidation and refresh strategies.**

7. **How do you test Flows? Provide examples for testing emissions, timing, and error scenarios.**

8. **Implement a Flow-based search feature with debouncing, cancellation of previous searches, and error handling.**

9. **Explain SharedFlow configuration options (`replay`, `extraBufferCapacity`, `onBufferOverflow`). Provide use cases for different configurations.**

10. **You need to merge multiple Flows with different emission rates. How do you handle timing and ordering?**

11. **Design a Flow-based download manager that tracks progress, handles failures, and supports pause/resume.**

12. **How do you convert between Flow and other reactive streams (RxJava, LiveData)? What are the considerations?**

13. **Implement a Flow that automatically retries on failure with different strategies based on error type.**

---

## 🚀 5. Performance, Optimization & Tooling

### Memory Optimization - Leak Detection & Prevention

1. **You suspect your app has memory leaks. Walk me through the complete investigation process using Android Studio Profiler.**

2. **Analyze this code and identify all potential memory leaks. Provide fixes for each:**
   ```kotlin
   class MainActivity : AppCompatActivity() {
       companion object {
           val callbacks = mutableListOf<() -> Unit>()
       }
       
       private val handler = Handler(Looper.getMainLooper())
       private var asyncTask: AsyncTask<*, *, *>? = null
       
       override fun onCreate(savedInstanceState: Bundle?) {
           super.onCreate(savedInstanceState)
           callbacks.add { finish() }
           
           asyncTask = object : AsyncTask<Void, Void, String>() {
               override fun doInBackground(vararg params: Void?): String {
                   Thread.sleep(30000)
                   return "Done"
               }
               
               override fun onPostExecute(result: String?) {
                   Toast.makeText(this@MainActivity, result, Toast.LENGTH_LONG).show()
               }
           }.execute()
           
           handler.postDelayed({
               // Some work
           }, 60000)
       }
   }
   ```

3. **How do you implement LeakCanary in a production app? What are the performance implications and how do you handle them?**

4. **Your app's memory usage keeps growing over time (memory creep). How do you identify and fix the root causes?**

5. **Explain the different types of memory leaks in Android. Provide prevention strategies for each type.**

6. **You have a large bitmap that needs to be shared across multiple Activities. Design a memory-efficient solution.**

7. **How do you optimize memory usage in RecyclerView with complex item layouts and images?**

8. **Your app crashes with OutOfMemoryError. Provide a systematic approach to diagnose and fix this.**

9. **Explain the Android memory model and garbage collection. How do you optimize for different GC strategies?**

10. **Design a memory-efficient image loading and caching system for an app with thousands of images.**

11. **How do you detect and prevent memory leaks in background services and WorkManager tasks?**

12. **Your app uses native libraries (JNI). How do you detect and prevent memory leaks in native code?**

### CPU & Rendering Performance

1. **Your app drops frames during scrolling. Walk me through the complete debugging process using Systrace/Perfetto.**

2. **Explain the Android rendering pipeline in detail. How do you optimize each stage?**

3. **You have a complex custom View that's causing jank. How do you profile and optimize its drawing performance?**

4. **Design a performance monitoring system that tracks jank, ANRs, and slow operations in production.**

5. **Your app's startup time is too slow. Provide a systematic approach to measure and improve it.**

6. **Explain the difference between cold, warm, and hot app startup. How do you optimize each type?**

7. **How do Baseline Profiles work? How do you generate and validate them for your app?**

8. **You have expensive operations running on the main thread. How do you identify and move them to background threads?**

9. **Design a frame rate monitoring system that alerts you when performance degrades.**

10. **Your app uses heavy animations. How do you ensure they run smoothly on low-end devices?**

11. **Explain overdraw and how to detect and fix it. What tools do you use?**

12. **How do you optimize performance for different screen densities and sizes?**

### Build System Optimization

1. **Your Gradle build takes 10 minutes. Provide a systematic approach to optimize it.**

2. **Explain Gradle's build cache. How do you configure it for maximum effectiveness in a team environment?**

3. **Design a modularization strategy for a large app. How do you decide module boundaries?**

4. **You want to parallelize your build process. What Gradle configurations enable this and what are the limitations?**

5. **How do you optimize dependency resolution in a multi-module project with version conflicts?**

6. **Design a build system that supports multiple app variants with different features and dependencies.**

7. **Your CI/CD pipeline builds are slow. How do you optimize them using Gradle features?**

8. **Explain Gradle's configuration cache. How does it improve build performance and what are the constraints?**

9. **You need to share build logic across multiple projects. How do you create and publish Gradle plugins?**

10. **How do you implement incremental builds effectively? What makes builds non-incremental?**

11. **Design a build system that automatically optimizes APK size based on target markets.**

12. **Your team has different development environments. How do you ensure consistent builds across all setups?**

---

## 🧪 6. Testing

### Testing Pyramid Implementation

1. **Design a complete testing strategy for a banking app. What would you test at each level of the pyramid?**

2. **You have a ViewModel that interacts with multiple repositories. How do you unit test it effectively?**

3. **Your app has complex business logic in Use Cases. Design the testing approach including mocking strategies.**

4. **How do you test coroutines and Flows? Provide examples for different scenarios (success, failure, cancellation).**

5. **Design integration tests for a feature that involves database, network, and UI interactions.**

6. **Your app supports offline functionality. How do you test the sync logic and conflict resolution?**

7. **How do you test error handling across different layers of your architecture?**

8. **Design a testing strategy for an app with multiple build flavors and configurations.**

9. **You need to test time-dependent functionality (like scheduled notifications). How do you approach this?**

10. **How do you test complex UI interactions like drag-and-drop, multi-touch gestures, and animations?**

11. **Your app integrates with third-party SDKs. How do you test these integrations without depending on external services?**

12. **Design a testing approach for accessibility features and compliance.**

### Testing Frameworks & Tools

1. **Compare MockK and Mockito for Android testing. When would you choose each?**

2. **You need to test a complex Compose UI with animations and state changes. Design the complete testing approach.**

3. **How do you test WorkManager tasks? Provide approaches for both unit and integration testing.**

4. **Your app uses Hilt for DI. How do you set up test doubles and different configurations for testing?**

5. **Design a screenshot testing strategy that works across different devices and API levels.**

6. **How do you test network interactions? Compare different approaches (MockWebServer, Wiremock, etc.).**

7. **You need to test database migrations. How do you ensure data integrity across version upgrades?**

8. **How do you implement property-based testing for Android? Provide examples with complex data structures.**

9. **Design a performance testing strategy that catches regressions in CI/CD.**

10. **Your app has flaky tests. How do you identify, debug, and fix test flakiness?**

11. **How do you test push notifications, deep links, and other external triggers?**

12. **Design a testing approach for apps that use machine learning models or complex algorithms.**

---

## 💼 7. Android System Design

### System Design Interview Approach

1. **Design a scalable messaging app like WhatsApp. Handle real-time messaging, media sharing, and offline support.**

2. **You're building a ride-sharing app like Uber. Design the complete system focusing on real-time location tracking and matching.**

3. **Design a social media feed like Instagram. Handle image/video uploads, feed generation, and real-time interactions.**

4. **Create a music streaming app like Spotify. Focus on offline playback, recommendations, and social features.**

5. **Design a video calling app like Zoom. Handle multiple participants, screen sharing, and network optimization.**

6. **You're building a food delivery app. Design the system for order management, real-time tracking, and payment processing.**

7. **Design a collaborative document editing app like Google Docs. Handle real-time collaboration and conflict resolution.**

8. **Create a fitness tracking app. Design data collection, analysis, and social comparison features.**

9. **Design a news aggregation app. Handle content curation, personalization, and offline reading.**

10. **You're building a banking app. Design the architecture for security, transaction processing, and compliance.**

11. **Design a photo storage and sharing app like Google Photos. Handle backup, organization, and sharing features.**

12. **Create a learning management system app. Design content delivery, progress tracking, and interactive features.**

### Offline-First Architecture

1. **Design a complete offline-first architecture for a note-taking app with real-time collaboration.**

2. **You have an e-commerce app that needs to work offline. How do you handle cart management, product browsing, and order placement?**

3. **Design conflict resolution strategies for an app where multiple users can edit the same data offline.**

4. **Your app needs to sync large amounts of data efficiently. Design the synchronization strategy including delta sync and compression.**

5. **How do you handle schema evolution in an offline-first app? Design migration strategies for both local and remote data.**

6. **Design a caching strategy that balances storage space, data freshness, and user experience.**

7. **Your offline app needs to handle partial network connectivity. How do you optimize for slow and unreliable networks?**

8. **Design an offline-first architecture for a social media app with posts, comments, and real-time interactions.**

9. **How do you implement optimistic UI updates in an offline-first app? Handle both success and failure scenarios.**

10. **Design a background sync system that respects battery life and data usage while ensuring data consistency.**

---

## 📱 8. Advanced Android Components & Storage

### Fragment Transactions - Advanced Management

1. **You have a complex navigation flow with nested fragments. How do you manage the back stack and state properly?**

2. **Design a Fragment communication system for a multi-pane tablet layout where fragments need to coordinate.**

3. **Your app crashes with "IllegalStateException: Can not perform this action after onSaveInstanceState". Explain all possible causes and solutions.**

4. **How do you implement shared element transitions between Fragments? Handle edge cases like configuration changes.**

5. **Design a Fragment factory system that supports dependency injection and complex initialization.**

6. **You need to replace a Fragment but keep its state for later restoration. How do you implement this?**

7. **How do you handle Fragment transactions in a ViewPager2? What are the lifecycle considerations?**

8. **Design a navigation system that supports both Fragment and Activity destinations with consistent behavior.**

9. **Your app has conditional navigation based on user state. How do you implement this with Fragments?**

10. **How do you test Fragment transactions and navigation flows? Provide both unit and integration testing approaches.**

11. **Design a Fragment pooling system to improve performance in apps with many similar Fragments.**

12. **You need to implement a wizard-like flow with Fragments. How do you handle validation, state management, and navigation?**

### Storage Solutions - Comprehensive Overview

1. **Design a complete data layer for an app that needs to support multiple users, offline sync, and data encryption.**

2. **You need to migrate from SQLite to Room while the app is in production. Design the migration strategy.**

3. **How do you implement full-text search in Room? Handle complex queries and performance optimization.**

4. **Design a caching strategy that uses multiple storage layers (memory, disk, network) with appropriate eviction policies.**

5. **Your app needs to store sensitive data securely. Compare different approaches and design a complete solution.**

6. **How do you handle database schema migrations in Room? Provide strategies for complex changes.**

7. **Design a storage solution for an app that handles large files (videos, documents) with efficient access patterns.**

8. **You need to implement data synchronization between local Room database and remote server. Handle conflicts and partial updates.**

9. **How do you optimize Room database performance for large datasets? Provide indexing and query optimization strategies.**

10. **Design a storage architecture that supports multiple data sources with different consistency requirements.**

11. **Your app needs to export/import data in different formats. How do you implement this efficiently?**

12. **How do you implement database encryption in Room? What are the performance implications?**

### BroadcastReceiver - Advanced Usage

1. **Design a system that listens for network changes and updates app behavior accordingly. Handle all edge cases.**

2. **You need to implement a custom broadcast system for inter-app communication. What security considerations apply?**

3. **How do you migrate from BroadcastReceiver to modern alternatives? Provide migration strategies for different use cases.**

4. **Design a battery optimization system that uses broadcasts to adjust app behavior based on battery state.**

5. **Your app needs to respond to system events but only when it's in the foreground. How do you implement this?**

6. **How do you test BroadcastReceiver functionality? Provide approaches for both unit and integration testing.**

7. **Design a notification management system that uses broadcasts to coordinate between different app components.**

8. **You need to implement a file monitoring system using broadcasts. Handle permissions and security.**

9. **How do you implement ordered broadcasts with result processing? Provide a real-world use case.**

10. **Design a system that uses broadcasts for plugin architecture, allowing third-party apps to extend functionality.**

### ContentProvider & ContentResolver

1. **Design a ContentProvider for a document management app that supports complex queries and file operations.**

2. **You need to share data between your app and third-party apps securely. Design the complete ContentProvider solution.**

3. **How do you implement batch operations in ContentProvider? Handle transactions and error recovery.**

4. **Design a ContentProvider that supports real-time updates and change notifications.**

5. **Your ContentProvider needs to handle large datasets efficiently. Implement pagination and lazy loading.**

6. **How do you secure a ContentProvider? Implement permission-based access control.**

7. **Design a ContentProvider that aggregates data from multiple sources (database, files, network).**

8. **You need to implement a ContentProvider for media files with metadata extraction. Handle different file types.**

9. **How do you test ContentProvider functionality? Provide comprehensive testing strategies.**

10. **Design a ContentProvider that supports offline synchronization with conflict resolution.**

---

## 🔒 9. Security & Obfuscation

### Code Obfuscation - ProGuard/R8

1. **Your app's APK is being reverse-engineered. Design a comprehensive obfuscation strategy using R8.**

2. **You're using reflection and dynamic class loading. How do you configure ProGuard/R8 to handle this correctly?**

3. **Design ProGuard rules for a library that will be used by other developers. What should you keep and what can you obfuscate?**

4. **Your obfuscated app crashes in production but works fine in debug. How do you debug this systematically?**

5. **How do you implement string encryption to protect sensitive constants in your code?**

6. **Design an obfuscation strategy that protects against both static analysis and dynamic analysis.**

7. **You're using annotation processing (like Room, Dagger). How do you configure obfuscation to work correctly?**

8. **How do you measure the effectiveness of your obfuscation? What metrics and tools do you use?**

9. **Design a build system that applies different levels of obfuscation based on build variants.**

10. **Your app uses native libraries (JNI). How do you obfuscate the native code and JNI interfaces?**

### Security Best Practices

1. **Design a complete security architecture for a banking app. Cover authentication, data protection, and communication security.**

2. **You need to implement certificate pinning that can be updated without app releases. Design the complete solution.**

3. **How do you protect against man-in-the-middle attacks in your app? Provide multiple layers of protection.**

4. **Design a secure storage system for user credentials that protects against both device compromise and app reverse engineering.**

5. **Your app handles PII (Personally Identifiable Information). Design the complete data protection strategy.**

6. **How do you implement secure communication with your backend? Handle key exchange, rotation, and revocation.**

7. **Design a system to detect and respond to security threats in real-time (jailbreak, debugging, tampering).**

8. **You need to implement biometric authentication. Design the complete flow including fallback mechanisms.**

9. **How do you secure your app's API keys and prevent them from being extracted?**

10. **Design a secure update mechanism that prevents malicious code injection.**

11. **Your app needs to comply with GDPR/CCPA. How do you implement data protection and user rights?**

12. **How do you implement secure logging that doesn't leak sensitive information?**

---

## 📦 10. Library Creation & Publication

### Creating Android Library (AAR)

1. **Design a networking library that other developers can easily integrate. What API design principles do you follow?**

2. **You're creating a UI component library. How do you ensure it works well with different themes and configurations?**

3. **Design a library versioning strategy that supports backward compatibility and smooth migrations.**

4. **Your library needs to work with different versions of Android and support libraries. How do you handle compatibility?**

5. **How do you design a library API that's both powerful and simple to use? Provide examples of good and bad API design.**

6. **You're creating a library that uses native code. How do you package and distribute it effectively?**

7. **Design a plugin system that allows third-party developers to extend your library's functionality.**

8. **How do you handle library initialization and configuration? Design patterns for different initialization strategies.**

9. **Your library needs to collect analytics without impacting the host app's performance. How do you implement this?**

10. **Design a library testing strategy that ensures compatibility across different Android versions and device configurations.**

11. **How do you implement feature flags in a library to enable gradual rollouts and A/B testing?**

12. **Your library conflicts with another popular library. How do you resolve dependency conflicts and provide migration paths?**

### App Size Optimization

1. **Your app's APK size is 50MB and needs to be reduced to under 20MB. Provide a systematic optimization approach.**

2. **Design a resource optimization strategy that maintains quality while reducing size across different device configurations.**

3. **You have a large number of similar images. How do you optimize them without losing visual quality?**

4. **How do you implement dynamic feature delivery for a complex app? Design the modularization strategy.**

5. **Your app includes multiple native libraries. How do you optimize their size and loading?**

6. **Design an asset delivery system that downloads resources on-demand based on user behavior.**

7. **How do you optimize your app for Android App Bundle? What are the key considerations?**

8. **You need to support multiple languages but most users only use one. How do you optimize for this?**

9. **Design a build system that automatically optimizes APK size based on target markets and device capabilities.**

10. **Your app uses vector graphics extensively. How do you optimize them for both size and runtime performance?**

11. **How do you implement progressive app loading where core features load first and additional features load later?**

12. **Design a monitoring system that tracks app size metrics and alerts when size increases unexpectedly.**

---

## 🧪 11. Comprehensive Testing Strategies

### UI/UX Testing Advanced Techniques

1. **Design a comprehensive UI testing strategy for an app with complex animations and custom views.**

2. **You need to test your app across 50+ device configurations. How do you automate this efficiently?**

3. **How do you test accessibility features comprehensively? Design tests for different accessibility services.**

4. **Your app has complex gesture interactions (pinch, zoom, multi-touch). How do you test these reliably?**

5. **Design a visual regression testing system that catches UI changes across different screen sizes and densities.**

6. **You need to test your app's behavior under different system conditions (low memory, slow network, etc.). How do you automate this?**

7. **How do you test deep links and app-to-app interactions? Handle both success and failure scenarios.**

8. **Design a testing strategy for apps that integrate with system features (camera, contacts, location).**

9. **Your app supports multiple themes and configurations. How do you test all combinations efficiently?**

10. **How do you test performance characteristics in UI tests? Measure frame rates, memory usage, and responsiveness.**

11. **Design a testing approach for apps with real-time features (chat, live updates, notifications).**

12. **You need to test your app's behavior during system events (calls, notifications, app switching). How do you simulate these?**

### Performance Testing

1. **Design a comprehensive performance testing strategy that covers startup time, memory usage, and battery consumption.**

2. **You need to detect performance regressions in your CI/CD pipeline. How do you implement automated performance testing?**

3. **How do you test your app's performance under stress conditions (high load, limited resources)?**

4. **Design a testing approach for apps that handle large datasets or complex computations.**

5. **You need to test network performance under different conditions (slow 3G, intermittent connectivity). How do you simulate this?**

6. **How do you test memory leaks systematically? Design both automated and manual testing approaches.**

7. **Your app uses background processing extensively. How do you test its impact on battery life and system performance?**

8. **Design a performance testing strategy for apps with heavy graphics or gaming components.**

9. **How do you test your app's scalability? Simulate different user loads and usage patterns.**

10. **You need to benchmark your app against competitors. How do you design fair and meaningful performance comparisons?**

11. **Design a monitoring system that tracks performance metrics in production and correlates them with user experience.**

12. **How do you test the performance impact of different optimization techniques? Design A/B testing for performance.**

---

## 🎯 Bonus: Emerging Technologies & Advanced Topics

### Kotlin Multiplatform Mobile (KMM)

1. **Design a KMM architecture for a cross-platform app. How do you structure shared and platform-specific code?**

2. **You need to share business logic between Android and iOS while keeping UI platform-specific. Design the complete solution.**

3. **How do you handle platform-specific dependencies in KMM? Provide examples for networking, database, and analytics.**

4. **Design a testing strategy for KMM projects. How do you test shared code on both platforms?**

5. **Your KMM project needs to integrate with existing native codebases. How do you handle this migration?**

### Advanced Compose Topics

1. **Design a custom Layout composable that implements a complex positioning algorithm.**

2. **You need to create a Compose animation that coordinates multiple elements with different timing. Implement this using Transition API.**

3. **How do you implement a custom remember function with complex key management and lifecycle awareness?**

4. **Design a Compose-based design system that supports theming, component variants, and accessibility.**

5. **You need to optimize Compose performance for a complex screen with hundreds of elements. Provide optimization strategies.**

### Modern CI/CD Practices

1. **Design a complete CI/CD pipeline for Android that includes testing, security scanning, and automated deployment.**

2. **You need to implement feature flags that work with your CI/CD pipeline. Design the complete system.**

3. **How do you implement automated security testing in your CI/CD pipeline? Include static analysis, dependency scanning, and runtime testing.**

4. **Design a deployment strategy that supports gradual rollouts, A/B testing, and quick rollbacks.**

5. **Your team needs to support multiple app variants and flavors in CI/CD. How do you structure the pipeline efficiently?**

---

*This question bank contains 400+ advanced questions covering every aspect of senior Android development. Each question is designed to test deep understanding, practical experience, and problem-solving skills expected at the senior level.*
