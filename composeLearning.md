# 📱 Complete Jetpack Compose Guide for Senior Android Developers

## Table of Contents
1. [Compose Fundamentals](#compose-fundamentals)
2. [State Management](#state-management)
3. [Side Effects](#side-effects)
4. [Layouts & UI](#layouts--ui)
5. [Animations](#animations)
6. [Gestures & Input](#gestures--input)
7. [Navigation](#navigation)
8. [Performance Optimization](#performance-optimization)
9. [Testing](#testing)
10. [Material Design 3](#material-design-3)
11. [Interoperability](#interoperability)
12. [Advanced Topics](#advanced-topics)
13. [Best Practices](#best-practices)
14. [Common Pitfalls](#common-pitfalls)

---

## Compose Fundamentals

### 1.1 Basic Composable Functions

```kotlin
// Basic composable
@Composable
fun Greeting(name: String) {
    Text(text = "Hello, $name!")
}

// Composable with modifier
@Composable
fun StyledGreeting(name: String, modifier: Modifier = Modifier) {
    Text(
        text = "Hello, $name!",
        modifier = modifier
            .padding(16.dp)
            .background(Color.Blue)
    )
}

// Composable with multiple children
@Composable
fun UserProfile(user: User) {
    Column {
        Text(user.name)
        Text(user.email)
        Text(user.phone)
    }
}
```

### 1.2 Composition Lifecycle

```kotlin
@Composable
fun LifecycleDemo() {
    // ENTER: When composable enters composition
    println("Composable is being composed")
    
    // RECOMPOSITION: When state changes
    var counter by remember { mutableStateOf(0) }
    
    // EXIT: When composable leaves composition
    DisposableEffect(Unit) {
        onDispose {
            println("Composable is being disposed")
        }
    }
    
    Button(onClick = { counter++ }) {
        Text("Count: $counter") // This recomposes
    }
}
```

### 1.3 Composition Phases

```kotlin
/**
 * Three phases of Compose:
 * 1. COMPOSITION - What to show (create/update UI tree)
 * 2. LAYOUT - Where to place it (measure & position)
 * 3. DRAWING - How to render it (draw on canvas)
 */

@Composable
fun PhaseExample() {
    // COMPOSITION phase - reads state
    val color by animateColorAsState(targetValue = Color.Red)
    
    Box(
        modifier = Modifier
            // LAYOUT phase - positioning
            .size(100.dp)
            .offset(x = 10.dp, y = 20.dp)
            // DRAWING phase - rendering
            .background(color)
            .drawBehind {
                drawCircle(Color.Blue)
            }
    )
}
```

### 1.4 Modifier Deep Dive

```kotlin
@Composable
fun ModifierChainExample() {
    // ORDER MATTERS! Modifiers apply from top to bottom
    Box(
        modifier = Modifier
            .size(100.dp)        // 1. Set size first
            .padding(16.dp)      // 2. Add padding (shrinks to 68dp)
            .background(Color.Blue) // 3. Background on 68dp box
            .padding(8.dp)       // 4. More padding (shrinks to 52dp)
            .background(Color.Red)  // 5. Background on 52dp box
    )
}

// Custom Modifier
fun Modifier.conditional(
    condition: Boolean,
    modifier: Modifier.() -> Modifier
): Modifier {
    return if (condition) {
        then(modifier(Modifier))
    } else {
        this
    }
}

// Usage
@Composable
fun ConditionalModifierExample(isHighlighted: Boolean) {
    Text(
        text = "Hello",
        modifier = Modifier.conditional(isHighlighted) {
            background(Color.Yellow)
        }
    )
}
```

---

## State Management

### 2.1 State Types

```kotlin
// 1. remember - survives recomposition, NOT configuration changes
@Composable
fun RememberExample() {
    var count by remember { mutableStateOf(0) }
    Button(onClick = { count++ }) {
        Text("Count: $count") // Lost on rotation
    }
}

// 2. rememberSaveable - survives configuration changes
@Composable
fun RememberSaveableExample() {
    var count by rememberSaveable { mutableStateOf(0) }
    Button(onClick = { count++ }) {
        Text("Count: $count") // Survives rotation
    }
}

// 3. ViewModel with StateFlow
@HiltViewModel
class MyViewModel @Inject constructor() : ViewModel() {
    private val _state = MutableStateFlow(MyState())
    val state = _state.asStateFlow()
    
    fun updateState(newValue: String) {
        _state.update { it.copy(value = newValue) }
    }
}

@Composable
fun ViewModelExample(viewModel: MyViewModel = hiltViewModel()) {
    val state by viewModel.state.collectAsStateWithLifecycle()
    Text(state.value)
}

// 4. derivedStateOf - computed state
@Composable
fun DerivedStateExample() {
    val listState = rememberLazyListState()
    
    // Only recomposes when first visible item changes
    val firstVisibleItemIndex by remember {
        derivedStateOf { listState.firstVisibleItemIndex }
    }
    
    Text("First visible: $firstVisibleItemIndex")
}

// 5. produceState - convert async data to State
@Composable
fun ProduceStateExample(userId: String): State<User?> {
    return produceState<User?>(initialValue = null, userId) {
        value = repository.loadUser(userId)
    }
}

// 6. snapshotFlow - observe Compose state in coroutines
@Composable
fun SnapshotFlowExample() {
    val listState = rememberLazyListState()
    
    LaunchedEffect(listState) {
        snapshotFlow { listState.firstVisibleItemIndex }
            .distinctUntilChanged()
            .collect { index ->
                println("First visible item: $index")
            }
    }
}
```

### 2.2 State Hoisting

```kotlin
// ❌ BAD - State not hoisted
@Composable
fun BadCounter() {
    var count by remember { mutableStateOf(0) }
    Column {
        Text("Count: $count")
        Button(onClick = { count++ }) { Text("Increment") }
    }
}

// ✅ GOOD - State hoisted
@Composable
fun GoodCounter(
    count: Int,
    onIncrement: () -> Unit
) {
    Column {
        Text("Count: $count")
        Button(onClick = onIncrement) { Text("Increment") }
    }
}

@Composable
fun CounterScreen() {
    var count by remember { mutableStateOf(0) }
    GoodCounter(
        count = count,
        onIncrement = { count++ }
    )
}
```

### 2.3 MVI/UDF Architecture

```kotlin
// State - represents UI state
data class ProfileUiState(
    val profiles: List<Profile> = emptyList(),
    val isLoading: Boolean = false,
    val error: String? = null,
    val selectedFilter: FilterType = FilterType.All,
    val isRefreshing: Boolean = false
) {
    val hasProfiles: Boolean get() = profiles.isNotEmpty()
    val showEmptyState: Boolean get() = !isLoading && profiles.isEmpty()
}

// Event - user actions
sealed interface ProfileEvent {
    data class OnProfileClick(val id: String) : ProfileEvent
    object OnRefresh : ProfileEvent
    data class OnFilterChange(val filter: FilterType) : ProfileEvent
    object OnRetry : ProfileEvent
    data class OnSearch(val query: String) : ProfileEvent
}

// Effect - one-time side effects (navigation, toasts)
sealed interface ProfileEffect {
    data class NavigateToDetail(val id: String) : ProfileEffect
    data class ShowToast(val message: String) : ProfileEffect
    object ShowError : ProfileEffect
}

// ViewModel
@HiltViewModel
class ProfileViewModel @Inject constructor(
    private val repository: ProfileRepository
) : ViewModel() {
    
    private val _state = MutableStateFlow(ProfileUiState())
    val state = _state.asStateFlow()
    
    private val _effect = Channel<ProfileEffect>()
    val effect = _effect.receiveAsFlow()
    
    fun onEvent(event: ProfileEvent) {
        when (event) {
            is ProfileEvent.OnProfileClick -> handleProfileClick(event.id)
            is ProfileEvent.OnRefresh -> loadProfiles(forceRefresh = true)
            is ProfileEvent.OnFilterChange -> applyFilter(event.filter)
            is ProfileEvent.OnRetry -> loadProfiles()
            is ProfileEvent.OnSearch -> searchProfiles(event.query)
        }
    }
    
    private fun handleProfileClick(id: String) {
        viewModelScope.launch {
            _effect.send(ProfileEffect.NavigateToDetail(id))
        }
    }
    
    private fun loadProfiles(forceRefresh: Boolean = false) {
        viewModelScope.launch {
            _state.update { it.copy(isLoading = true, error = null) }
            
            repository.getProfiles(forceRefresh)
                .onSuccess { profiles ->
                    _state.update { it.copy(
                        profiles = profiles,
                        isLoading = false
                    )}
                }
                .onFailure { error ->
                    _state.update { it.copy(
                        error = error.message,
                        isLoading = false
                    )}
                    _effect.send(ProfileEffect.ShowError)
                }
        }
    }
}

// Composable
@Composable
fun ProfileScreen(
    viewModel: ProfileViewModel = hiltViewModel(),
    navigateToDetail: (String) -> Unit
) {
    val state by viewModel.state.collectAsStateWithLifecycle()
    val context = LocalContext.current
    
    // Handle one-time effects
    LaunchedEffect(Unit) {
        viewModel.effect.collect { effect ->
            when (effect) {
                is ProfileEffect.NavigateToDetail -> navigateToDetail(effect.id)
                is ProfileEffect.ShowToast -> {
                    Toast.makeText(context, effect.message, Toast.LENGTH_SHORT).show()
                }
                is ProfileEffect.ShowError -> {
                    Toast.makeText(context, "Error loading profiles", Toast.LENGTH_SHORT).show()
                }
            }
        }
    }
    
    ProfileContent(
        state = state,
        onEvent = viewModel::onEvent
    )
}

@Composable
private fun ProfileContent(
    state: ProfileUiState,
    onEvent: (ProfileEvent) -> Unit
) {
    when {
        state.isLoading -> LoadingScreen()
        state.showEmptyState -> EmptyScreen(onRetry = { onEvent(ProfileEvent.OnRetry) })
        state.hasProfiles -> ProfileList(
            profiles = state.profiles,
            onProfileClick = { id -> onEvent(ProfileEvent.OnProfileClick(id)) }
        )
    }
}
```

### 2.4 Stability & Immutability

```kotlin
// ❌ UNSTABLE - Will cause unnecessary recompositions
data class MutableUser(
    var name: String,  // var makes it unstable
    val age: Int
)

@Composable
fun UnstableExample(user: MutableUser) {
    // This will recompose every time parent recomposes
    Text(user.name)
}

// ✅ STABLE - Immutable data class
data class User(
    val name: String,  // val makes it stable
    val age: Int
)

@Composable
fun StableExample(user: User) {
    // This can skip recomposition if user hasn't changed
    Text(user.name)
}

// Using @Stable annotation for complex types
@Stable
interface UiState {
    val isLoading: Boolean
}

@Immutable
data class ProfileState(
    override val isLoading: Boolean,
    val profiles: List<Profile>
) : UiState

// Collections stability
fun unstableList(): MutableList<String> = mutableListOf() // ❌ Unstable
fun stableList(): List<String> = listOf() // ✅ Stable
fun stableImmutableList(): ImmutableList<String> = persistentListOf() // ✅ Most stable
```

---

## Side Effects

### 3.1 LaunchedEffect

```kotlin
// Runs when key changes or enters composition
@Composable
fun LaunchedEffectExample(userId: String) {
    var user by remember { mutableStateOf<User?>(null) }
    
    LaunchedEffect(userId) { // Restarts when userId changes
        user = repository.loadUser(userId)
    }
    
    user?.let { UserDisplay(it) }
}

// Multiple keys
@Composable
fun MultipleKeysExample(userId: String, refresh: Boolean) {
    LaunchedEffect(userId, refresh) {
        // Runs when either userId OR refresh changes
        loadData(userId)
    }
}

// One-time effect
@Composable
fun OneTimeEffect() {
    LaunchedEffect(Unit) { // Only runs once
        analyticsLogger.logScreenView()
    }
}
```

### 3.2 DisposableEffect

```kotlin
// Cleanup resources
@Composable
fun DisposableEffectExample() {
    val context = LocalContext.current
    
    DisposableEffect(Unit) {
        val receiver = MyBroadcastReceiver()
        context.registerReceiver(receiver, IntentFilter("ACTION"))
        
        onDispose {
            context.unregisterReceiver(receiver)
        }
    }
}

// Lifecycle observer
@Composable
fun LifecycleAwareComposable(
    onStart: () -> Unit,
    onStop: () -> Unit
) {
    val lifecycleOwner = LocalLifecycleOwner.current
    
    DisposableEffect(lifecycleOwner) {
        val observer = LifecycleEventObserver { _, event ->
            when (event) {
                Lifecycle.Event.ON_START -> onStart()
                Lifecycle.Event.ON_STOP -> onStop()
                else -> {}
            }
        }
        
        lifecycleOwner.lifecycle.addObserver(observer)
        
        onDispose {
            lifecycleOwner.lifecycle.removeObserver(observer)
        }
    }
}
```

### 3.3 SideEffect

```kotlin
// Runs on every successful recomposition
@Composable
fun SideEffectExample(user: User) {
    SideEffect {
        // Update non-Compose state
        analyticsLogger.setUserId(user.id)
    }
    
    UserProfile(user)
}

// Publishing state to non-Compose code
@Composable
fun PublishToNonCompose(state: String) {
    SideEffect {
        // Publish to external system
        NonComposeTracker.updateState(state)
    }
}
```

### 3.4 rememberCoroutineScope

```kotlin
@Composable
fun CoroutineScopeExample() {
    val scope = rememberCoroutineScope()
    val scaffoldState = rememberBottomSheetScaffoldState()
    
    Button(
        onClick = {
            // Launch coroutine from event handler
            scope.launch {
                scaffoldState.bottomSheetState.expand()
            }
        }
    ) {
        Text("Show Bottom Sheet")
    }
}

// With error handling
@Composable
fun ErrorHandlingExample() {
    val scope = rememberCoroutineScope()
    var error by remember { mutableStateOf<String?>(null) }
    
    Button(
        onClick = {
            scope.launch {
                try {
                    performNetworkCall()
                } catch (e: Exception) {
                    error = e.message
                }
            }
        }
    ) {
        Text("Load Data")
    }
    
    error?.let { Text(it, color = Color.Red) }
}
```

### 3.5 rememberUpdatedState

```kotlin
// Capture latest value in LaunchedEffect
@Composable
fun TimerExample(onTimeout: () -> Unit) {
    // onTimeout might change, but LaunchedEffect shouldn't restart
    val currentOnTimeout by rememberUpdatedState(onTimeout)
    
    LaunchedEffect(Unit) {
        delay(5000)
        currentOnTimeout() // Always calls latest version
    }
}

// Without rememberUpdatedState (BAD)
@Composable
fun BadTimerExample(onTimeout: () -> Unit) {
    LaunchedEffect(onTimeout) { // ❌ Restarts timer when onTimeout changes!
        delay(5000)
        onTimeout()
    }
}
```

### 3.6 Complete Side Effect Decision Tree

```kotlin
/**
 * Which side effect to use?
 * 
 * Need to call suspend function?
 *   ├─ When composable enters composition? → LaunchedEffect
 *   ├─ From click/event handler? → rememberCoroutineScope
 *   └─ Convert non-Compose async to State? → produceState
 * 
 * Need cleanup (listeners, observers)?
 *   └─ DisposableEffect
 * 
 * Publish to non-Compose on every recomposition?
 *   └─ SideEffect
 * 
 * Observe Compose state in coroutine?
 *   └─ snapshotFlow
 * 
 * Need latest value in long-running effect?
 *   └─ rememberUpdatedState
 */
```

---

## Layouts & UI

### 4.1 Basic Layouts

```kotlin
// Column - vertical arrangement
@Composable
fun ColumnExample() {
    Column(
        modifier = Modifier.fillMaxSize(),
        verticalArrangement = Arrangement.spacedBy(8.dp),
        horizontalAlignment = Alignment.CenterHorizontally
    ) {
        Text("Item 1")
        Text("Item 2")
        Text("Item 3")
    }
}

// Row - horizontal arrangement
@Composable
fun RowExample() {
    Row(
        modifier = Modifier.fillMaxWidth(),
        horizontalArrangement = Arrangement.SpaceBetween,
        verticalAlignment = Alignment.CenterVertically
    ) {
        Text("Left")
        Text("Center")
        Text("Right")
    }
}

// Box - layered arrangement
@Composable
fun BoxExample() {
    Box(
        modifier = Modifier.size(200.dp),
        contentAlignment = Alignment.Center
    ) {
        Image(/* Background */)
        Text("Overlay") // On top
    }
}
```

### 4.2 Lazy Layouts

```kotlin
// LazyColumn - vertical scrolling list
@Composable
fun LazyColumnExample(items: List<Item>) {
    val listState = rememberLazyListState()
    
    LazyColumn(
        state = listState,
        contentPadding = PaddingValues(16.dp),
        verticalArrangement = Arrangement.spacedBy(8.dp)
    ) {
        // Single item
        item {
            HeaderItem()
        }
        
        // Multiple items
        items(items) { item ->
            ItemCard(item)
        }
        
        // Items with index
        itemsIndexed(items) { index, item ->
            ItemCard(item, index)
        }
        
        // Items with key for better performance
        items(
            items = items,
            key = { it.id } // Stable identity
        ) { item ->
            ItemCard(item)
        }
    }
}

// LazyRow - horizontal scrolling list
@Composable
fun LazyRowExample() {
    LazyRow(
        horizontalArrangement = Arrangement.spacedBy(16.dp),
        contentPadding = PaddingValues(horizontal = 16.dp)
    ) {
        items(20) { index ->
            Card(
                modifier = Modifier.size(150.dp)
            ) {
                Text("Item $index")
            }
        }
    }
}

// LazyVerticalGrid
@Composable
fun LazyGridExample() {
    LazyVerticalGrid(
        columns = GridCells.Adaptive(minSize = 128.dp),
        contentPadding = PaddingValues(16.dp),
        horizontalArrangement = Arrangement.spacedBy(8.dp),
        verticalArrangement = Arrangement.spacedBy(8.dp)
    ) {
        items(100) { index ->
            Card(modifier = Modifier.aspectRatio(1f)) {
                Text("Item $index")
            }
        }
    }
}

// Sticky headers
@Composable
fun StickyHeaderExample(groupedItems: Map<String, List<Item>>) {
    LazyColumn {
        groupedItems.forEach { (header, items) ->
            stickyHeader {
                Text(
                    text = header,
                    modifier = Modifier
                        .fillMaxWidth()
                        .background(Color.Gray)
                        .padding(16.dp)
                )
            }
            
            items(items) { item ->
                ItemCard(item)
            }
        }
    }
}

// Scroll to position
@Composable
fun ScrollToPositionExample() {
    val listState = rememberLazyListState()
    val scope = rememberCoroutineScope()
    
    Column {
        Button(
            onClick = {
                scope.launch {
                    // Animated scroll
                    listState.animateScrollToItem(50)
                    
                    // Instant scroll
                    // listState.scrollToItem(50)
                }
            }
        ) {
            Text("Scroll to item 50")
        }
        
        LazyColumn(state = listState) {
            items(100) { index ->
                Text("Item $index")
            }
        }
    }
}
```

### 4.3 Custom Layout

```kotlin
// Custom layout composable
@Composable
fun CustomLayout(
    modifier: Modifier = Modifier,
    content: @Composable () -> Unit
) {
    Layout(
        content = content,
        modifier = modifier
    ) { measurables, constraints ->
        // Measure children
        val placeables = measurables.map { measurable ->
            measurable.measure(constraints)
        }
        
        // Calculate layout size
        val width = placeables.maxOfOrNull { it.width } ?: 0
        val height = placeables.sumOf { it.height }
        
        // Place children
        layout(width, height) {
            var yPosition = 0
            placeables.forEach { placeable ->
                placeable.placeRelative(x = 0, y = yPosition)
                yPosition += placeable.height
            }
        }
    }
}

// Staggered grid layout
@Composable
fun StaggeredGridLayout(
    modifier: Modifier = Modifier,
    columns: Int = 2,
    content: @Composable () -> Unit
) {
    Layout(
        content = content,
        modifier = modifier
    ) { measurables, constraints ->
        val columnWidth = constraints.maxWidth / columns
        val columnConstraints = constraints.copy(maxWidth = columnWidth)
        
        val columnHeights = IntArray(columns) { 0 }
        val placeables = measurables.map { measurable ->
            val column = columnHeights.indices.minByOrNull { columnHeights[it] } ?: 0
            val placeable = measurable.measure(columnConstraints)
            columnHeights[column] += placeable.height
            column to placeable
        }
        
        val height = columnHeights.maxOrNull() ?: 0
        
        layout(constraints.maxWidth, height) {
            val columnYPositions = IntArray(columns) { 0 }
            
            placeables.forEach { (column, placeable) ->
                placeable.placeRelative(
                    x = column * columnWidth,
                    y = columnYPositions[column]
                )
                columnYPositions[column] += placeable.height
            }
        }
    }
}
```

### 4.4 SubcomposeLayout

```kotlin
// Measure children conditionally
@Composable
fun SubcomposeLayoutExample(
    mainContent: @Composable () -> Unit,
    dependentContent: @Composable (Int) -> Unit
) {
    SubcomposeLayout { constraints ->
        // First measure main content
        val mainPlaceables = subcompose("main", mainContent).map {
            it.measure(constraints)
        }
        
        val mainHeight = mainPlaceables.maxOfOrNull { it.height } ?: 0
        
        // Then measure dependent content based on main content's size
        val dependentPlaceables = subcompose("dependent") {
            dependentContent(mainHeight)
        }.map {
            it.measure(constraints)
        }
        
        val totalHeight = mainHeight + (dependentPlaceables.maxOfOrNull { it.height } ?: 0)
        
        layout(constraints.maxWidth, totalHeight) {
            var y = 0
            mainPlaceables.forEach {
                it.placeRelative(0, y)
                y += it.height
            }
            dependentPlaceables.forEach {
                it.placeRelative(0, y)
                y += it.height
            }
        }
    }
}
```

### 4.5 Constraint Layout

```kotlin
@Composable
fun ConstraintLayoutExample() {
    ConstraintLayout(
        modifier = Modifier.fillMaxSize()
    ) {
        val (title, subtitle, button) = createRefs()
        
        Text(
            text = "Title",
            modifier = Modifier.constrainAs(title) {
                top.linkTo(parent.top, margin = 16.dp)
                start.linkTo(parent.start, margin = 16.dp)
            }
        )
        
        Text(
            text = "Subtitle",
            modifier = Modifier.constrainAs(subtitle) {
                top.linkTo(title.bottom, margin = 8.dp)
                start.linkTo(title.start)
            }
        )
        
        Button(
            onClick = { },
            modifier = Modifier.constrainAs(button) {
                bottom.linkTo(parent.bottom, margin = 16.dp)
                end.linkTo(parent.end, margin = 16.dp)
            }
        ) {
            Text("Action")
        }
    }
}

// Guidelines
@Composable
fun ConstraintLayoutWithGuidelines() {
    ConstraintLayout(modifier = Modifier.fillMaxSize()) {
        val (image, text) = createRefs()
        val guideline = createGuidelineFromTop(0.3f)
        
        Image(
            painter = painterResource(id = R.drawable.image),
            contentDescription = null,
            modifier = Modifier.constrainAs(image) {
                top.linkTo(parent.top)
                bottom.linkTo(guideline)
                start.linkTo(parent.start)
                end.linkTo(parent.end)
                width = Dimension.fillToConstraints
                height = Dimension.fillToConstraints
            }
        )
        
        Text(
            text = "Below guideline",
            modifier = Modifier.constrainAs(text) {
                top.linkTo(guideline, margin = 16.dp)
                start.linkTo(parent.start, margin = 16.dp)
            }
        )
    }
}
```

---

## Animations

### 5.1 animate*AsState

```kotlin
// Simple value animation
@Composable
fun AnimateValueExample() {
    var expanded by remember { mutableStateOf(false) }
    
    val size by animateDpAsState(
        targetValue = if (expanded) 200.dp else 100.dp,
        animationSpec = spring(
            dampingRatio = Spring.DampingRatioMediumBouncy,
            stiffness = Spring.StiffnessLow
        )
    )
    
    Box(
        modifier = Modifier
            .size(size)
            .background(Color.Blue)
            .clickable { expanded = !expanded }
    )
}

// Multiple properties
@Composable
fun MultipleAnimationsExample() {
    var selected by remember { mutableStateOf(false) }
    
    val size by animateDpAsState(if (selected) 150.dp else 100.dp)
    val color by animateColorAsState(if (selected) Color.Red else Color.Blue)
    val elevation by animateDpAsState(if (selected) 8.dp else 2.dp)
    
    Card(
        modifier = Modifier.size(size),
        colors = CardDefaults.cardColors(containerColor = color),
        elevation = CardDefaults.cardElevation(defaultElevation = elevation),
        onClick = { selected = !selected }
    ) {
        Box(Modifier.fillMaxSize())
    }
}
```

### 5.2 Animatable

```kotlin
// Low-level animation API
@Composable
fun AnimatableExample() {
    val offset = remember { Animatable(0f) }
    val scope = rememberCoroutineScope()
    
    Box(
        modifier = Modifier
            .offset(x = offset.value.dp)
            .size(100.dp)
            .background(Color.Blue)
            .clickable {
                scope.launch {
                    // Animate to target
                    offset.animateTo(
                        targetValue = 200f,
                        animationSpec = tween(durationMillis = 500)
                    )
                    
                    // Snap back
                    offset.snapTo(0f)
                }
            }
    )
}

// Sequential animations
@Composable
fun SequentialAnimationsExample() {
    val alpha = remember { Animatable(0f) }
    val scale = remember { Animatable(0f) }
    
    LaunchedEffect(Unit) {
        // Fade in
        alpha.animateTo(1f, animationSpec = tween(500))
        
        // Then scale up
        scale.animateTo(1f, animationSpec = spring())
    }
    
    Box(
        modifier = Modifier
            .graphicsLayer(
                alpha = alpha.value,
                scaleX = scale.value,
                scaleY = scale.value
            )
            .size(100.dp)
            .background(Color.Blue)
    )
}

// Parallel animations
@Composable
fun ParallelAnimationsExample() {
    val alpha = remember { Animatable(0f) }
    val scale = remember { Animatable(0f) }
    
    LaunchedEffect(Unit) {
        launch { alpha.animateTo(1f) }
        launch { scale.animateTo(1f) }
    }
}
```

### 5.3 updateTransition

```kotlin
// State-based animations
@Composable
fun TransitionExample() {
    var state by remember { mutableStateOf(BoxState.Collapsed) }
    val transition = updateTransition(state, label = "box state")
    
    val size by transition.animateDp(label = "size") { boxState ->
        when (boxState) {
            BoxState.Collapsed -> 64.dp
            BoxState.Expanded -> 128.dp
        }
    }
    
    val color by transition.animateColor(label = "color") { boxState ->
        when (boxState) {
            BoxState.Collapsed -> Color.Gray
            BoxState.Expanded -> Color.Red
        }
    }
    
    Box(
        modifier = Modifier
            .size(size)
            .background(color)
            .clickable {
                state = if (state == BoxState.Collapsed) {
                    BoxState.Expanded
                } else {
                    BoxState.Collapsed
                }
            }
    )
}

enum class BoxState { Collapsed, Expanded }
```

### 5.4 AnimatedVisibility

```kotlin
@Composable
fun AnimatedVisibilityExample() {
    var visible by remember { mutableStateOf(false) }
    
    Column {
        Button(onClick = { visible = !visible }) {
            Text("Toggle")
        }
        
        AnimatedVisibility(
            visible = visible,
            enter = fadeIn() + expandVertically(),
            exit = fadeOut() + shrinkVertically()
        ) {
            Text(
                "Now you see me",
                modifier = Modifier.padding(16.dp)
            )
        }
    }
}

// Custom animations
@Composable
fun CustomAnimatedVisibility() {
    var visible by remember { mutableStateOf(false) }
    
    AnimatedVisibility(
        visible = visible,
        enter = slideInHorizontally(
            initialOffsetX = { -it }
        ) + fadeIn(),
        exit = slideOutHorizontally(
            targetOffsetX = { it }
        ) + fadeOut()
    ) {
        Card {
            Text("Sliding card")
        }
    }
}
```

### 5.5 AnimatedContent

```kotlin
@Composable
fun AnimatedContentExample() {
    var count by remember { mutableStateOf(0) }
    
    Column {
        Button(onClick = { count++ }) {
            Text("Increment")
        }
        
        AnimatedContent(
            targetState = count,
            transitionSpec = {
                slideInVertically { height -> height } + fadeIn() togetherWith
                    slideOutVertically { height -> -height } + fadeOut()
            }
        ) { targetCount ->
            Text(
                text = "Count: $targetCount",
                style = MaterialTheme.typography.displayLarge
            )
        }
    }
}
```

### 5.6 Shared Element Transitions

```kotlin
// New in Compose 1.6+
@Composable
fun SharedElementExample() {
    var showDetails by remember { mutableStateOf(false) }
    
    SharedTransitionLayout {
        AnimatedContent(
            targetState = showDetails,
            label = "content"
        ) { isShowingDetails ->
            if (!isShowingDetails) {
                // List screen
                Box(
                    modifier = Modifier
                        .sharedElement(
                            rememberSharedContentState(key = "image"),
                            animatedVisibilityScope = this
                        )
                        .size(100.dp)
                        .clickable { showDetails = true }
                ) {
                    AsyncImage(model = "url", contentDescription = null)
                }
            } else {
                // Detail screen
                Box(
                    modifier = Modifier
                        .sharedElement(
                            rememberSharedContentState(key = "image"),
                            animatedVisibilityScope = this
                        )
                        .fillMaxWidth()
                        .height(300.dp)
                        .clickable { showDetails = false }
                ) {
                    AsyncImage(model = "url", contentDescription = null)
                }
            }
        }
    }
}
```

### 5.7 Animation Specs

```kotlin
// Different animation specifications
@Composable
fun AnimationSpecsExample() {
    var trigger by remember { mutableStateOf(false) }
    
    // Spring animation (natural physics)
    val springValue by animateFloatAsState(
        targetValue = if (trigger) 1f else 0f,
        animationSpec = spring(
            dampingRatio = Spring.DampingRatioMediumBouncy,
            stiffness = Spring.StiffnessLow
        )
    )
    
    // Tween animation (duration-based)
    val tweenValue by animateFloatAsState(
        targetValue = if (trigger) 1f else 0f,
        animationSpec = tween(
            durationMillis = 1000,
            delayMillis = 100,
            easing = FastOutSlowInEasing
        )
    )
    
    // Keyframes animation (precise control)
    val keyframesValue by animateFloatAsState(
        targetValue = if (trigger) 1f else 0f,
        animationSpec = keyframes {
            durationMillis = 1000
            0f at 0
            0.5f at 500 using LinearEasing
            0.8f at 800
            1f at 1000
        }
    )
    
    // Repeatable animation
    val repeatableValue by animateFloatAsState(
        targetValue = if (trigger) 1f else 0f,
        animationSpec = repeatable(
            iterations = 3,
            animation = tween(300),
            repeatMode = RepeatMode.Reverse
        )
    )
    
    // Infinite repeatable
    val infiniteTransition = rememberInfiniteTransition()
    val infiniteValue by infiniteTransition.animateFloat(
        initialValue = 0f,
        targetValue = 1f,
        animationSpec = infiniteRepeatable(
            animation = tween(1000),
            repeatMode = RepeatMode.Reverse
        )
    )
}
```

---

## Gestures & Input

### 6.1 Click Gestures

```kotlin
@Composable
fun ClickGesturesExample() {
    // Simple click
    Box(
        modifier = Modifier
            .size(100.dp)
            .clickable { println("Clicked") }
    )
    
    // Clickable with ripple
    Box(
        modifier = Modifier
            .size(100.dp)
            .clickable(
                indication = ripple(color = Color.Red),
                interactionSource = remember { MutableInteractionSource() }
            ) {
                println("Clicked with ripple")
            }
    )
    
    // Combined click (single, double, long)
    Box(
        modifier = Modifier
            .size(100.dp)
            .combinedClickable(
                onClick = { println("Single click") },
                onDoubleClick = { println("Double click") },
                onLongClick = { println("Long press") }
            )
    )
}
```

### 6.2 Drag Gestures

```kotlin
@Composable
fun DragGesturesExample() {
    var offsetX by remember { mutableStateOf(0f) }
    var offsetY by remember { mutableStateOf(0f) }
    
    // Drag in any direction
    Box(
        modifier = Modifier
            .offset { IntOffset(offsetX.roundToInt(), offsetY.roundToInt()) }
            .size(100.dp)
            .background(Color.Blue)
            .pointerInput(Unit) {
                detectDragGestures { change, dragAmount ->
                    change.consume()
                    offsetX += dragAmount.x
                    offsetY += dragAmount.y
                }
            }
    )
}

@Composable
fun HorizontalDragExample() {
    var offsetX by remember { mutableStateOf(0f) }
    
    Box(
        modifier = Modifier
            .offset { IntOffset(offsetX.roundToInt(), 0) }
            .size(100.dp)
            .background(Color.Red)
            .pointerInput(Unit) {
                detectHorizontalDragGestures { change, dragAmount ->
                    change.consume()
                    offsetX += dragAmount
                }
            }
    )
}

// Swipeable with animation
@Composable
fun SwipeableExample() {
    val offsetX = remember { Animatable(0f) }
    val scope = rememberCoroutineScope()
    
    Box(
        modifier = Modifier
            .offset { IntOffset(offsetX.value.roundToInt(), 0) }
            .size(100.dp)
            .background(Color.Green)
            .pointerInput(Unit) {
                detectHorizontalDragGestures(
                    onDragEnd = {
                        scope.launch {
                            // Snap back
                            offsetX.animateTo(0f, spring())
                        }
                    },
                    onHorizontalDrag = { change, dragAmount ->
                        scope.launch {
                            offsetX.snapTo(offsetX.value + dragAmount)
                        }
                    }
                )
            }
    )
}
```

### 6.3 Transform Gestures

```kotlin
@Composable
fun TransformGesturesExample() {
    var scale by remember { mutableStateOf(1f) }
    var rotation by remember { mutableStateOf(0f) }
    var offsetX by remember { mutableStateOf(0f) }
    var offsetY by remember { mutableStateOf(0f) }
    
    Box(
        modifier = Modifier.fillMaxSize(),
        contentAlignment = Alignment.Center
    ) {
        Box(
            modifier = Modifier
                .graphicsLayer(
                    scaleX = scale,
                    scaleY = scale,
                    rotationZ = rotation,
                    translationX = offsetX,
                    translationY = offsetY
                )
                .size(200.dp)
                .background(Color.Blue)
                .pointerInput(Unit) {
                    detectTransformGestures { _, pan, zoom, rotationChange ->
                        scale *= zoom
                        rotation += rotationChange
                        offsetX += pan.x
                        offsetY += pan.y
                    }
                }
        )
    }
}
```

### 6.4 Tap & Press Gestures

```kotlin
@Composable
fun TapGesturesExample() {
    var tapPosition by remember { mutableStateOf(Offset.Zero) }
    
    Box(
        modifier = Modifier
            .fillMaxSize()
            .pointerInput(Unit) {
                detectTapGestures(
                    onTap = { offset ->
                        tapPosition = offset
                        println("Tapped at $offset")
                    },
                    onDoubleTap = { offset ->
                        println("Double tapped at $offset")
                    },
                    onLongPress = { offset ->
                        println("Long pressed at $offset")
                    },
                    onPress = { offset ->
                        println("Pressed at $offset")
                        // Wait for release or cancellation
                        tryAwaitRelease()
                        println("Released")
                    }
                )
            }
    ) {
        Text(
            text = "Tap position: $tapPosition",
            modifier = Modifier.align(Alignment.Center)
        )
    }
}
```

### 6.5 Custom Gesture Detection

```kotlin
@Composable
fun CustomGestureExample() {
    var circlePosition by remember { mutableStateOf(Offset.Zero) }
    
    Canvas(
        modifier = Modifier
            .fillMaxSize()
            .pointerInput(Unit) {
                awaitEachGesture {
                    val down = awaitFirstDown()
                    circlePosition = down.position
                    
                    do {
                        val event = awaitPointerEvent()
                        val position = event.changes.first().position
                        circlePosition = position
                    } while (event.changes.any { it.pressed })
                }
            }
    ) {
        drawCircle(
            color = Color.Blue,
            radius = 50f,
            center = circlePosition
        )
    }
}
```

### 6.6 Nested Scrolling

```kotlin
@Composable
fun NestedScrollExample() {
    val scrollState = rememberScrollState()
    
    Column(
        modifier = Modifier
            .fillMaxSize()
            .verticalScroll(scrollState)
    ) {
        repeat(3) {
            LazyRow(
                modifier = Modifier
                    .fillMaxWidth()
                    .height(200.dp)
            ) {
                items(20) { index ->
                    Card(
                        modifier = Modifier
                            .padding(8.dp)
                            .size(150.dp)
                    ) {
                        Box(contentAlignment = Alignment.Center) {
                            Text("Item $index")
                        }
                    }
                }
            }
        }
    }
}

// Custom nested scroll connection
@Composable
fun CustomNestedScrollExample() {
    val toolbarHeight = 200.dp
    val toolbarHeightPx = with(LocalDensity.current) { toolbarHeight.toPx() }
    val toolbarOffsetHeightPx = remember { mutableStateOf(0f) }
    
    val nestedScrollConnection = remember {
        object : NestedScrollConnection {
            override fun onPreScroll(available: Offset, source: NestedScrollSource): Offset {
                val delta = available.y
                val newOffset = toolbarOffsetHeightPx.value + delta
                toolbarOffsetHeightPx.value = newOffset.coerceIn(-toolbarHeightPx, 0f)
                return Offset.Zero
            }
        }
    }
    
    Box(
        Modifier
            .fillMaxSize()
            .nestedScroll(nestedScrollConnection)
    ) {
        LazyColumn(modifier = Modifier.fillMaxSize()) {
            items(100) { index ->
                Text("Item $index", modifier = Modifier.padding(16.dp))
            }
        }
        
        Box(
            modifier = Modifier
                .height(toolbarHeight)
                .offset { IntOffset(x = 0, y = toolbarOffsetHeightPx.value.roundToInt()) }
                .background(Color.Blue)
        )
    }
}
```

---

## Navigation

### 7.1 Basic Navigation

```kotlin
// Navigation setup
@Composable
fun AppNavigation() {
    val navController = rememberNavController()
    
    NavHost(
        navController = navController,
        startDestination = "home"
    ) {
        composable("home") {
            HomeScreen(
                onNavigateToProfile = { userId ->
                    navController.navigate("profile/$userId")
                }
            )
        }
        
        composable(
            route = "profile/{userId}",
            arguments = listOf(
                navArgument("userId") {
                    type = NavType.StringType
                }
            )
        ) { backStackEntry ->
            val userId = backStackEntry.arguments?.getString("userId")
            ProfileScreen(userId = userId ?: "")
        }
    }
}

// Type-safe navigation (recommended)
sealed class Screen(val route: String) {
    object Home : Screen("home")
    object Profile : Screen("profile/{userId}") {
        fun createRoute(userId: String) = "profile/$userId"
    }
    object Settings : Screen("settings")
}

@Composable
fun TypeSafeNavigation() {
    val navController = rememberNavController()
    
    NavHost(
        navController = navController,
        startDestination = Screen.Home.route
    ) {
        composable(Screen.Home.route) {
            HomeScreen(
                onNavigateToProfile = { userId ->
                    navController.navigate(Screen.Profile.createRoute(userId))
                }
            )
        }
        
        composable(Screen.Profile.route) { backStackEntry ->
            val userId = backStackEntry.arguments?.getString("userId")
            ProfileScreen(userId = userId ?: "")
        }
    }
}
```

### 7.2 Navigation with Arguments

```kotlin
// String arguments
composable(
    route = "article/{articleId}",
    arguments = listOf(
        navArgument("articleId") {
            type = NavType.StringType
        }
    )
) { backStackEntry ->
    val articleId = backStackEntry.arguments?.getString("articleId")
    ArticleScreen(articleId = articleId ?: "")
}

// Int arguments
composable(
    route = "user/{userId}",
    arguments = listOf(
        navArgument("userId") {
            type = NavType.IntType
        }
    )
) { backStackEntry ->
    val userId = backStackEntry.arguments?.getInt("userId") ?: 0
    UserScreen(userId = userId)
}

// Optional arguments
composable(
    route = "search?query={query}",
    arguments = listOf(
        navArgument("query") {
            type = NavType.StringType
            nullable = true
            defaultValue = null
        }
    )
) { backStackEntry ->
    val query = backStackEntry.arguments?.getString("query")
    SearchScreen(initialQuery = query)
}

// Navigate with optional argument
navController.navigate("search?query=$searchText")
navController.navigate("search") // Uses default value
```

### 7.3 Deep Links

```kotlin
composable(
    route = "profile/{userId}",
    deepLinks = listOf(
        navDeepLink {
            uriPattern = "myapp://profile/{userId}"
        },
        navDeepLink {
            uriPattern = "https://myapp.com/profile/{userId}"
        }
    ),
    arguments = listOf(
        navArgument("userId") {
            type = NavType.StringType
        }
    )
) { backStackEntry ->
    val userId = backStackEntry.arguments?.getString("userId")
    ProfileScreen(userId = userId ?: "")
}

// AndroidManifest.xml
/*
<activity ...>
    <intent-filter>
        <action android:name="android.intent.action.VIEW" />
        <category android:name="android.intent.category.DEFAULT" />
        <category android:name="android.intent.category.BROWSABLE" />
        <data
            android:scheme="https"
            android:host="myapp.com"
            android:pathPrefix="/profile" />
    </intent-filter>
</activity>
*/
```

### 7.4 Bottom Navigation

```kotlin
@Composable
fun BottomNavigationApp() {
    val navController = rememberNavController()
    val navBackStackEntry by navController.currentBackStackEntryAsState()
    val currentDestination = navBackStackEntry?.destination
    
    Scaffold(
        bottomBar = {
            NavigationBar {
                bottomNavItems.forEach { screen ->
                    NavigationBarItem(
                        icon = { Icon(screen.icon, contentDescription = null) },
                        label = { Text(screen.label) },
                        selected = currentDestination?.hierarchy?.any {
                            it.route == screen.route
                        } == true,
                        onClick = {
                            navController.navigate(screen.route) {
                                // Pop up to start destination
                                popUpTo(navController.graph.findStartDestination().id) {
                                    saveState = true
                                }
                                // Avoid multiple copies
                                launchSingleTop = true
                                // Restore state when reselecting
                                restoreState = true
                            }
                        }
                    )
                }
            }
        }
    ) { paddingValues ->
        NavHost(
            navController = navController,
            startDestination = "home",
            modifier = Modifier.padding(paddingValues)
        ) {
            composable("home") { HomeScreen() }
            composable("search") { SearchScreen() }
            composable("profile") { ProfileScreen() }
        }
    }
}

data class BottomNavItem(
    val route: String,
    val icon: ImageVector,
    val label: String
)

val bottomNavItems = listOf(
    BottomNavItem("home", Icons.Default.Home, "Home"),
    BottomNavItem("search", Icons.Default.Search, "Search"),
    BottomNavItem("profile", Icons.Default.Person, "Profile")
)
```

### 7.5 Nested Navigation

```kotlin
@Composable
fun NestedNavigation() {
    val navController = rememberNavController()
    
    NavHost(navController, startDestination = "auth") {
        // Auth flow
        navigation(startDestination = "login", route = "auth") {
            composable("login") {
                LoginScreen(
                    onLoginSuccess = {
                        navController.navigate("main") {
                            popUpTo("auth") { inclusive = true }
                        }
                    }
                )
            }
            composable("register") { RegisterScreen() }
        }
        
        // Main app flow
        navigation(startDestination = "home", route = "main") {
            composable("home") { HomeScreen() }
            composable("settings") { SettingsScreen() }
        }
    }
}
```

### 7.6 Sharing ViewModels Across Navigation

```kotlin
@Composable
fun SharedViewModelNavigation() {
    val navController = rememberNavController()
    
    NavHost(navController, startDestination = "list") {
        composable("list") { backStackEntry ->
            val parentEntry = remember(backStackEntry) {
                navController.getBackStackEntry("list")
            }
            val sharedViewModel: SharedViewModel = hiltViewModel(parentEntry)
            
            ListScreen(
                viewModel = sharedViewModel,
                onItemClick = { id ->
                    navController.navigate("detail/$id")
                }
            )
        }
        
        composable("detail/{itemId}") { backStackEntry ->
            val parentEntry = remember(backStackEntry) {
                navController.getBackStackEntry("list")
            }
            val sharedViewModel: SharedViewModel = hiltViewModel(parentEntry)
            
            DetailScreen(viewModel = sharedViewModel)
        }
    }
}
```

---

## Performance Optimization

### 8.1 Recomposition Optimization

```kotlin
// ❌ BAD - Unstable lambda causes recomposition
@Composable
fun UnoptimizedList(items: List<Item>) {
    LazyColumn {
        items(items) { item ->
            ItemRow(
                item = item,
                onClick = { id -> handleClick(id) } // New lambda each recomposition
            )
        }
    }
}

// ✅ GOOD - Stable lambda
@Composable
fun OptimizedList(items: List<Item>, onItemClick: (String) -> Unit) {
    LazyColumn {
        items(items, key = { it.id }) { item ->
            ItemRow(
                item = item,
                onClick = onItemClick // Stable reference
            )
        }
    }
}

// Even better with remember
@Composable
fun BetterOptimizedList(items: List<Item>, onItemClick: (String) -> Unit) {
    LazyColumn {
        items(items, key = { it.id }) { item ->
            val clickHandler = remember(item.id, onItemClick) {
                { onItemClick(item.id) }
            }
            ItemRow(
                item = item,
                onClick = clickHandler
            )
        }
    }
}
```

### 8.2 Lazy State Reading

```kotlin
// ❌ BAD - Reads list state eagerly
@Composable
fun EagerReading() {
    val listState = rememberLazyListState()
    val firstVisibleIndex = listState.firstVisibleItemIndex // Reads immediately
    
    Text("First visible: $firstVisibleIndex") // Causes recomposition
}

// ✅ GOOD - Uses derivedStateOf
@Composable
fun LazyReading() {
    val listState = rememberLazyListState()
    
    val showButton by remember {
        derivedStateOf {
            listState.firstVisibleItemIndex > 0
        }
    }
    
    AnimatedVisibility(visible = showButton) {
        ScrollToTopButton()
    }
}
```

### 8.3 Baseline Profiles

```kotlin
// build.gradle.kts
dependencies {
    "baselineProfile"(project(":baselineprofile"))
}

// Generate baseline profile
// Run app, perform key interactions, then:
// adb shell am broadcast -a com.android.benchmark.action.BASELINE_PROFILE_SAVE
```

### 8.4 Remember Calculations

```kotlin
// ❌ BAD - Recalculates on every recomposition
@Composable
fun ExpensiveCalculation(items: List<Item>) {
    val sortedItems = items.sortedBy { it.name } // Recalculated every time!
    LazyColumn {
        items(sortedItems) { /* ... */ }
    }
}

// ✅ GOOD - Remember expensive calculation
@Composable
fun RememberedCalculation(items: List<Item>) {
    val sortedItems = remember(items) {
        items.sortedBy { it.name }
    }
    LazyColumn {
        items(sortedItems) { /* ... */ }
    }
}

// ✅ BETTER - Use derivedStateOf for dependent state
@Composable
fun DerivedCalculation(items: State<List<Item>>) {
    val sortedItems by remember {
        derivedStateOf {
            items.value.sortedBy { it.name }
        }
    }
    LazyColumn {
        items(sortedItems) { /* ... */ }
    }
}
```

### 8.5 Composable Function Extraction

```kotlin
// ❌ BAD - Everything recomposes together
@Composable
fun MonolithicScreen(state: UiState) {
    Column {
        Text(state.title)
        Text(state.subtitle)
        LazyColumn {
            items(state.items) { item ->
                ItemRow(item)
            }
        }
        Button(onClick = { }) {
            Text(state.buttonText)
        }
    }
}

// ✅ GOOD - Extracted composables can skip recomposition
@Composable
fun ModularScreen(state: UiState) {
    Column {
        HeaderSection(state.title, state.subtitle)
        ItemList(state.items)
        ActionButton(state.buttonText)
    }
}

@Composable
private fun HeaderSection(title: String, subtitle: String) {
    Column {
        Text(title)
        Text(subtitle)
    }
}

@Composable
private fun ItemList(items: List<Item>) {
    LazyColumn {
        items(items, key = { it.id }) { item ->
            ItemRow(item)
        }
    }
}
```

### 8.6 Immutable Collections

```kotlin
// Add to build.gradle.kts
dependencies {
    implementation("org.jetbrains.kotlinx:kotlinx-collections-immutable:0.3.6")
}

// ✅ Stable, won't cause unnecessary recompositions
@Composable
fun ImmutableCollectionExample(items: ImmutableList<Item>) {
    LazyColumn {
        items(items.size) { index ->
            ItemRow(items[index])
        }
    }
}

// Convert regular list to immutable
val regularList = listOf(1, 2, 3)
val immutableList = regularList.toImmutableList()
```

### 8.7 Composition Local

```kotlin
// Share data down the tree without passing through every composable
val LocalUserSettings = compositionLocalOf<UserSettings> {
    error("No UserSettings provided")
}

@Composable
fun App() {
    val userSettings = remember { UserSettings() }
    
    CompositionLocalProvider(LocalUserSettings provides userSettings) {
        // Any child can access userSettings
        HomeScreen()
    }
}

@Composable
fun DeepNestedComponent() {
    val settings = LocalUserSettings.current
    Text("Theme: ${settings.theme}")
}
```

---

## Testing

### 9.1 Basic Compose Tests

```kotlin
class ProfileScreenTest {
    
    @get:Rule
    val composeTestRule = createComposeRule()
    
    @Test
    fun profileScreen_displaysUserName() {
        // Arrange
        val testUser = User(name = "John Doe")
        
        // Act
        composeTestRule.setContent {
            ProfileScreen(user = testUser)
        }
        
        // Assert
        composeTestRule
            .onNodeWithText("John Doe")
            .assertIsDisplayed()
    }
    
    @Test
    fun profileScreen_clickButton_triggersCallback() {
        // Arrange
        var clicked = false
        
        composeTestRule.setContent {
            ProfileScreen(onButtonClick = { clicked = true })
        }
        
        // Act
        composeTestRule
            .onNodeWithText("Click Me")
            .performClick()
        
        // Assert
        assertTrue(clicked)
    }
}
```

### 9.2 Testing with ViewModels

```kotlin
class ProfileViewModelTest {
    
    @get:Rule
    val mainDispatcherRule = MainDispatcherRule()
    
    private lateinit var viewModel: ProfileViewModel
    private lateinit var fakeRepository: FakeProfileRepository
    
    @Before
    fun setup() {
        fakeRepository = FakeProfileRepository()
        viewModel = ProfileViewModel(fakeRepository)
    }
    
    @Test
    fun `loadProfile success updates state`() = runTest {
        // Arrange
        val expectedUser = User(id = "1", name = "John")
        fakeRepository.setUser(expectedUser)
        
        // Act
        viewModel.loadProfile("1")
        
        // Assert
        val state = viewModel.state.value
        assertThat(state.user).isEqualTo(expectedUser)
        assertThat(state.isLoading).isFalse()
        assertThat(state.error).isNull()
    }
}

// Fake repository for testing
class FakeProfileRepository : ProfileRepository {
    private var user: User? = null
    private var shouldThrowError = false
    
    fun setUser(user: User) {
        this.user = user
    }
    
    fun setShouldThrowError(value: Boolean) {
        shouldThrowError = value
    }
    
    override suspend fun getProfile(id: String): Result<User> {
        return if (shouldThrowError) {
            Result.failure(Exception("Network error"))
        } else {
            Result.success(user!!)
        }
    }
}
```

### 9.3 Testing Interactions

```kotlin
@Test
fun lazyList_scrollToItem_displaysItem() {
    composeTestRule.setContent {
        LazyColumn(modifier = Modifier.testTag("list")) {
            items(100) { index ->
                Text("Item $index", modifier = Modifier.testTag("item-$index"))
            }
        }
    }
    
    // Scroll to item
    composeTestRule
        .onNodeWithTag("list")
        .performScrollToIndex(50)
    
    // Assert item is displayed
    composeTestRule
        .onNodeWithTag("item-50")
        .assertIsDisplayed()
}

@Test
fun textField_typeText_updatesValue() {
    composeTestRule.setContent {
        var text by remember { mutableStateOf("") }
        TextField(
            value = text,
            onValueChange = { text = it },
            modifier = Modifier.testTag("input")
        )
    }
    
    composeTestRule
        .onNodeWithTag("input")
        .performTextInput("Hello")
    
    composeTestRule
        .onNodeWithText("Hello")
        .assertIsDisplayed()
}
```

### 9.4 Testing Semantics

```kotlin
@Test
fun button_hasCorrectSemantics() {
    composeTestRule.setContent {
        Button(
            onClick = { },
            modifier = Modifier.semantics {
                contentDescription = "Submit button"
            }
        ) {
            Text("Submit")
        }
    }
    
    composeTestRule
        .onNodeWithContentDescription("Submit button")
        .assertIsEnabled()
        .assertHasClickAction()
}

// Adding custom semantics
@Composable
fun CustomButton(onClick: () -> Unit) {
    Button(
        onClick = onClick,
        modifier = Modifier.semantics {
            // Custom property for testing
            testTag = "custom-button"
            // Accessibility
            contentDescription = "Custom action button"
            // State
            stateDescription = "Ready to submit"
        }
    ) {
        Text("Custom")
    }
}
```

### 9.5 Testing Animations

```kotlin
@Test
fun animatedVisibility_showsAndHides() {
    composeTestRule.mainClock.autoAdvance = false
    
    composeTestRule.setContent {
        var visible by remember { mutableStateOf(false) }
        
        Column {
            Button(onClick = { visible = !visible }) {
                Text("Toggle")
            }
            AnimatedVisibility(visible) {
                Text("Animated content", modifier = Modifier.testTag("content"))
            }
        }
    }
    
    // Content not visible initially
    composeTestRule
        .onNodeWithTag("content")
        .assertDoesNotExist()
    
    // Click to show
    composeTestRule
        .onNodeWithText("Toggle")
        .performClick()
    
    // Advance animation
    composeTestRule.mainClock.advanceTimeBy(1000)
    
    // Content now visible
    composeTestRule
        .onNodeWithTag("content")
        .assertIsDisplayed()
}
```

### 9.6 Screenshot Testing

```kotlin
// Using Paparazzi or Shot library
@RunWith(PaparazziTestRunner::class)
class ScreenshotTest {
    
    @get:Rule
    val paparazzi = Paparazzi(
        deviceConfig = PIXEL_5,
        theme = "Theme.MyApp"
    )
    
    @Test
    fun profileScreen_lightTheme() {
        paparazzi.snapshot {
            ProfileScreen(
                user = testUser
            )
        }
    }
    
    @Test
    fun profileScreen_darkTheme() {
        paparazzi.snapshot {
            MyAppTheme(darkTheme = true) {
                ProfileScreen(user = testUser)
            }
        }
    }
}
```

---

## Material Design 3

### 10.1 Theme Setup

```kotlin
// Color.kt
val md_theme_light_primary = Color(0xFF6750A4)
val md_theme_light_onPrimary = Color(0xFFFFFFFF)
val md_theme_light_primaryContainer = Color(0xFFEADDFF)
// ... more colors

val md_theme_dark_primary = Color(0xFFD0BCFF)
val md_theme_dark_onPrimary = Color(0xFF381E72)
// ... more colors

// Theme.kt
@Composable
fun MyAppTheme(
    darkTheme: Boolean = isSystemInDarkTheme(),
    dynamicColor: Boolean = true, // Material You
    content: @Composable () -> Unit
) {
    val colorScheme = when {
        dynamicColor && Build.VERSION.SDK_INT >= Build.VERSION_CODES.S -> {
            val context = LocalContext.current
            if (darkTheme) dynamicDarkColorScheme(context)
            else dynamicLightColorScheme(context)
        }
        darkTheme -> darkColorScheme(
            primary = md_theme_dark_primary,
            onPrimary = md_theme_dark_onPrimary,
            // ... more colors
        )
        else -> lightColorScheme(
            primary = md_theme_light_primary,
            onPrimary = md_theme_light_onPrimary,
            // ... more colors
        )
    }
    
    MaterialTheme(
        colorScheme = colorScheme,
        typography = Typography,
        content = content
    )
}
```

### 10.2 Material Components

```kotlin
// Cards
@Composable
fun CardExamples() {
    Column(verticalArrangement = Arrangement.spacedBy(16.dp)) {
        // Elevated Card
        ElevatedCard(
            elevation = CardDefaults.cardElevation(defaultElevation = 6.dp)
        ) {
            Text("Elevated Card", modifier = Modifier.padding(16.dp))
        }
        
        // Filled Card
        Card {
            Text("Filled Card", modifier = Modifier.padding(16.dp))
        }
        
        // Outlined Card
        OutlinedCard {
            Text("Outlined Card", modifier = Modifier.padding(16.dp))
        }
    }
}

// Buttons
@Composable
fun ButtonExamples() {
    Column(verticalArrangement = Arrangement.spacedBy(8.dp)) {
        Button(onClick = { }) { Text("Filled Button") }
        
        FilledTonalButton(onClick = { }) { Text("Tonal Button") }
        
        OutlinedButton(onClick = { }) { Text("Outlined Button") }
        
        TextButton(onClick = { }) { Text("Text Button") }
        
        // FAB
        FloatingActionButton(onClick = { }) {
            Icon(Icons.Default.Add, contentDescription = "Add")
        }
        
        ExtendedFloatingActionButton(
            text = { Text("Extended FAB") },
            icon = { Icon(Icons.Default.Add, contentDescription = null) },
            onClick = { }
        )
    }
}

// Text Fields
@Composable
fun TextFieldExamples() {
    var text1 by remember { mutableStateOf("") }
    var text2 by remember { mutableStateOf("") }
    
    Column(verticalArrangement = Arrangement.spacedBy(8.dp)) {
        // Filled text field
        TextField(
            value = text1,
            onValueChange = { text1 = it },
            label = { Text("Label") },
            placeholder = { Text("Placeholder") },
            leadingIcon = { Icon(Icons.Default.Email, null) }
        )
        
        // Outlined text field
        OutlinedTextField(
            value = text2,
            onValueChange = { text2 = it },
            label = { Text("Outlined") }
        )
    }
}

// Navigation Bar
@Composable
fun NavigationBarExample() {
    var selectedItem by remember { mutableStateOf(0) }
    val items = listOf("Home", "Search", "Profile")
    val icons = listOf(Icons.Filled.Home, Icons.Filled.Search, Icons.Filled.Person)
    
    NavigationBar {
        items.forEachIndexed { index, item ->
            NavigationBarItem(
                icon = { Icon(icons[index], contentDescription = item) },
                label = { Text(item) },
                selected = selectedItem == index,
                onClick = { selectedItem = index }
            )
        }
    }
}
```

### 10.3 Top App Bars

```kotlin
@OptIn(ExperimentalMaterial3Api::class)
@Composable
fun TopAppBarExamples() {
    val scrollBehavior = TopAppBarDefaults.pinnedScrollBehavior()
    
    Scaffold(
        topBar = {
            // Small Top App Bar
            TopAppBar(
                title = { Text("Title") },
                navigationIcon = {
                    IconButton(onClick = { }) {
                        Icon(Icons.Default.ArrowBack, null)
                    }
                },
                actions = {
                    IconButton(onClick = { }) {
                        Icon(Icons.Default.Search, null)
                    }
                },
                scrollBehavior = scrollBehavior
            )
            
            // Or Center Aligned
            // CenterAlignedTopAppBar(...)
            
            // Or Medium
            // MediumTopAppBar(...)
            
            // Or Large
            // LargeTopAppBar(...)
        },
        modifier = Modifier.nestedScroll(scrollBehavior.nestedScrollConnection)
    ) { paddingValues ->
        LazyColumn(
            modifier = Modifier.padding(paddingValues)
        ) {
            items(50) {
                Text("Item $it", modifier = Modifier.padding(16.dp))
            }
        }
    }
}
```

### 10.4 Bottom Sheets

```kotlin
@OptIn(ExperimentalMaterial3Api::class)
@Composable
fun BottomSheetExample() {
    val sheetState = rememberModalBottomSheetState()
    var showBottomSheet by remember { mutableStateOf(false) }
    
    Button(onClick = { showBottomSheet = true }) {
        Text("Show Bottom Sheet")
    }
    
    if (showBottomSheet) {
        ModalBottomSheet(
            onDismissRequest = { showBottomSheet = false },
            sheetState = sheetState
        ) {
            Column(
                modifier = Modifier
                    .fillMaxWidth()
                    .padding(16.dp)
            ) {
                Text("Bottom Sheet Content")
                Spacer(modifier = Modifier.height(16.dp))
                Button(
                    onClick = { showBottomSheet = false },
                    modifier = Modifier.fillMaxWidth()
                ) {
                    Text("Close")
                }
            }
        }
    }
}
```

### 10.5 Dialogs

```kotlin
@Composable
fun DialogExamples() {
    var showAlertDialog by remember { mutableStateOf(false) }
    
    Button(onClick = { showAlertDialog = true }) {
        Text("Show Dialog")
    }
    
    if (showAlertDialog) {
        AlertDialog(
            onDismissRequest = { showAlertDialog = false },
            title = { Text("Dialog Title") },
            text = { Text("This is the dialog content") },
            confirmButton = {
                TextButton(onClick = { showAlertDialog = false }) {
                    Text("Confirm")
                }
            },
            dismissButton = {
                TextButton(onClick = { showAlertDialog = false }) {
                    Text("Cancel")
                }
            }
        )
    }
}
```

---

## Interoperability

### 11.1 Compose in Views

```kotlin
// In Activity
class MainActivity : ComponentActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)
        
        findViewById<ComposeView>(R.id.compose_view).setContent {
            MyAppTheme {
                Greeting("Android")
            }
        }
    }
}

// In XML
/*
<androidx.compose.ui.platform.ComposeView
    android:id="@+id/compose_view"
    android:layout_width="match_parent"
    android:layout_height="match_parent" />
*/
```

### 11.2 Views in Compose

```kotlin
@Composable
fun AndroidViewExample() {
    AndroidView(
        factory = { context ->
            TextView(context).apply {
                text = "Hello from View"
                textSize = 20f
            }
        },
        update = { textView ->
            textView.text = "Updated text"
        }
    )
}

// RecyclerView in Compose
@Composable
fun RecyclerViewInCompose() {
    AndroidView(
        factory = { context ->
            RecyclerView(context).apply {
                layoutManager = LinearLayoutManager(context)
                adapter = MyAdapter()
            }
        }
    )
}
```

### 11.3 Fragments

```kotlin
// Compose in Fragment
class MyFragment : Fragment() {
    override fun onCreateView(
        inflater: LayoutInflater,
        container: ViewGroup?,
        savedInstanceState: Bundle?
    ): View {
        return ComposeView(requireContext()).apply {
            setContent {
                MyAppTheme {
                    MyComposable()
                }
            }
        }
    }
}

// Fragment in Compose (not recommended)
@Composable
fun FragmentInCompose() {
    AndroidViewBinding(FragmentContainerBinding::inflate) { binding ->
        // Fragment operations
        val fragmentManager = LocalContext.current as FragmentActivity
        fragmentManager.supportFragmentManager
            .beginTransaction()
            .replace(binding.root.id, MyFragment())
            .commit()
    }
}
```

---

## Advanced Topics

### 12.1 Custom Remember

```kotlin
// Create custom remember function
@Composable
fun <T> rememberMutableStateListOf(vararg elements: T): SnapshotStateList<T> {
    return remember { mutableStateListOf(*elements) }
}

// Usage
@Composable
fun CustomRememberExample() {
    val items = rememberMutableStateListOf("Item 1", "Item 2")
    
    LazyColumn {
        items(items) { item ->
            Text(item)
        }
    }
}
```

### 12.2 Custom Modifier

```kotlin
fun Modifier.shimmer(show: Boolean): Modifier = composed {
    if (!show) return@composed this
    
    val transition = rememberInfiniteTransition()
    val translateAnim by transition.animateFloat(
        initialValue = 0f,
        targetValue = 1000f,
        animationSpec = infiniteRepeatable(
            animation = tween(durationMillis = 1200, easing = LinearEasing),
            repeatMode = RepeatMode.Restart
        )
    )
    
    background(
        Brush.horizontalGradient(
            colors = listOf(
                Color.LightGray.copy(alpha = 0.3f),
                Color.LightGray.copy(alpha = 0.5f),
                Color.LightGray.copy(alpha = 0.3f)
            ),
            startX = translateAnim - 300f,
            endX = translateAnim
        )
    )
}

// Usage
@Composable
fun ShimmerExample() {
    var isLoading by remember { mutableStateOf(true) }
    
    Box(
        modifier = Modifier
            .size(200.dp)
            .shimmer(show = isLoading)
    )
}
```

### 12.3 Canvas & Custom Drawing

```kotlin
@Composable
fun CustomDrawing() {
    Canvas(modifier = Modifier.fillMaxSize()) {
        // Draw circle
        drawCircle(
            color = Color.Blue,
            radius = 100f,
            center = Offset(200f, 200f)
        )
        
        // Draw line
        drawLine(
            color = Color.Red,
            start = Offset(0f, 0f),
            end = Offset(size.width, size.height),
            strokeWidth = 5f
        )
        
        // Draw rectangle
        drawRect(
            color = Color.Green,
            topLeft = Offset(100f, 100f),
            size = Size(200f, 150f)
        )
        
        // Draw path
        val path = Path().apply {
            moveTo(100f, 100f)
            lineTo(200f, 200f)
            lineTo(150f, 250f)
            close()
        }
        drawPath(
            path = path,
            color = Color.Magenta
        )
    }
}

// Custom chart example
@Composable
fun SimpleBarChart(values: List<Float>) {
    Canvas(modifier = Modifier.fillMaxSize()) {
        val barWidth = size.width / values.size
        val maxValue = values.maxOrNull() ?: 1f
        
        values.forEachIndexed { index, value ->
            val barHeight = (value / maxValue) * size.height
            drawRect(
                color = Color.Blue,
                topLeft = Offset(index * barWidth, size.height - barHeight),
                size = Size(barWidth * 0.8f, barHeight)
            )
        }
    }
}
```

### 12.4 Paging 3 Integration

```kotlin
@HiltViewModel
class PagingViewModel @Inject constructor(
    private val repository: ProfileRepository
) : ViewModel() {
    
    val profilesFlow: Flow<PagingData<Profile>> = Pager(
        config = PagingConfig(
            pageSize = 20,
            enablePlaceholders = false
        ),
        pagingSourceFactory = { ProfilePagingSource(repository) }
    ).flow.cachedIn(viewModelScope)
}

class ProfilePagingSource(
    private val repository: ProfileRepository
) : PagingSource<Int, Profile>() {
    
    override suspend fun load(params: LoadParams<Int>): LoadResult<Int, Profile> {
        return try {
            val page = params.key ?: 1
            val response = repository.getProfiles(page, params.loadSize)
            
            LoadResult.Page(
                data = response,
                prevKey = if (page == 1) null else page - 1,
                nextKey = if (response.isEmpty()) null else page + 1
            )
        } catch (e: Exception) {
            LoadResult.Error(e)
        }
    }
    
    override fun getRefreshKey(state: PagingState<Int, Profile>): Int? {
        return state.anchorPosition?.let { anchorPosition ->
            state.closestPageToPosition(anchorPosition)?.prevKey?.plus(1)
                ?: state.closestPageToPosition(anchorPosition)?.nextKey?.minus(1)
        }
    }
}

@Composable
fun PagingList(viewModel: PagingViewModel = hiltViewModel()) {
    val lazyPagingItems = viewModel.profilesFlow.collectAsLazyPagingItems()
    
    LazyColumn {
        items(lazyPagingItems.itemCount) { index ->
            lazyPagingItems[index]?.let { profile ->
                ProfileItem(profile)
            }
        }
        
        lazyPagingItems.apply {
            when {
                loadState.refresh is LoadState.Loading -> {
                    item { LoadingItem() }
                }
                loadState.append is LoadState.Loading -> {
                    item { LoadingItem() }
                }
                loadState.refresh is LoadState.Error -> {
                    val e = loadState.refresh as LoadState.Error
                    item { ErrorItem(e.error.message ?: "Error", onRetry = { retry() }) }
                }
                loadState.append is LoadState.Error -> {
                    val e = loadState.append as LoadState.Error
                    item { ErrorItem(e.error.message ?: "Error", onRetry = { retry() }) }
                }
            }
        }
    }
}
```

### 12.5 Side-to-Side Layouts

```kotlin
// Adaptive layout for different screen sizes
@Composable
fun AdaptiveLayout() {
    val windowSizeClass = calculateWindowSizeClass()
    
    when (windowSizeClass.widthSizeClass) {
        WindowWidthSizeClass.Compact -> {
            // Phone portrait
            CompactLayout()
        }
        WindowWidthSizeClass.Medium -> {
            // Tablet portrait or phone landscape
            MediumLayout()
        }
        WindowWidthSizeClass.Expanded -> {
            // Tablet landscape or desktop
            ExpandedLayout()
        }
    }
}

@Composable
fun ExpandedLayout() {
    Row(modifier = Modifier.fillMaxSize()) {
        // Master pane
        Box(
            modifier = Modifier
                .weight(0.4f)
                .fillMaxHeight()
        ) {
            ListPane()
        }
        
        // Detail pane
        Box(
            modifier = Modifier
                .weight(0.6f)
                .fillMaxHeight()
        ) {
            DetailPane()
        }
    }
}
```

---

## Best Practices

### 13.1 Naming Conventions

```kotlin
// Composable functions - PascalCase (like classes)
@Composable
fun UserProfile() { }

// State holder classes - lowercase with "State" suffix
data class ProfileUiState()

// Events - sealed interface with "Event" suffix
sealed interface ProfileEvent

// Effects - sealed interface with "Effect" suffix
sealed interface ProfileEffect

// ViewModels - PascalCase with "ViewModel" suffix
class ProfileViewModel : ViewModel()

// Repository - PascalCase with "Repository" suffix
interface ProfileRepository

// Use cases - PascalCase with "UseCase" suffix
class GetProfileUseCase
```

### 13.2 File Organization

```
app/
  src/main/java/
    com/example/app/
      core/
        di/
        network/
        database/
        util/
      feature/
        profile/
          data/
            ProfileRepositoryImpl.kt
            remote/
              ProfileApi.kt
              ProfileDto.kt
            local/
              ProfileEntity.kt
              ProfileDao.kt
          domain/
            ProfileRepository.kt
            model/
              Profile.kt
            usecase/
              GetProfileUseCase.kt
          presentation/
            ProfileScreen.kt
            ProfileViewModel.kt
            ProfileUiState.kt
            ProfileEvent.kt
            ProfileEffect.kt
            components/
              ProfileCard.kt
              ProfileHeader.kt
```

### 13.3 Common Patterns

```kotlin
// 1. State hoisting
@Composable
fun Parent() {
    var text by remember { mutableStateOf("") }
    Child(text = text, onTextChange = { text = it })
}

@Composable
fun Child(
    text: String,
    onTextChange: (String) -> Unit
) {
    TextField(value = text, onValueChange = onTextChange)
}

// 2. Slot APIs
@Composable
fun CustomCard(
    header: @Composable () -> Unit,
    content: @Composable () -> Unit,
    footer: @Composable () -> Unit
) {
    Card {
        Column {
            header()
            content()
            footer()
        }
    }
}

// 3. Remember factories
@Composable
fun rememberCustomState(): CustomState {
    return remember {
        CustomState()
    }
}

// 4. ViewModel with proper scoping
@Composable
fun MyScreen(
    viewModel: MyViewModel = hiltViewModel()
) {
    val state by viewModel.state.collectAsStateWithLifecycle()
    MyScreenContent(state = state, onEvent = viewModel::onEvent)
}

@Composable
private fun MyScreenContent(
    state: MyUiState,
    onEvent: (MyEvent) -> Unit
) {
    // Stateless UI
}
```

---

## Common Pitfalls

### 14.1 Avoid These Mistakes

```kotlin
// ❌ DON'T: Create new instances in composition
@Composable
fun BadExample() {
    val viewModel = MyViewModel() // New instance on every recomposition!
}

// ✅ DO: Use viewModel() or hiltViewModel()
@Composable
fun GoodExample() {
    val viewModel: MyViewModel = hiltViewModel()
}

// ❌ DON'T: Perform side effects directly in composable
@Composable
fun BadSideEffect() {
    database.insert(user) // Called on every recomposition!
}

// ✅ DO: Use side effect handlers
@Composable
fun GoodSideEffect() {
    LaunchedEffect(user) {
        database.insert(user)
    }
}

// ❌ DON'T: Use mutable state without remember
@Composable
fun BadState() {
    var count = 0 // Reset on every recomposition!
    Button(onClick = { count++ }) {
        Text("Count: $count")
    }
}

// ✅ DO: Use remember
@Composable
fun GoodState() {
    var count by remember { mutableStateOf(0) }
    Button(onClick = { count++ }) {
        Text("Count: $count")
    }
}

// ❌ DON'T: Update state during composition
@Composable
fun BadStateUpdate(someCondition: Boolean) {
    var state by remember { mutableStateOf(false) }
    if (someCondition) {
        state = true // Can cause infinite recomposition!
    }
}

// ✅ DO: Update state in event handlers or side effects
@Composable
fun GoodStateUpdate(someCondition: Boolean) {
    var state by remember { mutableStateOf(false) }
    LaunchedEffect(someCondition) {
        if (someCondition) {
            state = true
        }
    }
}

// ❌ DON'T: Use GlobalScope
LaunchedEffect(Unit) {
    GlobalScope.launch { } // Lives beyond composable lifecycle!
}

// ✅ DO: Use provided coroutine scope
LaunchedEffect(Unit) {
    // This scope is tied to composition lifecycle
}

// ❌ DON'T: Forget keys in lists
items(items) { item ->
    ItemRow(item) // Can cause animation issues
}

// ✅ DO: Provide stable keys
items(items, key = { it.id }) { item ->
    ItemRow(item)
}
```

### 14.2 Performance Anti-patterns

```kotlin
// ❌ DON'T: Read state unnecessarily
@Composable
fun BadPerformance(state: UiState) {
    println(state.toString()) // Reads all properties!
    Text(state.title) // Only need title
}

// ✅ DO: Read only what you need
@Composable
fun GoodPerformance(title: String) {
    Text(title)
}

// ❌ DON'T: Create unstable lambdas in loops
items(items) { item ->
    Button(onClick = { handleClick(item) }) { } // New lambda each item!
}

// ✅ DO: Use stable references
items(items, key = { it.id }) { item ->
    val onClick = remember(item.id) { { handleClick(item.id) } }
    Button(onClick = onClick) { }
}
```

---

## Quick Reference Cheat Sheet

```kotlin
// State
remember { }              // Survives recomposition
rememberSaveable { }      // Survives config changes
derivedStateOf { }        // Computed state
produceState { }          // Async to State
snapshotFlow { }          // State to Flow

// Side Effects
LaunchedEffect(key) { }   // Launch coroutine
DisposableEffect(key) { } // Cleanup
SideEffect { }            // Every recomposition
rememberCoroutineScope()  // Manual coroutine launch
rememberUpdatedState()    // Latest value in effect

// Layouts
Column { }                // Vertical
Row { }                   // Horizontal
Box { }                   // Layered
LazyColumn { }            // Vertical list
LazyRow { }               // Horizontal list
LazyVerticalGrid { }      // Grid

// Animations
animate*AsState()         // Simple animation
Animatable()              // Imperative animation
updateTransition()        // State-based animation
AnimatedVisibility()      // Show/hide animation
AnimatedContent()         // Content change animation

// Modifiers (order matters!)
.size()
.padding()
.background()
.clickable()
.fillMaxSize()
.weight()
.align()

// Testing
composeTestRule.onNodeWithText()
.performClick()
.assertIsDisplayed()
.assertTextEquals()
```

---

## Resources

### Official Documentation
- [Jetpack Compose Docs](https://developer.android.com/jetpack/compose)
- [Compose Samples](https://github.com/android/compose-samples)
- [Now in Android](https://github.com/android/nowinandroid)

### Learning Resources
- [Thinking in Compose](https://developer.android.com/jetpack/compose/mental-model)
- [Compose Performance](https://developer.android.com/jetpack/compose/performance)
- [Compose Testing](https://developer.android.com/jetpack/compose/testing)

### Tools
- Layout Inspector
- Composition Tracing
- Recomposition Counter
- Baseline Profile Generator

---

**Last Updated**: 2025
**Compose Version**: 1.6+
**Material3 Version**: 1.2+

---

Good luck with your senior Android interviews! 🚀

