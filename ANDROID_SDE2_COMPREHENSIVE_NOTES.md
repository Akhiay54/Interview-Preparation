# 📱 Android SDE2 Comprehensive Notes
*Essential knowledge for Android developers with 3+ years experience*

---

## Table of Contents

1. [Kotlin Fundamentals](#kotlin-fundamentals)
2. [Android Architecture](#android-architecture)
3. [Jetpack Compose](#jetpack-compose)
4. [Android Core Components](#android-core-components)
5. [Networking & Data](#networking--data)
6. [Performance & Optimization](#performance--optimization)
7. [Testing](#testing)
8. [Security](#security)
9. [Kotlin Multiplatform (KMP)](#kotlin-multiplatform-kmp)
10. [Advanced Topics](#advanced-topics)
11. [Best Practices](#best-practices)
12. [Common Interview Topics](#common-interview-topics)

---

## Kotlin Fundamentals

### Key Kotlin Features

**Null Safety:**
```kotlin
// Nullable vs Non-nullable
var name: String = "John"        // Non-nullable
var nullableName: String? = null // Nullable

// Safe calls and Elvis operator
val length = nullableName?.length ?: 0
val upperName = nullableName?.uppercase() ?: "Unknown"

// Not-null assertion (use carefully)
val definiteLength = nullableName!!.length
```

**Data Classes:**
```kotlin
data class User(
    val id: String,
    val name: String,
    val email: String,
    val age: Int = 0
) {
    // Auto-generates: equals(), hashCode(), toString(), copy(), componentN()
}

// Usage
val user = User("1", "John", "john@example.com")
val (id, name, email) = user // Destructuring
val updatedUser = user.copy(age = 25)
```

**Coroutines:**
```kotlin
// Basic coroutine
suspend fun fetchUser(id: String): User {
    return withContext(Dispatchers.IO) {
        api.getUser(id)
    }
}

// Flow for reactive streams
fun getUserFlow(id: String): Flow<User> = flow {
    emit(api.getUser(id))
}.flowOn(Dispatchers.IO)

// StateFlow for UI state
private val _uiState = MutableStateFlow<User?>(null)
val uiState: StateFlow<User?> = _uiState.asStateFlow()
```

**Extension Functions:**
```kotlin
fun String.toTitleCase(): String {
    return this.split(" ").joinToString(" ") { word ->
        word.replaceFirstChar { it.uppercase() }
    }
}

fun View.visible() { visibility = View.VISIBLE }
fun View.gone() { visibility = View.GONE }
```

---

## Android Architecture

### MVVM with Repository Pattern

```kotlin
// Data Layer
@Entity(tableName = "users")
data class UserEntity(
    @PrimaryKey val id: String,
    val name: String,
    val email: String
)

@Dao
interface UserDao {
    @Query("SELECT * FROM users WHERE id = :id")
    suspend fun getUser(id: String): UserEntity?
    
    @Insert(onConflict = OnConflictStrategy.REPLACE)
    suspend fun insertUser(user: UserEntity)
}

// Repository
class UserRepository @Inject constructor(
    private val api: UserApi,
    private val dao: UserDao
) {
    suspend fun getUser(id: String): Flow<User> = flow {
        // Emit cached data first
        dao.getUser(id)?.let { emit(it.toDomain()) }
        
        // Fetch fresh data
        try {
            val user = api.getUser(id)
            dao.insertUser(user.toEntity())
            emit(user)
        } catch (e: Exception) {
            // Handle error
        }
    }
}

// ViewModel
@HiltViewModel
class UserViewModel @Inject constructor(
    private val repository: UserRepository
) : ViewModel() {
    
    private val _uiState = MutableStateFlow<UserUiState>(UserUiState.Loading)
    val uiState: StateFlow<UserUiState> = _uiState.asStateFlow()
    
    fun loadUser(id: String) {
        viewModelScope.launch {
            repository.getUser(id)
                .onStart { _uiState.value = UserUiState.Loading }
                .catch { _uiState.value = UserUiState.Error(it.message) }
                .collect { user ->
                    _uiState.value = UserUiState.Success(user)
                }
        }
    }
}
```

### Clean Architecture

```kotlin
// Domain Layer
data class User(
    val id: String,
    val name: String,
    val email: String
)

interface UserRepository {
    suspend fun getUser(id: String): User
    suspend fun updateUser(user: User)
}

class GetUserUseCase @Inject constructor(
    private val repository: UserRepository
) {
    suspend operator fun invoke(id: String): User {
        return repository.getUser(id)
    }
}

// Data Layer
class UserRepositoryImpl @Inject constructor(
    private val api: UserApi,
    private val dao: UserDao
) : UserRepository {
    // Implementation
}
```

---

## Jetpack Compose

### Basic Composable

```kotlin
@Composable
fun UserProfile(user: User) {
    Column(
        modifier = Modifier
            .fillMaxSize()
            .padding(16.dp)
    ) {
        Text(
            text = user.name,
            style = MaterialTheme.typography.h4
        )
        Text(
            text = user.email,
            style = MaterialTheme.typography.body1
        )
    }
}
```

### State Management

```kotlin
@Composable
fun Counter() {
    var count by remember { mutableStateOf(0) }
    
    Column {
        Text("Count: $count")
        Button(onClick = { count++ }) {
            Text("Increment")
        }
    }
}

// State hoisting
@Composable
fun CounterScreen() {
    var count by remember { mutableStateOf(0) }
    
    Counter(
        count = count,
        onIncrement = { count++ }
    )
}
```

### Side Effects

```kotlin
@Composable
fun UserScreen(userId: String) {
    var user by remember { mutableStateOf<User?>(null) }
    
    LaunchedEffect(userId) {
        user = repository.getUser(userId)
    }
    
    DisposableEffect(userId) {
        // Setup
        onDispose {
            // Cleanup
        }
    }
    
    user?.let { UserProfile(it) }
}
```

### Custom Composable

```kotlin
@Composable
fun CustomButton(
    text: String,
    onClick: () -> Unit,
    modifier: Modifier = Modifier,
    enabled: Boolean = true
) {
    Button(
        onClick = onClick,
        modifier = modifier,
        enabled = enabled
    ) {
        Text(text = text)
    }
}
```

---

## Android Core Components

### Activity Lifecycle

```kotlin
class MainActivity : AppCompatActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)
    }
    
    override fun onStart() {
        super.onStart()
        // Activity becomes visible
    }
    
    override fun onResume() {
        super.onResume()
        // Activity is interactive
    }
    
    override fun onPause() {
        super.onPause()
        // Activity is partially visible
    }
    
    override fun onStop() {
        super.onStop()
        // Activity is not visible
    }
    
    override fun onDestroy() {
        super.onDestroy()
        // Activity is destroyed
    }
}
```

### Fragment Lifecycle

```kotlin
class UserFragment : Fragment() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
    }
    
    override fun onCreateView(
        inflater: LayoutInflater,
        container: ViewGroup?,
        savedInstanceState: Bundle?
    ): View? {
        return inflater.inflate(R.layout.fragment_user, container, false)
    }
    
    override fun onViewCreated(view: View, savedInstanceState: Bundle?) {
        super.onViewCreated(view, savedInstanceState)
        // Setup views
    }
    
    override fun onDestroyView() {
        super.onDestroyView()
        // Cleanup
    }
}
```

### Service

```kotlin
class MyService : Service() {
    override fun onBind(intent: Intent): IBinder? = null
    
    override fun onStartCommand(intent: Intent?, flags: Int, startId: Int): Int {
        // Handle service logic
        return START_STICKY
    }
}

// Start service
Intent(context, MyService::class.java).also { intent ->
    startService(intent)
}
```

### WorkManager

```kotlin
class SyncWorker(
    context: Context,
    params: WorkerParameters
) : CoroutineWorker(context, params) {
    
    override suspend fun doWork(): Result {
        return try {
            // Perform background work
            repository.syncData()
            Result.success()
        } catch (e: Exception) {
            Result.retry()
        }
    }
}

// Schedule work
val syncWork = PeriodicWorkRequestBuilder<SyncWorker>(
    repeatInterval = 1,
    repeatIntervalTimeUnit = TimeUnit.HOURS
).build()

WorkManager.getInstance(context).enqueueUniquePeriodicWork(
    "sync_work",
    ExistingPeriodicWorkPolicy.KEEP,
    syncWork
)
```

---

## Networking & Data

### Retrofit Setup

```kotlin
interface UserApi {
    @GET("users/{id}")
    suspend fun getUser(@Path("id") id: String): User
    
    @POST("users")
    suspend fun createUser(@Body user: User): User
    
    @PUT("users/{id}")
    suspend fun updateUser(@Path("id") id: String, @Body user: User): User
}

@Module
@InstallIn(SingletonComponent::class)
object NetworkModule {
    
    @Provides
    @Singleton
    fun provideOkHttpClient(): OkHttpClient {
        return OkHttpClient.Builder()
            .addInterceptor(HttpLoggingInterceptor().apply {
                level = HttpLoggingInterceptor.Level.BODY
            })
            .connectTimeout(30, TimeUnit.SECONDS)
            .readTimeout(30, TimeUnit.SECONDS)
            .build()
    }
    
    @Provides
    @Singleton
    fun provideRetrofit(okHttpClient: OkHttpClient): Retrofit {
        return Retrofit.Builder()
            .baseUrl("https://api.example.com/")
            .client(okHttpClient)
            .addConverterFactory(GsonConverterFactory.create())
            .build()
    }
    
    @Provides
    @Singleton
    fun provideUserApi(retrofit: Retrofit): UserApi {
        return retrofit.create(UserApi::class.java)
    }
}
```

### Room Database

```kotlin
@Database(
    entities = [UserEntity::class],
    version = 1
)
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
                ).build()
                INSTANCE = instance
                instance
            }
        }
    }
}
```

### DataStore (Preferences)

```kotlin
class UserPreferences @Inject constructor(
    private val dataStore: DataStore<Preferences>
) {
    private val userTokenKey = stringPreferencesKey("user_token")
    private val userIdKey = stringPreferencesKey("user_id")
    
    val userToken: Flow<String?> = dataStore.data.map { preferences ->
        preferences[userTokenKey]
    }
    
    suspend fun saveUserToken(token: String) {
        dataStore.edit { preferences ->
            preferences[userTokenKey] = token
        }
    }
    
    suspend fun clearUserData() {
        dataStore.edit { preferences ->
            preferences.clear()
        }
    }
}
```

---

## Performance & Optimization

### Memory Management

```kotlin
// Avoid memory leaks
class MyActivity : AppCompatActivity() {
    private var _binding: ActivityMainBinding? = null
    private val binding get() = _binding!!
    
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        _binding = ActivityMainBinding.inflate(layoutInflater)
        setContentView(binding.root)
    }
    
    override fun onDestroy() {
        super.onDestroy()
        _binding = null
    }
}

// Use WeakReference for callbacks
class MyManager {
    private val callbacks = mutableListOf<WeakReference<Callback>>()
    
    fun addCallback(callback: Callback) {
        callbacks.add(WeakReference(callback))
    }
    
    fun notifyCallbacks() {
        callbacks.removeAll { it.get() == null }
        callbacks.forEach { it.get()?.onEvent() }
    }
}
```

### Image Loading

```kotlin
// Coil for image loading
@Composable
fun AsyncImage(
    url: String,
    contentDescription: String?,
    modifier: Modifier = Modifier
) {
    AsyncImage(
        model = ImageRequest.Builder(LocalContext.current)
            .data(url)
            .crossfade(true)
            .build(),
        contentDescription = contentDescription,
        modifier = modifier,
        contentScale = ContentScale.Crop
    )
}

// Custom image loading with caching
class ImageLoader @Inject constructor(
    private val context: Context
) {
    private val cache = LruCache<String, Bitmap>(100)
    
    suspend fun loadImage(url: String): Bitmap? {
        return withContext(Dispatchers.IO) {
            cache.get(url) ?: downloadImage(url)?.also {
                cache.put(url, it)
            }
        }
    }
}
```

### RecyclerView Optimization

```kotlin
class UserAdapter : ListAdapter<User, UserViewHolder>(UserDiffCallback()) {
    
    override fun onCreateViewHolder(parent: ViewGroup, viewType: Int): UserViewHolder {
        val binding = ItemUserBinding.inflate(
            LayoutInflater.from(parent.context), parent, false
        )
        return UserViewHolder(binding)
    }
    
    override fun onBindViewHolder(holder: UserViewHolder, position: Int) {
        holder.bind(getItem(position))
    }
}

class UserViewHolder(
    private val binding: ItemUserBinding
) : RecyclerView.ViewHolder(binding.root) {
    
    fun bind(user: User) {
        binding.apply {
            userName.text = user.name
            userEmail.text = user.email
            // Use Coil for image loading
            userAvatar.load(user.avatarUrl)
        }
    }
}
```

---

## 🧪 Testing

### Unit Testing

```kotlin
@RunWith(MockitoJUnitRunner::class)
class UserViewModelTest {
    
    @Mock
    private lateinit var repository: UserRepository
    
    @Mock
    private lateinit var savedStateHandle: SavedStateHandle
    
    private lateinit var viewModel: UserViewModel
    
    @Before
    fun setup() {
        viewModel = UserViewModel(repository, savedStateHandle)
    }
    
    @Test
    fun `loadUser should emit success state`() = runTest {
        // Given
        val user = User("1", "John", "john@example.com")
        whenever(repository.getUser("1")).thenReturn(flowOf(user))
        
        // When
        viewModel.loadUser("1")
        
        // Then
        val states = mutableListOf<UserUiState>()
        viewModel.uiState.test {
            states.add(awaitItem()) // Loading
            states.add(awaitItem()) // Success
        }
        
        assertTrue(states.last() is UserUiState.Success)
    }
}
```

### UI Testing

```kotlin
@RunWith(AndroidJUnit4::class)
class UserScreenTest {
    
    @get:Rule
    val composeTestRule = createComposeRule()
    
    @Test
    fun userScreen_displaysUserData() {
        // Given
        val user = User("1", "John Doe", "john@example.com")
        
        // When
        composeTestRule.setContent {
            UserScreen(user = user)
        }
        
        // Then
        composeTestRule.onNodeWithText("John Doe").assertIsDisplayed()
        composeTestRule.onNodeWithText("john@example.com").assertIsDisplayed()
    }
    
    @Test
    fun userScreen_clickButton_triggersAction() {
        var clicked = false
        
        composeTestRule.setContent {
            Button(onClick = { clicked = true }) {
                Text("Click me")
            }
        }
        
        composeTestRule.onNodeWithText("Click me").performClick()
        
        assertTrue(clicked)
    }
}
```

### Integration Testing

```kotlin
@HiltAndroidTest
class UserRepositoryTest {
    
    @get:Rule
    val hiltRule = HiltAndroidRule(this)
    
    @Inject
    lateinit var repository: UserRepository
    
    @Inject
    lateinit var database: AppDatabase
    
    @Before
    fun setup() {
        hiltRule.inject()
    }
    
    @Test
    fun getUser_shouldReturnCachedData() = runTest {
        // Given
        val user = User("1", "John", "john@example.com")
        
        // When
        repository.getUser("1").collect { result ->
            // Then
            assertEquals(user, result)
        }
    }
}
```

---

## Security

### Network Security

```xml
<!-- network_security_config.xml -->
<network-security-config>
    <domain-config cleartextTrafficPermitted="false">
        <domain includeSubdomains="true">api.example.com</domain>
    </domain-config>
</network-security-config>
```

```kotlin
// Certificate pinning
class CertificatePinner {
    fun getCertificatePinner(): CertificatePinner {
        return CertificatePinner.Builder()
            .add("api.example.com", "sha256/AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA=")
            .build()
    }
}
```

### Data Encryption

```kotlin
class EncryptedSharedPreferences @Inject constructor(
    context: Context
) {
    private val masterKey = MasterKey.Builder(context)
        .setKeyScheme(MasterKey.KeyScheme.AES256_GCM)
        .build()
    
    private val sharedPreferences = EncryptedSharedPreferences.create(
        context,
        "secure_prefs",
        masterKey,
        EncryptedSharedPreferences.PrefKeyEncryptionScheme.AES256_SIV,
        EncryptedSharedPreferences.PrefValueEncryptionScheme.AES256_GCM
    )
    
    fun saveSecureData(key: String, value: String) {
        sharedPreferences.edit().putString(key, value).apply()
    }
    
    fun getSecureData(key: String): String? {
        return sharedPreferences.getString(key, null)
    }
}
```

### Biometric Authentication

```kotlin
class BiometricManager @Inject constructor(
    private val context: Context
) {
    private val biometricPrompt = BiometricPrompt.PromptInfo.Builder()
        .setTitle("Authenticate")
        .setSubtitle("Use biometric to authenticate")
        .setNegativeButtonText("Cancel")
        .build()
    
    fun authenticate(
        activity: FragmentActivity,
        onSuccess: () -> Unit,
        onError: (String) -> Unit
    ) {
        val biometricPrompt = BiometricPrompt(activity, object : BiometricPrompt.AuthenticationCallback() {
            override fun onAuthenticationSucceeded(result: BiometricPrompt.AuthenticationResult) {
                onSuccess()
            }
            
            override fun onAuthenticationError(errorCode: Int, errString: CharSequence) {
                onError(errString.toString())
            }
        })
        
        biometricPrompt.authenticate(biometricPrompt)
    }
}
```

---

## Kotlin Multiplatform (KMP)

### Shared Module Setup

```kotlin
// build.gradle.kts (shared module)
kotlin {
    androidTarget()
    iosX64()
    iosArm64()
    iosSimulatorArm64()
    
    sourceSets {
        val commonMain by getting {
            dependencies {
                implementation("org.jetbrains.kotlinx:kotlinx-coroutines-core:1.7.3")
                implementation("io.ktor:ktor-client-core:2.3.7")
                implementation("org.jetbrains.kotlinx:kotlinx-serialization-json:1.6.0")
            }
        }
        
        val androidMain by getting {
            dependencies {
                implementation("io.ktor:ktor-client-android:2.3.7")
            }
        }
        
        val iosMain by creating {
            dependencies {
                implementation("io.ktor:ktor-client-darwin:2.3.7")
            }
        }
    }
}
```

### Shared Business Logic

```kotlin
// Shared repository
class UserRepositoryImpl(
    private val api: UserApi,
    private val database: UserDatabase
) : UserRepository {
    
    override suspend fun getUser(id: String): User {
        return try {
            val user = api.getUser(id)
            database.saveUser(user)
            user
        } catch (e: Exception) {
            database.getUser(id) ?: throw e
        }
    }
}

// Platform-specific implementations
expect class Platform() {
    val platform: String
}

actual class Platform actual constructor() {
    actual val platform: String = "Android ${android.os.Build.VERSION.SDK_INT}"
}
```

---

## Advanced Topics

### Dependency Injection with Hilt

```kotlin
@HiltAndroidApp
class MyApplication : Application()

@Module
@InstallIn(SingletonComponent::class)
object AppModule {
    
    @Provides
    @Singleton
    fun provideUserRepository(
        api: UserApi,
        database: AppDatabase
    ): UserRepository {
        return UserRepositoryImpl(api, database)
    }
    
    @Provides
    @Singleton
    fun provideUserApi(retrofit: Retrofit): UserApi {
        return retrofit.create(UserApi::class.java)
    }
}

@AndroidEntryPoint
class MainActivity : AppCompatActivity() {
    @Inject
    lateinit var userRepository: UserRepository
}
```

### Navigation Component

```kotlin
@AndroidEntryPoint
class MainActivity : AppCompatActivity() {
    private lateinit var navController: NavController
    
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)
        
        val navHostFragment = supportFragmentManager
            .findFragmentById(R.id.nav_host_fragment) as NavHostFragment
        navController = navHostFragment.navController
    }
}

// Navigation graph
<navigation xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:id="@+id/nav_graph"
    app:startDestination="@id/userListFragment">
    
    <fragment
        android:id="@+id/userListFragment"
        android:name="com.example.UserListFragment"
        android:label="Users">
        <action
            android:id="@+id/action_userList_to_userDetail"
            app:destination="@id/userDetailFragment" />
    </fragment>
    
    <fragment
        android:id="@+id/userDetailFragment"
        android:name="com.example.UserDetailFragment"
        android:label="User Detail">
        <argument
            android:name="userId"
            app:argType="string" />
    </fragment>
</navigation>
```

### Paging 3

```kotlin
class UserPagingSource(
    private val api: UserApi
) : PagingSource<Int, User>() {
    
    override suspend fun load(params: LoadParams<Int>): LoadResult<Int, User> {
        return try {
            val page = params.key ?: 1
            val response = api.getUsers(page, params.loadSize)
            
            LoadResult.Page(
                data = response.users,
                prevKey = if (page == 1) null else page - 1,
                nextKey = if (response.users.isEmpty()) null else page + 1
            )
        } catch (e: Exception) {
            LoadResult.Error(e)
        }
    }
    
    override fun getRefreshKey(state: PagingState<Int, User>): Int? {
        return state.anchorPosition?.let { anchorPosition ->
            state.closestPageToPosition(anchorPosition)?.prevKey?.plus(1)
                ?: state.closestPageToPosition(anchorPosition)?.nextKey?.minus(1)
        }
    }
}

class UserRepository {
    fun getUsers(): Flow<PagingData<User>> {
        return Pager(
            config = PagingConfig(pageSize = 20),
            pagingSourceFactory = { UserPagingSource(api) }
        ).flow
    }
}
```

---

## Best Practices

### Code Organization

```kotlin
// Package structure
com.example.app/
├── data/
│   ├── local/
│   ├── remote/
│   └── repository/
├── domain/
│   ├── model/
│   ├── repository/
│   └── usecase/
├── presentation/
│   ├── ui/
│   ├── viewmodel/
│   └── adapter/
└── di/
```

### Error Handling

```kotlin
sealed class Result<out T> {
    data class Success<T>(val data: T) : Result<T>()
    data class Error(val exception: Throwable) : Result<Nothing>()
    object Loading : Result<Nothing>()
}

class SafeApiCall {
    suspend fun <T> safeApiCall(apiCall: suspend () -> T): Result<T> {
        return try {
            Result.Success(apiCall.invoke())
        } catch (throwable: Throwable) {
            Result.Error(throwable)
        }
    }
}
```

### Resource Management

```kotlin
class ResourceManager {
    fun getString(@StringRes resId: Int): String {
        return context.getString(resId)
    }
    
    fun getColor(@ColorRes resId: Int): Int {
        return ContextCompat.getColor(context, resId)
    }
    
    fun getDimension(@DimenRes resId: Int): Float {
        return context.resources.getDimension(resId)
    }
}
```

---

## 🎯 Common Interview Topics

### Memory Management
- **Memory Leaks**: Common causes and prevention
- **Garbage Collection**: How it works in Android
- **Memory Profiling**: Using Android Studio Profiler

### Performance Optimization
- **UI Performance**: Overdraw, layout optimization
- **Network Optimization**: Caching, compression
- **Battery Optimization**: Background processing

### Architecture Patterns
- **MVVM vs MVP vs MVI**: When to use each
- **Clean Architecture**: Principles and implementation
- **Repository Pattern**: Data abstraction

### Kotlin Features
- **Coroutines**: Structured concurrency
- **Flow**: Reactive streams
- **Extension Functions**: Utility functions
- **Data Classes**: Immutable data

### Android Components
- **Activity Lifecycle**: State management
- **Fragment Lifecycle**: UI components
- **Service Types**: Foreground, background, bound
- **Content Providers**: Data sharing

### Testing Strategies
- **Unit Testing**: Business logic
- **UI Testing**: User interactions
- **Integration Testing**: Component interaction
- **Test Coverage**: Measuring quality

---

## 📚 Key Libraries & Tools

### Essential Libraries
- **Retrofit**: HTTP client
- **Room**: Database ORM
- **Hilt**: Dependency injection
- **Navigation**: Screen navigation
- **Paging**: Large data sets
- **WorkManager**: Background tasks
- **DataStore**: Preferences storage
- **Coil**: Image loading

### Development Tools
- **Android Studio**: IDE
- **Layout Inspector**: UI debugging
- **Profiler**: Performance analysis
- **Lint**: Code quality
- **Git**: Version control

### Testing Tools
- **JUnit**: Unit testing
- **Mockito**: Mocking
- **Espresso**: UI testing
- **Robolectric**: Android testing

---

## 🚀 Quick Reference

### Common Patterns
```kotlin
// Singleton
object Singleton {
    fun doSomething() {}
}

// Builder pattern
class UserBuilder {
    private var name: String = ""
    private var email: String = ""
    
    fun name(name: String) = apply { this.name = name }
    fun email(email: String) = apply { this.email = email }
    fun build() = User(name, email)
}

// Observer pattern
class Observable {
    private val observers = mutableListOf<Observer>()
    
    fun addObserver(observer: Observer) {
        observers.add(observer)
    }
    
    fun notifyObservers() {
        observers.forEach { it.update() }
    }
}
```

### Useful Extensions
```kotlin
fun View.onClick(debounceTime: Long = 300L, action: () -> Unit) {
    setOnClickListener {
        isClickable = false
        action()
        postDelayed({ isClickable = true }, debounceTime)
    }
}

fun Context.toast(message: String, duration: Int = Toast.LENGTH_SHORT) {
    Toast.makeText(this, message, duration).show()
}

fun String.isValidEmail(): Boolean {
    return android.util.Patterns.EMAIL_ADDRESS.matcher(this).matches()
}
```

---

*This comprehensive guide covers the essential knowledge for Android SDE2 level developers. Keep practicing and stay updated with the latest Android developments! 🚀* 
