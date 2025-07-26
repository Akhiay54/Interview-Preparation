# 🚀 Ultimate Android System Design Prep (Senior / SDE-2+)

*A comprehensive guide to not just pass, but ace the Senior Android System Design interview. This document focuses on architecture, trade-offs, and reasoning like a seasoned engineer.*

---

## Section 1 — The Core Knowledge Matrix

| Category                | Why It Matters                               | Key Components & APIs                                                              |
|-------------------------|----------------------------------------------|------------------------------------------------------------------------------------|
| **Offline & Caching**   | Resilience, performance, offline-first UX    | `Room`, `SQLCipher`, `DataStore`, `OkHttp Cache`, `Paging 3`, `LruCache`             |
| **Background Work**     | Constraint-aware, reliable, battery-safe     | `WorkManager`, `Foreground Services`, `JobScheduler`, `AlarmManager`, `BroadcastReceiver` |
| **Architecture & DI**   | Scalability, testability, separation of concerns | `MVVM/MVI`, Clean Architecture Layers, `Hilt`, `Koin`, Multi-Module Gradle Structure |
| **Concurrency**         | UI responsiveness, data safety, cancellation | `Kotlin Coroutines`, `Flow`, `StateFlow/SharedFlow`, `Dispatchers`, `CoroutineScope` |
| **State Management**    | Predictable UI, handling configuration changes | `ViewModel`, `onSaveInstanceState`, Jetpack Compose `State`, `remember`, `derivedStateOf` |
| **Security & Privacy**  | Protecting user data and trust             | `EncryptedSharedPreferences`, `SQLCipher`, `BiometricPrompt`, `Certificate Pinning`, `ProGuard` |
| **SDK & Library Design**  | Creating robust, decoupled, reusable code  | Facade Pattern, Builder Pattern, `WorkManager` for internal jobs, Clear public API surface |

---

## Section 2 — High-Impact Interview Scenarios & How to Tackle Them

Here are classic design questions, augmented with the key points you should aim to discuss.

1.  **Design a Resilient Downloader SDK**
    *   **Key Discussion Points:**
        *   **API Design:** A clean public API (e.g., `Downloader.enqueue(request)`). Use a Facade to hide internal complexity.
        *   **Persistence & State:** Use a `Room` database to store download tasks (URL, file path, status, progress). This ensures recovery after a crash or reboot.
        *   **Execution Logic:** Use a `Foreground Service` managed by `WorkManager` for the actual download, showing user notification.
        *   **Features:** Discuss handling pause/resume, chunked downloading for large files, and network-aware logic (e.g., WiFi-only).

2.  **Design an Offline-First News Feed App**
    *   **Key Discussion Points:**
        *   **Source of Truth:** The `Room` database is the Single Source of Truth. The UI *only* observes the database (e.g., via `Flow`).
        *   **Data Flow:** `UI <- ViewModel <- Repository <- (Network + Database)`. Network fetches data, saves it to DB, DB notifies UI.
        *   **Pagination:** Use `Paging 3` library, which handles remote (network) and local (DB) paging out of the box.
        *   **Synchronization:** Use `WorkManager` for periodic background sync. For real-time updates, use WebSockets or FCM Push to trigger a sync.

3.  **Design a Chat Application (e.g., WhatsApp)**
    *   **Key Discussion Points:**
        *   **Real-time Communication:** Use **WebSockets** for persistent, bidirectional connection. REST is only for initial data loads (contacts, etc.).
        *   **Optimistic UI:** When a user sends a message, *immediately* save it to the local `Room` DB with a "sending" status. The UI updates instantly.
        *   **Reliability:** Use a local ID generated on the client. The server, upon receiving the message, confirms with the server ID and the original local ID, allowing the client to update the message status from "sending" to "sent".
        *   **Offline Queue:** Use `WorkManager` to detect when connection is restored and send any messages still in the "sending" state.

4.  **Design an Analytics SDK**
    *   **Key Discussion Points:**
        *   **API Design:** A simple, static API: `Analytics.log("event_name", params)`.
        *   **Queuing & Persistence:** Logged events go into a `Room` database immediately. Don't do network calls on the event-logging thread.
        *   **Batching & Uploading:** Use `WorkManager` to schedule a periodic background job. This job pulls a "batch" of events from the DB, sends them to the server, and on success, deletes them from the local DB.
        *   **Constraints:** `WorkManager` constraints are key. Upload only on `NetworkType.CONNECTED` (or `UNMETERED` to save user data). Implement retry/backoff strategies.

5.  **Design a Location-Tracking Feature (e.g., for a food delivery app)**
    *   **Key Discussion Points:**
        *   **Core Component:** A **Foreground Service** is mandatory for reliable background location tracking.
        *   **Battery Optimization:** This is the main challenge.
            *   Use the `FusedLocationProviderClient`.
            *   Adjust the update frequency (e.g., every 5s when moving, every 60s when stationary).
            *   Batch location updates before sending to the backend.
        *   **Data Flow:** Driver app sends location via WebSocket -> Backend processes -> Backend pushes location to Rider app via WebSocket.
        *   **UI Smoothing:** On the rider's map, animate the driver marker between location updates instead of having it jump.

6.  **Design an Image Loading & Caching Library (like Glide/Picasso)**
    *   **Key Discussion Points:**
        *   **Multi-Level Caching:** Memory cache (`LruCache`) for instant access, disk cache (`DiskLruCache`) for persistence across sessions.
        *   **Request Deduplication:** If the same URL is requested multiple times, only make one network call and share the result.
        *   **Threading:** All network/disk I/O on background threads (`Dispatchers.IO`), UI updates on `Dispatchers.Main`.
        *   **Lifecycle Awareness:** Cancel requests when the target `ImageView` is recycled or the Activity/Fragment is destroyed.

7.  **Design a Multi-Module App Architecture**
    *   **Key Discussion Points:**
        *   **Module Structure:** `:app`, `:core`, `:data`, `:design-system`, `:feature-*` modules with strict dependency rules.
        *   **Navigation Between Features:** Use Jetpack Navigation with feature-level nav graphs included in the main graph.
        *   **Dependency Injection:** Hilt modules in each layer provide dependencies to dependent modules.
        *   **Build Performance:** Parallel compilation, incremental builds, and configuration caching benefits.

8.  **Design a Push Notification System**
    *   **Key Discussion Points:**
        *   **FCM Integration:** Use Firebase Cloud Messaging for reliable delivery across Android versions.
        *   **Token Management:** Handle token refresh, sync tokens with backend, handle multi-device scenarios.
        *   **Notification Categories:** Different notification channels for different types (chat, promotions, alerts).
        *   **Deep Linking:** Handle notification taps that should navigate to specific app screens, even when app is killed.

9.  **Design a Video Streaming Feature (like TikTok/Netflix)**
    *   **Key Discussion Points:**
        *   **Player Implementation:** Use `ExoPlayer` for adaptive bitrate streaming (HLS/DASH support).
        *   **Pre-loading Strategy:** For feed-based apps, pre-load upcoming videos to reduce startup latency.
        *   **Bandwidth Adaptation:** Automatically adjust video quality based on network conditions.
        *   **Offline Downloads:** Use `ExoPlayer`'s `DownloadManager` for offline viewing capability.

10. **Design an E-commerce Shopping Cart System**
    *   **Key Discussion Points:**
        *   **State Management:** Complex state with items, quantities, pricing, discounts - consider MVI architecture.
        *   **Persistence:** Cart should survive app kills, use `Room` database with proper sync to backend.
        *   **Inventory Synchronization:** Handle cases where items become unavailable while in cart.
        *   **Payment Integration:** Secure payment flow with tokenization, PCI compliance considerations.

11. **Design a Social Media Feed with Rich Interactions**
    *   **Key Discussion Points:**
        *   **Feed Algorithm:** Discuss how to handle ranked vs chronological feeds, pagination strategies.
        *   **Rich Media:** Handle different post types (text, images, videos) with proper recycling in `RecyclerView`/`LazyColumn`.
        *   **Interaction States:** Like/unlike, follow/unfollow with optimistic UI updates and conflict resolution.
        *   **Real-time Updates:** Use WebSockets or FCM for live updates (new posts, likes, comments).

12. **Design an Authentication & Security Architecture**
    *   **Key Discussion Points:**
        *   **Token Management:** JWT tokens with automatic refresh, secure storage using `EncryptedSharedPreferences`.
        *   **Biometric Authentication:** Integrate `BiometricPrompt` for app unlock and sensitive operations.
        *   **Session Management:** Handle multiple device login, force logout scenarios, session timeout.
        *   **Security Measures:** Certificate pinning, root detection, obfuscation strategies.

13. **Design a Database Migration & Sync Strategy**
    *   **Key Discussion Points:**
        *   **Schema Evolution:** Handle `Room` database migrations gracefully, fallback strategies for failed migrations.
        *   **Conflict Resolution:** Last-write-wins vs operational transforms for concurrent edits.
        *   **Incremental Sync:** Use timestamps/version numbers to sync only changed data since last sync.
        *   **Large Dataset Handling:** Pagination, lazy loading, and archival strategies for performance.

14. **Design a Performance Monitoring SDK**
    *   **Key Discussion Points:**
        *   **Metrics Collection:** App startup time, frame drops, memory usage, network performance without impacting app performance.
        *   **Sampling Strategy:** Don't monitor 100% of users - implement intelligent sampling based on user segments.
        *   **Battery Consideration:** Use `WorkManager` for uploads, batch data collection, monitor only critical paths.
        *   **Crash Reporting Integration:** Correlation between performance metrics and crash occurrences.

15. **Design a Camera & Media Capture Pipeline**
    *   **Key Discussion Points:**
        *   **Camera API Choice:** Camera2 vs CameraX, handling different Android versions and device capabilities.
        *   **Media Processing:** Image compression, video encoding, thumbnail generation on background threads.
        *   **Storage Strategy:** Temporary vs permanent storage, cleanup policies, handling storage permissions.
        *   **Memory Management:** Large bitmaps handling, image recycling, preventing OOM errors.

16. **Design a Complex Search & Filtering System**
    *   **Key Discussion Points:**
        *   **Search Implementation:** Local search with `FTS` (Full Text Search) in `Room`, debounced queries, search suggestions.
        *   **Filter State Management:** Complex filter combinations, URL-like state serialization for deep linking.
        *   **Performance:** Efficient query optimization, result caching, pagination of search results.
        *   **Offline Search:** Local indexing strategies, sync with remote search when connectivity returns.

---

## Section 3 — Designing for Scale (The FAANG Factor)

Large-scale apps have problems that smaller apps don't. Showing awareness here signals senior-level thinking.

*   **Feature Flags & Phased Rollouts:**
    *   Design your features to be enabled/disabled remotely. A feature flag SDK (like Firebase Remote Config) determines if a feature's code path is active.
    *   This allows for A/B testing (showing different UI to different user segments) and safe, phased rollouts (e.g., enabling a feature for 1% of users, then 10%, etc.). You can also use a "kill switch" to instantly disable a buggy feature in production.
*   **Client-Side Telemetry & Observability:**
    *   Beyond just product analytics, your app should report on its own health: performance metrics (startup time, frame drops), crash rates, network errors.
    *   Design a robust system (similar to the analytics SDK) to log this data. This is crucial for monitoring the health of your app across millions of devices.
*   **Dynamic Delivery & App Size:**
    *   A modular architecture is the prerequisite for `Dynamic Feature Modules`.
    *   Discuss how you would split features into on-demand modules to reduce the initial APK size, a critical metric for user acquisition in many markets.

---

## Section 4 — Modern Android Considerations

*   **Jetpack Compose Performance:**
    *   **Stability:** Explain what makes a Composable "stable" vs "unstable" and why it matters for avoiding unnecessary recompositions. Data classes with `val` properties are key.
    *   **State Management:** Know when to use `remember`, `rememberSaveable`, and when to hoist state to a `ViewModel`. Use `derivedStateOf` to prevent recomposition when a derived value doesn't change.
    *   **Profiling:** Mention using Android Studio's Layout Inspector and Profiler to specifically debug recomposition issues.
*   **Designing for Foldables & Large Screens:**
    *   Don't design for a single phone screen size. Your UI should be adaptive.
    *   Discuss using adaptive layouts like `BoxWithConstraints`, creating different Composables for different screen size classes, and leveraging components like `SlidingPaneLayout` for list/detail views.
    *   State preservation across configuration changes (folding/unfolding) is critical.

---

## Section 5 — Common Interview Pitfalls & How to Avoid Them

1.  **Not Asking Clarifying Questions:** Don't jump into a solution. Spend the first 5 minutes asking about functional/non-functional requirements, scale, and constraints.
2.  **Not Discussing Trade-offs:** There is never one "right" answer. Say things like, "We could use WebSockets for real-time, but that's complex. An alternative is periodic polling, which is simpler but less efficient. For a chat app, WebSockets are the better trade-off."
3.  **Ignoring the "Non-Functional" Requirements:** Forgetting about battery, data usage, security, or scalability is a huge red flag.
4.  **Only Describing "What," Not "Why":** Don't just list technologies. Explain *why* you chose them. "I'll use WorkManager *because* it respects battery optimizations and survives reboots."
5.  **Designing in a Vacuum:** Acknowledge the backend. You don't need to design it, but show you understand the client-server contract (REST APIs, WebSockets, etc.).

This guide provides the **framework**. Your next step is to take each scenario and talk through it out loud, drawing diagrams on a whiteboard or paper. Good luck! 💪
