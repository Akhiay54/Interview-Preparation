# 🏗️ System Design Interview Guide
*Comprehensive guide for Android developers transitioning to system design*

---

## 📋 Table of Contents

1. [System Design Fundamentals](#system-design-fundamentals)
2. [Mobile-First System Design](#mobile-first-system-design)
3. [Scalability Patterns](#scalability-patterns)
4. [Database Design](#database-design)
5. [Caching Strategies](#caching-strategies)
6. [Load Balancing](#load-balancing)
7. [Microservices Architecture](#microservices-architecture)
8. [Real-time Systems](#real-time-systems)
9. [Security & Authentication](#security--authentication)
10. [Performance Optimization](#performance-optimization)
11. [Common System Design Questions](#common-system-design-questions)
12. [Mobile App System Design](#mobile-app-system-design)
13. [Interview Framework](#interview-framework)

---

## 🎯 System Design Fundamentals

### What is System Design?

System design is the process of defining the architecture, components, modules, interfaces, and data for a system to satisfy specified requirements.

**Key Goals:**
- **Scalability**: Handle growth in users/data
- **Reliability**: System works correctly under failures
- **Availability**: System is accessible when needed
- **Performance**: Fast response times
- **Maintainability**: Easy to modify and extend

### System Design Interview Process

1. **Clarify Requirements** (5-10 minutes)
2. **Estimate Scale** (2-3 minutes)
3. **High-Level Design** (10-15 minutes)
4. **Detailed Design** (15-20 minutes)
5. **Identify Bottlenecks** (5-10 minutes)
6. **Optimize** (5-10 minutes)

### Requirements Gathering Framework

**Functional Requirements:**
- What features does the system need?
- What are the core use cases?
- What are the user workflows?

**Non-Functional Requirements:**
- **Scalability**: How many users/data?
- **Performance**: Response time requirements?
- **Availability**: Uptime requirements?
- **Consistency**: Data consistency needs?
- **Durability**: Data persistence requirements?

---

## 📱 Mobile-First System Design

### Mobile App Architecture Considerations

**Client-Server Communication:**
```kotlin
// Example: Offline-first architecture
class UserRepository @Inject constructor(
    private val api: UserApi,
    private val database: UserDatabase,
    private val networkManager: NetworkManager
) {
    suspend fun getUser(id: String): Flow<User> = flow {
        // 1. Emit cached data immediately
        database.getUser(id)?.let { emit(it) }
        
        // 2. Fetch fresh data if online
        if (networkManager.isOnline()) {
            try {
                val user = api.getUser(id)
                database.insertUser(user)
                emit(user)
            } catch (e: Exception) {
                // Handle error gracefully
            }
        }
    }
}
```

**Key Mobile Considerations:**
- **Offline Support**: Sync when connection restored
- **Battery Optimization**: Minimize network calls
- **Data Usage**: Compress payloads
- **Push Notifications**: Real-time updates
- **App Updates**: Seamless deployment

### Mobile-Specific Challenges

**Network Connectivity:**
```kotlin
class NetworkAwareRepository {
    private val connectivityManager = context.getSystemService(Context.CONNECTIVITY_SERVICE) as ConnectivityManager
    
    fun isOnline(): Boolean {
        val network = connectivityManager.activeNetwork
        val capabilities = connectivityManager.getNetworkCapabilities(network)
        return capabilities?.hasCapability(NetworkCapabilities.NET_CAPABILITY_INTERNET) == true
    }
    
    suspend fun syncData() {
        if (isOnline()) {
            // Sync pending changes
            val pendingChanges = database.getPendingChanges()
            pendingChanges.forEach { change ->
                api.syncChange(change)
                database.markSynced(change.id)
            }
        }
    }
}
```

**Battery Optimization:**
- Batch network requests
- Use WorkManager for background tasks
- Implement exponential backoff
- Cache aggressively

---

## 📈 Scalability Patterns

### Horizontal vs Vertical Scaling

**Vertical Scaling (Scale Up):**
- Add more resources to existing server
- CPU, RAM, Storage
- **Pros**: Simple, immediate
- **Cons**: Limited, expensive, single point of failure

**Horizontal Scaling (Scale Out):**
- Add more servers
- **Pros**: Unlimited, cost-effective, fault-tolerant
- **Cons**: Complex, requires load balancing

### Load Balancing Strategies

```kotlin
// Example: Client-side load balancing
class LoadBalancer {
    private val servers = listOf(
        "api1.example.com",
        "api2.example.com", 
        "api3.example.com"
    )
    
    fun getNextServer(): String {
        // Round-robin, least connections, or weighted
        return servers[Random.nextInt(servers.size)]
    }
}
```

**Load Balancing Algorithms:**
- **Round Robin**: Distribute requests sequentially
- **Least Connections**: Send to server with fewest active connections
- **Weighted Round Robin**: Assign different weights to servers
- **IP Hash**: Consistent server for same client IP

### Auto Scaling

```kotlin
// Example: Auto-scaling based on CPU usage
class AutoScaler {
    fun shouldScaleUp(cpuUsage: Double): Boolean {
        return cpuUsage > 70.0
    }
    
    fun shouldScaleDown(cpuUsage: Double): Boolean {
        return cpuUsage < 30.0
    }
    
    suspend fun scaleUp() {
        // Provision new server
        val newServer = provisionServer()
        loadBalancer.addServer(newServer)
    }
}
```

---

## 🗄️ Database Design

### Database Types & Use Cases

**Relational Databases (SQL):**
- **Use Cases**: ACID transactions, complex queries, data integrity
- **Examples**: PostgreSQL, MySQL, SQLite
- **Mobile**: Room (Android), Core Data (iOS)

**NoSQL Databases:**
- **Document**: MongoDB, CouchDB
- **Key-Value**: Redis, DynamoDB
- **Column-Family**: Cassandra, HBase
- **Graph**: Neo4j, Amazon Neptune

### Database Sharding

```kotlin
// Example: User data sharding by user ID
class ShardedUserRepository {
    private val shards = mapOf(
        0 to "db-shard-0",
        1 to "db-shard-1", 
        2 to "db-shard-2"
    )
    
    fun getShard(userId: String): String {
        val hash = userId.hashCode()
        val shardIndex = abs(hash) % shards.size
        return shards[shardIndex]!!
    }
    
    suspend fun getUser(userId: String): User? {
        val shard = getShard(userId)
        return databaseManager.getConnection(shard).getUser(userId)
    }
}
```

**Sharding Strategies:**
- **Hash-based**: Distribute by hash of key
- **Range-based**: Distribute by key ranges
- **Directory-based**: Lookup table for shard mapping

### Database Replication

**Master-Slave Replication:**
```kotlin
class ReplicatedDatabase {
    private val master: Database
    private val slaves: List<Database>
    
    suspend fun write(data: Any) {
        // Write to master
        master.write(data)
        // Replicate to slaves asynchronously
        slaves.forEach { slave ->
            launch { slave.write(data) }
        }
    }
    
    suspend fun read(): Any {
        // Read from any slave (load balancing)
        return slaves.random().read()
    }
}
```

---

## ⚡ Caching Strategies

### Cache Types & Placement

**Client-Side Caching:**
```kotlin
// Android: Room + Retrofit caching
@Database(entities = [User::class], version = 1)
abstract class AppDatabase : RoomDatabase() {
    abstract fun userDao(): UserDao
}

// HTTP caching with OkHttp
val client = OkHttpClient.Builder()
    .cache(Cache(File(cacheDir, "http_cache"), 10 * 1024 * 1024))
    .build()
```

**Server-Side Caching:**
- **Application Cache**: In-memory cache (Redis, Memcached)
- **Database Cache**: Query result caching
- **CDN**: Static content delivery

### Cache Patterns

**Cache-Aside (Lazy Loading):**
```kotlin
class CachedUserService {
    private val cache = ConcurrentHashMap<String, User>()
    
    suspend fun getUser(id: String): User? {
        // 1. Check cache first
        cache[id]?.let { return it }
        
        // 2. Fetch from database
        val user = database.getUser(id)
        
        // 3. Store in cache
        user?.let { cache[id] = it }
        
        return user
    }
}
```

**Write-Through:**
```kotlin
class WriteThroughCache {
    suspend fun updateUser(user: User) {
        // Update cache and database simultaneously
        cache.put(user.id, user)
        database.updateUser(user)
    }
}
```

**Write-Behind:**
```kotlin
class WriteBehindCache {
    private val writeQueue = ConcurrentLinkedQueue<User>()
    
    suspend fun updateUser(user: User) {
        // Update cache immediately
        cache.put(user.id, user)
        // Queue for database update
        writeQueue.offer(user)
    }
    
    // Background job to flush queue
    private suspend fun flushQueue() {
        while (writeQueue.isNotEmpty()) {
            val user = writeQueue.poll()
            database.updateUser(user)
        }
    }
}
```

---

## 🔄 Microservices Architecture

### Service Decomposition

**Domain-Driven Design:**
```kotlin
// Example: E-commerce microservices
// User Service
class UserService {
    suspend fun createUser(user: User): User
    suspend fun getUser(id: String): User?
    suspend fun updateProfile(id: String, profile: Profile): User
}

// Order Service  
class OrderService {
    suspend fun createOrder(order: Order): Order
    suspend fun getOrder(id: String): Order?
    suspend fun updateOrderStatus(id: String, status: OrderStatus): Order
}

// Payment Service
class PaymentService {
    suspend fun processPayment(payment: Payment): PaymentResult
    suspend fun refundPayment(paymentId: String): RefundResult
}
```

**Communication Patterns:**
- **Synchronous**: REST APIs, gRPC
- **Asynchronous**: Message queues (Kafka, RabbitMQ)
- **Event-Driven**: Event sourcing, CQRS

### Service Discovery

```kotlin
class ServiceRegistry {
    private val services = ConcurrentHashMap<String, ServiceInstance>()
    
    fun register(service: ServiceInstance) {
        services[service.name] = service
    }
    
    fun discover(serviceName: String): ServiceInstance? {
        return services[serviceName]
    }
    
    fun healthCheck() {
        services.values.forEach { service ->
            if (!service.isHealthy()) {
                services.remove(service.name)
            }
        }
    }
}
```

---

## ⚡ Real-time Systems

### WebSocket Implementation

```kotlin
// Android WebSocket client
class WebSocketManager {
    private var webSocket: WebSocket? = null
    
    fun connect(url: String) {
        val request = Request.Builder().url(url).build()
        webSocket = client.newWebSocket(request, object : WebSocketListener() {
            override fun onMessage(webSocket: WebSocket, text: String) {
                // Handle incoming message
                handleMessage(text)
            }
            
            override fun onFailure(webSocket: WebSocket, t: Throwable, response: Response?) {
                // Handle connection failure
                reconnect()
            }
        })
    }
    
    fun sendMessage(message: String) {
        webSocket?.send(message)
    }
    
    private fun reconnect() {
        // Implement exponential backoff
        delay(1000)
        connect(url)
    }
}
```

### Server-Sent Events (SSE)

```kotlin
// Android SSE client
class SSEManager {
    fun connect(url: String) {
        val request = Request.Builder().url(url).build()
        client.newCall(request).enqueue(object : Callback {
            override fun onResponse(call: Call, response: Response) {
                response.body?.source()?.let { source ->
                    while (true) {
                        val line = source.readUtf8Line()
                        if (line?.startsWith("data: ") == true) {
                            val data = line.substring(6)
                            handleEvent(data)
                        }
                    }
                }
            }
        })
    }
}
```

---

## 🔐 Security & Authentication

### Authentication Patterns

**JWT (JSON Web Tokens):**
```kotlin
class JWTManager {
    fun createToken(user: User): String {
        val claims = mapOf(
            "userId" to user.id,
            "email" to user.email,
            "exp" to (System.currentTimeMillis() / 1000 + 3600) // 1 hour
        )
        return JWT.create()
            .withPayload(claims)
            .sign(Algorithm.HMAC256(secret))
    }
    
    fun validateToken(token: String): User? {
        return try {
            val decodedJWT = JWT.decode(token)
            val userId = decodedJWT.getClaim("userId").asString()
            userRepository.getUser(userId)
        } catch (e: Exception) {
            null
        }
    }
}
```

**OAuth 2.0:**
```kotlin
class OAuthManager {
    suspend fun authenticate(code: String): AuthResult {
        val tokenResponse = tokenApi.exchangeCodeForToken(code)
        val userInfo = userApi.getUserInfo(tokenResponse.accessToken)
        return AuthResult(tokenResponse, userInfo)
    }
}
```

### Security Best Practices

**Input Validation:**
```kotlin
class InputValidator {
    fun validateEmail(email: String): Boolean {
        return android.util.Patterns.EMAIL_ADDRESS.matcher(email).matches()
    }
    
    fun sanitizeInput(input: String): String {
        return input.replace(Regex("[<>\"']"), "")
    }
}
```

**Rate Limiting:**
```kotlin
class RateLimiter {
    private val requests = ConcurrentHashMap<String, MutableList<Long>>()
    
    fun isAllowed(userId: String, limit: Int, windowMs: Long): Boolean {
        val now = System.currentTimeMillis()
        val userRequests = requests.getOrPut(userId) { mutableListOf() }
        
        // Remove old requests
        userRequests.removeAll { it < now - windowMs }
        
        // Check limit
        if (userRequests.size >= limit) {
            return false
        }
        
        // Add current request
        userRequests.add(now)
        return true
    }
}
```

---

## ⚡ Performance Optimization

### Database Optimization

**Indexing:**
```sql
-- Example: User table indexes
CREATE INDEX idx_user_email ON users(email);
CREATE INDEX idx_user_created_at ON users(created_at);
CREATE INDEX idx_user_status ON users(status);
```

**Query Optimization:**
```kotlin
// Bad: N+1 query problem
suspend fun getUsersWithPosts(): List<UserWithPosts> {
    val users = userDao.getAllUsers()
    return users.map { user ->
        val posts = postDao.getPostsByUserId(user.id)
        UserWithPosts(user, posts)
    }
}

// Good: Single query with JOIN
suspend fun getUsersWithPosts(): List<UserWithPosts> {
    return userDao.getUsersWithPosts()
}
```

### CDN Implementation

```kotlin
class CDNManager {
    private val cdnUrl = "https://cdn.example.com"
    
    fun getImageUrl(imageId: String, size: String): String {
        return "$cdnUrl/images/$imageId/$size.jpg"
    }
    
    fun getVideoUrl(videoId: String, quality: String): String {
        return "$cdnUrl/videos/$videoId/$quality.mp4"
    }
}
```

---

## 🎯 Common System Design Questions

### 1. Design a URL Shortener

**Requirements:**
- Shorten long URLs
- Redirect to original URL
- Track click analytics
- Handle high traffic

**High-Level Design:**
```
Client → Load Balancer → Web Servers → Cache → Database
```

**Key Components:**
- **URL Generation**: Hash-based or counter-based
- **Database**: Store mappings (short → long URL)
- **Cache**: Redis for fast lookups
- **Analytics**: Track clicks, referrers

**Scale Estimation:**
- 100M URLs per day
- 10:1 read/write ratio
- 5 years retention

### 2. Design a Chat Application

**Requirements:**
- Real-time messaging
- Group chats
- Message history
- Online status

**Architecture:**
```
Mobile App → WebSocket → Message Service → Database
                ↓
            Notification Service → Push Service
```

**Components:**
- **WebSocket**: Real-time communication
- **Message Service**: Handle message routing
- **Database**: Store messages and user data
- **Push Service**: Offline notifications

### 3. Design a Social Media Feed

**Requirements:**
- Personalized feed
- Real-time updates
- Handle millions of users
- Support multiple content types

**Architecture:**
```
User → Feed Service → Content Service → Database
                ↓
            Recommendation Engine
```

**Key Considerations:**
- **Feed Generation**: Pre-computed vs real-time
- **Content Storage**: Media files on CDN
- **Caching**: User feeds in cache
- **Scaling**: Shard by user ID

---

## 📱 Mobile App System Design

### Offline-First Architecture

```kotlin
class OfflineFirstApp {
    // Local database for offline data
    private val localDatabase: AppDatabase
    
    // Sync service for background sync
    private val syncService: SyncService
    
    // Conflict resolution
    private val conflictResolver: ConflictResolver
    
    suspend fun createPost(post: Post) {
        // 1. Save locally first
        localDatabase.insertPost(post)
        
        // 2. Queue for sync
        syncService.queueSync(SyncOperation.CreatePost(post))
        
        // 3. Update UI immediately
        updateUI()
    }
    
    suspend fun syncData() {
        val pendingOperations = syncService.getPendingOperations()
        pendingOperations.forEach { operation ->
            try {
                when (operation) {
                    is SyncOperation.CreatePost -> {
                        val response = api.createPost(operation.post)
                        localDatabase.updatePost(response)
                    }
                    // Handle other operations
                }
            } catch (e: Exception) {
                // Handle sync failure
                syncService.retryLater(operation)
            }
        }
    }
}
```

### Push Notification System

```kotlin
class PushNotificationManager {
    private val firebaseMessaging = FirebaseMessaging.getInstance()
    
    fun setupPushNotifications() {
        firebaseMessaging.token.addOnCompleteListener { task ->
            if (task.isSuccessful) {
                val token = task.result
                // Send token to server
                api.registerPushToken(token)
            }
        }
    }
    
    fun handleNotification(remoteMessage: RemoteMessage) {
        val data = remoteMessage.data
        when (data["type"]) {
            "message" -> handleNewMessage(data)
            "like" -> handleNewLike(data)
            "comment" -> handleNewComment(data)
        }
    }
}
```

---

## 🎯 Interview Framework

### Step-by-Step Approach

**1. Clarify Requirements (5-10 minutes)**
```
Interviewer: "Design a photo sharing app like Instagram"
You: "Let me clarify a few things:
- What's the scale? (users, photos per day)
- What are the core features? (upload, view, like, comment)
- Any specific requirements? (real-time, offline support)
- What's the target platform? (mobile, web, both)"
```

**2. Estimate Scale (2-3 minutes)**
```
- Daily Active Users: 10M
- Photos uploaded per day: 1M
- Read/Write ratio: 100:1
- Storage requirements: 1TB per day
- Peak QPS: 10K
```

**3. High-Level Design (10-15 minutes)**
```
Draw the main components:
- Mobile App
- Load Balancer
- Web Servers
- CDN
- Database
- Cache
- Message Queue
```

**4. Detailed Design (15-20 minutes)**
```
- Database schema
- API design
- Caching strategy
- Data flow
- Error handling
```

**5. Identify Bottlenecks (5-10 minutes)**
```
- Database read/write capacity
- Network bandwidth
- Storage limitations
- Cache hit ratio
```

**6. Optimize (5-10 minutes)**
```
- Add read replicas
- Implement caching
- Use CDN
- Database sharding
- Load balancing
```

### Common Mistakes to Avoid

1. **Jumping to implementation too quickly**
2. **Not asking clarifying questions**
3. **Ignoring non-functional requirements**
4. **Not considering scale**
5. **Forgetting about failure scenarios**
6. **Not discussing trade-offs**

### Questions to Ask

**Functional Requirements:**
- What are the core features?
- What are the user workflows?
- Any specific business rules?

**Non-Functional Requirements:**
- How many users do you expect?
- What's the expected response time?
- What's the availability requirement?
- Any data consistency requirements?

**Technical Constraints:**
- Any technology preferences?
- Budget constraints?
- Time to market requirements?
- Compliance requirements?

---

## 📚 Additional Resources

### Books
- "Designing Data-Intensive Applications" by Martin Kleppmann
- "System Design Interview" by Alex Xu
- "Building Microservices" by Sam Newman

### Online Resources
- [High Scalability](http://highscalability.com/)
- [System Design Primer](https://github.com/donnemartin/system-design-primer)
- [AWS Architecture Center](https://aws.amazon.com/architecture/)

### Practice Platforms
- [Grokking the System Design Interview](https://www.educative.io/courses/grokking-the-system-design-interview)
- [System Design Interview](https://www.systemdesigninterview.com/)
- [LeetCode System Design](https://leetcode.com/discuss/interview-question/system-design)

---

## 🎯 Conclusion

System design interviews test your ability to:
- Think at scale
- Make trade-offs
- Communicate complex ideas
- Consider real-world constraints

**Key Success Factors:**
1. **Practice**: Design systems regularly
2. **Learn**: Study existing systems
3. **Communicate**: Explain your thinking clearly
4. **Iterate**: Start simple, then optimize

Remember: There's no perfect design, only better designs for specific requirements and constraints.

---

*Happy designing! 🚀* 