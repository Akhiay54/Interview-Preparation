# 🚀 GitHub Trending Repositories - Complete Implementation Guide

## 📋 Problem Analysis

### **Requirements:**
- ✅ **GitHub trending listing screen**
- ✅ **Sortable by stars and name**
- ✅ **Cache support for offline mode**
- ✅ **Internet not connected screen**
- ✅ **Pull to refresh functionality**
- ✅ **90 minutes time limit**

### **API Details:**
- **Endpoint**: `https://api.github.com/orgs/octokit/repos`
- **Headers**: 
  - `Accept: application/vnd.github+json`
  - `X-GitHub-Api-Version: 2022-11-28`

### **Evaluation Criteria:**
- **SOLID principles**
- **Clean architecture with defined layers**
- **Dependency injection**
- **Unit tests**
- **Optimum threading**
- **Optimum caching**

---

## 🏗️ Architecture Overview

### **Tech Stack:**
- **Language**: Kotlin
- **Architecture**: MVVM + Repository Pattern
- **DI**: Dagger Hilt
- **Async**: Coroutines + StateFlow
- **UI**: ViewBinding + RecyclerView + ListAdapter
- **Database**: Room (for caching)
- **Network**: Retrofit + OkHttp
- **Testing**: JUnit + MockK

### **Project Structure:**
```
com.github.trending/
├── data/
│   ├── local/
│   │   ├── database/
│   │   │   ├── AppDatabase.kt
│   │   │   ├── RepoDao.kt
│   │   │   └── RepoEntity.kt
│   │   └── preferences/
│   │       └── PreferencesManager.kt
│   ├── remote/
│   │   ├── api/
│   │   │   ├── GitHubApiService.kt
│   │   │   └── dto/
│   │   │       └── GitHubRepoDto.kt
│   │   └── interceptors/
│   │       └── NetworkInterceptor.kt
│   └── repository/
│       └── GitHubRepositoryImpl.kt
├── domain/
│   ├── model/
│   │   └── GitHubRepo.kt
│   ├── repository/
│   │   └── GitHubRepository.kt
│   └── usecase/
│       ├── GetRepositoriesUseCase.kt
│       ├── SortRepositoriesUseCase.kt
│       └── RefreshRepositoriesUseCase.kt
├── presentation/
│   ├── ui/
│   │   ├── main/
│   │   │   ├── MainActivity.kt
│   │   │   ├── MainFragment.kt
│   │   │   └── MainViewModel.kt
│   │   ├── adapter/
│   │   │   └── RepoAdapter.kt
│   │   └── common/
│   │       ├── LoadingFragment.kt
│   │       └── ErrorFragment.kt
│   └── utils/
│       ├── NetworkUtils.kt
│       └── Extensions.kt
├── di/
│   ├── AppModule.kt
│   ├── DatabaseModule.kt
│   ├── NetworkModule.kt
│   └── RepositoryModule.kt
└── utils/
    ├── Constants.kt
    ├── Resource.kt
    └── SortType.kt
```

---

## 📱 UI Implementation

### **1. Main Activity Layout**

```xml
<!-- activity_main.xml -->
<?xml version="1.0" encoding="utf-8"?>
<androidx.constraintlayout.widget.ConstraintLayout 
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:fitsSystemWindows="true"
    tools:context=".presentation.ui.main.MainActivity">

    <androidx.fragment.app.FragmentContainerView
        android:id="@+id/fragment_container"
        android:layout_width="0dp"
        android:layout_height="0dp"
        app:layout_constraintTop_toTopOf="parent"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintEnd_toEndOf="parent" />

</androidx.constraintlayout.widget.ConstraintLayout>
```

### **2. Main Fragment Layout**

```xml
<!-- fragment_main.xml -->
<?xml version="1.0" encoding="utf-8"?>
<androidx.constraintlayout.widget.ConstraintLayout 
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent">

    <!-- Toolbar -->
    <com.google.android.material.appbar.MaterialToolbar
        android:id="@+id/toolbar"
        android:layout_width="0dp"
        android:layout_height="?attr/actionBarSize"
        android:background="?attr/colorPrimary"
        app:title="GitHub Trending"
        app:titleTextColor="?attr/colorOnPrimary"
        app:menu="@menu/main_menu"
        app:layout_constraintTop_toTopOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintEnd_toEndOf="parent" />

    <!-- Pull to Refresh -->
    <androidx.swiperefreshlayout.widget.SwipeRefreshLayout
        android:id="@+id/swipe_refresh"
        android:layout_width="0dp"
        android:layout_height="0dp"
        app:layout_constraintTop_toBottomOf="@id/toolbar"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintEnd_toEndOf="parent">

        <!-- RecyclerView -->
        <androidx.recyclerview.widget.RecyclerView
            android:id="@+id/recycler_view"
            android:layout_width="match_parent"
            android:layout_height="match_parent"
            android:padding="8dp"
            android:clipToPadding="false"
            tools:listitem="@layout/item_repository" />

    </androidx.swiperefreshlayout.widget.SwipeRefreshLayout>

    <!-- Loading State -->
    <com.google.android.material.progressindicator.CircularProgressIndicator
        android:id="@+id/progress_bar"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:visibility="gone"
        app:layout_constraintTop_toTopOf="parent"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintEnd_toEndOf="parent" />

    <!-- Error State -->
    <LinearLayout
        android:id="@+id/error_layout"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:orientation="vertical"
        android:gravity="center"
        android:visibility="gone"
        app:layout_constraintTop_toTopOf="parent"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintEnd_toEndOf="parent">

        <ImageView
            android:id="@+id/error_icon"
            android:layout_width="64dp"
            android:layout_height="64dp"
            android:src="@drawable/ic_error"
            android:layout_marginBottom="16dp" />

        <TextView
            android:id="@+id/error_message"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="Something went wrong"
            android:textSize="16sp"
            android:textColor="?attr/colorOnSurface"
            android:layout_marginBottom="16dp" />

        <com.google.android.material.button.MaterialButton
            android:id="@+id/retry_button"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="Retry"
            style="@style/Widget.Material3.Button.OutlinedButton" />

    </LinearLayout>

    <!-- No Internet State -->
    <LinearLayout
        android:id="@+id/no_internet_layout"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:orientation="vertical"
        android:gravity="center"
        android:visibility="gone"
        app:layout_constraintTop_toTopOf="parent"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintEnd_toEndOf="parent">

        <ImageView
            android:layout_width="64dp"
            android:layout_height="64dp"
            android:src="@drawable/ic_wifi_off"
            android:layout_marginBottom="16dp" />

        <TextView
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="No Internet Connection"
            android:textSize="18sp"
            android:textStyle="bold"
            android:textColor="?attr/colorOnSurface"
            android:layout_marginBottom="8dp" />

        <TextView
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="Please check your connection and try again"
            android:textSize="14sp"
            android:textColor="?attr/colorOnSurfaceVariant"
            android:layout_marginBottom="16dp" />

        <com.google.android.material.button.MaterialButton
            android:id="@+id/retry_connection_button"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="Try Again" />

    </LinearLayout>

</androidx.constraintlayout.widget.ConstraintLayout>
```

### **3. Repository Item Layout**

```xml
<!-- item_repository.xml -->
<?xml version="1.0" encoding="utf-8"?>
<com.google.android.material.card.MaterialCardView 
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:layout_margin="8dp"
    app:cardElevation="2dp"
    app:cardCornerRadius="8dp">

    <androidx.constraintlayout.widget.ConstraintLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:padding="16dp">

        <!-- Repository Name -->
        <TextView
            android:id="@+id/repo_name"
            android:layout_width="0dp"
            android:layout_height="wrap_content"
            android:textSize="18sp"
            android:textStyle="bold"
            android:textColor="?attr/colorOnSurface"
            android:maxLines="1"
            android:ellipsize="end"
            app:layout_constraintTop_toTopOf="parent"
            app:layout_constraintStart_toStartOf="parent"
            app:layout_constraintEnd_toStartOf="@id/stars_layout"
            tools:text="octokit.rb" />

        <!-- Description -->
        <TextView
            android:id="@+id/repo_description"
            android:layout_width="0dp"
            android:layout_height="wrap_content"
            android:layout_marginTop="4dp"
            android:textSize="14sp"
            android:textColor="?attr/colorOnSurfaceVariant"
            android:maxLines="2"
            android:ellipsize="end"
            app:layout_constraintTop_toBottomOf="@id/repo_name"
            app:layout_constraintStart_toStartOf="parent"
            app:layout_constraintEnd_toEndOf="parent"
            tools:text="Ruby toolkit for the GitHub API" />

        <!-- Language -->
        <TextView
            android:id="@+id/repo_language"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_marginTop="8dp"
            android:textSize="12sp"
            android:textColor="?attr/colorPrimary"
            android:background="@drawable/language_background"
            android:padding="4dp"
            app:layout_constraintTop_toBottomOf="@id/repo_description"
            app:layout_constraintStart_toStartOf="parent"
            tools:text="Ruby" />

        <!-- Stars Layout -->
        <LinearLayout
            android:id="@+id/stars_layout"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:orientation="horizontal"
            android:gravity="center_vertical"
            app:layout_constraintTop_toTopOf="parent"
            app:layout_constraintEnd_toEndOf="parent">

            <ImageView
                android:layout_width="16dp"
                android:layout_height="16dp"
                android:src="@drawable/ic_star"
                android:layout_marginEnd="4dp" />

            <TextView
                android:id="@+id/repo_stars"
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:textSize="14sp"
                android:textColor="?attr/colorOnSurface"
                tools:text="3.2k" />

        </LinearLayout>

        <!-- Updated At -->
        <TextView
            android:id="@+id/repo_updated"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_marginTop="8dp"
            android:textSize="12sp"
            android:textColor="?attr/colorOnSurfaceVariant"
            app:layout_constraintTop_toBottomOf="@id/repo_language"
            app:layout_constraintEnd_toEndOf="parent"
            tools:text="Updated 2 days ago" />

    </androidx.constraintlayout.widget.ConstraintLayout>

</com.google.android.material.card.MaterialCardView>
```

### **4. Menu Resource**

```xml
<!-- menu/main_menu.xml -->
<?xml version="1.0" encoding="utf-8"?>
<menu xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto">

    <item
        android:id="@+id/action_sort"
        android:title="Sort"
        android:icon="@drawable/ic_sort"
        app:showAsAction="always">
        
        <menu>
            <item
                android:id="@+id/sort_by_name"
                android:title="Sort by Name" />
            <item
                android:id="@+id/sort_by_stars"
                android:title="Sort by Stars" />
        </menu>
        
    </item>

</menu>
```

---

## 🏛️ Data Layer Implementation

### **1. Data Models**

```kotlin
// domain/model/GitHubRepo.kt
data class GitHubRepo(
    val id: Long,
    val name: String,
    val fullName: String,
    val description: String?,
    val language: String?,
    val stargazersCount: Int,
    val forksCount: Int,
    val updatedAt: String,
    val htmlUrl: String,
    val owner: Owner
) {
    data class Owner(
        val login: String,
        val avatarUrl: String
    )
}

// data/remote/dto/GitHubRepoDto.kt
@JsonClass(generateAdapter = true)
data class GitHubRepoDto(
    @Json(name = "id") val id: Long,
    @Json(name = "name") val name: String,
    @Json(name = "full_name") val fullName: String,
    @Json(name = "description") val description: String?,
    @Json(name = "language") val language: String?,
    @Json(name = "stargazers_count") val stargazersCount: Int,
    @Json(name = "forks_count") val forksCount: Int,
    @Json(name = "updated_at") val updatedAt: String,
    @Json(name = "html_url") val htmlUrl: String,
    @Json(name = "owner") val owner: OwnerDto
) {
    @JsonClass(generateAdapter = true)
    data class OwnerDto(
        @Json(name = "login") val login: String,
        @Json(name = "avatar_url") val avatarUrl: String
    )
}

// Extension function to convert DTO to Domain model
fun GitHubRepoDto.toDomain(): GitHubRepo {
    return GitHubRepo(
        id = id,
        name = name,
        fullName = fullName,
        description = description,
        language = language,
        stargazersCount = stargazersCount,
        forksCount = forksCount,
        updatedAt = updatedAt,
        htmlUrl = htmlUrl,
        owner = GitHubRepo.Owner(
            login = owner.login,
            avatarUrl = owner.avatarUrl
        )
    )
}
```

### **2. Room Database**

```kotlin
// data/local/database/RepoEntity.kt
@Entity(tableName = "repositories")
data class RepoEntity(
    @PrimaryKey val id: Long,
    val name: String,
    val fullName: String,
    val description: String?,
    val language: String?,
    val stargazersCount: Int,
    val forksCount: Int,
    val updatedAt: String,
    val htmlUrl: String,
    val ownerLogin: String,
    val ownerAvatarUrl: String,
    val cachedAt: Long = System.currentTimeMillis()
)

// Extension functions
fun RepoEntity.toDomain(): GitHubRepo {
    return GitHubRepo(
        id = id,
        name = name,
        fullName = fullName,
        description = description,
        language = language,
        stargazersCount = stargazersCount,
        forksCount = forksCount,
        updatedAt = updatedAt,
        htmlUrl = htmlUrl,
        owner = GitHubRepo.Owner(
            login = ownerLogin,
            avatarUrl = ownerAvatarUrl
        )
    )
}

fun GitHubRepo.toEntity(): RepoEntity {
    return RepoEntity(
        id = id,
        name = name,
        fullName = fullName,
        description = description,
        language = language,
        stargazersCount = stargazersCount,
        forksCount = forksCount,
        updatedAt = updatedAt,
        htmlUrl = htmlUrl,
        ownerLogin = owner.login,
        ownerAvatarUrl = owner.avatarUrl
    )
}

// data/local/database/RepoDao.kt
@Dao
interface RepoDao {
    
    @Query("SELECT * FROM repositories ORDER BY name ASC")
    suspend fun getAllReposSortedByName(): List<RepoEntity>
    
    @Query("SELECT * FROM repositories ORDER BY stargazersCount DESC")
    suspend fun getAllReposSortedByStars(): List<RepoEntity>
    
    @Insert(onConflict = OnConflictStrategy.REPLACE)
    suspend fun insertRepos(repos: List<RepoEntity>)
    
    @Query("DELETE FROM repositories")
    suspend fun clearAll()
    
    @Query("SELECT COUNT(*) FROM repositories")
    suspend fun getRepoCount(): Int
    
    @Query("SELECT MAX(cachedAt) FROM repositories")
    suspend fun getLastCacheTime(): Long?
}

// data/local/database/AppDatabase.kt
@Database(
    entities = [RepoEntity::class],
    version = 1,
    exportSchema = false
)
@TypeConverters(Converters::class)
abstract class AppDatabase : RoomDatabase() {
    abstract fun repoDao(): RepoDao
}
```

### **3. API Service**

```kotlin
// data/remote/api/GitHubApiService.kt
interface GitHubApiService {
    
    @GET("orgs/octokit/repos")
    suspend fun getOctokitRepositories(
        @Header("Accept") accept: String = "application/vnd.github+json",
        @Header("X-GitHub-Api-Version") apiVersion: String = "2022-11-28"
    ): Response<List<GitHubRepoDto>>
}
```

### **4. Repository Implementation**

```kotlin
// domain/repository/GitHubRepository.kt
interface GitHubRepository {
    suspend fun getRepositories(forceRefresh: Boolean = false): Flow<Resource<List<GitHubRepo>>>
    suspend fun getSortedRepositories(sortType: SortType): Flow<Resource<List<GitHubRepo>>>
    suspend fun refreshRepositories(): Resource<List<GitHubRepo>>
}

// data/repository/GitHubRepositoryImpl.kt
@Singleton
class GitHubRepositoryImpl @Inject constructor(
    private val apiService: GitHubApiService,
    private val repoDao: RepoDao,
    private val networkUtils: NetworkUtils,
    private val dispatcherProvider: CoroutineDispatcherProvider
) : GitHubRepository {
    
    companion object {
        private const val CACHE_TIMEOUT = 5 * 60 * 1000L // 5 minutes
    }
    
    override suspend fun getRepositories(forceRefresh: Boolean): Flow<Resource<List<GitHubRepo>>> = flow {
        emit(Resource.Loading())
        
        // Check if we should use cache
        if (!forceRefresh && shouldUseCache()) {
            val cachedRepos = repoDao.getAllReposSortedByName().map { it.toDomain() }
            if (cachedRepos.isNotEmpty()) {
                emit(Resource.Success(cachedRepos))
                return@flow
            }
        }
        
        // Try to fetch from network
        if (networkUtils.isNetworkAvailable()) {
            try {
                val response = apiService.getOctokitRepositories()
                if (response.isSuccessful) {
                    val repos = response.body()?.map { it.toDomain() } ?: emptyList()
                    
                    // Cache the data
                    repoDao.clearAll()
                    repoDao.insertRepos(repos.map { it.toEntity() })
                    
                    emit(Resource.Success(repos))
                } else {
                    // Try to load from cache on API error
                    val cachedRepos = repoDao.getAllReposSortedByName().map { it.toDomain() }
                    if (cachedRepos.isNotEmpty()) {
                        emit(Resource.Success(cachedRepos))
                    } else {
                        emit(Resource.Error("Failed to load repositories"))
                    }
                }
            } catch (e: Exception) {
                // Try to load from cache on network error
                val cachedRepos = repoDao.getAllReposSortedByName().map { it.toDomain() }
                if (cachedRepos.isNotEmpty()) {
                    emit(Resource.Success(cachedRepos))
                } else {
                    emit(Resource.Error(e.message ?: "Network error"))
                }
            }
        } else {
            // No network - try cache
            val cachedRepos = repoDao.getAllReposSortedByName().map { it.toDomain() }
            if (cachedRepos.isNotEmpty()) {
                emit(Resource.Success(cachedRepos))
            } else {
                emit(Resource.Error("No internet connection", isNetworkError = true))
            }
        }
    }.flowOn(dispatcherProvider.io)
    
    override suspend fun getSortedRepositories(sortType: SortType): Flow<Resource<List<GitHubRepo>>> = flow {
        emit(Resource.Loading())
        
        try {
            val repos = when (sortType) {
                SortType.NAME -> repoDao.getAllReposSortedByName()
                SortType.STARS -> repoDao.getAllReposSortedByStars()
            }.map { it.toDomain() }
            
            emit(Resource.Success(repos))
        } catch (e: Exception) {
            emit(Resource.Error(e.message ?: "Failed to sort repositories"))
        }
    }.flowOn(dispatcherProvider.io)
    
    override suspend fun refreshRepositories(): Resource<List<GitHubRepo>> {
        return withContext(dispatcherProvider.io) {
            if (!networkUtils.isNetworkAvailable()) {
                return@withContext Resource.Error("No internet connection", isNetworkError = true)
            }
            
            try {
                val response = apiService.getOctokitRepositories()
                if (response.isSuccessful) {
                    val repos = response.body()?.map { it.toDomain() } ?: emptyList()
                    
                    // Update cache
                    repoDao.clearAll()
                    repoDao.insertRepos(repos.map { it.toEntity() })
                    
                    Resource.Success(repos)
                } else {
                    Resource.Error("Failed to refresh repositories")
                }
            } catch (e: Exception) {
                Resource.Error(e.message ?: "Network error")
            }
        }
    }
    
    private suspend fun shouldUseCache(): Boolean {
        val lastCacheTime = repoDao.getLastCacheTime() ?: 0
        val currentTime = System.currentTimeMillis()
        return (currentTime - lastCacheTime) < CACHE_TIMEOUT
    }
}
```

---

## 🎯 Domain Layer Implementation

### **1. Use Cases**

```kotlin
// domain/usecase/GetRepositoriesUseCase.kt
@Singleton
class GetRepositoriesUseCase @Inject constructor(
    private val repository: GitHubRepository
) {
    suspend operator fun invoke(forceRefresh: Boolean = false): Flow<Resource<List<GitHubRepo>>> {
        return repository.getRepositories(forceRefresh)
    }
}

// domain/usecase/SortRepositoriesUseCase.kt
@Singleton
class SortRepositoriesUseCase @Inject constructor(
    private val repository: GitHubRepository
) {
    suspend operator fun invoke(sortType: SortType): Flow<Resource<List<GitHubRepo>>> {
        return repository.getSortedRepositories(sortType)
    }
}

// domain/usecase/RefreshRepositoriesUseCase.kt
@Singleton
class RefreshRepositoriesUseCase @Inject constructor(
    private val repository: GitHubRepository
) {
    suspend operator fun invoke(): Resource<List<GitHubRepo>> {
        return repository.refreshRepositories()
    }
}
```

### **2. Utility Classes**

```kotlin
// utils/Resource.kt
sealed class Resource<T>(
    val data: T? = null,
    val message: String? = null,
    val isNetworkError: Boolean = false
) {
    class Success<T>(data: T) : Resource<T>(data)
    class Error<T>(message: String, data: T? = null, isNetworkError: Boolean = false) : Resource<T>(data, message, isNetworkError)
    class Loading<T>(data: T? = null) : Resource<T>(data)
}

// utils/SortType.kt
enum class SortType {
    NAME, STARS
}

// utils/NetworkUtils.kt
@Singleton
class NetworkUtils @Inject constructor(
    @ApplicationContext private val context: Context
) {
    fun isNetworkAvailable(): Boolean {
        val connectivityManager = context.getSystemService(Context.CONNECTIVITY_SERVICE) as ConnectivityManager
        val network = connectivityManager.activeNetwork ?: return false
        val networkCapabilities = connectivityManager.getNetworkCapabilities(network) ?: return false
        return networkCapabilities.hasTransport(NetworkCapabilities.TRANSPORT_WIFI) ||
                networkCapabilities.hasTransport(NetworkCapabilities.TRANSPORT_CELLULAR)
    }
}
```

---

## 🎨 Presentation Layer Implementation

### **1. MainActivity**

```kotlin
// presentation/ui/main/MainActivity.kt
@AndroidEntryPoint
class MainActivity : AppCompatActivity() {
    
    private lateinit var binding: ActivityMainBinding
    
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        enableEdgeToEdge()
        
        binding = ActivityMainBinding.inflate(layoutInflater)
        setContentView(binding.root)
        
        if (savedInstanceState == null) {
            supportFragmentManager.beginTransaction()
                .replace(R.id.fragment_container, MainFragment())
                .commit()
        }
    }
}
```

### **2. MainFragment**

```kotlin
// presentation/ui/main/MainFragment.kt
@AndroidEntryPoint
class MainFragment : Fragment() {
    
    private var _binding: FragmentMainBinding? = null
    private val binding get() = _binding!!
    
    private val viewModel: MainViewModel by viewModels()
    private lateinit var repoAdapter: RepoAdapter
    
    override fun onCreateView(inflater: LayoutInflater, container: ViewGroup?, savedInstanceState: Bundle?): View {
        _binding = FragmentMainBinding.inflate(inflater, container, false)
        return binding.root
    }
    
    override fun onViewCreated(view: View, savedInstanceState: Bundle?) {
        super.onViewCreated(view, savedInstanceState)
        
        setupToolbar()
        setupRecyclerView()
        setupSwipeRefresh()
        setupClickListeners()
        observeViewModel()
        
        // Load data
        viewModel.loadRepositories()
    }
    
    private fun setupToolbar() {
        binding.toolbar.setOnMenuItemClickListener { menuItem ->
            when (menuItem.itemId) {
                R.id.sort_by_name -> {
                    viewModel.sortRepositories(SortType.NAME)
                    true
                }
                R.id.sort_by_stars -> {
                    viewModel.sortRepositories(SortType.STARS)
                    true
                }
                else -> false
            }
        }
    }
    
    private fun setupRecyclerView() {
        repoAdapter = RepoAdapter { repo ->
            // Handle item click - could open browser or detail screen
            val intent = Intent(Intent.ACTION_VIEW, Uri.parse(repo.htmlUrl))
            startActivity(intent)
        }
        
        binding.recyclerView.apply {
            layoutManager = LinearLayoutManager(requireContext())
            adapter = repoAdapter
            
            // Add item decoration for spacing
            addItemDecoration(DividerItemDecoration(requireContext(), DividerItemDecoration.VERTICAL))
        }
    }
    
    private fun setupSwipeRefresh() {
        binding.swipeRefresh.setOnRefreshListener {
            viewModel.refreshRepositories()
        }
    }
    
    private fun setupClickListeners() {
        binding.retryButton.setOnClickListener {
            viewModel.loadRepositories(forceRefresh = true)
        }
        
        binding.retryConnectionButton.setOnClickListener {
            viewModel.loadRepositories(forceRefresh = true)
        }
    }
    
    private fun observeViewModel() {
        viewLifecycleOwner.lifecycleScope.launch {
            repeatOnLifecycle(Lifecycle.State.STARTED) {
                viewModel.uiState.collect { state ->
                    updateUiState(state)
                }
            }
        }
    }
    
    private fun updateUiState(state: MainUiState) {
        binding.swipeRefresh.isRefreshing = false
        
        when (state) {
            is MainUiState.Loading -> {
                binding.progressBar.isVisible = true
                binding.recyclerView.isVisible = false
                binding.errorLayout.isVisible = false
                binding.noInternetLayout.isVisible = false
            }
            
            is MainUiState.Success -> {
                binding.progressBar.isVisible = false
                binding.recyclerView.isVisible = true
                binding.errorLayout.isVisible = false
                binding.noInternetLayout.isVisible = false
                
                repoAdapter.submitList(state.repositories)
            }
            
            is MainUiState.Error -> {
                binding.progressBar.isVisible = false
                binding.recyclerView.isVisible = false
                
                if (state.isNetworkError) {
                    binding.noInternetLayout.isVisible = true
                    binding.errorLayout.isVisible = false
                } else {
                    binding.errorLayout.isVisible = true
                    binding.noInternetLayout.isVisible = false
                    binding.errorMessage.text = state.message
                }
            }
        }
    }
    
    override fun onDestroyView() {
        super.onDestroyView()
        _binding = null
    }
}
```

### **3. MainViewModel**

```kotlin
// presentation/ui/main/MainViewModel.kt
@HiltViewModel
class MainViewModel @Inject constructor(
    private val getRepositoriesUseCase: GetRepositoriesUseCase,
    private val sortRepositoriesUseCase: SortRepositoriesUseCase,
    private val refreshRepositoriesUseCase: RefreshRepositoriesUseCase
) : ViewModel() {
    
    private val _uiState = MutableStateFlow<MainUiState>(MainUiState.Loading)
    val uiState = _uiState.asStateFlow()
    
    fun loadRepositories(forceRefresh: Boolean = false) {
        viewModelScope.launch {
            getRepositoriesUseCase(forceRefresh).collect { resource ->
                _uiState.value = when (resource) {
                    is Resource.Loading -> MainUiState.Loading
                    is Resource.Success -> MainUiState.Success(resource.data ?: emptyList())
                    is Resource.Error -> MainUiState.Error(
                        message = resource.message ?: "Unknown error",
                        isNetworkError = resource.isNetworkError
                    )
                }
            }
        }
    }
    
    fun sortRepositories(sortType: SortType) {
        viewModelScope.launch {
            sortRepositoriesUseCase(sortType).collect { resource ->
                when (resource) {
                    is Resource.Success -> {
                        _uiState.value = MainUiState.Success(resource.data ?: emptyList())
                    }
                    is Resource.Error -> {
                        // Keep current state but could show a toast
                    }
                    is Resource.Loading -> {
                        // Optional: show loading state
                    }
                }
            }
        }
    }
    
    fun refreshRepositories() {
        viewModelScope.launch {
            val result = refreshRepositoriesUseCase()
            when (result) {
                is Resource.Success -> {
                    _uiState.value = MainUiState.Success(result.data ?: emptyList())
                }
                is Resource.Error -> {
                    _uiState.value = MainUiState.Error(
                        message = result.message ?: "Failed to refresh",
                        isNetworkError = result.isNetworkError
                    )
                }
                is Resource.Loading -> {
                    // Handle loading if needed
                }
            }
        }
    }
}

// UI State
sealed class MainUiState {
    object Loading : MainUiState()
    data class Success(val repositories: List<GitHubRepo>) : MainUiState()
    data class Error(val message: String, val isNetworkError: Boolean = false) : MainUiState()
}
```

### **4. RecyclerView Adapter**

```kotlin
// presentation/ui/adapter/RepoAdapter.kt
class RepoAdapter(
    private val onItemClick: (GitHubRepo) -> Unit
) : ListAdapter<GitHubRepo, RepoAdapter.RepoViewHolder>(RepoDiffCallback()) {
    
    override fun onCreateViewHolder(parent: ViewGroup, viewType: Int): RepoViewHolder {
        val binding = ItemRepositoryBinding.inflate(
            LayoutInflater.from(parent.context), parent, false
        )
        return RepoViewHolder(binding)
    }
    
    override fun onBindViewHolder(holder: RepoViewHolder, position: Int) {
        holder.bind(getItem(position))
    }
    
    inner class RepoViewHolder(
        private val binding: ItemRepositoryBinding
    ) : RecyclerView.ViewHolder(binding.root) {
        
        fun bind(repo: GitHubRepo) {
            binding.apply {
                repoName.text = repo.name
                repoDescription.text = repo.description ?: "No description available"
                repoLanguage.text = repo.language ?: "Unknown"
                repoStars.text = formatStarCount(repo.stargazersCount)
                repoUpdated.text = formatUpdatedTime(repo.updatedAt)
                
                // Set language visibility
                repoLanguage.isVisible = !repo.language.isNullOrBlank()
                
                root.setOnClickListener {
                    onItemClick(repo)
                }
            }
        }
        
        private fun formatStarCount(count: Int): String {
            return when {
                count >= 1000 -> "${(count / 1000.0).format(1)}k"
                else -> count.toString()
            }
        }
        
        private fun formatUpdatedTime(updatedAt: String): String {
            return try {
                val formatter = SimpleDateFormat("yyyy-MM-dd'T'HH:mm:ss'Z'", Locale.getDefault())
                val date = formatter.parse(updatedAt)
                val now = Date()
                val diff = now.time - (date?.time ?: 0)
                
                when {
                    diff < 24 * 60 * 60 * 1000 -> "Updated today"
                    diff < 7 * 24 * 60 * 60 * 1000 -> "Updated ${diff / (24 * 60 * 60 * 1000)} days ago"
                    else -> "Updated on ${SimpleDateFormat("MMM dd, yyyy", Locale.getDefault()).format(date)}"
                }
            } catch (e: Exception) {
                "Recently updated"
            }
        }
    }
}

class RepoDiffCallback : DiffUtil.ItemCallback<GitHubRepo>() {
    override fun areItemsTheSame(oldItem: GitHubRepo, newItem: GitHubRepo): Boolean {
        return oldItem.id == newItem.id
    }
    
    override fun areContentsTheSame(oldItem: GitHubRepo, newItem: GitHubRepo): Boolean {
        return oldItem == newItem
    }
}

// Extension function
private fun Double.format(digits: Int) = "%.${digits}f".format(this)
```

---

## 💉 Dependency Injection

### **1. App Module**

```kotlin
// di/AppModule.kt
@Module
@InstallIn(SingletonComponent::class)
object AppModule {
    
    @Provides
    @Singleton
    fun provideCoroutineDispatcherProvider(): CoroutineDispatcherProvider {
        return RealCoroutineDispatcherProvider()
    }
}
```

### **2. Network Module**

```kotlin
// di/NetworkModule.kt
@Module
@InstallIn(SingletonComponent::class)
object NetworkModule {
    
    @Provides
    @Singleton
    fun provideOkHttpClient(): OkHttpClient {
        return OkHttpClient.Builder()
            .connectTimeout(30, TimeUnit.SECONDS)
            .readTimeout(30, TimeUnit.SECONDS)
            .writeTimeout(30, TimeUnit.SECONDS)
            .apply {
                if (BuildConfig.DEBUG) {
                    addInterceptor(HttpLoggingInterceptor().apply {
                        level = HttpLoggingInterceptor.Level.BODY
                    })
                }
            }
            .build()
    }
    
    @Provides
    @Singleton
    fun provideRetrofit(okHttpClient: OkHttpClient): Retrofit {
        return Retrofit.Builder()
            .baseUrl("https://api.github.com/")
            .client(okHttpClient)
            .addConverterFactory(MoshiConverterFactory.create())
            .build()
    }
    
    @Provides
    @Singleton
    fun provideGitHubApiService(retrofit: Retrofit): GitHubApiService {
        return retrofit.create(GitHubApiService::class.java)
    }
}
```

### **3. Database Module**

```kotlin
// di/DatabaseModule.kt
@Module
@InstallIn(SingletonComponent::class)
object DatabaseModule {
    
    @Provides
    @Singleton
    fun provideAppDatabase(@ApplicationContext context: Context): AppDatabase {
        return Room.databaseBuilder(
            context,
            AppDatabase::class.java,
            "github_trending_database"
        ).build()
    }
    
    @Provides
    fun provideRepoDao(database: AppDatabase): RepoDao {
        return database.repoDao()
    }
}
```

### **4. Repository Module**

```kotlin
// di/RepositoryModule.kt
@Module
@InstallIn(SingletonComponent::class)
abstract class RepositoryModule {
    
    @Binds
    abstract fun bindGitHubRepository(
        gitHubRepositoryImpl: GitHubRepositoryImpl
    ): GitHubRepository
}
```

---

## 🧪 Testing Implementation

### **1. Unit Tests for ViewModel**

```kotlin
// test/presentation/ui/main/MainViewModelTest.kt
@ExperimentalCoroutinesApi
@RunWith(MockitoJUnitRunner::class)
class MainViewModelTest {
    
    @get:Rule
    val instantTaskExecutorRule = InstantTaskExecutorRule()
    
    @Mock
    private lateinit var getRepositoriesUseCase: GetRepositoriesUseCase
    
    @Mock
    private lateinit var sortRepositoriesUseCase: SortRepositoriesUseCase
    
    @Mock
    private lateinit var refreshRepositoriesUseCase: RefreshRepositoriesUseCase
    
    private lateinit var viewModel: MainViewModel
    private val testDispatcher = StandardTestDispatcher()
    
    @Before
    fun setup() {
        Dispatchers.setMain(testDispatcher)
        viewModel = MainViewModel(
            getRepositoriesUseCase,
            sortRepositoriesUseCase,
            refreshRepositoriesUseCase
        )
    }
    
    @After
    fun tearDown() {
        Dispatchers.resetMain()
    }
    
    @Test
    fun `loadRepositories should emit Success when use case returns success`() = runTest {
        // Given
        val mockRepos = listOf(
            GitHubRepo(
                id = 1,
                name = "test-repo",
                fullName = "octokit/test-repo",
                description = "Test repository",
                language = "Kotlin",
                stargazersCount = 100,
                forksCount = 50,
                updatedAt = "2023-01-01T00:00:00Z",
                htmlUrl = "https://github.com/octokit/test-repo",
                owner = GitHubRepo.Owner("octokit", "https://avatar.url")
            )
        )
        
        whenever(getRepositoriesUseCase(false)).thenReturn(
            flowOf(Resource.Success(mockRepos))
        )
        
        // When
        viewModel.loadRepositories()
        testScheduler.advanceUntilIdle()
        
        // Then
        val currentState = viewModel.uiState.value
        assertTrue(currentState is MainUiState.Success)
        assertEquals(mockRepos, (currentState as MainUiState.Success).repositories)
    }
    
    @Test
    fun `loadRepositories should emit Error when use case returns error`() = runTest {
        // Given
        val errorMessage = "Network error"
        whenever(getRepositoriesUseCase(false)).thenReturn(
            flowOf(Resource.Error(errorMessage))
        )
        
        // When
        viewModel.loadRepositories()
        testScheduler.advanceUntilIdle()
        
        // Then
        val currentState = viewModel.uiState.value
        assertTrue(currentState is MainUiState.Error)
        assertEquals(errorMessage, (currentState as MainUiState.Error).message)
    }
    
    @Test
    fun `sortRepositories should update state with sorted data`() = runTest {
        // Given
        val mockRepos = listOf(
            GitHubRepo(
                id = 1,
                name = "a-repo",
                fullName = "octokit/a-repo",
                description = "First repo",
                language = "Kotlin",
                stargazersCount = 50,
                forksCount = 25,
                updatedAt = "2023-01-01T00:00:00Z",
                htmlUrl = "https://github.com/octokit/a-repo",
                owner = GitHubRepo.Owner("octokit", "https://avatar.url")
            ),
            GitHubRepo(
                id = 2,
                name = "z-repo",
                fullName = "octokit/z-repo",
                description = "Second repo",
                language = "Java",
                stargazersCount = 100,
                forksCount = 50,
                updatedAt = "2023-01-02T00:00:00Z",
                htmlUrl = "https://github.com/octokit/z-repo",
                owner = GitHubRepo.Owner("octokit", "https://avatar.url")
            )
        )
        
        whenever(sortRepositoriesUseCase(SortType.NAME)).thenReturn(
            flowOf(Resource.Success(mockRepos))
        )
        
        // When
        viewModel.sortRepositories(SortType.NAME)
        testScheduler.advanceUntilIdle()
        
        // Then
        val currentState = viewModel.uiState.value
        assertTrue(currentState is MainUiState.Success)
        assertEquals(mockRepos, (currentState as MainUiState.Success).repositories)
    }
}
```

### **2. Unit Tests for Repository**

```kotlin
// test/data/repository/GitHubRepositoryImplTest.kt
@ExperimentalCoroutinesApi
@RunWith(MockitoJUnitRunner::class)
class GitHubRepositoryImplTest {
    
    @Mock
    private lateinit var apiService: GitHubApiService
    
    @Mock
    private lateinit var repoDao: RepoDao
    
    @Mock
    private lateinit var networkUtils: NetworkUtils
    
    @Mock
    private lateinit var dispatcherProvider: CoroutineDispatcherProvider
    
    private lateinit var repository: GitHubRepositoryImpl
    private val testDispatcher = StandardTestDispatcher()
    
    @Before
    fun setup() {
        whenever(dispatcherProvider.io).thenReturn(testDispatcher)
        repository = GitHubRepositoryImpl(apiService, repoDao, networkUtils, dispatcherProvider)
    }
    
    @Test
    fun `getRepositories should return cached data when network is unavailable`() = runTest {
        // Given
        whenever(networkUtils.isNetworkAvailable()).thenReturn(false)
        val cachedEntities = listOf(
            RepoEntity(
                id = 1,
                name = "test-repo",
                fullName = "octokit/test-repo",
                description = "Test",
                language = "Kotlin",
                stargazersCount = 100,
                forksCount = 50,
                updatedAt = "2023-01-01T00:00:00Z",
                htmlUrl = "https://github.com/octokit/test-repo",
                ownerLogin = "octokit",
                ownerAvatarUrl = "https://avatar.url"
            )
        )
        whenever(repoDao.getAllReposSortedByName()).thenReturn(cachedEntities)
        
        // When
        val result = repository.getRepositories().toList()
        
        // Then
        assertEquals(2, result.size) // Loading + Success
        assertTrue(result[0] is Resource.Loading)
        assertTrue(result[1] is Resource.Success)
        assertEquals(1, (result[1] as Resource.Success).data?.size)
    }
    
    @Test
    fun `getRepositories should return network error when no cache and no network`() = runTest {
        // Given
        whenever(networkUtils.isNetworkAvailable()).thenReturn(false)
        whenever(repoDao.getAllReposSortedByName()).thenReturn(emptyList())
        
        // When
        val result = repository.getRepositories().toList()
        
        // Then
        assertEquals(2, result.size) // Loading + Error
        assertTrue(result[0] is Resource.Loading)
        assertTrue(result[1] is Resource.Error)
        assertTrue((result[1] as Resource.Error).isNetworkError)
    }
    
    @Test
    fun `refreshRepositories should fetch from network and update cache`() = runTest {
        // Given
        whenever(networkUtils.isNetworkAvailable()).thenReturn(true)
        val mockDto = GitHubRepoDto(
            id = 1,
            name = "test-repo",
            fullName = "octokit/test-repo",
            description = "Test",
            language = "Kotlin",
            stargazersCount = 100,
            forksCount = 50,
            updatedAt = "2023-01-01T00:00:00Z",
            htmlUrl = "https://github.com/octokit/test-repo",
            owner = GitHubRepoDto.OwnerDto("octokit", "https://avatar.url")
        )
        whenever(apiService.getOctokitRepositories()).thenReturn(
            Response.success(listOf(mockDto))
        )
        
        // When
        val result = repository.refreshRepositories()
        
        // Then
        assertTrue(result is Resource.Success)
        verify(repoDao).clearAll()
        verify(repoDao).insertRepos(any())
    }
}
```

### **3. UI Tests**

```kotlin
// androidTest/presentation/ui/main/MainFragmentTest.kt
@HiltAndroidTest
@RunWith(AndroidJUnit4::class)
class MainFragmentTest {
    
    @get:Rule
    var hiltRule = HiltAndroidRule(this)
    
    @Before
    fun setup() {
        hiltRule.inject()
    }
    
    @Test
    fun fragment_should_display_repositories_list() {
        launchFragmentInContainer<MainFragment>()
        
        // Wait for data to load
        Thread.sleep(2000)
        
        onView(withId(R.id.recycler_view))
            .check(matches(isDisplayed()))
    }
    
    @Test
    fun swipe_refresh_should_trigger_refresh() {
        launchFragmentInContainer<MainFragment>()
        
        onView(withId(R.id.swipe_refresh))
            .perform(swipeDown())
        
        // Verify refresh was triggered
        onView(withId(R.id.recycler_view))
            .check(matches(isDisplayed()))
    }
}
```

---

## 📝 Additional Resources

### **1. Drawable Resources**

```xml
<!-- drawable/ic_star.xml -->
<vector xmlns:android="http://schemas.android.com/apk/res/android"
    android:width="24dp"
    android:height="24dp"
    android:viewportWidth="24"
    android:viewportHeight="24">
    <path
        android:fillColor="@color/star_color"
        android:pathData="M12,2l3.09,6.26L22,9.27l-5,4.87 1.18,6.88L12,17.77l-6.18,3.25L7,14.14 2,9.27l6.91,-1.01L12,2z"/>
</vector>

<!-- drawable/ic_sort.xml -->
<vector xmlns:android="http://schemas.android.com/apk/res/android"
    android:width="24dp"
    android:height="24dp"
    android:viewportWidth="24"
    android:viewportHeight="24">
    <path
        android:fillColor="?attr/colorOnPrimary"
        android:pathData="M3,18h6v-2H3V18zM3,6v2h18V6H3zM3,13h12v-2H3V13z"/>
</vector>

<!-- drawable/language_background.xml -->
<shape xmlns:android="http://schemas.android.com/apk/res/android"
    android:shape="rectangle">
    <solid android:color="@color/language_background" />
    <corners android:radius="12dp" />
</shape>
```

### **2. Colors and Themes**

```xml
<!-- values/colors.xml -->
<resources>
    <color name="star_color">#FFD700</color>
    <color name="language_background">#E3F2FD</color>
</resources>

<!-- values/themes.xml -->
<resources>
    <style name="Theme.GitHubTrending" parent="Theme.Material3.DayNight">
        <item name="colorPrimary">@color/md_theme_light_primary</item>
        <item name="colorOnPrimary">@color/md_theme_light_onPrimary</item>
        <item name="colorPrimaryContainer">@color/md_theme_light_primaryContainer</item>
        <item name="android:statusBarColor">@color/md_theme_light_primary</item>
    </style>
</resources>
```

### **3. Dependencies (build.gradle.kts)**

```kotlin
dependencies {
    // Core Android
    implementation(libs.androidx.core.ktx)
    implementation(libs.androidx.lifecycle.runtime.ktx)
    implementation(libs.androidx.activity.compose)
    implementation(libs.androidx.fragment.ktx)
    
    // UI
    implementation(libs.material)
    implementation(libs.androidx.constraintlayout)
    implementation(libs.androidx.recyclerview)
    implementation(libs.androidx.swiperefreshlayout)
    
    // Architecture Components
    implementation(libs.androidx.lifecycle.viewmodel.ktx)
    implementation(libs.androidx.lifecycle.livedata.ktx)
    implementation(libs.androidx.navigation.fragment.ktx)
    implementation(libs.androidx.navigation.ui.ktx)
    
    // Coroutines
    implementation(libs.kotlinx.coroutines.android)
    implementation(libs.kotlinx.coroutines.core)
    
    // Dependency Injection
    implementation(libs.hilt.android)
    kapt(libs.hilt.compiler)
    
    // Network
    implementation(libs.retrofit)
    implementation(libs.retrofit.converter.moshi)
    implementation(libs.okhttp)
    implementation(libs.okhttp.logging.interceptor)
    implementation(libs.moshi)
    implementation(libs.moshi.kotlin)
    kapt(libs.moshi.kotlin.codegen)
    
    // Database
    implementation(libs.androidx.room.runtime)
    implementation(libs.androidx.room.ktx)
    kapt(libs.androidx.room.compiler)
    
    // Testing
    testImplementation(libs.junit)
    testImplementation(libs.mockito.core)
    testImplementation(libs.mockito.kotlin)
    testImplementation(libs.androidx.arch.core.testing)
    testImplementation(libs.kotlinx.coroutines.test)
    testImplementation(libs.turbine)
    
    androidTestImplementation(libs.androidx.junit)
    androidTestImplementation(libs.androidx.espresso.core)
    androidTestImplementation(libs.hilt.android.testing)
    kaptAndroidTest(libs.hilt.compiler)
}
```

---

## 🎯 Implementation Strategy (90 Minutes)

### **Time Allocation:**
- **Minutes 0-15**: Project setup, dependencies, basic structure
- **Minutes 15-30**: Data models, API service, database setup
- **Minutes 30-50**: Repository implementation with caching
- **Minutes 50-70**: UI implementation (Fragment, ViewModel, Adapter)
- **Minutes 70-85**: Testing (at least one unit test)
- **Minutes 85-90**: Final testing and cleanup

### **Priority Order:**
1. ✅ **Basic project structure and dependencies**
2. ✅ **Data models and API service**
3. ✅ **Repository with basic network call**
4. ✅ **UI with RecyclerView displaying data**
5. ✅ **Pull to refresh functionality**
6. ✅ **Sorting functionality**
7. ✅ **Caching with Room**
8. ✅ **Error handling and offline support**
9. ✅ **Unit tests**
10. ✅ **Polish and edge cases**

---

## 🚀 Key Interview Points to Highlight

### **Architecture:**
- "I'm using MVVM with Repository pattern for clean separation of concerns"
- "StateFlow for reactive UI updates with lifecycle awareness"
- "Single source of truth with Room database for offline support"

### **Performance:**
- "ListAdapter with DiffUtil for efficient RecyclerView updates"
- "Coroutines for non-blocking network calls"
- "Smart caching strategy with 5-minute timeout"

### **Error Handling:**
- "Graceful degradation - show cached data when network fails"
- "Specific UI states for different error scenarios"
- "User-friendly error messages and retry mechanisms"

### **Testing:**
- "Unit tests for business logic in ViewModel and Repository"
- "Mockito for dependency mocking"
- "Coroutine testing with TestDispatcher"

---

**Good luck with your interview! 🍀 This implementation covers all requirements and demonstrates modern Android development best practices.**
