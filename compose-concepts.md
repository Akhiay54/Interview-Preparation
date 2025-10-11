# 🧠 Jetpack Compose - Concepts & Understanding Guide

> **This guide focuses on UNDERSTANDING, not just code. Read this BEFORE the code examples.**

---

## Table of Contents
1. [Core Philosophy](#core-philosophy)
2. [Mental Models](#mental-models)
3. [Understanding State](#understanding-state)
4. [Recomposition Deep Dive](#recomposition-deep-dive)
5. [Side Effects Explained](#side-effects-explained)
6. [Performance Concepts](#performance-concepts)
7. [Architecture Thinking](#architecture-thinking)
8. [Interview Questions with Explanations](#interview-questions-with-explanations)

---

## Core Philosophy

### What Problem Does Compose Solve?

**Traditional Android (XML + Views):**
```
Problem: You manage WHEN to update UI
- findViewById()
- Manually update each view
- Easy to have inconsistent state
- Hard to track what changed
```

**Compose Approach:**
```
Solution: You describe WHAT UI should look like
- UI = f(state)
- Give me state, I'll give you UI
- State changes → Compose figures out what to update
- Single source of truth
```

### The Declarative Paradigm

**Imperative (Old way):**
```
"Computer, do these steps:"
1. Find the button
2. Change its text
3. Change its color
4. Move it 10px down
```

**Declarative (Compose):**
```
"Computer, the button should look like this:"
Button(
    text = if (isLoggedIn) "Logout" else "Login",
    color = if (isError) Red else Blue
)

Computer: "OK, I'll figure out what changed and update it"
```

### Why This Matters

**Interview Question:** "Why did Google create Compose?"

**Answer:**
1. **Less bugs** - No manual synchronization between state and UI
2. **Easier to reason about** - Just describe final state
3. **Less code** - No findViewById, no XML
4. **Better tooling** - Live previews, type safety
5. **Modern patterns** - Built for Kotlin, coroutines, Flow

---

## Mental Models

### 1. Composable Functions Are Data, Not Views

**Wrong mental model:**
```
"Composable functions create Views/Widgets"
```

**Correct mental model:**
```
"Composable functions describe UI structure"
They emit data that Compose runtime uses to build actual UI
```

**What Actually Happens:**

```
Your Code:
  @Composable fun MyButton() { Button(...) }
         ↓
Compose Compiler:
  Transforms into optimized code
         ↓
Composition:
  Creates a tree of "nodes" (not Views yet!)
         ↓
Layout:
  Measures and positions nodes
         ↓
Drawing:
  Converts to actual pixels on screen
```

### 2. Recomposition = Re-running Function

**Key Insight:** When state changes, Compose **re-runs your function**

```kotlin
@Composable
fun Counter() {
    var count by remember { mutableStateOf(0) }
    
    println("This runs EVERY recomposition") // <-- Mind blown!
    
    Button(onClick = { count++ }) {
        Text("Count: $count")
    }
}
```

**What happens:**
1. First run: count = 0, UI shows "Count: 0"
2. Button clicked: count becomes 1
3. **Function runs AGAIN** (recomposition): UI shows "Count: 1"
4. Button clicked: count becomes 2
5. **Function runs AGAIN**: UI shows "Count: 2"

**This is why:**
- ❌ Don't create ViewModels in composables (new instance each time!)
- ❌ Don't perform side effects directly (runs multiple times!)
- ✅ Use `remember` for expensive calculations
- ✅ Use `LaunchedEffect` for side effects

### 3. The Tree Structure

```
Compose builds a tree:

App
├─ Screen
│  ├─ TopBar
│  └─ Content
│     ├─ Header
│     └─ List
│        ├─ Item1
│        ├─ Item2
│        └─ Item3
```

**Key Points:**
- Only changed parts recompose (not entire tree)
- Data flows DOWN the tree (parent → child)
- Events flow UP the tree (child → parent)

---

## Understanding State

### What IS State?

**State = Any value that can change over time and affects UI**

Examples:
- User input in TextField
- Network request result
- Selected tab
- Scroll position
- "Is loading" flag

### The Golden Rule

```
UI = f(state)

If state doesn't change → UI doesn't change
If UI changes → state must have changed
```

### State vs Local Variables

```kotlin
@Composable
fun Example1() {
    var count = 0  // ❌ Regular variable
    Button(onClick = { count++ }) {
        Text("Count: $count") // Always shows 0!
    }
}
// Why? count resets to 0 on every recomposition

@Composable
fun Example2() {
    var count by remember { mutableStateOf(0) } // ✅ State
    Button(onClick = { count++ }) {
        Text("Count: $count") // Updates correctly!
    }
}
// Why? remember keeps value across recompositions
```

### Types of State and When to Use Each

#### 1. **remember** - Survives recomposition only

**Use when:**
- UI state that doesn't need to survive rotation
- Temporary state (animation progress, scroll position)
- Derived calculations

**Example scenario:** Expanded/collapsed card state

```
Phone is upright → Card expanded → Rotate phone → Card resets to collapsed
This is EXPECTED behavior (user rotated = new context)
```

#### 2. **rememberSaveable** - Survives configuration changes

**Use when:**
- Form input (user's typed text)
- Selection state
- Navigation state
- Anything user expects to persist after rotation

**Example scenario:** Text in search box

```
User types "pizza" → Rotate phone → Still shows "pizza"
This is EXPECTED (user would be frustrated if text disappeared)
```

#### 3. **ViewModel + StateFlow** - Survives activity recreation

**Use when:**
- Business logic state
- Network data
- Database queries
- Complex state management

**Example scenario:** User profile data

```
Load user data → Rotate phone → Don't re-fetch (already have it)
Navigate away → Come back → Still have it (ViewModel alive)
```

### State Hoisting - The Most Important Pattern

**Problem:** Where should state live?

**Answer:** Hoist it to the lowest common ancestor

```
Parent needs to know about it?
  └─ Yes → State lives in Parent
       └─ Child receives as parameter
  └─ No → State lives in Child
       └─ Child manages it internally
```

**Example:**

```
HomeScreen (needs to know selected tab)
  ↓
  state: selectedTab = 0
  ↓
TabBar (receives selectedTab, reports changes)
  onTabClick → tells parent
  ↓
Content (shows content based on selectedTab)
```

**Why hoist?**
1. **Single source of truth** - One place holds the truth
2. **Testability** - Can test UI without state
3. **Reusability** - Child composable is "dumb" and reusable
4. **Shareable** - Multiple children can use same state

### derivedStateOf - The Optimization Hero

**Problem:**

```kotlin
val listState = rememberLazyListState()
val showButton = listState.firstVisibleItemIndex > 0

// Problem: firstVisibleItemIndex changes CONSTANTLY while scrolling
// Your composable recomposes HUNDREDS of times
```

**Solution:**

```kotlin
val showButton by remember {
    derivedStateOf {
        listState.firstVisibleItemIndex > 0
    }
}

// Only recomposes when result changes (true/false)
// Not when firstVisibleItemIndex changes from 45 → 46
```

**When to use:**
- Derived from frequently changing state
- Result changes less often than input
- Want to throttle recompositions

---

## Recomposition Deep Dive

### What Triggers Recomposition?

**Only ONE thing:** State read during composition changes

```kotlin
@Composable
fun MyComposable(user: User) {
    println(user.name)  // Reads user.name
    // If user.name changes → Recompose
    // If user.age changes → NO recompose (we don't read it)
}
```

### Smart Recomposition

Compose is smart about recomposition:

```kotlin
@Composable
fun Parent() {
    var count by remember { mutableStateOf(0) }
    
    Column {
        Text("Static text")        // Never recomposes
        Text("Count: $count")      // Recomposes when count changes
        StaticChild()              // Never recomposes
        DynamicChild(count)        // Recomposes when count changes
    }
}
```

**Key insight:** Only composables that read the changed state recompose

### Skipping Recomposition

**Compose can SKIP recomposition if:**

1. **All parameters are stable**
2. **All parameters are equal to last time**

**Stable types:**
- Primitives (Int, String, Boolean, etc.)
- Immutable data classes (all val, no vars)
- @Stable annotated classes
- @Immutable annotated classes

**Example:**

```kotlin
data class User(val name: String, val age: Int) // Stable

@Composable
fun UserCard(user: User) {
    Text(user.name)
}

// Parent recomposes but user didn't change?
// → UserCard skips recomposition! 🎉
```

### Unstable Types That Kill Performance

```kotlin
// ❌ UNSTABLE - has 'var'
data class User(var name: String)

// ❌ UNSTABLE - MutableList is mutable
data class State(val items: MutableList<Item>)

// ❌ UNSTABLE - lambda is recreated each time
items(list) { item ->
    ItemRow(item, onClick = { handleClick(item) })
}

// ✅ STABLE - all vals
data class User(val name: String)

// ✅ STABLE - List is immutable
data class State(val items: List<Item>)

// ✅ STABLE - remember the lambda
items(list) { item ->
    val onClick = remember(item.id) { { handleClick(item.id) } }
    ItemRow(item, onClick)
}
```

### Why This Matters

**Interview Question:** "How do you optimize Compose performance?"

**Answer:**
1. Use immutable data classes
2. Hoist state properly
3. Use remember for expensive calculations
4. Use derivedStateOf for derived state
5. Use keys in LazyColumn
6. Extract composables to skip unnecessary recomposition

---

## Side Effects Explained

### What's a Side Effect?

**Side Effect = Something that happens OUTSIDE of composition**

Examples:
- Network call
- Database write
- Logging
- Navigation
- Showing toast
- Starting animation

### Why Do We Need Special Handling?

**Problem:**

```kotlin
@Composable
fun BadExample(userId: String) {
    val user = repository.loadUser(userId) // ❌ DISASTER!
    // This runs EVERY recomposition
    // Could load user 100 times!
}
```

**Solution:**

```kotlin
@Composable
fun GoodExample(userId: String) {
    var user by remember { mutableStateOf<User?>(null) }
    
    LaunchedEffect(userId) { // ✅ Runs once per userId
        user = repository.loadUser(userId)
    }
    
    user?.let { UserDisplay(it) }
}
```

### The Side Effect Decision Tree

```
I need to do something...

Is it a suspend function?
├─ YES → Need coroutine scope
│  │
│  ├─ Should start when composable appears?
│  │  └─ YES → LaunchedEffect
│  │
│  └─ Should start from click/callback?
│     └─ YES → rememberCoroutineScope
│
└─ NO → Is it a one-time action?
   ├─ YES → SideEffect
   └─ NO → Need cleanup?
      └─ YES → DisposableEffect
```

### LaunchedEffect - "Do This When Component Appears"

**Mental Model:** "When this composable appears or when key changes, launch this coroutine"

**Use cases:**
- Load data when screen appears
- Start animation
- Collect Flow
- Subscribe to updates

**Key insight:** Coroutine is cancelled when:
- Composable leaves composition (screen navigates away)
- Key changes (userId changes)

### DisposableEffect - "Setup and Cleanup"

**Mental Model:** "When this appears, set up something. When it disappears or key changes, clean up."

**Use cases:**
- Register/unregister listeners
- Add/remove observers
- Start/stop location updates
- Subscribe/unsubscribe

**Pattern:**
```
Enter composition → onDispose block is created
Leave composition → onDispose block is called
Key changes → onDispose called, then effect runs again
```

### rememberCoroutineScope - "Let Me Control When"

**Mental Model:** "Give me a scope so I can launch coroutines from callbacks"

**Use cases:**
- Launch from button click
- Launch from gesture
- Manual animation control

**Key difference from LaunchedEffect:**
- LaunchedEffect: Automatic (runs when key changes)
- rememberCoroutineScope: Manual (you call launch)

### SideEffect - "Sync Compose State to Non-Compose"

**Mental Model:** "Every time this composable successfully recomposes, run this"

**Use cases:**
- Update analytics
- Sync to external state management
- Update non-Compose code

**Rare!** You probably don't need this

### rememberUpdatedState - "Give Me Latest Version"

**Problem:**

```kotlin
@Composable
fun Timer(onTimeout: () -> Unit) {
    LaunchedEffect(Unit) { // Only runs once
        delay(5000)
        onTimeout() // Calls OLD version if onTimeout changed!
    }
}
```

**Solution:**

```kotlin
@Composable
fun Timer(onTimeout: () -> Unit) {
    val currentOnTimeout by rememberUpdatedState(onTimeout)
    
    LaunchedEffect(Unit) {
        delay(5000)
        currentOnTimeout() // Always calls LATEST version
    }
}
```

**When to use:**
- Long-running effects that shouldn't restart
- But need latest callback/value

---

## Performance Concepts

### The Three Phases (Must Understand for Interviews!)

Every frame in Compose goes through 3 phases:

#### Phase 1: Composition
```
What: Decide what UI to show
When: When state changes
Cost: Running your @Composable functions
```

**Example:**
```kotlin
@Composable
fun MyScreen() {
    // This code runs in COMPOSITION phase
    val state by viewModel.state.collectAsState()
    if (state.isLoading) {
        LoadingSpinner()
    } else {
        Content(state.data)
    }
}
```

#### Phase 2: Layout
```
What: Measure and position UI elements
When: After composition OR when size changes
Cost: Measuring each composable
```

**Example:**
```kotlin
Column {  // Column measures children
    Text("Hello")  // Text measures itself
    Image(...)     // Image measures itself
}
// Column positions children based on measurements
```

#### Phase 3: Drawing
```
What: Render pixels to screen
When: After layout OR when visual properties change
Cost: Drawing operations (Canvas calls)
```

**Example:**
```kotlin
Box(
    modifier = Modifier
        .background(Color.Red)  // Drawing phase
        .drawBehind { /* ... */ }  // Drawing phase
)
```

### Phase Optimization Strategy

**Best to worst (performance):**

1. **Drawing only** - Change color/alpha
   ```kotlin
   val color by animateColorAsState(...)
   Box(modifier = Modifier.background(color))
   // Only redraws, no composition/layout
   ```

2. **Layout only** - Change position/size
   ```kotlin
   val offset by animateOffsetAsState(...)
   Box(modifier = Modifier.offset(offset))
   // Skips composition, but needs layout
   ```

3. **Composition** - Change structure
   ```kotlin
   if (showMore) {
       ExtraContent()  // Adds new composables
   }
   // Needs all three phases
   ```

**Interview Tip:** "I'd use graphicsLayer for animations because it only affects drawing phase"

### Modifier Order Matters!

**This is HUGE for interviews:**

```kotlin
// Order matters because modifiers apply sequentially

Box(
    Modifier
        .size(100.dp)           // 1. Size = 100x100
        .padding(10.dp)         // 2. Add 10dp padding (now 80x80 content area)
        .background(Color.Blue) // 3. Blue background on 80x80
)

Box(
    Modifier
        .background(Color.Blue) // 1. Blue background on ??? size
        .padding(10.dp)         // 2. Add 10dp padding
        .size(100.dp)           // 3. Size = 100x100
)
// Different result!
```

**Mental model:** Each modifier wraps the previous one like nested boxes

### LazyColumn Performance

**Keys are critical:**

```kotlin
// ❌ Bad - Compose can't track items
items(userList) { user ->
    UserCard(user)
}
// Problem: Delete item 2 → Compose recomposes ALL items below it

// ✅ Good - Stable identity
items(userList, key = { it.id }) { user ->
    UserCard(user)
}
// Delete item 2 → Only item 2 is removed, others stay
```

**Why keys matter:**
1. **Recomposition** - Compose knows which items changed
2. **Animations** - Items can animate to new positions
3. **State preservation** - Item state survives reordering

---

## Architecture Thinking

### Why MVI (Model-View-Intent) with Compose?

**Old MVVM:**
```
View ←→ ViewModel
- Two-way data binding
- Hard to test
- Hard to debug (who changed what?)
```

**MVI with Compose:**
```
View → Events → ViewModel
        ↓
    Updates State
        ↓
View ← State Flow ← ViewModel

- Unidirectional data flow
- Easy to debug (events = log)
- Single source of truth
- Time-travel debugging possible
```

### The Three Pieces

#### 1. State - Represents UI

```kotlin
data class ProfileUiState(
    val user: User? = null,
    val isLoading: Boolean = false,
    val error: String? = null
)

// Why single state object?
// - Atomic updates (never inconsistent)
// - Easy to test (just create state)
// - Easy to reason about (one thing)
```

#### 2. Events - User Actions

```kotlin
sealed interface ProfileEvent {
    object Refresh : ProfileEvent
    data class OnUserClick(val id: String) : ProfileEvent
}

// Why sealed interface?
// - Exhaustive when statement
// - Type-safe
// - Can't create invalid events
```

#### 3. Effects - One-Time Side Effects

```kotlin
sealed interface ProfileEffect {
    data class NavigateTo(val route: String) : ProfileEffect
    data class ShowToast(val message: String) : ProfileEffect
}

// Why separate from state?
// - Navigation shouldn't be in state (it's an action)
// - Toast shouldn't be in state (it's one-time)
// - Clear separation of concerns
```

### The Flow

```
User clicks button
    ↓
Event sent to ViewModel
    ↓
ViewModel processes event
    ↓
ViewModel updates State (loading = true)
    ↓
UI recomposes (shows loading)
    ↓
ViewModel makes API call
    ↓
ViewModel updates State (user = data, loading = false)
    ↓
UI recomposes (shows data)
```

### Why This Architecture?

**Interview Question:** "Why use MVI instead of MVVM in Compose?"

**Answer:**
1. **Predictability** - Every state change is explicit
2. **Debuggable** - Log events to see what happened
3. **Testable** - Test by sending events, checking state
4. **Compose-friendly** - State flows naturally to UI
5. **Time-travel** - Can replay events for debugging

---

## Interview Questions with Explanations

### Q1: "Explain the difference between remember and rememberSaveable"

**Answer:**

"Both keep values across recompositions, but differ in scope:

**remember:**
- Survives recomposition (state change)
- Lost on configuration change (rotation)
- Faster (no serialization)
- Use for: UI state, animations, temporary data

**rememberSaveable:**
- Survives recomposition AND configuration changes
- Uses Bundle (same as onSaveInstanceState)
- Slower (serialization overhead)
- Use for: User input, selections, important UI state

**Example:** Search box text should use rememberSaveable (user expects it after rotation), but animation progress can use remember (OK to reset)."

### Q2: "What causes recomposition and how do you optimize it?"

**Answer:**

"Recomposition is triggered when state that was READ during composition changes.

**Optimization strategies:**

1. **Use immutable data** - Compose can skip if parameters equal
2. **derivedStateOf** - Reduce recomposition frequency
3. **Hoist state** - Only components that need it recompose
4. **remember calculations** - Don't recalculate each time
5. **Stable lambdas** - Use remember or pass stable references
6. **Keys in lists** - Help Compose track items

**Red flag:** If you see every composable recomposing, likely using mutable state or unstable types."

### Q3: "When would you use LaunchedEffect vs DisposableEffect?"

**Answer:**

"Decision based on what you need:

**LaunchedEffect:**
- Need to run suspending code
- When composable appears or key changes
- Examples: API call, collect Flow, delay
- Automatically cancels coroutine on removal

**DisposableEffect:**
- Need to setup AND cleanup resources
- Non-suspending work
- Examples: register listener, add observer
- Must call onDispose for cleanup

**Key insight:** LaunchedEffect IS a DisposableEffect internally - it's just specialized for coroutines."

### Q4: "Explain the three phases of Compose"

**Answer:**

"Every frame has three sequential phases:

**Composition:**
- Runs @Composable functions
- Builds UI tree structure
- Decides WHAT to show
- Triggered by: State changes

**Layout:**
- Measures each element
- Positions elements
- Decides WHERE to place things
- Triggered by: Composition changes OR size changes

**Drawing:**
- Renders to screen
- Draws actual pixels
- Decides HOW to render
- Triggered by: Layout changes OR visual property changes

**Performance tip:** Aim to skip composition/layout phases when possible. Use graphicsLayer for animations - it only affects drawing."

### Q5: "What is state hoisting and why is it important?"

**Answer:**

"State hoisting = Moving state to the lowest common ancestor that needs it.

**Why it matters:**

1. **Single source of truth** - One place knows the truth
2. **Shareable** - Multiple children can use same state
3. **Testable** - Stateless composables are pure functions
4. **Reusable** - Stateless components work anywhere
5. **Predictable** - Data flows down, events flow up

**Pattern:**
```
Component with state is 'stateful'
Component receiving state as parameter is 'stateless'
```

Aim for as many stateless components as possible."

### Q6: "How do you share data between composables?"

**Answer:**

"Four main approaches:

1. **Parameter passing** - Direct, for parent-child
2. **Hoist to common ancestor** - For siblings
3. **ViewModel scope** - Share ViewModel between destinations
4. **CompositionLocal** - For cross-cutting concerns (theme, etc.)

**Never use:**
- Singletons (kills testability)
- Global state (race conditions)
- Finding composables (no findViewById equivalent)

**Best practice:** Start with parameter passing, only escalate if needed."

### Q7: "Explain Smart Recomposition"

**Answer:**

"Compose intelligently decides which composables to recompose:

**Skip if:**
- Parameters haven't changed (equals check)
- Parameters are stable types
- No state reads in the composable

**Example:**
```
Parent state changes → Parent recomposes
Child parameters unchanged → Child SKIPS recomposition
```

**Why it's smart:**
Compose tracks what state each composable reads. Only recomposes if THAT specific state changes.

**Performance impact:** Can skip 80-90% of recompositions in optimized apps."

### Q8: "What are stable types and why do they matter?"

**Answer:**

"Stable type = Type where Compose can detect changes reliably.

**Stable if:**
- Equals result for two instances never changes
- Public properties don't change
- Changes to public properties are notified

**Automatically stable:**
- Primitives (Int, String, Boolean)
- Immutable data classes (all val)
- Functions

**Unstable:**
- Mutable data classes (any var)
- MutableList, MutableSet
- Classes without @Stable/@Immutable

**Why it matters:**
Unstable parameters → Compose can't skip recomposition → Performance suffers

**Fix:** Use immutable collections (List vs MutableList) or @Stable annotation."

### Q9: "How do you test Compose UI?"

**Answer:**

"Three levels:

**1. UI Tests (ComposeTestRule):**
```
- Test user interactions
- Verify UI state
- Check accessibility
```

**2. ViewModel Tests:**
```
- Test business logic
- Verify state updates
- Check effects emission
```

**3. Screenshot Tests:**
```
- Visual regression
- Multiple configurations
- Design validation
```

**Key insight:** Separate stateful and stateless composables. Test stateless ones with different states, test ViewModels separately."

### Q10: "What's the difference between Modifier.offset and Modifier.graphicsLayer?"

**Answer:**

"Both move content, but different phases:

**Modifier.offset:**
- Affects LAYOUT phase
- Changes actual position
- Other elements adjust
- Causes layout recalculation

**Modifier.graphicsLayer:**
- Affects DRAWING phase only
- Visual offset (like transform)
- Other elements stay in place
- Much faster for animations

**Rule of thumb:**
Use graphicsLayer for animations, offset for actual layout changes.

**Interview tip:** This shows you understand the three phases!"

---

## Understanding Through Analogies

### Compose is Like React

If you know React:

```
React                  →  Compose
-----                     -------
Components             →  @Composable functions
useState               →  remember { mutableStateOf() }
useEffect              →  LaunchedEffect
useRef                 →  remember
useMemo                →  remember or derivedStateOf
useContext             →  CompositionLocal
Props                  →  Parameters
State management       →  ViewModel + StateFlow
JSX                    →  Kotlin DSL
```

### Compose is Like Building Blocks

**Think of it like LEGO:**

```
@Composable function = LEGO piece blueprint
remember = Keeping pieces between rebuilds
State = Instructions that change
Recomposition = Rebuilding with new instructions
Modifier = Decorating/transforming pieces
Layout = Arranging pieces in space
```

### State Flow is Like a River

```
ViewModel (source) → StateFlow → Compose (consumer)

River flows one direction
Multiple observers can drink from river
Source controls what flows
Observers just watch (read-only)
```

---

## Common Misconceptions

### ❌ "Recomposition is slow"

**Truth:** Recomposition is extremely fast. Compose is designed for frequent recomposition.

**Caveat:** UNNECESSARY recomposition wastes resources. Optimize by making composables skippable.

### ❌ "You should minimize recomposition"

**Truth:** You should minimize UNNECESSARY recomposition. Some recomposition is expected and fine.

**Focus on:** Making recomposition cheap when it happens.

### ❌ "Compose creates new Views on recomposition"

**Truth:** Compose reuses underlying Views. Only updates what changed.

**Reality:** Much more efficient than it seems.

### ❌ "LaunchedEffect runs every recomposition"

**Truth:** LaunchedEffect only runs when keys change or on first composition.

**Why confusing:** The code is inside composable that recomposes, but effect doesn't rerun.

### ❌ "ViewModel is tied to View in Compose"

**Truth:** ViewModel is lifecycle-aware, tied to Activity/Fragment/NavBackStackEntry.

**Compose advantage:** Can share ViewModel between multiple composables easily.

---

## Key Takeaways for Interviews

### Must Know Cold:

1. **UI = f(state)** - Core principle
2. **Recomposition** - What triggers it, how to optimize
3. **State hoisting** - Single source of truth
4. **Side effects** - When to use which one
5. **Three phases** - Composition, Layout, Drawing
6. **Stability** - What makes types stable
7. **remember vs rememberSaveable** - When to use each

### Show Deep Understanding:

1. Explain WHY, not just WHAT
2. Compare trade-offs (X vs Y depends on...)
3. Give real examples from your project
4. Mention performance implications
5. Discuss testing approach

### Red Flags to Avoid:

1. ❌ "I just use rememberSaveable everywhere" (Shows no understanding)
2. ❌ "Recomposition is bad" (Misunderstanding core concept)
3. ❌ "I put all state in ViewModel" (No understanding of hoisting)
4. ❌ Can't explain when to use LaunchedEffect vs DisposableEffect
5. ❌ No mention of immutability/stability

---

## Study Strategy

### Week 1: Core Concepts
- [ ] Understand declarative paradigm
- [ ] Master state (remember, rememberSaveable, derivedStateOf)
- [ ] Understand recomposition deeply
- [ ] Practice state hoisting

### Week 2: Advanced Topics
- [ ] Master all side effects
- [ ] Understand three phases
- [ ] Learn stability/immutability
- [ ] Practice MVI architecture

### Week 3: Practice
- [ ] Build small projects using concepts
- [ ] Optimize existing code
- [ ] Write tests
- [ ] Explain concepts to someone else

### Interview Prep:
- [ ] Can explain every concept without code
- [ ] Can give real-world examples
- [ ] Know trade-offs and alternatives
- [ ] Prepared for "why" questions

---

## Further Reading

### Must Read (Official):
1. Thinking in Compose
2. Managing State
3. Side Effects in Compose
4. Compose Phases
5. Performance Best Practices

### Recommended:
1. Compose Internals (Jorge Castillo)
2. Now in Android source code
3. Compose compiler deep dive
4. State management comparison article

---

**Remember:** Understanding > Memorizing

Focus on the WHY behind decisions. Interviewers want to know you can think, not just copy code.

Good luck! 🚀

