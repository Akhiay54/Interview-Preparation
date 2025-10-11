# 🚀 GitHub Trending Repositories - Jetpack Compose Implementation

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

---

## 🏗️ Architecture Overview

### **Tech Stack:**
- **Language**: Kotlin
- **UI**: Jetpack Compose
- **Architecture**: MVVM + Repository Pattern
- **DI**: Dagger Hilt
- **Async**: Coroutines + StateFlow
- **Database**: Room (for caching)
- **Network**: Retrofit + OkHttp
- **Testing**: JUnit + MockK + Compose Testing

### **Project Structure:**
```
com.github.trending.compose/
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
│   │   │   ├── MainScreen.kt
│   │   │   └── MainViewModel.kt
│   │   ├── components/
│   │   │   ├── RepositoryItem.kt
│   │   │   ├── LoadingScreen.kt
│   │   │   ├── ErrorScreen.kt
│   │   │   └── NoInternetScreen.kt
│   │   └── theme/
│   │       ├── Color.kt
│   │       ├── Theme.kt
│   │       └── Type.kt
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

## 🎨 Compose UI Implementation

### **1. MainActivity**

```kotlin
// presentation/ui/main/MainActivity.kt
@AndroidEntryPoint
class MainActivity : ComponentActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        enableEdgeToEdge()
        
        setContent {
            GitHubTrendingTheme {
                Surface(
                    modifier = Modifier.fillMaxSize(),
                    color = MaterialTheme.colorScheme.background
                ) {
                    MainScreen()
                }
            }
        }
    }
}
```

### **2. Main Screen**

```kotlin
// presentation/ui/main/MainScreen.kt
@OptIn(ExperimentalMaterial3Api::class)
@Composable
fun MainScreen(
    viewModel: MainViewModel = hiltViewModel()
) {
    val uiState by viewModel.uiState.collectAsState()
    val pullRefreshState = rememberPullRefreshState(
        refreshing = uiState is MainUiState.Loading,
        onRefresh = { viewModel.refreshRepositories() }
    )
    
    LaunchedEffect(Unit) {
        viewModel.loadRepositories()
    }
    
    Scaffold(
        topBar = {
            TopAppBar(
                title = { Text("GitHub Trending") },
                actions = {
                    SortMenu(
                        onSortByName = { viewModel.sortRepositories(SortType.NAME) },
                        onSortByStars = { viewModel.sortRepositories(SortType.STARS) }
                    )
                },
                colors = TopAppBarDefaults.topAppBarColors(
                    containerColor = MaterialTheme.colorScheme.primary,
                    titleContentColor = MaterialTheme.colorScheme.onPrimary,
                    actionIconContentColor = MaterialTheme.colorScheme.onPrimary
                )
            )
        }
    ) { paddingValues ->
        Box(
            modifier = Modifier
                .fillMaxSize()
                .padding(paddingValues)
                .pullRefresh(pullRefreshState)
        ) {
            when (uiState) {
                is MainUiState.Loading -> {
                    LoadingScreen()
                }
                
                is MainUiState.Success -> {
                    RepositoryList(
                        repositories = uiState.repositories,
                        onItemClick = { repo ->
                            // Handle item click - open browser
                            // Could be passed as parameter or handled via navigation
                        }
                    )
                }
                
                is MainUiState.Error -> {
                    if (uiState.isNetworkError) {
                        NoInternetScreen(
                            onRetry = { viewModel.loadRepositories(forceRefresh = true) }
                        )
                    } else {
                        ErrorScreen(
                            message = uiState.message,
                            onRetry = { viewModel.loadRepositories(forceRefresh = true) }
                        )
                    }
                }
            }
            
            PullRefreshIndicator(
                refreshing = uiState is MainUiState.Loading,
                state = pullRefreshState,
                modifier = Modifier.align(Alignment.TopCenter)
            )
        }
    }
}

@Composable
fun SortMenu(
    onSortByName: () -> Unit,
    onSortByStars: () -> Unit
) {
    var expanded by remember { mutableStateOf(false) }
    
    Box {
        IconButton(onClick = { expanded = true }) {
            Icon(
                imageVector = Icons.Default.Sort,
                contentDescription = "Sort"
            )
        }
        
        DropdownMenu(
            expanded = expanded,
            onDismissRequest = { expanded = false }
        ) {
            DropdownMenuItem(
                text = { Text("Sort by Name") },
                onClick = {
                    onSortByName()
                    expanded = false
                }
            )
            DropdownMenuItem(
                text = { Text("Sort by Stars") },
                onClick = {
                    onSortByStars()
                    expanded = false
                }
            )
        }
    }
}
```

### **3. Repository List**

```kotlin
// presentation/ui/main/RepositoryList.kt
@Composable
fun RepositoryList(
    repositories: List<GitHubRepo>,
    onItemClick: (GitHubRepo) -> Unit,
    modifier: Modifier = Modifier
) {
    LazyColumn(
        modifier = modifier.fillMaxSize(),
        contentPadding = PaddingValues(16.dp),
        verticalArrangement = Arrangement.spacedBy(8.dp)
    ) {
        items(
            items = repositories,
            key = { repo -> repo.id }
        ) { repo ->
            RepositoryItem(
                repository = repo,
                onClick = { onItemClick(repo) }
            )
        }
    }
}
```

### **4. Repository Item Component**

```kotlin
// presentation/ui/components/RepositoryItem.kt
@Composable
fun RepositoryItem(
    repository: GitHubRepo,
    onClick: () -> Unit,
    modifier: Modifier = Modifier
) {
    Card(
        modifier = modifier
            .fillMaxWidth()
            .clickable { onClick() },
        elevation = CardDefaults.cardElevation(defaultElevation = 4.dp),
        colors = CardDefaults.cardColors(
            containerColor = MaterialTheme.colorScheme.surface
        )
    ) {
        Column(
            modifier = Modifier
                .fillMaxWidth()
                .padding(16.dp)
        ) {
            // Header Row - Name and Stars
            Row(
                modifier = Modifier.fillMaxWidth(),
                horizontalArrangement = Arrangement.SpaceBetween,
                verticalAlignment = Alignment.CenterVertically
            ) {
                Text(
                    text = repository.name,
                    style = MaterialTheme.typography.headlineSmall,
                    fontWeight = FontWeight.Bold,
                    color = MaterialTheme.colorScheme.onSurface,
                    modifier = Modifier.weight(1f),
                    maxLines = 1,
                    overflow = TextOverflow.Ellipsis
                )
                
                StarCount(count = repository.stargazersCount)
            }
            
            Spacer(modifier = Modifier.height(8.dp))
            
            // Description
            if (!repository.description.isNullOrBlank()) {
                Text(
                    text = repository.description,
                    style = MaterialTheme.typography.bodyMedium,
                    color = MaterialTheme.colorScheme.onSurfaceVariant,
                    maxLines = 2,
                    overflow = TextOverflow.Ellipsis
                )
                
                Spacer(modifier = Modifier.height(8.dp))
            }
            
            // Bottom Row - Language and Updated Time
            Row(
                modifier = Modifier.fillMaxWidth(),
                horizontalArrangement = Arrangement.SpaceBetween,
                verticalAlignment = Alignment.CenterVertically
            ) {
                if (!repository.language.isNullOrBlank()) {
                    LanguageChip(language = repository.language)
                } else {
                    Spacer(modifier = Modifier.width(1.dp))
                }
                
                Text(
                    text = formatUpdatedTime(repository.updatedAt),
                    style = MaterialTheme.typography.bodySmall,
                    color = MaterialTheme.colorScheme.onSurfaceVariant
                )
            }
        }
    }
}

@Composable
fun StarCount(
    count: Int,
    modifier: Modifier = Modifier
) {
    Row(
        modifier = modifier,
        verticalAlignment = Alignment.CenterVertically
    ) {
        Icon(
            imageVector = Icons.Default.Star,
            contentDescription = "Stars",
            tint = Color(0xFFFFD700), // Gold color
            modifier = Modifier.size(16.dp)
        )
        
        Spacer(modifier = Modifier.width(4.dp))
        
        Text(
            text = formatStarCount(count),
            style = MaterialTheme.typography.bodyMedium,
            color = MaterialTheme.colorScheme.onSurface
        )
    }
}

@Composable
fun LanguageChip(
    language: String,
    modifier: Modifier = Modifier
) {
    Surface(
        modifier = modifier,
        shape = RoundedCornerShape(12.dp),
        color = MaterialTheme.colorScheme.primaryContainer
    ) {
        Text(
            text = language,
            modifier = Modifier.padding(horizontal = 8.dp, vertical = 4.dp),
            style = MaterialTheme.typography.bodySmall,
            color = MaterialTheme.colorScheme.onPrimaryContainer
        )
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

private fun Double.format(digits: Int) = "%.${digits}f".format(this)
```

### **5. Loading Screen**

```kotlin
// presentation/ui/components/LoadingScreen.kt
@Composable
fun LoadingScreen(
    modifier: Modifier = Modifier
) {
    Box(
        modifier = modifier.fillMaxSize(),
        contentAlignment = Alignment.Center
    ) {
        Column(
            horizontalAlignment = Alignment.CenterHorizontally
        ) {
            CircularProgressIndicator(
                modifier = Modifier.size(48.dp),
                color = MaterialTheme.colorScheme.primary
            )
            
            Spacer(modifier = Modifier.height(16.dp))
            
            Text(
                text = "Loading repositories...",
                style = MaterialTheme.typography.bodyLarge,
                color = MaterialTheme.colorScheme.onSurface
            )
        }
    }
}
```

### **6. Error Screen**

```kotlin
// presentation/ui/components/ErrorScreen.kt
@Composable
fun ErrorScreen(
    message: String,
    onRetry: () -> Unit,
    modifier: Modifier = Modifier
) {
    Box(
        modifier = modifier.fillMaxSize(),
        contentAlignment = Alignment.Center
    ) {
        Column(
            horizontalAlignment = Alignment.CenterHorizontally,
            modifier = Modifier.padding(32.dp)
        ) {
            Icon(
                imageVector = Icons.Default.Error,
                contentDescription = "Error",
                modifier = Modifier.size(64.dp),
                tint = MaterialTheme.colorScheme.error
            )
            
            Spacer(modifier = Modifier.height(16.dp))
            
            Text(
                text = "Something went wrong",
                style = MaterialTheme.typography.headlineSmall,
                color = MaterialTheme.colorScheme.onSurface,
                textAlign = TextAlign.Center
            )
            
            Spacer(modifier = Modifier.height(8.dp))
            
            Text(
                text = message,
                style = MaterialTheme.typography.bodyMedium,
                color = MaterialTheme.colorScheme.onSurfaceVariant,
                textAlign = TextAlign.Center
            )
            
            Spacer(modifier = Modifier.height(24.dp))
            
            Button(
                onClick = onRetry,
                colors = ButtonDefaults.buttonColors(
                    containerColor = MaterialTheme.colorScheme.primary
                )
            ) {
                Text("Retry")
            }
        }
    }
}
```

### **7. No Internet Screen**

```kotlin
// presentation/ui/components/NoInternetScreen.kt
@Composable
fun NoInternetScreen(
    onRetry: () -> Unit,
    modifier: Modifier = Modifier
) {
    Box(
        modifier = modifier.fillMaxSize(),
        contentAlignment = Alignment.Center
    ) {
        Column(
            horizontalAlignment = Alignment.CenterHorizontally,
            modifier = Modifier.padding(32.dp)
        ) {
            Icon(
                imageVector = Icons.Default.WifiOff,
                contentDescription = "No Internet",
                modifier = Modifier.size(64.dp),
                tint = MaterialTheme.colorScheme.onSurfaceVariant
            )
            
            Spacer(modifier = Modifier.height(16.dp))
            
            Text(
                text = "No Internet Connection",
                style = MaterialTheme.typography.headlineSmall,
                color = MaterialTheme.colorScheme.onSurface,
                textAlign = TextAlign.Center,
                fontWeight = FontWeight.Bold
            )
            
            Spacer(modifier = Modifier.height(8.dp))
            
            Text(
                text = "Please check your connection and try again",
                style = MaterialTheme.typography.bodyMedium,
                color = MaterialTheme.colorScheme.onSurfaceVariant,
                textAlign = TextAlign.Center
            )
            
            Spacer(modifier = Modifier.height(24.dp))
            
            Button(
                onClick = onRetry,
                colors = ButtonDefaults.buttonColors(
                    containerColor = MaterialTheme.colorScheme.primary
                )
            ) {
                Text("Try Again")
            }
        }
    }
}
```

---

## 🎨 Theme Implementation

### **1. Color Scheme**

```kotlin
// presentation/ui/theme/Color.kt
import androidx.compose.ui.graphics.Color

val Purple80 = Color(0xFFD0BCFF)
val PurpleGrey80 = Color(0xFFCCC2DC)
val Pink80 = Color(0xFFEFB8C8)

val Purple40 = Color(0xFF6650a4)
val PurpleGrey40 = Color(0xFF625b71)
val Pink40 = Color(0xFF7D5260)

// GitHub specific colors
val GitHubBlue = Color(0xFF0969DA)
val GitHubGray = Color(0xFF656D76)
val StarGold = Color(0xFFFFD700)
val LanguageBackground = Color(0xFFE3F2FD)
```

### **2. Typography**

```kotlin
// presentation/ui/theme/Type.kt
import androidx.compose.material3.Typography
import androidx.compose.ui.text.TextStyle
import androidx.compose.ui.text.font.FontFamily
import androidx.compose.ui.text.font.FontWeight
import androidx.compose.ui.unit.sp

val Typography = Typography(
    bodyLarge = TextStyle(
        fontFamily = FontFamily.Default,
        fontWeight = FontWeight.Normal,
        fontSize = 16.sp,
        lineHeight = 24.sp,
        letterSpacing = 0.5.sp
    ),
    headlineSmall = TextStyle(
        fontFamily = FontFamily.Default,
        fontWeight = FontWeight.Bold,
        fontSize = 18.sp,
        lineHeight = 24.sp,
        letterSpacing = 0.sp
    ),
    bodyMedium = TextStyle(
        fontFamily = FontFamily.Default,
        fontWeight = FontWeight.Normal,
        fontSize = 14.sp,
        lineHeight = 20.sp,
        letterSpacing = 0.25.sp
    ),
    bodySmall = TextStyle(
        fontFamily = FontFamily.Default,
        fontWeight = FontWeight.Normal,
        fontSize = 12.sp,
        lineHeight = 16.sp,
        letterSpacing = 0.4.sp
    )
)
```

### **3. Theme**

```kotlin
// presentation/ui/theme/Theme.kt
import android.app.Activity
import android.os.Build
import androidx.compose.foundation.isSystemInDarkTheme
import androidx.compose.material3.MaterialTheme
import androidx.compose.material3.darkColorScheme
import androidx.compose.material3.dynamicDarkColorScheme
import androidx.compose.material3.dynamicLightColorScheme
import androidx.compose.material3.lightColorScheme
import androidx.compose.runtime.Composable
import androidx.compose.runtime.SideEffect
import androidx.compose.ui.graphics.toArgb
import androidx.compose.ui.platform.LocalContext
import androidx.compose.ui.platform.LocalView
import androidx.core.view.WindowCompat

private val DarkColorScheme = darkColorScheme(
    primary = Purple80,
    secondary = PurpleGrey80,
    tertiary = Pink80
)

private val LightColorScheme = lightColorScheme(
    primary = GitHubBlue,
    secondary = PurpleGrey40,
    tertiary = Pink40,
    background = Color(0xFFFFFBFE),
    surface = Color(0xFFFFFBFE),
    onPrimary = Color.White,
    onSecondary = Color.White,
    onTertiary = Color.White,
    onBackground = Color(0xFF1C1B1F),
    onSurface = Color(0xFF1C1B1F),
)

@Composable
fun GitHubTrendingTheme(
    darkTheme: Boolean = isSystemInDarkTheme(),
    dynamicColor: Boolean = true,
    content: @Composable () -> Unit
) {
    val colorScheme = when {
        dynamicColor && Build.VERSION.SDK_INT >= Build.VERSION_CODES.S -> {
            val context = LocalContext.current
            if (darkTheme) dynamicDarkColorScheme(context) else dynamicLightColorScheme(context)
        }

        darkTheme -> DarkColorScheme
        else -> LightColorScheme
    }
    
    val view = LocalView.current
    if (!view.isInEditMode) {
        SideEffect {
            val window = (view.context as Activity).window
            window.statusBarColor = colorScheme.primary.toArgb()
            WindowCompat.getInsetsController(window, view).isAppearanceLightStatusBars = darkTheme
        }
    }

    MaterialTheme(
        colorScheme = colorScheme,
        typography = Typography,
        content = content
    )
}
```

---

## 🏛️ Data Layer (Same as XML Version)

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

### **2. Repository Implementation** (Same as XML version)

```kotlin
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

## 🎯 ViewModel Implementation

### **MainViewModel (Enhanced for Compose)**

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
    
    private val _isRefreshing = MutableStateFlow(false)
    val isRefreshing = _isRefreshing.asStateFlow()
    
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
                        // Keep current state but could show a snackbar
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
            _isRefreshing.value = true
            val result = refreshRepositoriesUseCase()
            _isRefreshing.value = false
            
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

---

## 🧪 Compose Testing Implementation

### **1. Compose UI Tests**

```kotlin
// androidTest/presentation/ui/main/MainScreenTest.kt
@HiltAndroidTest
@RunWith(AndroidJUnit4::class)
class MainScreenTest {
    
    @get:Rule(order = 0)
    var hiltRule = HiltAndroidRule(this)
    
    @get:Rule(order = 1)
    val composeTestRule = createAndroidComposeRule<MainActivity>()
    
    @Before
    fun setup() {
        hiltRule.inject()
    }
    
    @Test
    fun mainScreen_displaysLoadingState() {
        composeTestRule.setContent {
            GitHubTrendingTheme {
                LoadingScreen()
            }
        }
        
        composeTestRule
            .onNodeWithText("Loading repositories...")
            .assertIsDisplayed()
    }
    
    @Test
    fun mainScreen_displaysRepositoryList() {
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
        
        composeTestRule.setContent {
            GitHubTrendingTheme {
                RepositoryList(
                    repositories = mockRepos,
                    onItemClick = { }
                )
            }
        }
        
        composeTestRule
            .onNodeWithText("test-repo")
            .assertIsDisplayed()
        
        composeTestRule
            .onNodeWithText("Test repository")
            .assertIsDisplayed()
        
        composeTestRule
            .onNodeWithText("Kotlin")
            .assertIsDisplayed()
    }
    
    @Test
    fun repositoryItem_clickTriggersCallback() {
        val mockRepo = GitHubRepo(
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
        
        var clickedRepo: GitHubRepo? = null
        
        composeTestRule.setContent {
            GitHubTrendingTheme {
                RepositoryItem(
                    repository = mockRepo,
                    onClick = { clickedRepo = mockRepo }
                )
            }
        }
        
        composeTestRule
            .onNodeWithText("test-repo")
            .performClick()
        
        assertEquals(mockRepo, clickedRepo)
    }
    
    @Test
    fun errorScreen_displaysErrorMessage() {
        val errorMessage = "Network error occurred"
        
        composeTestRule.setContent {
            GitHubTrendingTheme {
                ErrorScreen(
                    message = errorMessage,
                    onRetry = { }
                )
            }
        }
        
        composeTestRule
            .onNodeWithText("Something went wrong")
            .assertIsDisplayed()
        
        composeTestRule
            .onNodeWithText(errorMessage)
            .assertIsDisplayed()
        
        composeTestRule
            .onNodeWithText("Retry")
            .assertIsDisplayed()
    }
    
    @Test
    fun noInternetScreen_displaysCorrectContent() {
        composeTestRule.setContent {
            GitHubTrendingTheme {
                NoInternetScreen(onRetry = { })
            }
        }
        
        composeTestRule
            .onNodeWithText("No Internet Connection")
            .assertIsDisplayed()
        
        composeTestRule
            .onNodeWithText("Please check your connection and try again")
            .assertIsDisplayed()
        
        composeTestRule
            .onNodeWithText("Try Again")
            .assertIsDisplayed()
    }
}
```

### **2. Component Tests**

```kotlin
// androidTest/presentation/ui/components/RepositoryItemTest.kt
@RunWith(AndroidJUnit4::class)
class RepositoryItemTest {
    
    @get:Rule
    val composeTestRule = createComposeRule()
    
    private val mockRepo = GitHubRepo(
        id = 1,
        name = "octokit.rb",
        fullName = "octokit/octokit.rb",
        description = "Ruby toolkit for the GitHub API",
        language = "Ruby",
        stargazersCount = 3200,
        forksCount = 1500,
        updatedAt = "2023-01-01T00:00:00Z",
        htmlUrl = "https://github.com/octokit/octokit.rb",
        owner = GitHubRepo.Owner("octokit", "https://avatar.url")
    )
    
    @Test
    fun repositoryItem_displaysAllInformation() {
        composeTestRule.setContent {
            GitHubTrendingTheme {
                RepositoryItem(
                    repository = mockRepo,
                    onClick = { }
                )
            }
        }
        
        // Check repository name
        composeTestRule
            .onNodeWithText("octokit.rb")
            .assertIsDisplayed()
        
        // Check description
        composeTestRule
            .onNodeWithText("Ruby toolkit for the GitHub API")
            .assertIsDisplayed()
        
        // Check language
        composeTestRule
            .onNodeWithText("Ruby")
            .assertIsDisplayed()
        
        // Check star count (formatted)
        composeTestRule
            .onNodeWithText("3.2k")
            .assertIsDisplayed()
    }
    
    @Test
    fun repositoryItem_handlesNullDescription() {
        val repoWithoutDescription = mockRepo.copy(description = null)
        
        composeTestRule.setContent {
            GitHubTrendingTheme {
                RepositoryItem(
                    repository = repoWithoutDescription,
                    onClick = { }
                )
            }
        }
        
        composeTestRule
            .onNodeWithText("octokit.rb")
            .assertIsDisplayed()
        
        // Description should not be displayed
        composeTestRule
            .onNodeWithText("Ruby toolkit for the GitHub API")
            .assertDoesNotExist()
    }
    
    @Test
    fun repositoryItem_handlesNullLanguage() {
        val repoWithoutLanguage = mockRepo.copy(language = null)
        
        composeTestRule.setContent {
            GitHubTrendingTheme {
                RepositoryItem(
                    repository = repoWithoutLanguage,
                    onClick = { }
                )
            }
        }
        
        composeTestRule
            .onNodeWithText("octokit.rb")
            .assertIsDisplayed()
        
        // Language chip should not be displayed
        composeTestRule
            .onNodeWithText("Ruby")
            .assertDoesNotExist()
    }
}
```

### **3. ViewModel Tests (Same as XML version)**

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
}
```

---

## 📦 Dependencies for Compose

### **build.gradle.kts (app module)**

```kotlin
dependencies {
    // Core Android
    implementation(libs.androidx.core.ktx)
    implementation(libs.androidx.lifecycle.runtime.ktx)
    implementation(libs.androidx.activity.compose)
    
    // Compose BOM
    implementation(platform(libs.androidx.compose.bom))
    implementation(libs.androidx.ui)
    implementation(libs.androidx.ui.graphics)
    implementation(libs.androidx.ui.tooling.preview)
    implementation(libs.androidx.material3)
    
    // Compose additional
    implementation(libs.androidx.lifecycle.viewmodel.compose)
    implementation(libs.androidx.navigation.compose)
    implementation(libs.androidx.hilt.navigation.compose)
    
    // Pull to refresh for Compose
    implementation("com.google.accompanist:accompanist-swiperefresh:0.32.0")
    
    // Architecture Components
    implementation(libs.androidx.lifecycle.viewmodel.ktx)
    implementation(libs.androidx.lifecycle.livedata.ktx)
    
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
    
    // Compose Testing
    androidTestImplementation(platform(libs.androidx.compose.bom))
    androidTestImplementation(libs.androidx.ui.test.junit4)
    androidTestImplementation(libs.androidx.junit)
    androidTestImplementation(libs.androidx.espresso.core)
    androidTestImplementation(libs.hilt.android.testing)
    kaptAndroidTest(libs.hilt.compiler)
    
    debugImplementation(libs.androidx.ui.tooling)
    debugImplementation(libs.androidx.ui.test.manifest)
}
```

---

## 🎯 Compose-Specific Advantages

### **1. Declarative UI:**
- **State-driven**: UI automatically updates when state changes
- **Less boilerplate**: No findViewById, no ViewBinding setup
- **Reactive**: Built-in support for StateFlow/LiveData

### **2. Better Performance:**
- **Recomposition**: Only affected parts of UI update
- **Smart defaults**: Automatic optimizations
- **Memory efficient**: No view hierarchy overhead

### **3. Modern Development:**
- **Kotlin-first**: Leverages Kotlin features
- **Type safety**: Compile-time UI validation
- **Preview support**: See UI changes instantly

### **4. Testing Benefits:**
- **Semantic testing**: Test by content, not IDs
- **Isolated components**: Easy to test individual composables
- **Better assertions**: More readable test code

---

## 🚀 Implementation Strategy (90 Minutes)

### **Time Allocation for Compose:**
- **Minutes 0-10**: Project setup, Compose dependencies
- **Minutes 10-25**: Data models, API service, database setup
- **Minutes 25-45**: Repository implementation with caching
- **Minutes 45-70**: Compose UI implementation (Screens, Components)
- **Minutes 70-85**: Testing (Compose UI tests + Unit tests)
- **Minutes 85-90**: Final testing and polish

### **Priority Order:**
1. ✅ **Basic Compose setup and theme**
2. ✅ **Data models and API service**
3. ✅ **Repository with network + cache**
4. ✅ **Main screen with LazyColumn**
5. ✅ **Pull to refresh with Accompanist**
6. ✅ **Sorting functionality**
7. ✅ **Error states and loading**
8. ✅ **Compose UI tests**
9. ✅ **Unit tests**
10. ✅ **Polish and animations**

---

## 🎯 Key Interview Points for Compose

### **Architecture:**
- "Using Compose with MVVM for reactive, declarative UI"
- "StateFlow integration provides automatic recomposition"
- "Unidirectional data flow with clear state management"

### **Performance:**
- "Compose recomposition only updates changed parts"
- "LazyColumn provides efficient scrolling for large lists"
- "Smart recomposition with stable parameters"

### **Modern Development:**
- "Declarative UI reduces boilerplate and bugs"
- "Type-safe UI development with Kotlin"
- "Better testing with semantic assertions"

### **Compose Benefits:**
- "Faster development with live previews"
- "Better state management integration"
- "More maintainable UI code"

---

**This Compose implementation provides the same functionality as the XML version but with modern, declarative UI patterns. Perfect for demonstrating cutting-edge Android development skills! 🚀**
