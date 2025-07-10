# Android Developer Interview Questions - Complete Guide
## Kotlin, Compose, CMP (Compose Multiplatform), KMP (Kotlin Multiplatform)

---

## Table of Contents
1. [Kotlin Fundamentals](#kotlin-fundamentals)
2. [Kotlin Advanced](#kotlin-advanced)
3. [Android Core](#android-core)
4. [Android Architecture](#android-architecture)
5. [Jetpack Compose](#jetpack-compose)
6. [Compose Advanced](#compose-advanced)
7. [Kotlin Multiplatform (KMP)](#kotlin-multiplatform-kmp)
8. [Compose Multiplatform (CMP)](#compose-multiplatform-cmp)
9. [Testing](#testing)
10. [Performance & Optimization](#performance--optimization)
11. [Security](#security)
12. [System Design](#system-design)
13. [Behavioral Questions](#behavioral-questions)
14. [Coding Challenges](#coding-challenges)
15. [Advanced Topics](#advanced-topics)
16. [Interview Tips and Strategies](#interview-tips-and-strategies)
17. [Additional Resources](#additional-resources)

---

## Kotlin Fundamentals

### Basic Concepts
1. **What is Kotlin and why was it created?**
2. **Explain the difference between `val` and `var`**
3. **What is null safety in Kotlin? Explain `?`, `!!`, `?.`, and `?:`**
4. **What are data classes and what methods do they auto-generate?**
5. **Explain the difference between `==` and `===` in Kotlin**
6. **What is string interpolation and how is it used?**
7. **Explain when expressions vs if expressions**
8. **What are sealed classes and when would you use them?**
9. **Explain the difference between `object`, `class`, and `data class`**
10. **What is type inference in Kotlin?**

### Functions and Lambdas
11. **What are higher-order functions?**
12. **Explain lambda expressions and their syntax**
13. **What is the difference between lambda and anonymous functions?**
14. **What are extension functions and how do you create them?**
15. **Explain function types and function literals**
16. **What is the `it` keyword in lambdas?**
17. **How do you handle multiple parameters in lambdas?**
18. **What are inline functions and when should you use them?**
19. **Explain `noinline` and `crossinline` keywords**
20. **What is function composition?**

### Collections
21. **Explain the difference between List, MutableList, and ArrayList**
22. **What are the main collection types in Kotlin?**
23. **Explain `map`, `filter`, `reduce`, and `fold` operations**
24. **What is the difference between `forEach` and `map`?**
25. **How do you create immutable collections?**
26. **Explain `flatMap` and when to use it**
27. **What are sequences and how do they differ from collections?**
28. **Explain lazy evaluation in Kotlin sequences**
29. **How do you sort collections in Kotlin?**
30. **What is the difference between `groupBy` and `partition`?**

### Object-Oriented Programming
31. **Explain inheritance in Kotlin**
32. **What is the `open` keyword?**
33. **How do you override methods and properties?**
34. **What are abstract classes vs interfaces?**
35. **Explain delegation in Kotlin**
36. **What is the `by` keyword used for?**
37. **How do you implement multiple interfaces?**
38. **What are companion objects?**
39. **Explain visibility modifiers in Kotlin**
40. **What is the difference between `internal` and `protected`?**

---

## Kotlin Advanced

### Generics and Variance
41. **What are generics in Kotlin?**
42. **Explain variance: `in`, `out`, and `*` (star projection)**
43. **What is type erasure?**
44. **How do you create generic functions?**
45. **What are reified type parameters?**
46. **Explain PECS (Producer Extends Consumer Super) principle**
47. **What is contravariance and covariance?**
48. **How do you handle generic constraints?**
49. **What are higher-kinded types?**
50. **Explain type aliases and their use cases**

### Coroutines
51. **What are Kotlin coroutines?**
52. **Explain the difference between `suspend` functions and regular functions**
53. **What is a CoroutineScope?**
54. **Explain different coroutine dispatchers (Main, IO, Default, Unconfined)**
55. **What is the difference between `launch` and `async`?**
56. **How do you handle exceptions in coroutines?**
57. **What is structured concurrency?**
58. **Explain `withContext` and when to use it**
59. **What are coroutine builders?**
60. **How do you cancel coroutines?**
61. **What is `SupervisorJob` and when to use it?**
62. **Explain flow in Kotlin coroutines**
63. **What is the difference between hot and cold flows?**
64. **How do you handle backpressure in flows?**
65. **What are flow operators (`map`, `filter`, `collect`, etc.)?**
66. **Explain `StateFlow` and `SharedFlow`**
67. **What is `Channel` in coroutines?**
68. **How do you test coroutines?**
69. **What is coroutine context and how do you combine contexts?**
70. **Explain coroutine cancellation and cooperation**

### Advanced Features
71. **What are delegated properties?**
72. **Explain `lazy` delegation**
73. **What is `lateinit` and how is it different from nullable types?**
74. **How do you create custom delegated properties?**
75. **What are property delegates (`by` keyword)?**
76. **Explain reflection in Kotlin**
77. **What are annotations and how do you create custom ones?**
78. **How do you use Kotlin with Java (interoperability)?**
79. **What is the `@JvmStatic` annotation?**
80. **Explain Kotlin's type system (Nothing, Unit, Any)**

---

## Android Core

### Components and Lifecycle
81. **Explain the Android application components**
82. **What is the Activity lifecycle?**
83. **Describe Fragment lifecycle**
84. **What is the difference between Activity and Fragment?**
85. **How do you handle configuration changes?**
86. **What is `onSaveInstanceState` and `onRestoreInstanceState`?**
87. **Explain Service types (Started, Bound, Foreground)**
88. **What is BroadcastReceiver and its types?**
89. **How do you handle implicit vs explicit intents?**
90. **What is ContentProvider and when to use it?**

### Views and Layouts
91. **Explain different layout types (Linear, Relative, Constraint, etc.)**
92. **What is the difference between `match_parent` and `wrap_content`?**
93. **How do you optimize layout performance?**
94. **What is ViewBinding and how is it different from findViewById?**
95. **Explain DataBinding and its benefits**
96. **What is the difference between ViewBinding and DataBinding?**
97. **How do you handle custom views?**
98. **What is the View drawing process (measure, layout, draw)?**
99. **Explain RecyclerView and its components**
100. **What is the difference between RecyclerView and ListView?**

### Data Storage
101. **What are the different data storage options in Android?**
102. **Explain SharedPreferences and its use cases**
103. **What is SQLite and how do you use it in Android?**
104. **What is Room database and its components?**
105. **Explain Room entities, DAOs, and database**
106. **What are Room migrations?**
107. **How do you handle database transactions in Room?**
108. **What is the difference between internal and external storage?**
109. **How do you handle file operations in Android?**
110. **What is DataStore and how is it different from SharedPreferences?**

### Networking
111. **How do you make network requests in Android?**
112. **What is Retrofit and how do you use it?**
113. **Explain OkHttp and its features**
114. **How do you handle JSON parsing (Gson, Moshi, etc.)?**
115. **What is the difference between synchronous and asynchronous requests?**
116. **How do you handle network errors and retries?**
117. **What is caching in networking?**
118. **How do you implement authentication in network requests?**
119. **What is certificate pinning?**
120. **How do you handle different network states?**

---

## Android Architecture

### Architecture Patterns
121. **What is MVC, MVP, and MVVM?**
122. **Explain the benefits of MVVM architecture**
123. **What is Clean Architecture?**
124. **How do you implement Repository pattern?**
125. **What is the difference between Model and Entity?**
126. **Explain the role of Use Cases in Clean Architecture**
127. **What is dependency injection and why is it important?**
128. **How do you implement Dependency Injection manually?**
129. **What is Dagger and how does it work?**
130. **Explain Hilt and its benefits over Dagger**

### Android Architecture Components
131. **What is ViewModel and its lifecycle?**
132. **How do you share data between ViewModels?**
133. **What is LiveData and its benefits?**
134. **Explain the difference between LiveData and ObservableField**
135. **What is the Observer pattern in Android?**
136. **How do you handle LiveData transformations?**
137. **What is MediatorLiveData?**
138. **Explain Navigation Component and its benefits**
139. **What is WorkManager and when to use it?**
140. **How do you handle background processing?**

### State Management
141. **How do you manage state in Android applications?**
142. **What is the difference between local and global state?**
143. **How do you handle configuration changes with state?**
144. **What is the role of SavedStateHandle?**
145. **How do you implement state persistence?**
146. **What are the best practices for state management?**
147. **How do you handle complex state scenarios?**
148. **What is the difference between stateful and stateless components?**
149. **How do you debug state-related issues?**
150. **What is state hoisting?**

---

## Jetpack Compose

### Compose Fundamentals
151. **What is Jetpack Compose?**
152. **How is Compose different from the traditional View system?**
153. **What is declarative UI?**
154. **Explain the concept of Composable functions**
155. **What is the `@Composable` annotation?**
156. **How do you create a simple Compose UI?**
157. **What is the Compose compiler?**
158. **Explain the Compose runtime**
159. **What is recomposition in Compose?**
160. **How do you handle state in Compose?**

### State Management in Compose
161. **What is `remember` and when to use it?**
162. **Explain `mutableStateOf` and `State`**
163. **What is the difference between `by` and `=` when using state?**
164. **How do you lift state up in Compose?**
165. **What is `rememberSaveable`?**
166. **Explain `derivedStateOf` and its use cases**
167. **What is `LaunchedEffect` and when to use it?**
168. **How do you handle side effects in Compose?**
169. **What is `DisposableEffect`?**
170. **Explain `SideEffect` and its purpose**

### Compose UI Components
171. **How do you create custom Composables?**
172. **What are the basic layout Composables (Column, Row, Box)?**
173. **How do you handle modifiers in Compose?**
174. **What is the modifier chain and how does it work?**
175. **How do you create responsive layouts in Compose?**
176. **What is `LazyColumn` and `LazyRow`?**
177. **How do you handle lists in Compose?**
178. **What is the difference between `LazyColumn` and `Column`?**
179. **How do you implement custom layouts in Compose?**
180. **What is `ConstraintLayout` in Compose?**

### Compose Theming and Styling
181. **How do you implement theming in Compose?**
182. **What is Material Design in Compose?**
183. **How do you create custom themes?**
184. **What are color schemes in Compose?**
185. **How do you handle dark mode in Compose?**
186. **What is typography in Compose theming?**
187. **How do you create custom shapes?**
188. **What is the elevation system in Compose?**
189. **How do you handle animations in Compose?**
190. **What are the different animation APIs in Compose?**

---

## Compose Advanced

### Performance and Optimization
191. **How do you optimize Compose performance?**
192. **What causes unnecessary recomposition?**
193. **How do you debug recomposition issues?**
194. **What is the Layout Inspector for Compose?**
195. **How do you use `remember` effectively?**
196. **What is the difference between `remember` and `rememberSaveable`?**
197. **How do you handle expensive operations in Compose?**
198. **What is the Compose Compiler Metrics?**
199. **How do you optimize list performance in Compose?**
200. **What are the best practices for Compose performance?**

### Advanced Compose Concepts
201. **What is CompositionLocal and when to use it?**
202. **How do you create custom CompositionLocal?**
203. **What is the Compose phase (Composition, Layout, Drawing)?**
204. **How do you handle focus in Compose?**
205. **What is semantic tree in Compose?**
206. **How do you implement accessibility in Compose?**
207. **What is the difference between `Modifier.clickable` and `Modifier.pointerInput`?**
208. **How do you handle gesture detection in Compose?**
209. **What is `Canvas` in Compose and how to use it?**
210. **How do you create custom drawing in Compose?**

### Navigation and Architecture
211. **How do you implement navigation in Compose?**
212. **What is the Navigation Compose library?**
213. **How do you pass data between screens in Compose?**
214. **What is the difference between `navigate` and `popUpTo`?**
215. **How do you handle deep linking in Compose?**
216. **What is the ViewModel integration with Compose?**
217. **How do you handle dependency injection in Compose?**
218. **What is the recommended architecture for Compose apps?**
219. **How do you test Compose UIs?**
220. **What is the Compose testing framework?**

---

## Kotlin Multiplatform (KMP)

### KMP Fundamentals
221. **What is Kotlin Multiplatform?**
222. **What are the benefits of using KMP?**
223. **How does KMP differ from other cross-platform solutions?**
224. **What is the expect/actual mechanism?**
225. **How do you set up a KMP project?**
226. **What are the different KMP targets?**
227. **How do you share code between platforms?**
228. **What is the common source set?**
229. **How do you handle platform-specific implementations?**
230. **What is the KMP project structure?**

### KMP Architecture
231. **How do you design architecture for KMP projects?**
232. **What is the recommended way to structure KMP modules?**
233. **How do you handle dependency injection in KMP?**
234. **What is the role of interfaces in KMP?**
235. **How do you implement repository pattern in KMP?**
236. **What are the best practices for KMP architecture?**
237. **How do you handle platform-specific dependencies?**
238. **What is the difference between shared and platform modules?**
239. **How do you manage build configurations in KMP?**
240. **What is the Kotlin/Native memory model?**

### KMP Libraries and Tools
241. **What are the popular KMP libraries?**
242. **How do you use Ktor in KMP projects?**
243. **What is SQLDelight and how to use it in KMP?**
244. **How do you handle serialization in KMP?**
245. **What is the KMP coroutines support?**
246. **How do you implement logging in KMP?**
247. **What is the DateTime library for KMP?**
248. **How do you handle image loading in KMP?**
249. **What is the KMP testing approach?**
250. **How do you debug KMP applications?**

---

## Compose Multiplatform (CMP)

### CMP Fundamentals
251. **What is Compose Multiplatform?**
252. **How does CMP extend Jetpack Compose?**
253. **What platforms does CMP support?**
254. **How do you set up a CMP project?**
255. **What is the difference between CMP and Flutter?**
256. **How do you share UI code across platforms?**
257. **What are the limitations of CMP?**
258. **How do you handle platform-specific UI in CMP?**
259. **What is the CMP project structure?**
260. **How do you build and deploy CMP applications?**

### CMP Development
261. **How do you create platform-specific Composables?**
262. **What is the expect/actual pattern in CMP?**
263. **How do you handle resources in CMP?**
264. **What is the CMP resource system?**
265. **How do you implement navigation in CMP?**
266. **What are the CMP-specific modifiers?**
267. **How do you handle window management in CMP?**
268. **What is the CMP theming approach?**
269. **How do you handle different screen sizes in CMP?**
270. **What is the CMP accessibility support?**

### CMP Advanced Topics
271. **How do you optimize CMP performance?**
272. **What are the CMP-specific performance considerations?**
273. **How do you handle platform-specific APIs in CMP?**
274. **What is the CMP interop with native code?**
275. **How do you test CMP applications?**
276. **What is the CMP debugging approach?**
277. **How do you handle state management in CMP?**
278. **What are the CMP deployment strategies?**
279. **How do you handle updates in CMP apps?**
280. **What is the future roadmap of CMP?**

---

## Testing

### Unit Testing
281. **What is unit testing and why is it important?**
282. **How do you write unit tests in Kotlin?**
283. **What is JUnit and how to use it?**
284. **What is the difference between JUnit 4 and JUnit 5?**
285. **How do you test coroutines?**
286. **What is MockK and how to use it?**
287. **How do you mock dependencies in tests?**
288. **What is the difference between mocking and stubbing?**
289. **How do you test ViewModels?**
290. **What is the TestCoroutineDispatcher?**

### Integration Testing
291. **What is integration testing in Android?**
292. **How do you test Room databases?**
293. **What is the in-memory database testing?**
294. **How do you test network calls?**
295. **What is WireMock and how to use it?**
296. **How do you test repositories?**
297. **What is the difference between unit and integration tests?**
298. **How do you test use cases?**
299. **What is the test pyramid concept?**
300. **How do you handle test data?**

### UI Testing
301. **What is UI testing in Android?**
302. **How do you write Espresso tests?**
303. **What is the difference between Espresso and UI Automator?**
304. **How do you test Compose UIs?**
305. **What is the Compose testing framework?**
306. **How do you test user interactions?**
307. **What is the Page Object Model in testing?**
308. **How do you handle asynchronous operations in UI tests?**
309. **What is the Robot pattern in testing?**
310. **How do you test accessibility features?**

---

## Performance & Optimization

### Memory Management
311. **How does memory management work in Android?**
312. **What is garbage collection in Android?**
313. **How do you detect memory leaks?**
314. **What is LeakCanary and how to use it?**
315. **What are the common causes of memory leaks?**
316. **How do you optimize memory usage?**
317. **What is the difference between heap and stack memory?**
318. **How do you handle large bitmaps?**
319. **What is memory profiling?**
320. **How do you use the Memory Profiler?**

### Performance Optimization
321. **How do you optimize app startup time?**
322. **What is the App Startup library?**
323. **How do you optimize UI rendering?**
324. **What is overdraw and how to fix it?**
325. **How do you optimize RecyclerView performance?**
326. **What is the difference between `notifyDataSetChanged` and specific notify methods?**
327. **How do you implement lazy loading?**
328. **What is image optimization in Android?**
329. **How do you handle large datasets?**
330. **What is the role of background processing?**

### Profiling and Debugging
331. **How do you use Android Profiler?**
332. **What is the CPU Profiler?**
333. **How do you analyze method traces?**
334. **What is the Network Profiler?**
335. **How do you debug performance issues?**
336. **What is systrace and how to use it?**
337. **How do you optimize battery usage?**
338. **What is Doze mode and App Standby?**
339. **How do you handle background execution limits?**
340. **What is the Battery Historian tool?**

---

## Security

### Android Security
341. **What are the Android security features?**
342. **How does the Android permission system work?**
343. **What is the difference between normal and dangerous permissions?**
344. **How do you handle runtime permissions?**
345. **What is the Android Keystore?**
346. **How do you implement biometric authentication?**
347. **What is certificate pinning?**
348. **How do you secure network communications?**
349. **What is ProGuard and R8?**
350. **How do you obfuscate code?**

### Data Security
351. **How do you encrypt data in Android?**
352. **What is the EncryptedSharedPreferences?**
353. **How do you handle sensitive data?**
354. **What is the Android Security Provider?**
355. **How do you implement secure storage?**
356. **What is the difference between symmetric and asymmetric encryption?**
357. **How do you handle API keys securely?**
358. **What is the NDK security considerations?**
359. **How do you prevent reverse engineering?**
360. **What is root detection?**

---

## System Design

### Mobile System Design
361. **How do you design a chat application?**
362. **Design a social media feed**
363. **How would you design a photo sharing app?**
364. **Design an offline-first application**
365. **How do you handle real-time updates?**
366. **Design a location-based service**
367. **How would you design a video streaming app?**
368. **Design a payment processing system**
369. **How do you handle push notifications at scale?**
370. **Design a caching strategy for mobile apps**

### Scalability and Architecture
371. **How do you design for scalability?**
372. **What is microservices architecture?**
373. **How do you handle API versioning?**
374. **What is the role of CDN in mobile apps?**
375. **How do you design for offline functionality?**
376. **What is eventual consistency?**
377. **How do you handle data synchronization?**
378. **What is the CAP theorem?**
379. **How do you design distributed systems?**
380. **What is load balancing?**

---

## Behavioral Questions

### Experience and Background
381. **Tell me about your Android development experience**
382. **What's your favorite Android project you've worked on?**
383. **How do you stay updated with Android development?**
384. **What's the most challenging bug you've fixed?**
385. **How do you approach learning new technologies?**
386. **Tell me about a time you had to refactor legacy code**
387. **How do you handle conflicting requirements?**
388. **Describe a time you had to work with a difficult team member**
389. **How do you prioritize tasks in a project?**
390. **Tell me about a time you missed a deadline**

### Technical Decision Making
391. **How do you choose between different architectural patterns?**
392. **When would you use Compose vs traditional Views?**
393. **How do you decide on third-party libraries?**
394. **What factors do you consider for performance optimization?**
395. **How do you handle technical debt?**
396. **When would you choose KMP over native development?**
397. **How do you approach code reviews?**
398. **What's your testing strategy?**
399. **How do you handle breaking changes in dependencies?**
400. **How do you ensure code quality?**

---

## Coding Challenges

### Kotlin Specific
401. **Implement a custom delegated property**
402. **Write a function that uses coroutines to fetch data from multiple sources**
403. **Create a sealed class hierarchy for API responses**
404. **Implement a generic repository pattern**
405. **Write a higher-order function that retries failed operations**
406. **Create a custom scope function**
407. **Implement a thread-safe singleton**
408. **Write a function that transforms a list using various operations**
409. **Create a custom annotation processor**
410. **Implement a state machine using sealed classes**

### Android Specific
411. **Implement a custom View that draws a pie chart**
412. **Create a RecyclerView adapter with multiple view types**
413. **Implement a custom ViewPager transformation**
414. **Write a background service that processes data**
415. **Create a custom broadcast receiver**
416. **Implement a content provider**
417. **Write a custom animation**
418. **Create a custom layout manager**
419. **Implement a gesture detector**
420. **Write a custom drawable**

### Compose Challenges
421. **Create a custom Composable that mimics a specific UI design**
422. **Implement a custom layout in Compose**
423. **Write a Composable that handles complex state**
424. **Create a custom animation in Compose**
425. **Implement a drag-and-drop interface**
426. **Write a custom modifier**
427. **Create a reusable component library**
428. **Implement a custom theme system**
429. **Write a performance-optimized list**
430. **Create a custom gesture handler**

### Algorithm and Data Structure
431. **Implement LRU cache for image loading**
432. **Write a function to detect memory leaks**
433. **Implement a binary search for sorted list**
434. **Create a thread-safe queue**
435. **Write a function to merge sorted lists**
436. **Implement a trie for autocomplete**
437. **Create a custom hash map**
438. **Write a function to find the longest common subsequence**
439. **Implement a graph traversal algorithm**
440. **Create a custom priority queue**

---

## Advanced Topics

### Kotlin Compiler and Internals
441. **How does the Kotlin compiler work?**
442. **What is the Kotlin IR backend?**
443. **How does type inference work in Kotlin?**
444. **What is the Kotlin metadata?**
445. **How does the Kotlin/JVM interop work?**
446. **What is the Kotlin compiler plugin system?**
447. **How do you create custom compiler plugins?**
448. **What is the Kotlin Symbol Processing (KSP)?**
449. **How does annotation processing work in Kotlin?**
450. **What is the difference between KAPT and KSP?**

### Android Internals
451. **How does the Android runtime work?**
452. **What is the Android Zygote process?**
453. **How does the Android app launching process work?**
454. **What is the Android Binder mechanism?**
455. **How does the Android memory management work?**
456. **What is the Android Graphics Pipeline?**
457. **How does the Android input system work?**
458. **What is the Android HAL (Hardware Abstraction Layer)?**
459. **How does the Android boot process work?**
460. **What is the Android Framework layer?**

### Emerging Technologies
461. **What is Android 14's new features?**
462. **How does Predictive Back Gesture work?**
463. **What is the new Photo Picker API?**
464. **How do you implement Per-app language preferences?**
465. **What is the Themed App Icons feature?**
466. **How does the new Privacy Dashboard work?**
467. **What is the Android Runtime (ART) improvements?**
468. **How do you use the new Material You theming?**
469. **What is the new Splash Screen API?**
470. **How does the new App Bundles work?**

---

## Interview Tips and Strategies

### Preparation Strategy
- **Study the fundamentals thoroughly**
- **Practice coding problems daily**
- **Build sample projects showcasing different concepts**
- **Stay updated with latest Android/Kotlin developments**
- **Review your past projects and be ready to discuss them**

### During the Interview
- **Ask clarifying questions**
- **Think out loud while solving problems**
- **Start with a simple solution, then optimize**
- **Discuss trade-offs and alternatives**
- **Be honest about what you don't know**

### Common Mistakes to Avoid
- **Not asking questions about requirements**
- **Jumping to code without planning**
- **Ignoring edge cases**
- **Not considering performance implications**
- **Failing to test your solution**

---

## Additional Resources

### Books
- "Kotlin in Action" by Dmitry Jemerov
- "Android Programming: The Big Nerd Ranch Guide"
- "Effective Java" by Joshua Bloch
- "Clean Code" by Robert C. Martin
- "Design Patterns" by Gang of Four

### Online Resources
- Official Android Developer Documentation
- Kotlin Documentation
- Android Developers Blog
- Kotlin Blog
- Stack Overflow Android/Kotlin tags

### Practice Platforms
- LeetCode
- HackerRank
- CodeWars
- GeeksforGeeks
- Pramp (for mock interviews)

---

*This comprehensive guide covers the vast majority of questions you might encounter in Android developer interviews. Focus on understanding the concepts rather than memorizing answers, and always be prepared to write code to demonstrate your knowledge.* 