# Senior Android Developer Interview Guide

A comprehensive preparation guide for experienced Android/Kotlin developers targeting senior-level positions. This guide focuses on advanced concepts, internal workings, and frequently asked interview topics.

---

## 🧠 1. Core Android Fundamentals (The Senior Perspective)

### Activity & Fragment Lifecycle - Advanced Concepts

**Process Death & State Restoration:**
- **What happens:** Android kills your app process to free memory, but keeps the task in the recent apps list
- **Detection:** Use `adb shell am kill <package>` or Developer Options > "Don't keep activities"
- **Modern Solution:** `SavedStateHandle` in ViewModels automatically handles process death
```kotlin
class MyViewModel(private val savedStateHandle: SavedStateHandle) : ViewModel() {
    var userInput: String
        get() = savedStateHandle.get<String>("user_input") ?: ""
        set(value) = savedStateHandle.set("user_input", value)
}
```

**Configuration Changes Deep Dive:**
- **The Problem:** Activity recreation destroys all non-persistent state
- **ViewModel Survival:** ViewModels survive configuration changes but NOT process death
- **Best Practice:** Use `SavedStateHandle` + `ViewModel` + persistent storage (Room/DataStore)

**Fragment Lifecycle Gotchas:**
- Fragment's view lifecycle is separate from fragment lifecycle
- Use `viewLifecycleOwner` for UI-related observers to avoid leaks
- `onDestroyView()` vs `onDestroy()` - know when each is called

### Tasks and Back Stack - Real-World Scenarios

**Launch Modes & Their Use Cases:**
- **`singleTop`:** Notification handling - prevents duplicate activities when app is already open
- **`singleTask`:** Main/Home activities - ensures only one instance exists
- **`singleInstance`:** Camera/Phone apps - runs in its own task, no other activities allowed
- **`singleInstancePerTask`:** (API 28+) - One instance per task, allows multiple tasks

**Intent Flags in Practice:**
- `FLAG_ACTIVITY_CLEAR_TOP` + `FLAG_ACTIVITY_SINGLE_TOP` = Navigate to existing activity and clear above it
- `FLAG_ACTIVITY_NEW_TASK` + `FLAG_ACTIVITY_CLEAR_TASK` = Start fresh task (logout scenario)

### Services & Background Work - Post-Android 8 Reality

**Background Execution Limits:**
- **Doze Mode & App Standby:** System aggressively limits background processing
- **Background Service Limitations:** Started services killed after a few minutes when app goes to background
- **Whitelist Exceptions:** Only specific use cases (music, navigation, etc.) can run unrestricted

**Service Types & Modern Alternatives:**
```kotlin
// ❌ Old Way - Started Service (now heavily restricted)
class OldBackgroundService : Service() {
    // Will be killed by system in background
}

// ✅ Modern Way - WorkManager
class DataSyncWorker(context: Context, params: WorkerParameters) : CoroutineWorker(context, params) {
    override suspend fun doWork(): Result {
        // Guaranteed execution, even if app is killed
        return try {
            syncData()
            Result.success()
        } catch (e: Exception) {
            Result.retry()
        }
    }
}
```

**WorkManager Advanced Concepts:**
- **Constraints:** Network, battery, storage requirements
- **Chaining:** Sequential and parallel work execution
- **Unique Work:** Prevent duplicate work with `ExistingWorkPolicy`

### Context - Memory Leak Prevention

**Context Types & Their Lifecycles:**
- **Application Context:** Lives for entire app lifetime - safe for singletons
- **Activity Context:** Dies with activity - dangerous for long-lived objects
- **Service Context:** Lives with service - appropriate for service-scoped operations

**Common Leak Scenarios:**
```kotlin
// ❌ Memory Leak - Static reference holds Activity
class LeakyClass {
    companion object {
        private var activityReference: Activity? = null // LEAK!
    }
}

// ✅ Correct - Use Application Context or WeakReference
class SafeClass(private val appContext: Context) {
    // Use Application context for long-lived operations
}
```

---

## 🏗️ 2. Modern UI Development

### Jetpack Compose - Advanced State Management

**State Management Hierarchy:**
```kotlin
// Basic state - dies with composition
val count by remember { mutableStateOf(0) }

// Survives configuration changes
val input by rememberSaveable { mutableStateOf("") }

// Derived state - recomputes only when dependencies change
val isValid by remember(input) { 
    derivedStateOf { input.length > 3 }
}
```

**Recomposition Optimization:**
- **Stable Types:** Primitives, data classes with stable properties
- **@Stable Annotation:** Tells Compose a type is stable even if it can't prove it
- **@Immutable Annotation:** Stronger guarantee - object never changes after creation
- **Key Function:** Forces recomposition when key changes, skips when same

**Side Effects - When & Why:**
```kotlin
// LaunchedEffect - Coroutine tied to composition lifecycle
LaunchedEffect(userId) {
    viewModel.loadUser(userId)
}

// DisposableEffect - Cleanup required (listeners, observers)
DisposableEffect(lifecycleOwner) {
    val observer = LifecycleEventObserver { _, event -> }
    lifecycleOwner.lifecycle.addObserver(observer)
    onDispose {
        lifecycleOwner.lifecycle.removeObserver(observer)
    }
}

// SideEffect - Execute on every recomposition
SideEffect {
    analytics.trackScreenView(screenName)
}
```

**Unidirectional Data Flow (UDF) Architecture:**
```kotlin
@Composable
fun MyScreen(viewModel: MyViewModel = hiltViewModel()) {
    val uiState by viewModel.uiState.collectAsState()
    
    MyContent(
        state = uiState,
        onEvent = viewModel::handleEvent // Events flow up
    )
}

class MyViewModel : ViewModel() {
    private val _uiState = MutableStateFlow(MyUiState())
    val uiState: StateFlow<MyUiState> = _uiState.asStateFlow() // State flows down
    
    fun handleEvent(event: MyEvent) {
        // Handle events and update state
    }
}
```

### Traditional View System - Advanced Techniques

**Custom View Pipeline:**
```kotlin
class CustomView : View {
    override fun onMeasure(widthMeasureSpec: Int, heightMeasureSpec: Int) {
        // Determine size requirements
        val desiredWidth = calculateDesiredWidth()
        val desiredHeight = calculateDesiredHeight()
        
        val width = resolveSize(desiredWidth, widthMeasureSpec)
        val height = resolveSize(desiredHeight, heightMeasureSpec)
        
        setMeasuredDimension(width, height)
    }
    
    override fun onLayout(changed: Boolean, left: Int, top: Int, right: Int, bottom: Int) {
        // Position child views (for ViewGroup)
    }
    
    override fun onDraw(canvas: Canvas) {
        // Draw the view content
    }
}
```

**RecyclerView Performance Optimization:**
```kotlin
// DiffUtil for efficient updates
class MyDiffCallback : DiffUtil.ItemCallback<MyItem>() {
    override fun areItemsTheSame(oldItem: MyItem, newItem: MyItem): Boolean {
        return oldItem.id == newItem.id
    }
    
    override fun areContentsTheSame(oldItem: MyItem, newItem: MyItem): Boolean {
        return oldItem == newItem
    }
}

// Shared RecycledViewPool for multiple RecyclerViews
val sharedPool = RecyclerView.RecycledViewPool()
recyclerView1.setRecycledViewPool(sharedPool)
recyclerView2.setRecycledViewPool(sharedPool)

// Prefetching optimization
(recyclerView.layoutManager as LinearLayoutManager).apply {
    initialPrefetchItemCount = 4
}
```

---

## 🏛️ 3. Architecture & Design Patterns

### Architectural Patterns Comparison

**MVVM vs MVI:**

**MVVM (Model-View-ViewModel):**
- **Pros:** Simple, well-understood, good separation of concerns
- **Cons:** Can lead to multiple state sources, harder to track state changes
```kotlin
class MVVMViewModel : ViewModel() {
    private val _loading = MutableLiveData<Boolean>()
    val loading: LiveData<Boolean> = _loading
    
    private val _error = MutableLiveData<String?>()
    val error: LiveData<String?> = _error
    
    private val _data = MutableLiveData<List<Item>>()
    val data: LiveData<List<Item>> = _data
}
```

**MVI (Model-View-Intent):**
- **Pros:** Single source of truth, predictable state changes, easier testing
- **Cons:** More boilerplate, steeper learning curve
```kotlin
data class MyUiState(
    val loading: Boolean = false,
    val error: String? = null,
    val data: List<Item> = emptyList()
)

class MVIViewModel : ViewModel() {
    private val _uiState = MutableStateFlow(MyUiState())
    val uiState: StateFlow<MyUiState> = _uiState.asStateFlow()
    
    fun handleIntent(intent: MyIntent) {
        when (intent) {
            is MyIntent.LoadData -> loadData()
            is MyIntent.RefreshData -> refreshData()
        }
    }
}
```

### Clean Architecture - The Dependency Rule

**Layer Responsibilities:**
- **Presentation:** UI logic, state management, user interactions
- **Domain:** Business logic, use cases, entities (no Android dependencies)
- **Data:** Data sources, repositories, network/database implementations

**Dependency Rule:** Inner layers know nothing about outer layers
```kotlin
// Domain Layer - Pure Kotlin, no Android dependencies
class GetUserUseCase(private val userRepository: UserRepository) {
    suspend operator fun invoke(userId: String): Result<User> {
        return userRepository.getUser(userId)
    }
}

// Data Layer - Implements domain interfaces
class UserRepositoryImpl(
    private val apiService: ApiService,
    private val userDao: UserDao
) : UserRepository {
    override suspend fun getUser(userId: String): Result<User> {
        return try {
            val user = apiService.getUser(userId)
            userDao.insertUser(user.toEntity())
            Result.success(user.toDomain())
        } catch (e: Exception) {
            Result.failure(e)
        }
    }
}
```

### SOLID Principles with Android Examples

**Single Responsibility Principle:**
```kotlin
// ❌ Violates SRP - Multiple responsibilities
class UserManager {
    fun saveUser(user: User) { /* save to database */ }
    fun validateUser(user: User): Boolean { /* validation logic */ }
    fun sendWelcomeEmail(user: User) { /* email logic */ }
}

// ✅ Follows SRP - Single responsibility each
class UserValidator {
    fun validate(user: User): Boolean { /* validation only */ }
}

class UserRepository {
    fun save(user: User) { /* persistence only */ }
}

class EmailService {
    fun sendWelcomeEmail(user: User) { /* email only */ }
}
```

**Dependency Inversion Principle:**
```kotlin
// ❌ High-level module depends on low-level module
class OrderService {
    private val emailSender = SmtpEmailSender() // Concrete dependency
}

// ✅ Both depend on abstraction
interface EmailSender {
    suspend fun sendEmail(email: Email)
}

class OrderService(private val emailSender: EmailSender) {
    // Depends on abstraction, not concretion
}
```

### Dependency Injection - Hilt vs Koin

**Hilt (Compile-time DI):**
- **Pros:** Compile-time safety, better performance, generated code
- **Cons:** More complex setup, annotation-heavy, Dagger learning curve
```kotlin
@HiltAndroidApp
class MyApplication : Application()

@Module
@InstallIn(SingletonComponent::class)
object NetworkModule {
    @Provides
    @Singleton
    fun provideApiService(): ApiService = Retrofit.Builder()
        .baseUrl("https://api.example.com")
        .build()
        .create(ApiService::class.java)
}

@AndroidEntryPoint
class MainActivity : AppCompatActivity()

@HiltViewModel
class MyViewModel @Inject constructor(
    private val repository: UserRepository
) : ViewModel()
```

**Koin (Runtime DI/Service Locator):**
- **Pros:** Simple setup, Kotlin DSL, easier learning curve
- **Cons:** Runtime errors, reflection overhead, not true DI
```kotlin
val appModule = module {
    single<ApiService> { 
        Retrofit.Builder()
            .baseUrl("https://api.example.com")
            .build()
            .create(ApiService::class.java)
    }
    
    factory { UserRepository(get()) }
    viewModel { MyViewModel(get()) }
}

class MyApplication : Application() {
    override fun onCreate() {
        super.onCreate()
        startKoin {
            androidContext(this@MyApplication)
            modules(appModule)
        }
    }
}
```

---

## ⚡ 4. Concurrency & Asynchronous Programming

### Kotlin Coroutines - Advanced Concepts

**CoroutineContext Components:**
```kotlin
val context = Dispatchers.IO + 
              SupervisorJob() + 
              CoroutineName("MyCoroutine") +
              CoroutineExceptionHandler { _, throwable ->
                  Log.e("Coroutine", "Error", throwable)
              }
```

**Structured Concurrency Benefits:**
- **Automatic Cleanup:** Parent cancellation cancels all children
- **Exception Propagation:** Child failures can cancel parent (unless supervised)
- **Resource Management:** No leaked coroutines

**Exception Handling Strategies:**
```kotlin
// Strategy 1: try-catch for individual operations
viewModelScope.launch {
    try {
        val result = repository.getData()
        _uiState.value = UiState.Success(result)
    } catch (e: Exception) {
        _uiState.value = UiState.Error(e.message)
    }
}

// Strategy 2: CoroutineExceptionHandler for unhandled exceptions
private val exceptionHandler = CoroutineExceptionHandler { _, throwable ->
    _uiState.value = UiState.Error(throwable.message)
}

viewModelScope.launch(exceptionHandler) {
    val result = repository.getData() // Exceptions handled by handler
    _uiState.value = UiState.Success(result)
}

// Strategy 3: supervisorScope for independent child operations
viewModelScope.launch {
    supervisorScope {
        launch { loadUserData() } // Failure won't cancel other operations
        launch { loadSettings() }
        launch { loadNotifications() }
    }
}
```

### Flow - Cold vs Hot Streams

**Cold Flows (Flow):**
- New producer for each collector
- Lazy - only produces when collected
- Perfect for one-shot operations or transformations
```kotlin
fun getUsers(): Flow<List<User>> = flow {
    emit(repository.getUsers()) // New API call for each collector
}
```

**Hot Flows (StateFlow/SharedFlow):**
- Single producer, multiple collectors
- Active regardless of collectors
- Perfect for state management and events
```kotlin
class UserViewModel : ViewModel() {
    private val _users = MutableStateFlow<List<User>>(emptyList())
    val users: StateFlow<List<User>> = _users.asStateFlow()
    
    private val _events = MutableSharedFlow<UserEvent>()
    val events: SharedFlow<UserEvent> = _events.asSharedFlow()
    
    init {
        // Producer starts immediately, all collectors get same data
        viewModelScope.launch {
            _users.value = repository.getUsers()
        }
    }
}
```

**StateFlow vs SharedFlow Configuration:**
```kotlin
// StateFlow - Always has current value, conflated
val state = MutableStateFlow(initialValue)

// SharedFlow - Configurable replay and buffer
val events = MutableSharedFlow<Event>(
    replay = 1,           // Keep last 1 event for new subscribers
    extraBufferCapacity = 64, // Buffer 64 events before suspending
    onBufferOverflow = BufferOverflow.DROP_OLDEST
)
```

---

## 🚀 5. Performance, Optimization & Tooling

### Memory Optimization - Leak Detection & Prevention

**Common Memory Leak Sources:**
1. **Static References to Context:**
```kotlin
// ❌ Leak - Static holds Activity reference
object Analytics {
    private var context: Context? = null // LEAK!
}

// ✅ Safe - Use Application context
object Analytics {
    private var appContext: Context? = null
    
    fun init(context: Context) {
        appContext = context.applicationContext
    }
}
```

2. **Anonymous Inner Classes:**
```kotlin
// ❌ Leak - Handler holds Activity reference
class MainActivity : AppCompatActivity() {
    private val handler = Handler(Looper.getMainLooper())
    
    private fun scheduleTask() {
        handler.postDelayed({
            // This lambda holds reference to Activity
        }, 60000)
    }
}

// ✅ Safe - Static inner class + WeakReference
class MainActivity : AppCompatActivity() {
    private val handler = Handler(Looper.getMainLooper())
    
    private fun scheduleTask() {
        handler.postDelayed(MyRunnable(WeakReference(this)), 60000)
    }
    
    private class MyRunnable(private val activityRef: WeakReference<MainActivity>) : Runnable {
        override fun run() {
            val activity = activityRef.get() ?: return
            // Safe to use activity
        }
    }
}
```

**Memory Profiling Workflow:**
1. **Capture Heap Dump** during suspected leak scenario
2. **Analyze with Memory Profiler** - look for unexpected object counts
3. **Use LeakCanary** for automatic leak detection in debug builds
4. **Track Allocations** to find excessive object creation

### CPU & Rendering Performance

**Understanding UI Jank:**
- **16ms Rule:** 60 FPS = 16.67ms per frame
- **Main Thread Blocking:** Any work > 16ms causes dropped frames
- **Common Causes:** Heavy computations, synchronous I/O, inefficient layouts

**Systrace/Perfetto Analysis:**
```bash
# Capture systrace
python systrace.py -t 10 -o trace.html sched freq idle am wm gfx view binder_driver hal dalvik camera input res

# Key metrics to analyze:
# - Frame rendering time
# - CPU usage patterns  
# - Main thread blocking
# - GPU rendering pipeline
```

**Baseline Profiles for App Startup:**
```kotlin
// Generate baseline profile
@ExperimentalBaselineProfilesApi
@RunWith(AndroidJUnit4::class)
class BaselineProfileGenerator {
    @get:Rule
    val baselineProfileRule = BaselineProfileRule()

    @Test
    fun startup() = baselineProfileRule.collectBaselineProfile(
        packageName = "com.example.app"
    ) {
        startActivityAndWait()
        // Navigate through critical user journeys
    }
}
```

### Build System Optimization

**Gradle Build Performance:**
```kotlin
// gradle.properties optimizations
org.gradle.jvmargs=-Xmx4g -XX:MaxMetaspaceSize=512m
org.gradle.parallel=true
org.gradle.configureondemand=true
org.gradle.caching=true
android.useAndroidX=true
android.enableJetifier=false

// Build script optimizations
android {
    compileSdkVersion 34
    
    compileOptions {
        sourceCompatibility JavaVersion.VERSION_11
        targetCompatibility JavaVersion.VERSION_11
    }
    
    // Enable build cache
    buildCache {
        local {
            enabled = true
        }
    }
}
```

**Modularization Strategies:**
- **By Feature:** `:feature:login`, `:feature:profile`, `:feature:chat`
- **By Layer:** `:data`, `:domain`, `:presentation`
- **By Type:** `:core:network`, `:core:database`, `:core:ui`

**Benefits:**
- **Build Performance:** Parallel compilation, incremental builds
- **Code Organization:** Clear boundaries, easier navigation
- **Team Scalability:** Independent feature development

---

## 🧪 6. Testing

### Testing Pyramid Implementation

**Unit Tests (70%):**
```kotlin
@Test
fun `when user login is called with valid credentials, then success state is emitted`() = runTest {
    // Given
    val mockRepository = mockk<UserRepository>()
    coEvery { mockRepository.login(any(), any()) } returns Result.success(mockUser)
    val viewModel = LoginViewModel(mockRepository)
    
    // When
    viewModel.login("user@example.com", "password")
    
    // Then
    assertEquals(LoginUiState.Success(mockUser), viewModel.uiState.value)
}
```

**Integration Tests (20%):**
```kotlin
@Test
fun `when repository fetches user data, then database is updated`() = runTest {
    // Given
    val database = Room.inMemoryDatabaseBuilder(context, AppDatabase::class.java).build()
    val apiService = mockk<ApiService>()
    val repository = UserRepositoryImpl(apiService, database.userDao())
    
    coEvery { apiService.getUser(any()) } returns mockApiUser
    
    // When
    repository.getUser("123")
    
    // Then
    val savedUser = database.userDao().getUser("123")
    assertNotNull(savedUser)
}
```

**UI Tests (10%):**
```kotlin
@Test
fun whenLoginButtonClicked_thenNavigatesToHomeScreen() {
    // Given
    composeTestRule.setContent {
        LoginScreen(
            onNavigateToHome = { navigatedToHome = true }
        )
    }
    
    // When
    composeTestRule.onNodeWithText("Login").performClick()
    
    // Then
    assertTrue(navigatedToHome)
}
```

### Testing Frameworks & Tools

**Coroutine Testing:**
```kotlin
@Test
fun testCoroutineFlow() = runTest {
    val flow = flowOf(1, 2, 3).onEach { delay(1000) }
    
    flow.test {
        assertEquals(1, awaitItem())
        assertEquals(2, awaitItem())
        assertEquals(3, awaitItem())
        awaitComplete()
    }
}
```

**MockK Advanced Features:**
```kotlin
// Relaxed mocks - return default values
val mockRepository = mockk<UserRepository>(relaxed = true)

// Verify call order
verifyOrder {
    repository.clearCache()
    repository.fetchUsers()
}

// Capture arguments
val slot = slot<User>()
every { repository.saveUser(capture(slot)) } returns Unit
// Use slot.captured to access the argument
```

---

## 💼 7. Android System Design

### System Design Interview Approach

**1. Clarify Requirements (5 minutes):**
- Functional requirements (what features?)
- Non-functional requirements (users, performance, offline support?)
- Platform constraints (Android-specific considerations)

**2. High-Level Design (10 minutes):**
- Draw major components and their interactions
- Identify data flow and API contracts
- Consider offline/online scenarios

**3. Detailed Design (15 minutes):**
- Deep dive into critical components
- Discuss data models and storage
- Address scalability and performance

**4. Trade-offs & Alternatives (5 minutes):**
- Discuss alternative approaches
- Explain why you chose your solution
- Identify potential bottlenecks

### Example: Offline-First News Reader App

**Requirements Clarification:**
- Read articles offline after initial download
- Sync new articles when online
- Handle conflicts (article updates)
- Support 10K+ articles locally

**High-Level Architecture:**
```
┌─────────────────┐    ┌──────────────────┐    ┌─────────────────┐
│   Presentation  │    │      Domain      │    │      Data       │
│                 │    │                  │    │                 │
│ • ArticleScreen │◄──►│ • GetArticlesUC  │◄──►│ • ArticleRepo   │◄──► 
│ • ArticleList   │    │ • SyncArticlesUC │    │ • RemoteDS      │
│ • OfflineUI     │    │ • MarkReadUC     │    │ • LocalDS (Room)│
└─────────────────┘    └──────────────────┘    └─────────────────┘
                                                         │
                                                ┌─────────────────┐
                                                │   WorkManager   │
                                                │ • SyncWorker    │
                                                │ • ImageWorker   │
                                                └─────────────────┘
```

**Data Model:**
```kotlin
@Entity(tableName = "articles")
data class ArticleEntity(
    @PrimaryKey val id: String,
    val title: String,
    val content: String,
    val imageUrl: String?,
    val publishedAt: Long,
    val lastModified: Long,
    val isRead: Boolean = false,
    val isFavorite: Boolean = false,
    val syncStatus: SyncStatus = SyncStatus.SYNCED
)

enum class SyncStatus {
    SYNCED,      // In sync with server
    PENDING,     // Local changes not synced
    CONFLICT     // Server has newer version
}
```

**Caching Strategy:**
```kotlin
class ArticleRepository(
    private val remoteDataSource: ArticleRemoteDataSource,
    private val localDataSource: ArticleLocalDataSource,
    private val syncManager: SyncManager
) {
    // Cache-aside pattern with offline-first approach
    suspend fun getArticles(): Flow<List<Article>> {
        return localDataSource.getArticles()
            .onStart {
                // Trigger background sync
                syncManager.scheduleSync()
            }
    }
    
    suspend fun syncArticles() {
        try {
            val remoteArticles = remoteDataSource.getArticles()
            val localArticles = localDataSource.getAllArticles()
            
            // Conflict resolution: Last-write-wins with user preference
            val mergedArticles = mergeWithConflictResolution(localArticles, remoteArticles)
            localDataSource.insertArticles(mergedArticles)
        } catch (e: NetworkException) {
            // Graceful degradation - continue with cached data
            Timber.w("Sync failed, using cached data: ${e.message}")
        }
    }
}
```

**Key Trade-offs Discussed:**
- **Storage:** SQLite vs Files (chose SQLite for querying capabilities)
- **Sync Strategy:** Real-time vs Periodic (chose periodic for battery efficiency)
- **Conflict Resolution:** Last-write-wins vs Manual resolution (chose LWW for simplicity)
- **Image Caching:** In-app vs System cache (chose Coil with custom cache for control)

### Example: Real-Time Chat Feature

**Architecture Decisions:**
- **Real-time Communication:** WebSocket for active chat, FCM for background notifications
- **Message Persistence:** Room database with message status tracking
- **State Management:** MVI pattern with sealed classes for message states

```kotlin
sealed class MessageStatus {
    object Sending : MessageStatus()
    object Sent : MessageStatus()
    object Delivered : MessageStatus()
    object Read : MessageStatus()
    data class Failed(val error: String) : MessageStatus()
}

data class ChatUiState(
    val messages: List<Message> = emptyList(),
    val connectionStatus: ConnectionStatus = ConnectionStatus.Connecting,
    val typingUsers: List<User> = emptyList(),
    val isLoading: Boolean = false
)
```

**Message Delivery Guarantees:**
```kotlin
class MessageRepository {
    suspend fun sendMessage(message: Message) {
        // 1. Save to local DB immediately (optimistic UI)
        localDataSource.insertMessage(message.copy(status = MessageStatus.Sending))
        
        try {
            // 2. Send via WebSocket
            val sentMessage = webSocketClient.sendMessage(message)
            
            // 3. Update status on success
            localDataSource.updateMessageStatus(message.id, MessageStatus.Sent)
        } catch (e: Exception) {
            // 4. Mark as failed, retry later
            localDataSource.updateMessageStatus(message.id, MessageStatus.Failed(e.message))
            retryManager.scheduleRetry(message.id)
        }
    }
}
```

**Trade-offs:**
- **WebSocket vs HTTP Polling:** WebSocket for real-time, but more complex connection management
- **Message Ordering:** Server timestamps vs client-side ordering (chose server for consistency)
- **Offline Handling:** Queue messages locally, send when reconnected
- **Encryption:** End-to-end vs transport-level (depends on security requirements)

---

## 🎯 Interview Success Tips

### Technical Communication
- **Start with clarifying questions** - don't assume requirements
- **Think out loud** - explain your reasoning process
- **Draw diagrams** - visual representations help communicate complex ideas
- **Discuss trade-offs** - every decision has pros and cons
- **Be honest about unknowns** - it's okay to say "I don't know, but here's how I'd find out"

### Code Quality Indicators
- **Error handling** - always consider failure scenarios
- **Resource management** - show awareness of memory, battery, network
- **Testing approach** - demonstrate how you'd verify your solution
- **Performance considerations** - discuss scalability and optimization
- **Security awareness** - consider data protection and user privacy

### Common Pitfalls to Avoid
- **Over-engineering** - start simple, then add complexity if needed
- **Ignoring constraints** - always consider Android-specific limitations
- **Forgetting edge cases** - network failures, low memory, process death
- **Not asking questions** - clarify requirements before diving into solutions
- **Focusing only on happy path** - discuss error scenarios and recovery

Remember: Senior interviews test not just what you know, but how you think, communicate, and approach complex problems. Demonstrate your experience through practical examples and show that you understand the real-world implications of your technical decisions.

---

## 📱 8. Advanced Android Components & Storage

### Fragment Transactions - Advanced Management

**Fragment Transaction Types:**
```kotlin
// Replace - destroys current fragment
supportFragmentManager.beginTransaction()
    .replace(R.id.container, NewFragment())
    .addToBackStack("transaction_name")
    .commit()

// Add - keeps existing fragments
supportFragmentManager.beginTransaction()
    .add(R.id.container, OverlayFragment())
    .addToBackStack(null)
    .commit()

// Show/Hide - for performance when switching frequently
supportFragmentManager.beginTransaction()
    .hide(currentFragment)
    .show(targetFragment)
    .commit()
```

**Fragment Communication Patterns:**
```kotlin
// ✅ Modern Way - Shared ViewModel
class SharedViewModel : ViewModel() {
    private val _selectedItem = MutableLiveData<Item>()
    val selectedItem: LiveData<Item> = _selectedItem
    
    fun selectItem(item: Item) {
        _selectedItem.value = item
    }
}

// In both fragments
class FragmentA : Fragment() {
    private val sharedViewModel: SharedViewModel by activityViewModels()
    
    private fun onItemClick(item: Item) {
        sharedViewModel.selectItem(item)
    }
}

class FragmentB : Fragment() {
    private val sharedViewModel: SharedViewModel by activityViewModels()
    
    override fun onViewCreated(view: View, savedInstanceState: Bundle?) {
        sharedViewModel.selectedItem.observe(viewLifecycleOwner) { item ->
            // React to selection
        }
    }
}
```

**Fragment Result API (Modern Approach):**
```kotlin
// Fragment A - Setting result listener
override fun onCreate(savedInstanceState: Bundle?) {
    super.onCreate(savedInstanceState)
    
    setFragmentResultListener("request_key") { _, bundle ->
        val result = bundle.getString("result_data")
        // Handle result
    }
}

// Fragment B - Sending result
private fun sendResult() {
    setFragmentResult("request_key", bundleOf("result_data" to "value"))
    findNavController().popBackStack()
}
```

### Storage Solutions - Comprehensive Overview

**Persistent Storage Options:**

**1. Room Database (Structured Data):**
```kotlin
@Entity(tableName = "users")
data class User(
    @PrimaryKey val id: String,
    val name: String,
    val email: String,
    @ColumnInfo(name = "created_at") val createdAt: Long
)

@Dao
interface UserDao {
    @Query("SELECT * FROM users WHERE id = :userId")
    suspend fun getUser(userId: String): User?
    
    @Insert(onConflict = OnConflictStrategy.REPLACE)
    suspend fun insertUser(user: User)
    
    @Transaction
    @Query("SELECT * FROM users WHERE id IN (:userIds)")
    suspend fun getUsersWithPosts(userIds: List<String>): List<UserWithPosts>
}

@Database(
    entities = [User::class, Post::class],
    version = 2,
    exportSchema = true
)
@TypeConverters(Converters::class)
abstract class AppDatabase : RoomDatabase() {
    abstract fun userDao(): UserDao
    
    companion object {
        @Volatile
        private var INSTANCE: AppDatabase? = null
        
        fun getDatabase(context: Context): AppDatabase {
            return INSTANCE ?: synchronized(this) {
                val instance = Room.databaseBuilder(
                    context.applicationContext,
                    AppDatabase::class.java,
                    "app_database"
                )
                .addMigrations(MIGRATION_1_2)
                .build()
                INSTANCE = instance
                instance
            }
        }
        
        private val MIGRATION_1_2 = object : Migration(1, 2) {
            override fun migrate(database: SupportSQLiteDatabase) {
                database.execSQL("ALTER TABLE users ADD COLUMN created_at INTEGER NOT NULL DEFAULT 0")
            }
        }
    }
}
```

**2. DataStore (Key-Value & Typed):**
```kotlin
// Preferences DataStore
val Context.dataStore: DataStore<Preferences> by preferencesDataStore(name = "settings")

class SettingsRepository(private val dataStore: DataStore<Preferences>) {
    private object PreferencesKeys {
        val THEME_MODE = stringPreferencesKey("theme_mode")
        val NOTIFICATIONS_ENABLED = booleanPreferencesKey("notifications_enabled")
    }
    
    val themeMode: Flow<String> = dataStore.data
        .catch { exception ->
            if (exception is IOException) {
                emit(emptyPreferences())
            } else {
                throw exception
            }
        }
        .map { preferences ->
            preferences[PreferencesKeys.THEME_MODE] ?: "system"
        }
    
    suspend fun setThemeMode(mode: String) {
        dataStore.edit { preferences ->
            preferences[PreferencesKeys.THEME_MODE] = mode
        }
    }
}

// Proto DataStore (Type-safe)
@Serializable
data class UserPreferences(
    val theme: Theme = Theme.SYSTEM,
    val language: String = "en",
    val notificationsEnabled: Boolean = true
)

val Context.userPreferencesStore: DataStore<UserPreferences> by dataStore(
    fileName = "user_preferences.pb",
    serializer = UserPreferencesSerializer
)
```

**3. File Storage:**
```kotlin
class FileStorageManager(private val context: Context) {
    
    // Internal storage (private to app)
    suspend fun saveToInternalStorage(filename: String, data: String) {
        withContext(Dispatchers.IO) {
            context.openFileOutput(filename, Context.MODE_PRIVATE).use { output ->
                output.write(data.toByteArray())
            }
        }
    }
    
    // External storage (with scoped storage)
    suspend fun saveToExternalStorage(filename: String, data: ByteArray) {
        if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.Q) {
            // Scoped Storage (Android 10+)
            val resolver = context.contentResolver
            val contentValues = ContentValues().apply {
                put(MediaStore.MediaColumns.DISPLAY_NAME, filename)
                put(MediaStore.MediaColumns.MIME_TYPE, "application/octet-stream")
                put(MediaStore.MediaColumns.RELATIVE_PATH, Environment.DIRECTORY_DOWNLOADS)
            }
            
            val uri = resolver.insert(MediaStore.Downloads.EXTERNAL_CONTENT_URI, contentValues)
            uri?.let {
                resolver.openOutputStream(it)?.use { output ->
                    output.write(data)
                }
            }
        } else {
            // Legacy external storage
            val file = File(Environment.getExternalStoragePublicDirectory(Environment.DIRECTORY_DOWNLOADS), filename)
            file.writeBytes(data)
        }
    }
}
```

**In-Memory Storage:**
```kotlin
// LruCache for memory-efficient caching
class ImageCache {
    private val maxMemory = (Runtime.getRuntime().maxMemory() / 1024).toInt()
    private val cacheSize = maxMemory / 8
    
    private val memoryCache = object : LruCache<String, Bitmap>(cacheSize) {
        override fun sizeOf(key: String, bitmap: Bitmap): Int {
            return bitmap.byteCount / 1024
        }
    }
    
    fun getBitmap(key: String): Bitmap? = memoryCache.get(key)
    
    fun putBitmap(key: String, bitmap: Bitmap) {
        if (getBitmap(key) == null) {
            memoryCache.put(key, bitmap)
        }
    }
}

// Application-level singleton for global state
class AppStateManager private constructor() {
    private val _currentUser = MutableStateFlow<User?>(null)
    val currentUser: StateFlow<User?> = _currentUser.asStateFlow()
    
    private val cache = mutableMapOf<String, Any>()
    
    fun cacheData(key: String, data: Any) {
        cache[key] = data
    }
    
    fun getCachedData(key: String): Any? = cache[key]
    
    companion object {
        @Volatile
        private var INSTANCE: AppStateManager? = null
        
        fun getInstance(): AppStateManager {
            return INSTANCE ?: synchronized(this) {
                INSTANCE ?: AppStateManager().also { INSTANCE = it }
            }
        }
    }
}
```

### BroadcastReceiver - Advanced Usage

**Static vs Dynamic Registration:**
```kotlin
// Static Registration (Manifest) - Limited in modern Android
<receiver android:name=".BootReceiver"
    android:enabled="true"
    android:exported="true">
    <intent-filter android:priority="1000">
        <action android:name="android.intent.action.BOOT_COMPLETED" />
    </intent-filter>
</receiver>

// Dynamic Registration (Recommended)
class MainActivity : AppCompatActivity() {
    private val networkReceiver = object : BroadcastReceiver() {
        override fun onReceive(context: Context?, intent: Intent?) {
            val connectivityManager = context?.getSystemService(Context.CONNECTIVITY_SERVICE) as ConnectivityManager
            val activeNetwork = connectivityManager.activeNetworkInfo
            val isConnected = activeNetwork?.isConnectedOrConnecting == true
            
            // Handle network state change
        }
    }
    
    override fun onResume() {
        super.onResume()
        val filter = IntentFilter(ConnectivityManager.CONNECTIVITY_ACTION)
        registerReceiver(networkReceiver, filter)
    }
    
    override fun onPause() {
        super.onPause()
        unregisterReceiver(networkReceiver)
    }
}
```

**Modern Alternatives:**
```kotlin
// Use WorkManager for background tasks
class NetworkSyncWorker(context: Context, params: WorkerParameters) : CoroutineWorker(context, params) {
    override suspend fun doWork(): Result {
        return try {
            syncData()
            Result.success()
        } catch (e: Exception) {
            Result.retry()
        }
    }
}

// Schedule with network constraint
val constraints = Constraints.Builder()
    .setRequiredNetworkType(NetworkType.CONNECTED)
    .build()

val syncWork = OneTimeWorkRequestBuilder<NetworkSyncWorker>()
    .setConstraints(constraints)
    .build()

WorkManager.getInstance(context).enqueue(syncWork)
```

### ContentProvider & ContentResolver

**ContentProvider Implementation:**
```kotlin
class MyContentProvider : ContentProvider() {
    private lateinit var dbHelper: DatabaseHelper
    
    companion object {
        const val AUTHORITY = "com.example.provider"
        const val USERS_PATH = "users"
        val CONTENT_URI: Uri = Uri.parse("content://$AUTHORITY/$USERS_PATH")
        
        private const val USERS = 1
        private const val USER_ID = 2
        
        private val uriMatcher = UriMatcher(UriMatcher.NO_MATCH).apply {
            addURI(AUTHORITY, USERS_PATH, USERS)
            addURI(AUTHORITY, "$USERS_PATH/#", USER_ID)
        }
    }
    
    override fun onCreate(): Boolean {
        dbHelper = DatabaseHelper(context!!)
        return true
    }
    
    override fun query(
        uri: Uri,
        projection: Array<String>?,
        selection: String?,
        selectionArgs: Array<String>?,
        sortOrder: String?
    ): Cursor? {
        val db = dbHelper.readableDatabase
        
        return when (uriMatcher.match(uri)) {
            USERS -> db.query("users", projection, selection, selectionArgs, null, null, sortOrder)
            USER_ID -> {
                val id = uri.lastPathSegment
                db.query("users", projection, "id=?", arrayOf(id), null, null, sortOrder)
            }
            else -> throw IllegalArgumentException("Unknown URI: $uri")
        }
    }
    
    override fun insert(uri: Uri, values: ContentValues?): Uri? {
        val db = dbHelper.writableDatabase
        
        return when (uriMatcher.match(uri)) {
            USERS -> {
                val id = db.insert("users", null, values)
                if (id > 0) {
                    val newUri = ContentUris.withAppendedId(CONTENT_URI, id)
                    context?.contentResolver?.notifyChange(newUri, null)
                    newUri
                } else null
            }
            else -> throw IllegalArgumentException("Unknown URI: $uri")
        }
    }
    
    override fun getType(uri: Uri): String? {
        return when (uriMatcher.match(uri)) {
            USERS -> "vnd.android.cursor.dir/vnd.example.user"
            USER_ID -> "vnd.android.cursor.item/vnd.example.user"
            else -> null
        }
    }
    
    // Implement update() and delete() similarly
}
```

**ContentResolver Usage:**
```kotlin
class UserRepository(private val context: Context) {
    
    suspend fun getAllUsers(): List<User> = withContext(Dispatchers.IO) {
        val users = mutableListOf<User>()
        
        context.contentResolver.query(
            MyContentProvider.CONTENT_URI,
            null, null, null, null
        )?.use { cursor ->
            while (cursor.moveToNext()) {
                val id = cursor.getString(cursor.getColumnIndexOrThrow("id"))
                val name = cursor.getString(cursor.getColumnIndexOrThrow("name"))
                users.add(User(id, name))
            }
        }
        
        users
    }
    
    suspend fun insertUser(user: User): Uri? = withContext(Dispatchers.IO) {
        val values = ContentValues().apply {
            put("id", user.id)
            put("name", user.name)
        }
        
        context.contentResolver.insert(MyContentProvider.CONTENT_URI, values)
    }
}
```

---

## 🔒 9. Security & Obfuscation

### Code Obfuscation - ProGuard/R8

**R8 Configuration (build.gradle.kts):**
```kotlin
android {
    buildTypes {
        release {
            isMinifyEnabled = true
            isShrinkResources = true
            proguardFiles(
                getDefaultProguardFile("proguard-android-optimize.txt"),
                "proguard-rules.pro"
            )
        }
    }
}
```

**ProGuard Rules (proguard-rules.pro):**
```proguard
# Keep application class
-keep public class * extends android.app.Application

# Keep all model classes (for JSON serialization)
-keep class com.example.data.model.** { *; }

# Keep Retrofit interfaces
-keep interface com.example.network.** { *; }

# Keep Room entities and DAOs
-keep class * extends androidx.room.RoomDatabase
-keep @androidx.room.Entity class *
-keep @androidx.room.Dao class *

# Keep custom views
-keep public class * extends android.view.View {
    public <init>(android.content.Context);
    public <init>(android.content.Context, android.util.AttributeSet);
    public <init>(android.content.Context, android.util.AttributeSet, int);
}

# Remove logging in release
-assumenosideeffects class android.util.Log {
    public static boolean isLoggable(java.lang.String, int);
    public static int v(...);
    public static int i(...);
    public static int w(...);
    public static int d(...);
    public static int e(...);
}

# Optimize and obfuscate
-optimizations !code/simplification/arithmetic,!code/simplification/cast,!field/*,!class/merging/*
-optimizationpasses 5
-allowaccessmodification
-dontpreverify
```

### Security Best Practices

**1. Secure Network Communication:**
```kotlin
// Certificate Pinning with OkHttp
class NetworkSecurityManager {
    
    fun createSecureOkHttpClient(): OkHttpClient {
        val certificatePinner = CertificatePinner.Builder()
            .add("api.example.com", "sha256/AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA=")
            .add("api.example.com", "sha256/BBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBB=")
            .build()
        
        return OkHttpClient.Builder()
            .certificatePinner(certificatePinner)
            .addInterceptor(createAuthInterceptor())
            .build()
    }
    
    private fun createAuthInterceptor() = Interceptor { chain ->
        val originalRequest = chain.request()
        val authenticatedRequest = originalRequest.newBuilder()
            .header("Authorization", "Bearer ${getSecureToken()}")
            .build()
        chain.proceed(authenticatedRequest)
    }
}
```

**2. Secure Storage:**
```kotlin
// EncryptedSharedPreferences
class SecureStorageManager(private val context: Context) {
    
    private val masterKey = MasterKey.Builder(context)
        .setKeyScheme(MasterKey.KeyScheme.AES256_GCM)
        .build()
    
    private val encryptedPrefs = EncryptedSharedPreferences.create(
        context,
        "secure_prefs",
        masterKey,
        EncryptedSharedPreferences.PrefKeyEncryptionScheme.AES256_SIV,
        EncryptedSharedPreferences.PrefValueEncryptionScheme.AES256_GCM
    )
    
    fun storeSecureData(key: String, value: String) {
        encryptedPrefs.edit()
            .putString(key, value)
            .apply()
    }
    
    fun getSecureData(key: String): String? {
        return encryptedPrefs.getString(key, null)
    }
}

// Keystore for cryptographic keys
class KeystoreManager {
    
    fun generateSecretKey(alias: String) {
        val keyGenerator = KeyGenerator.getInstance(KeyProperties.KEY_ALGORITHM_AES, "AndroidKeyStore")
        val keyGenParameterSpec = KeyGenParameterSpec.Builder(
            alias,
            KeyProperties.PURPOSE_ENCRYPT or KeyProperties.PURPOSE_DECRYPT
        )
            .setBlockModes(KeyProperties.BLOCK_MODE_GCM)
            .setEncryptionPaddings(KeyProperties.ENCRYPTION_PADDING_NONE)
            .setUserAuthenticationRequired(true)
            .setUserAuthenticationValidityDurationSeconds(30)
            .build()
        
        keyGenerator.init(keyGenParameterSpec)
        keyGenerator.generateKey()
    }
    
    fun encryptData(alias: String, data: ByteArray): ByteArray {
        val keyStore = KeyStore.getInstance("AndroidKeyStore")
        keyStore.load(null)
        
        val secretKey = keyStore.getKey(alias, null) as SecretKey
        val cipher = Cipher.getInstance("AES/GCM/NoPadding")
        cipher.init(Cipher.ENCRYPT_MODE, secretKey)
        
        return cipher.doFinal(data)
    }
}
```

**3. Runtime Application Self-Protection (RASP):**
```kotlin
class SecurityManager {
    
    fun checkAppIntegrity(): Boolean {
        return checkSignature() && 
               checkDebuggingStatus() && 
               checkRootStatus() &&
               checkEmulatorStatus()
    }
    
    private fun checkSignature(): Boolean {
        // Verify app signature hasn't been tampered with
        val packageInfo = context.packageManager.getPackageInfo(
            context.packageName, 
            PackageManager.GET_SIGNATURES
        )
        val signature = packageInfo.signatures[0].toCharsString()
        return signature == EXPECTED_SIGNATURE_HASH
    }
    
    private fun checkDebuggingStatus(): Boolean {
        return (context.applicationInfo.flags and ApplicationInfo.FLAG_DEBUGGABLE) == 0
    }
    
    private fun checkRootStatus(): Boolean {
        val rootPaths = arrayOf(
            "/system/app/Superuser.apk",
            "/sbin/su",
            "/system/bin/su",
            "/system/xbin/su"
        )
        return rootPaths.none { File(it).exists() }
    }
}
```

---

## 📦 10. Library Creation & Publication

### Creating Android Library (AAR)

**Library Module Setup:**
```kotlin
// library/build.gradle.kts
plugins {
    id("com.android.library")
    id("org.jetbrains.kotlin.android")
    id("maven-publish")
}

android {
    namespace = "com.example.mylibrary"
    compileSdk = 34
    
    defaultConfig {
        minSdk = 21
        
        testInstrumentationRunner = "androidx.test.runner.AndroidJUnitRunner"
        consumerProguardFiles("consumer-rules.pro")
    }
    
    buildTypes {
        release {
            isMinifyEnabled = false
            proguardFiles(
                getDefaultProguardFile("proguard-android-optimize.txt"),
                "proguard-rules.pro"
            )
        }
    }
    
    publishing {
        singleVariant("release") {
            withSourcesJar()
            withJavadocJar()
        }
    }
}

dependencies {
    api("androidx.core:core-ktx:1.12.0")
    implementation("androidx.appcompat:appcompat:1.6.1")
    
    testImplementation("junit:junit:4.13.2")
    androidTestImplementation("androidx.test.ext:junit:1.1.5")
}
```

**Publishing Configuration:**
```kotlin
publishing {
    publications {
        register<MavenPublication>("release") {
            groupId = "com.example"
            artifactId = "mylibrary"
            version = "1.0.0"
            
            afterEvaluate {
                from(components["release"])
            }
            
            pom {
                name.set("My Android Library")
                description.set("A useful Android library")
                url.set("https://github.com/username/mylibrary")
                
                licenses {
                    license {
                        name.set("The Apache License, Version 2.0")
                        url.set("http://www.apache.org/licenses/LICENSE-2.0.txt")
                    }
                }
                
                developers {
                    developer {
                        id.set("username")
                        name.set("Your Name")
                        email.set("your.email@example.com")
                    }
                }
            }
        }
    }
    
    repositories {
        maven {
            name = "GitHubPackages"
            url = uri("https://maven.pkg.github.com/username/mylibrary")
            credentials {
                username = project.findProperty("gpr.user") as String? ?: System.getenv("USERNAME")
                password = project.findProperty("gpr.key") as String? ?: System.getenv("TOKEN")
            }
        }
    }
}
```

**Library API Design:**
```kotlin
// Public API - Keep stable and backward compatible
class MyLibrary private constructor() {
    
    companion object {
        @JvmStatic
        fun initialize(context: Context, config: LibraryConfig) {
            // Initialize library
        }
        
        @JvmStatic
        fun getInstance(): MyLibrary {
            return instance ?: throw IllegalStateException("Library not initialized")
        }
    }
    
    // Builder pattern for complex configuration
    class Builder {
        private var apiKey: String? = null
        private var enableLogging: Boolean = false
        
        fun setApiKey(key: String) = apply { this.apiKey = key }
        fun enableLogging(enable: Boolean) = apply { this.enableLogging = enable }
        
        fun build(): LibraryConfig {
            requireNotNull(apiKey) { "API key is required" }
            return LibraryConfig(apiKey!!, enableLogging)
        }
    }
}

// Version compatibility
@RequiresApi(Build.VERSION_CODES.O)
fun newApiFeature() {
    // Use new API features
}

@JvmOverloads
fun flexibleMethod(
    required: String,
    optional: String = "default",
    callback: ((Result) -> Unit)? = null
) {
    // Method with optional parameters for Java compatibility
}
```

### App Size Optimization

**APK Size Reduction Techniques:**

**1. Resource Optimization:**
```kotlin
// build.gradle.kts
android {
    buildTypes {
        release {
            isShrinkResources = true
            isMinifyEnabled = true
        }
    }
    
    // Remove unused resources
    bundle {
        language {
            enableSplit = true
        }
        density {
            enableSplit = true
        }
        abi {
            enableSplit = true
        }
    }
    
    // Compress images
    androidResources {
        noCompress += listOf("webp", "jpg", "jpeg", "png", "gif")
    }
}
```

**2. Native Library Optimization:**
```kotlin
android {
    defaultConfig {
        ndk {
            abiFilters += listOf("arm64-v8a", "armeabi-v7a") // Remove x86 if not needed
        }
    }
    
    packagingOptions {
        pickFirst("**/libc++_shared.so")
        pickFirst("**/libjsc.so")
    }
}
```

**3. Dynamic Feature Modules:**
```kotlin
// feature/build.gradle.kts
plugins {
    id("com.android.dynamic-feature")
}

android {
    namespace = "com.example.feature"
}

dependencies {
    implementation(project(":app"))
}

// In app/build.gradle.kts
android {
    dynamicFeatures += setOf(":feature")
}
```

**4. App Bundle Optimization:**
```kotlin
// Upload App Bundle instead of APK for automatic optimization
./gradlew bundleRelease

// Analyze bundle size
bundletool build-apks --bundle=app-release.aab --output=app.apks
bundletool get-size total --apks=app.apks
```

---

## 🧪 11. Comprehensive Testing Strategies

### UI/UX Testing Advanced Techniques

**Compose Testing:**
```kotlin
@get:Rule
val composeTestRule = createComposeRule()

@Test
fun testUserInteractionFlow() {
    var buttonClicked = false
    
    composeTestRule.setContent {
        MyScreen(
            onButtonClick = { buttonClicked = true }
        )
    }
    
    // Test UI state
    composeTestRule
        .onNodeWithText("Click Me")
        .assertIsDisplayed()
        .performClick()
    
    // Verify interaction
    assertTrue(buttonClicked)
    
    // Test state changes
    composeTestRule
        .onNodeWithText("Clicked!")
        .assertIsDisplayed()
}

@Test
fun testAnimationCompletion() {
    composeTestRule.setContent {
        AnimatedComponent()
    }
    
    // Trigger animation
    composeTestRule
        .onNodeWithTag("trigger")
        .performClick()
    
    // Wait for animation to complete
    composeTestRule.waitForIdle()
    
    // Verify final state
    composeTestRule
        .onNodeWithTag("animated_element")
        .assertIsDisplayed()
}
```

**Espresso Advanced Patterns:**
```kotlin
@Test
fun testRecyclerViewInteraction() {
    // Custom ViewMatcher
    fun withRecyclerView(recyclerViewId: Int): RecyclerViewMatcher {
        return RecyclerViewMatcher(recyclerViewId)
    }
    
    // Test RecyclerView item interaction
    onView(withRecyclerView(R.id.recycler_view).atPosition(0))
        .check(matches(hasDescendant(withText("Item 1"))))
        .perform(click())
    
    // Test swipe actions
    onView(withRecyclerView(R.id.recycler_view).atPosition(1))
        .perform(swipeLeft())
    
    // Verify item removed
    onView(withRecyclerView(R.id.recycler_view).atPosition(1))
        .check(matches(hasDescendant(withText("Item 3"))))
}

@Test
fun testNetworkDependentUI() {
    // Mock network responses
    val mockWebServer = MockWebServer()
    mockWebServer.enqueue(MockResponse().setBody("""{"users": []}"""))
    
    // Configure app to use mock server
    val baseUrl = mockWebServer.url("/").toString()
    
    // Launch activity
    ActivityScenario.launch(MainActivity::class.java)
    
    // Test loading state
    onView(withId(R.id.progress_bar))
        .check(matches(isDisplayed()))
    
    // Wait for network call completion
    IdlingRegistry.getInstance().register(NetworkIdlingResource())
    
    // Test loaded state
    onView(withId(R.id.empty_view))
        .check(matches(isDisplayed()))
    
    mockWebServer.shutdown()
}
```

**Screenshot Testing:**
```kotlin
@Test
fun testScreenshotComparison() {
    val screenshotRule = ScreenshotTestRule()
    
    composeTestRule.setContent {
        MyTheme {
            ProfileScreen(user = sampleUser)
        }
    }
    
    // Capture and compare screenshot
    screenshotRule.assertAgainstGolden(
        node = composeTestRule.onRoot(),
        goldenName = "profile_screen_light_theme"
    )
}
```

### Performance Testing

**Memory Leak Testing:**
```kotlin
@Test
fun testActivityMemoryLeak() {
    val scenario = ActivityScenario.launch(MainActivity::class.java)
    
    // Simulate activity lifecycle
    scenario.moveToState(Lifecycle.State.CREATED)
    scenario.moveToState(Lifecycle.State.DESTROYED)
    
    // Force garbage collection
    Runtime.getRuntime().gc()
    Thread.sleep(1000)
    
    // Check for leaks using LeakCanary in tests
    assertThat(LeakCanary.getLeaks()).isEmpty()
}
```

**Benchmark Testing:**
```kotlin
@RunWith(AndroidJUnit4::class)
class DatabaseBenchmark {
    
    @get:Rule
    val benchmarkRule = BenchmarkRule()
    
    @Test
    fun benchmarkDatabaseInsert() {
        val database = createTestDatabase()
        
        benchmarkRule.measureRepeated {
            database.userDao().insertUser(createTestUser())
        }
    }
}
```

