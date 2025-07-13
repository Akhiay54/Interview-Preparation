# Advanced System Design for Backend SDE2
*Comprehensive guide for experienced backend developers (3+ years) targeting SDE2 positions*

---

## 📋 Table of Contents

1. [System Design Fundamentals & Enterprise Patterns](#system-design-fundamentals--enterprise-patterns)
2. [Distributed Systems Deep Dive](#distributed-systems-deep-dive)
3. [Microservices Architecture & Patterns](#microservices-architecture--patterns)
4. [Database Design & Data Modeling](#database-design--data-modeling)
5. [Caching Strategies & CDN](#caching-strategies--cdn)
6. [Message Queues & Event-Driven Architecture](#message-queues--event-driven-architecture)
7. [Load Balancing & Service Discovery](#load-balancing--service-discovery)
8. [Security & Authentication](#security--authentication)
9. [Performance Optimization & Monitoring](#performance-optimization--monitoring)
10. [Real-time Systems & WebSockets](#real-time-systems--websockets)
11. [Data Processing & Analytics](#data-processing--analytics)
12. [Cloud Architecture & DevOps](#cloud-architecture--devops)
13. [Common System Design Questions](#common-system-design-questions)
14. [Interview Framework & Best Practices](#interview-framework--best-practices)

---

## System Design Fundamentals & Enterprise Patterns

### Enterprise System Design Principles

**CAP Theorem Deep Understanding:**
- **Consistency**: All nodes see the same data at the same time
- **Availability**: Every request receives a response
- **Partition Tolerance**: System continues operating despite network failures

**Real-world Trade-offs:**
```java
// Example: Eventual Consistency Pattern
@Service
public class OrderService {
    @Transactional
    public Order createOrder(OrderRequest request) {
        // 1. Write to primary database (strong consistency)
        Order order = orderRepository.save(new Order(request));
        
        // 2. Publish event for eventual consistency
        eventPublisher.publish(new OrderCreatedEvent(order));
        
        // 3. Return immediately (availability)
        return order;
    }
}

@Component
public class InventoryEventHandler {
    @EventListener
    public void handleOrderCreated(OrderCreatedEvent event) {
        // Eventually update inventory (eventual consistency)
        inventoryService.updateStock(event.getProductId(), event.getQuantity());
    }
}
```

**ACID vs BASE:**
- **ACID**: Atomicity, Consistency, Isolation, Durability
- **BASE**: Basically Available, Soft state, Eventual consistency

### Enterprise Architecture Patterns

**CQRS (Command Query Responsibility Segregation):**
```java
// Command Side
@Service
public class OrderCommandService {
    public void createOrder(CreateOrderCommand command) {
        Order order = new Order(command);
        orderRepository.save(order);
        eventStore.append(new OrderCreatedEvent(order));
    }
}

// Query Side
@Service
public class OrderQueryService {
    public OrderDTO getOrder(String orderId) {
        return orderReadRepository.findById(orderId);
    }
    
    public List<OrderDTO> getOrdersByUser(String userId) {
        return orderReadRepository.findByUserId(userId);
    }
}
```

**Event Sourcing:**
```java
@Entity
public class Order {
    @Id
    private String id;
    private List<OrderEvent> events = new ArrayList<>();
    
    public void apply(OrderEvent event) {
        events.add(event);
        // Apply event to current state
        switch (event.getType()) {
            case ORDER_CREATED:
                this.status = OrderStatus.CREATED;
                break;
            case ORDER_CONFIRMED:
                this.status = OrderStatus.CONFIRMED;
                break;
        }
    }
    
    public OrderState getCurrentState() {
        return events.stream()
            .reduce(new OrderState(), this::applyEvent, (a, b) -> b);
    }
}
```

---

## Distributed Systems Deep Dive

### Distributed System Challenges

**Network Partition Handling:**
```java
@Service
public class DistributedLockService {
    private final RedisTemplate<String, String> redisTemplate;
    
    public boolean acquireLock(String key, String value, Duration timeout) {
        String script = """
            if redis.call('set', KEYS[1], ARGV[1], 'NX', 'PX', ARGV[2]) then
                return 1
            else
                return 0
            end
            """;
        
        return redisTemplate.execute(
            new DefaultRedisScript<>(script, Boolean.class),
            Collections.singletonList(key),
            value,
            timeout.toMillis()
        );
    }
    
    public boolean releaseLock(String key, String value) {
        String script = """
            if redis.call('get', KEYS[1]) == ARGV[1] then
                return redis.call('del', KEYS[1])
            else
                return 0
            end
            """;
        
        return redisTemplate.execute(
            new DefaultRedisScript<>(script, Boolean.class),
            Collections.singletonList(key),
            value
        );
    }
}
```

**Consensus Algorithms:**
- **Raft**: Leader election, log replication, safety
- **Paxos**: Classic consensus algorithm
- **ZAB**: ZooKeeper Atomic Broadcast

### Distributed Data Management

**Consistent Hashing:**
```java
public class ConsistentHashRing<T> {
    private final TreeMap<Long, T> ring = new TreeMap<>();
    private final HashFunction hashFunction;
    private final int virtualNodes;
    
    public ConsistentHashRing(HashFunction hashFunction, int virtualNodes) {
        this.hashFunction = hashFunction;
        this.virtualNodes = virtualNodes;
    }
    
    public void addNode(T node) {
        for (int i = 0; i < virtualNodes; i++) {
            String virtualNodeName = node.toString() + "#" + i;
            long hash = hashFunction.hash(virtualNodeName);
            ring.put(hash, node);
        }
    }
    
    public T getNode(String key) {
        if (ring.isEmpty()) return null;
        
        long hash = hashFunction.hash(key);
        Map.Entry<Long, T> entry = ring.ceilingEntry(hash);
        
        if (entry == null) {
            entry = ring.firstEntry();
        }
        
        return entry.getValue();
    }
}
```

**Distributed Transactions (Saga Pattern):**
```java
@Service
public class OrderSagaOrchestrator {
    
    @Transactional
    public void processOrder(OrderRequest request) {
        try {
            // Step 1: Reserve inventory
            InventoryReservation reservation = inventoryService.reserve(request.getItems());
            
            // Step 2: Process payment
            PaymentResult payment = paymentService.process(request.getPayment());
            
            // Step 3: Create order
            Order order = orderService.create(request);
            
            // Step 4: Confirm reservation
            inventoryService.confirmReservation(reservation.getId());
            
        } catch (Exception e) {
            // Compensating actions
            compensateTransaction(request);
            throw e;
        }
    }
    
    private void compensateTransaction(OrderRequest request) {
        // Rollback in reverse order
        // Implementation depends on specific business logic
    }
}
```

---

## Microservices Architecture & Patterns

### Service Decomposition Strategies

**Domain-Driven Design (DDD):**
```java
// Bounded Context: Order Management
@Aggregate
public class Order {
    @Id
    private OrderId id;
    private CustomerId customerId;
    private List<OrderItem> items;
    private OrderStatus status;
    private Money totalAmount;
    
    public void addItem(ProductId productId, int quantity, Money price) {
        OrderItem item = new OrderItem(productId, quantity, price);
        items.add(item);
        recalculateTotal();
    }
    
    public void confirm() {
        if (status != OrderStatus.CREATED) {
            throw new IllegalStateException("Order cannot be confirmed");
        }
        status = OrderStatus.CONFIRMED;
        domainEvents.add(new OrderConfirmedEvent(this));
    }
}

// Bounded Context: Inventory Management
@Aggregate
public class Product {
    @Id
    private ProductId id;
    private int availableQuantity;
    
    public void reserve(int quantity) {
        if (availableQuantity < quantity) {
            throw new InsufficientStockException();
        }
        availableQuantity -= quantity;
    }
}
```

**API Gateway Pattern:**
```java
@Component
public class ApiGatewayFilter implements GlobalFilter {
    
    @Override
    public Mono<Void> filter(ServerWebExchange exchange, GatewayFilterChain chain) {
        ServerHttpRequest request = exchange.getRequest();
        
        // Authentication
        String token = request.getHeaders().getFirst("Authorization");
        if (!isValidToken(token)) {
            return unauthorized(exchange);
        }
        
        // Rate limiting
        String clientId = extractClientId(token);
        if (!rateLimiter.allowRequest(clientId)) {
            return tooManyRequests(exchange);
        }
        
        // Logging
        logRequest(request);
        
        return chain.filter(exchange)
            .doFinally(signalType -> logResponse(exchange.getResponse()));
    }
}
```

### Service Communication Patterns

**Synchronous Communication (REST/gRPC):**
```java
@Service
public class OrderService {
    private final ProductServiceClient productServiceClient;
    private final CircuitBreaker circuitBreaker;
    
    public OrderDTO createOrder(CreateOrderRequest request) {
        return circuitBreaker.run(() -> {
            // Call product service synchronously
            ProductDTO product = productServiceClient.getProduct(request.getProductId());
            
            if (product.getStock() < request.getQuantity()) {
                throw new InsufficientStockException();
            }
            
            return createOrderInternal(request, product);
        });
    }
}
```

**Asynchronous Communication (Events):**
```java
@Component
public class OrderEventHandler {
    
    @EventListener
    public void handleOrderCreated(OrderCreatedEvent event) {
        // Publish to message queue for other services
        kafkaTemplate.send("order-events", event);
    }
    
    @EventListener
    public void handlePaymentProcessed(PaymentProcessedEvent event) {
        // Update order status
        orderService.updateStatus(event.getOrderId(), OrderStatus.PAID);
    }
}
```

---

## Database Design & Data Modeling

### Advanced Database Patterns

**Database Sharding:**
```java
@Service
public class ShardedUserService {
    private final Map<Integer, UserRepository> shards;
    
    public UserService(Map<Integer, UserRepository> shards) {
        this.shards = shards;
    }
    
    public User findById(String userId) {
        int shardId = getShardId(userId);
        return shards.get(shardId).findById(userId);
    }
    
    public User save(User user) {
        int shardId = getShardId(user.getId());
        return shards.get(shardId).save(user);
    }
    
    private int getShardId(String userId) {
        return Math.abs(userId.hashCode()) % shards.size();
    }
}
```

**Read Replicas Pattern:**
```java
@Configuration
public class DatabaseConfig {
    
    @Bean
    @Primary
    @Qualifier("master")
    public DataSource masterDataSource() {
        return DataSourceBuilder.create()
            .url("jdbc:mysql://master:3306/mydb")
            .username("user")
            .password("password")
            .build();
    }
    
    @Bean
    @Qualifier("replica")
    public DataSource replicaDataSource() {
        return DataSourceBuilder.create()
            .url("jdbc:mysql://replica:3306/mydb")
            .username("user")
            .password("password")
            .build();
    }
}

@Service
public class UserService {
    private final JdbcTemplate masterJdbcTemplate;
    private final JdbcTemplate replicaJdbcTemplate;
    
    @Transactional
    public void createUser(User user) {
        // Write to master
        masterJdbcTemplate.update("INSERT INTO users (id, name) VALUES (?, ?)", 
            user.getId(), user.getName());
    }
    
    public User getUser(String id) {
        // Read from replica
        return replicaJdbcTemplate.queryForObject(
            "SELECT * FROM users WHERE id = ?", 
            new Object[]{id}, 
            new UserRowMapper()
        );
    }
}
```

**Polyglot Persistence:**
```java
@Service
public class UserProfileService {
    private final UserRepository userRepository; // PostgreSQL
    private final UserSessionRepository sessionRepository; // Redis
    private final UserAnalyticsRepository analyticsRepository; // MongoDB
    
    public UserProfile getUserProfile(String userId) {
        // Get basic user data from PostgreSQL
        User user = userRepository.findById(userId);
        
        // Get session data from Redis
        UserSession session = sessionRepository.findById(userId);
        
        // Get analytics from MongoDB
        UserAnalytics analytics = analyticsRepository.findById(userId);
        
        return new UserProfile(user, session, analytics);
    }
}
```

### Data Modeling Patterns

**Event Sourcing with CQRS:**
```java
@Entity
public class OrderEvent {
    @Id
    private String id;
    private String orderId;
    private String eventType;
    private String eventData;
    private LocalDateTime timestamp;
    private long version;
}

@Service
public class OrderEventStore {
    
    public void append(String orderId, OrderEvent event) {
        event.setOrderId(orderId);
        event.setVersion(getNextVersion(orderId));
        eventRepository.save(event);
    }
    
    public List<OrderEvent> getEvents(String orderId) {
        return eventRepository.findByOrderIdOrderByVersion(orderId);
    }
    
    public OrderState getCurrentState(String orderId) {
        List<OrderEvent> events = getEvents(orderId);
        return events.stream()
            .reduce(new OrderState(), this::applyEvent, (a, b) -> b);
    }
}
```

---

## Caching Strategies & CDN

### Multi-Level Caching

**Application-Level Caching:**
```java
@Service
public class ProductService {
    private final Cache<String, Product> productCache;
    private final ProductRepository productRepository;
    
    public ProductService() {
        this.productCache = Caffeine.newBuilder()
            .maximumSize(10_000)
            .expireAfterWrite(Duration.ofMinutes(30))
            .build();
    }
    
    public Product getProduct(String productId) {
        return productCache.get(productId, key -> {
            // Cache miss - load from database
            return productRepository.findById(key);
        });
    }
    
    public void updateProduct(Product product) {
        productRepository.save(product);
        // Invalidate cache
        productCache.invalidate(product.getId());
    }
}
```

**Distributed Caching (Redis):**
```java
@Service
public class UserSessionService {
    private final RedisTemplate<String, UserSession> redisTemplate;
    
    public UserSession getSession(String sessionId) {
        String key = "session:" + sessionId;
        return redisTemplate.opsForValue().get(key);
    }
    
    public void createSession(UserSession session) {
        String key = "session:" + session.getId();
        redisTemplate.opsForValue().set(key, session, Duration.ofHours(24));
    }
    
    public void invalidateSession(String sessionId) {
        String key = "session:" + sessionId;
        redisTemplate.delete(key);
    }
}
```

**Cache-Aside Pattern:**
```java
@Service
public class CacheAsideService {
    private final Cache<String, Object> cache;
    private final DatabaseService databaseService;
    
    public Object getData(String key) {
        // Try cache first
        Object cached = cache.getIfPresent(key);
        if (cached != null) {
            return cached;
        }
        
        // Cache miss - load from database
        Object data = databaseService.getData(key);
        if (data != null) {
            cache.put(key, data);
        }
        
        return data;
    }
    
    public void updateData(String key, Object data) {
        // Update database first
        databaseService.updateData(key, data);
        
        // Then update cache
        cache.put(key, data);
    }
    
    public void deleteData(String key) {
        // Delete from database first
        databaseService.deleteData(key);
        
        // Then invalidate cache
        cache.invalidate(key);
    }
}
```

---

## Message Queues & Event-Driven Architecture

### Message Queue Patterns

**Producer-Consumer Pattern:**
```java
@Component
public class OrderEventProducer {
    private final KafkaTemplate<String, OrderEvent> kafkaTemplate;
    
    public void publishOrderCreated(Order order) {
        OrderEvent event = new OrderCreatedEvent(order);
        kafkaTemplate.send("order-events", order.getId(), event);
    }
    
    public void publishOrderCancelled(Order order) {
        OrderEvent event = new OrderCancelledEvent(order);
        kafkaTemplate.send("order-events", order.getId(), event);
    }
}

@Component
public class OrderEventConsumer {
    
    @KafkaListener(topics = "order-events", groupId = "inventory-service")
    public void handleOrderEvent(OrderEvent event) {
        switch (event.getType()) {
            case ORDER_CREATED:
                handleOrderCreated((OrderCreatedEvent) event);
                break;
            case ORDER_CANCELLED:
                handleOrderCancelled((OrderCancelledEvent) event);
                break;
        }
    }
    
    private void handleOrderCreated(OrderCreatedEvent event) {
        // Update inventory
        inventoryService.reserveStock(event.getProductId(), event.getQuantity());
    }
}
```

**Dead Letter Queue Pattern:**
```java
@Component
public class DeadLetterQueueHandler {
    
    @RabbitListener(queues = "order-processing-dlq")
    public void handleDeadLetter(Message message) {
        try {
            // Log the failed message
            log.error("Processing failed message: {}", message);
            
            // Extract original message
            OrderEvent event = extractEvent(message);
            
            // Retry logic or manual intervention
            if (shouldRetry(event)) {
                retryProcessing(event);
            } else {
                // Send to manual review queue
                sendToManualReview(event);
            }
            
        } catch (Exception e) {
            log.error("Error processing dead letter", e);
        }
    }
}
```

**Event Sourcing with Message Queues:**
```java
@Service
public class EventSourcingService {
    private final EventStore eventStore;
    private final KafkaTemplate<String, DomainEvent> kafkaTemplate;
    
    public void saveEvent(String aggregateId, DomainEvent event) {
        // Save to event store
        eventStore.append(aggregateId, event);
        
        // Publish to message queue
        kafkaTemplate.send("domain-events", aggregateId, event);
    }
    
    public List<DomainEvent> getEvents(String aggregateId) {
        return eventStore.getEvents(aggregateId);
    }
}
```

---

## Load Balancing & Service Discovery

### Advanced Load Balancing

**Client-Side Load Balancing:**
```java
@Configuration
public class LoadBalancerConfig {
    
    @Bean
    @LoadBalanced
    public RestTemplate restTemplate() {
        return new RestTemplate();
    }
    
    @Bean
    public LoadBalancerClient loadBalancerClient() {
        return new RoundRobinLoadBalancer();
    }
}

public class RoundRobinLoadBalancer implements LoadBalancerClient {
    private final AtomicInteger counter = new AtomicInteger(0);
    private final List<ServiceInstance> instances;
    
    @Override
    public ServiceInstance choose(String serviceId) {
        if (instances.isEmpty()) {
            return null;
        }
        
        int index = counter.getAndIncrement() % instances.size();
        return instances.get(index);
    }
}
```

**Service Discovery with Consul:**
```java
@Configuration
public class ConsulConfig {
    
    @Bean
    public ConsulServiceRegistry consulServiceRegistry(ConsulClient consulClient) {
        return new ConsulServiceRegistry(consulClient);
    }
    
    @Bean
    public ConsulDiscoveryClient consulDiscoveryClient(ConsulClient consulClient) {
        return new ConsulDiscoveryClient(consulClient);
    }
}

@Service
public class ServiceRegistrationService {
    private final ConsulServiceRegistry serviceRegistry;
    
    @EventListener(ApplicationReadyEvent.class)
    public void registerService() {
        Registration registration = Registration.builder()
            .id("order-service-" + System.getProperty("server.port"))
            .name("order-service")
            .port(Integer.parseInt(System.getProperty("server.port")))
            .address("localhost")
            .check(Registration.RegCheck.http("http://localhost:" + 
                System.getProperty("server.port") + "/health"))
            .build();
        
        serviceRegistry.register(registration);
    }
}
```

---

## Security & Authentication

### Advanced Security Patterns

**JWT Token Management:**
```java
@Service
public class JwtTokenService {
    private final String secretKey;
    private final long expirationTime;
    
    public String generateToken(UserDetails userDetails) {
        Map<String, Object> claims = new HashMap<>();
        claims.put("roles", userDetails.getAuthorities());
        claims.put("userId", userDetails.getUsername());
        
        return Jwts.builder()
            .setClaims(claims)
            .setSubject(userDetails.getUsername())
            .setIssuedAt(new Date())
            .setExpiration(new Date(System.currentTimeMillis() + expirationTime))
            .signWith(SignatureAlgorithm.HS512, secretKey)
            .compact();
    }
    
    public Claims validateToken(String token) {
        return Jwts.parser()
            .setSigningKey(secretKey)
            .parseClaimsJws(token)
            .getBody();
    }
}
```

**OAuth2 Implementation:**
```java
@Configuration
@EnableAuthorizationServer
public class AuthorizationServerConfig extends AuthorizationServerConfigurerAdapter {
    
    @Override
    public void configure(ClientDetailsServiceConfigurer clients) throws Exception {
        clients.inMemory()
            .withClient("web-client")
            .secret(passwordEncoder.encode("secret"))
            .authorizedGrantTypes("password", "refresh_token")
            .scopes("read", "write")
            .accessTokenValiditySeconds(3600)
            .refreshTokenValiditySeconds(86400);
    }
    
    @Override
    public void configure(AuthorizationServerEndpointsConfigurer endpoints) {
        endpoints.authenticationManager(authenticationManager)
            .userDetailsService(userDetailsService);
    }
}
```

**Rate Limiting:**
```java
@Component
public class RateLimitingFilter implements Filter {
    private final RateLimiter rateLimiter;
    
    @Override
    public void doFilter(ServletRequest request, ServletResponse response, 
                        FilterChain chain) throws IOException, ServletException {
        
        HttpServletRequest httpRequest = (HttpServletRequest) request;
        String clientId = extractClientId(httpRequest);
        
        if (!rateLimiter.allowRequest(clientId)) {
            HttpServletResponse httpResponse = (HttpServletResponse) response;
            httpResponse.setStatus(HttpStatus.TOO_MANY_REQUESTS.value());
            httpResponse.getWriter().write("Rate limit exceeded");
            return;
        }
        
        chain.doFilter(request, response);
    }
}

@Service
public class TokenBucketRateLimiter implements RateLimiter {
    private final Map<String, TokenBucket> buckets = new ConcurrentHashMap<>();
    
    @Override
    public boolean allowRequest(String clientId) {
        TokenBucket bucket = buckets.computeIfAbsent(clientId, 
            k -> new TokenBucket(10, 10, 1)); // 10 tokens, refill 1 per second
        
        return bucket.tryConsume(1);
    }
}
```

---

## Performance Optimization & Monitoring

### Performance Monitoring

**Application Performance Monitoring (APM):**
```java
@Aspect
@Component
public class PerformanceMonitoringAspect {
    
    @Around("@annotation(Monitored)")
    public Object monitorPerformance(ProceedingJoinPoint joinPoint) throws Throwable {
        long startTime = System.currentTimeMillis();
        String methodName = joinPoint.getSignature().getName();
        
        try {
            Object result = joinPoint.proceed();
            long duration = System.currentTimeMillis() - startTime;
            
            // Record metrics
            recordMetrics(methodName, duration, "success");
            
            return result;
        } catch (Exception e) {
            long duration = System.currentTimeMillis() - startTime;
            recordMetrics(methodName, duration, "error");
            throw e;
        }
    }
    
    private void recordMetrics(String methodName, long duration, String status) {
        // Send to monitoring system (Prometheus, DataDog, etc.)
        meterRegistry.timer("method.execution.time", 
            "method", methodName, "status", status).record(duration, TimeUnit.MILLISECONDS);
    }
}
```

**Database Performance Optimization:**
```java
@Service
public class OptimizedUserService {
    private final UserRepository userRepository;
    
    @Transactional(readOnly = true)
    public List<UserDTO> getUsersWithPagination(int page, int size) {
        Pageable pageable = PageRequest.of(page, size, Sort.by("createdAt").descending());
        Page<User> users = userRepository.findAll(pageable);
        
        return users.getContent().stream()
            .map(this::toDTO)
            .collect(Collectors.toList());
    }
    
    @Transactional
    public void batchUpdateUsers(List<UserUpdateRequest> updates) {
        // Use batch processing for better performance
        List<User> usersToUpdate = new ArrayList<>();
        
        for (UserUpdateRequest update : updates) {
            User user = userRepository.findById(update.getUserId());
            user.update(update);
            usersToUpdate.add(user);
        }
        
        userRepository.saveAll(usersToUpdate);
    }
}
```

**Connection Pooling:**
```java
@Configuration
public class DatabaseConfig {
    
    @Bean
    public DataSource dataSource() {
        HikariConfig config = new HikariConfig();
        config.setJdbcUrl("jdbc:mysql://localhost:3306/mydb");
        config.setUsername("user");
        config.setPassword("password");
        config.setMaximumPoolSize(20);
        config.setMinimumIdle(5);
        config.setConnectionTimeout(30000);
        config.setIdleTimeout(600000);
        config.setMaxLifetime(1800000);
        
        return new HikariDataSource(config);
    }
}
```

---

## Real-time Systems & WebSockets

### WebSocket Implementation

**WebSocket Server:**
```java
@Configuration
@EnableWebSocket
public class WebSocketConfig implements WebSocketConfigurer {
    
    @Override
    public void registerWebSocketHandlers(WebSocketHandlerRegistry registry) {
        registry.addHandler(new ChatWebSocketHandler(), "/chat")
            .setAllowedOrigins("*");
    }
}

@Component
public class ChatWebSocketHandler extends TextWebSocketHandler {
    private final Map<String, WebSocketSession> sessions = new ConcurrentHashMap<>();
    
    @Override
    public void afterConnectionEstablished(WebSocketSession session) {
        String userId = extractUserId(session);
        sessions.put(userId, session);
    }
    
    @Override
    protected void handleTextMessage(WebSocketSession session, TextMessage message) {
        ChatMessage chatMessage = parseMessage(message.getPayload());
        
        // Broadcast to all connected users
        sessions.values().forEach(s -> {
            try {
                s.sendMessage(new TextMessage(chatMessage.toJson()));
            } catch (IOException e) {
                log.error("Error sending message", e);
            }
        });
    }
    
    @Override
    public void afterConnectionClosed(WebSocketSession session, CloseStatus status) {
        String userId = extractUserId(session);
        sessions.remove(userId);
    }
}
```

**Server-Sent Events (SSE):**
```java
@RestController
public class NotificationController {
    
    @GetMapping(value = "/notifications/stream", produces = MediaType.TEXT_EVENT_STREAM_VALUE)
    public Flux<ServerSentEvent<String>> streamNotifications() {
        return Flux.interval(Duration.ofSeconds(1))
            .map(sequence -> ServerSentEvent.<String>builder()
                .id(String.valueOf(sequence))
                .event("notification")
                .data("Notification " + sequence)
                .build());
    }
}
```

---

## Data Processing & Analytics

### Stream Processing

**Apache Kafka Streams:**
```java
@Configuration
public class KafkaStreamsConfig {
    
    @Bean
    public KStream<String, OrderEvent> orderStream(StreamsBuilder streamsBuilder) {
        return streamsBuilder.stream("order-events");
    }
    
    @Bean
    public KStream<String, OrderAnalytics> orderAnalyticsStream(
            KStream<String, OrderEvent> orderStream) {
        
        return orderStream
            .groupByKey()
            .windowedBy(TimeWindows.of(Duration.ofHours(1)))
            .aggregate(
                OrderAnalytics::new,
                (key, event, analytics) -> analytics.update(event),
                Materialized.as("order-analytics-store")
            )
            .toStream()
            .map((key, value) -> new KeyValue<>(key.key(), value));
    }
}
```

**Batch Processing:**
```java
@Service
public class BatchProcessingService {
    
    @Scheduled(fixedRate = 3600000) // Every hour
    public void processDailyAnalytics() {
        LocalDate yesterday = LocalDate.now().minusDays(1);
        
        List<Order> orders = orderRepository.findByDate(yesterday);
        
        DailyAnalytics analytics = orders.stream()
            .collect(Collectors.groupingBy(
                Order::getProductId,
                Collectors.collectingAndThen(
                    Collectors.summingInt(Order::getQuantity),
                    DailyAnalytics::new
                )
            ));
        
        analyticsRepository.save(analytics);
    }
}
```

---

## Cloud Architecture & DevOps

### Container Orchestration

**Docker Configuration:**
```dockerfile
# Dockerfile
FROM openjdk:11-jre-slim

WORKDIR /app

COPY target/application.jar app.jar

EXPOSE 8080

ENTRYPOINT ["java", "-jar", "app.jar"]
```

**Kubernetes Deployment:**
```yaml
# deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: order-service
spec:
  replicas: 3
  selector:
    matchLabels:
      app: order-service
  template:
    metadata:
      labels:
        app: order-service
    spec:
      containers:
      - name: order-service
        image: order-service:latest
        ports:
        - containerPort: 8080
        env:
        - name: SPRING_PROFILES_ACTIVE
          value: "production"
        resources:
          requests:
            memory: "512Mi"
            cpu: "250m"
          limits:
            memory: "1Gi"
            cpu: "500m"
        livenessProbe:
          httpGet:
            path: /actuator/health
            port: 8080
          initialDelaySeconds: 30
          periodSeconds: 10
        readinessProbe:
          httpGet:
            path: /actuator/health/readiness
            port: 8080
          initialDelaySeconds: 5
          periodSeconds: 5
```

**Service Mesh (Istio):**
```yaml
# virtual-service.yaml
apiVersion: networking.istio.io/v1alpha3
kind: VirtualService
metadata:
  name: order-service
spec:
  hosts:
  - order-service
  http:
  - route:
    - destination:
        host: order-service
        subset: v1
      weight: 90
    - destination:
        host: order-service
        subset: v2
      weight: 10
```

---

## Common System Design Questions

### 1. Design a URL Shortener

**Requirements:**
- Shorten long URLs
- Handle 100M URLs per day
- 5-year retention
- 99.9% availability

**Solution:**
```java
@Service
public class UrlShortenerService {
    private final UrlRepository urlRepository;
    private final Cache<String, String> urlCache;
    
    public String shortenUrl(String longUrl) {
        // Generate short code
        String shortCode = generateShortCode();
        
        // Save to database
        UrlMapping mapping = new UrlMapping(shortCode, longUrl);
        urlRepository.save(mapping);
        
        // Cache for fast access
        urlCache.put(shortCode, longUrl);
        
        return "https://short.ly/" + shortCode;
    }
    
    public String getLongUrl(String shortCode) {
        // Try cache first
        String longUrl = urlCache.getIfPresent(shortCode);
        if (longUrl != null) {
            return longUrl;
        }
        
        // Cache miss - load from database
        UrlMapping mapping = urlRepository.findByShortCode(shortCode);
        if (mapping != null) {
            urlCache.put(shortCode, mapping.getLongUrl());
            return mapping.getLongUrl();
        }
        
        throw new UrlNotFoundException();
    }
    
    private String generateShortCode() {
        // Use base62 encoding for 6-character codes
        return Base62.encode(UUID.randomUUID().getMostSignificantBits() & 0x7fffffffffffffffL)
            .substring(0, 6);
    }
}
```

### 2. Design a Chat System

**Requirements:**
- Real-time messaging
- Support 1M concurrent users
- Message persistence
- Online/offline status

**Solution:**
```java
@Service
public class ChatService {
    private final MessageRepository messageRepository;
    private final WebSocketService webSocketService;
    private final UserPresenceService presenceService;
    
    public void sendMessage(ChatMessage message) {
        // Save to database
        messageRepository.save(message);
        
        // Send to online recipients
        List<String> onlineRecipients = presenceService.getOnlineUsers(message.getRecipients());
        webSocketService.sendToUsers(onlineRecipients, message);
        
        // Send push notification to offline users
        List<String> offlineRecipients = message.getRecipients().stream()
            .filter(recipient -> !onlineRecipients.contains(recipient))
            .collect(Collectors.toList());
        
        pushNotificationService.sendNotifications(offlineRecipients, message);
    }
    
    public List<ChatMessage> getMessages(String conversationId, int limit, String beforeId) {
        return messageRepository.findByConversationIdOrderByTimestampDesc(
            conversationId, limit, beforeId);
    }
}
```

### 3. Design a Rate Limiter

**Requirements:**
- Support 1M requests per second
- Sliding window
- Multiple rate limit types

**Solution:**
```java
@Service
public class RateLimiterService {
    private final RedisTemplate<String, String> redisTemplate;
    
    public boolean isAllowed(String key, RateLimitConfig config) {
        String script = """
            local current = redis.call('get', KEYS[1])
            local now = tonumber(ARGV[1])
            local window = tonumber(ARGV[2])
            local limit = tonumber(ARGV[3])
            
            if current == false then
                redis.call('setex', KEYS[1], window, 1)
                return 1
            end
            
            local count = tonumber(current)
            if count >= limit then
                return 0
            end
            
            redis.call('incr', KEYS[1])
            return 1
            """;
        
        long now = System.currentTimeMillis() / 1000;
        String windowKey = key + ":" + (now / config.getWindowSeconds());
        
        return redisTemplate.execute(
            new DefaultRedisScript<>(script, Boolean.class),
            Collections.singletonList(windowKey),
            String.valueOf(now),
            String.valueOf(config.getWindowSeconds()),
            String.valueOf(config.getLimit())
        );
    }
}
```

---

## Interview Framework & Best Practices

### System Design Interview Process

**1. Requirements Clarification (5-10 minutes):**
- Functional requirements
- Non-functional requirements (scale, performance, availability)
- Constraints and assumptions

**2. Capacity Estimation (2-3 minutes):**
- Traffic estimates
- Storage requirements
- Bandwidth calculations

**3. High-Level Design (10-15 minutes):**
- System components
- Data flow
- Technology choices

**4. Detailed Design (15-20 minutes):**
- Database schema
- API design
- Component interactions

**5. Bottleneck Identification (5-10 minutes):**
- Performance bottlenecks
- Scalability issues
- Single points of failure

**6. Optimization (5-10 minutes):**
- Caching strategies
- Load balancing
- Database optimization

### Best Practices

**1. Start with a Simple Design:**
- Begin with a basic solution
- Iteratively add complexity
- Justify each addition

**2. Focus on Trade-offs:**
- CAP theorem implications
- Performance vs consistency
- Cost vs availability

**3. Consider Real-world Constraints:**
- Network latency
- Hardware limitations
- Operational complexity

**4. Demonstrate System Thinking:**
- Show understanding of distributed systems
- Consider failure scenarios
- Plan for scale

**5. Communicate Effectively:**
- Use diagrams when helpful
- Explain your reasoning
- Ask clarifying questions

---

## Conclusion

This comprehensive guide covers advanced system design concepts essential for backend developers with 3+ years of experience targeting SDE2 positions. Key takeaways:

1. **Deep understanding of distributed systems** and their challenges
2. **Practical implementation** of enterprise patterns
3. **Real-world considerations** for scalability and reliability
4. **Modern architecture patterns** like microservices and event-driven design
5. **Performance optimization** and monitoring strategies

Remember to practice these concepts through real-world projects and system design interviews. The key is not just knowing the patterns, but understanding when and how to apply them effectively.

---

*This document is designed for experienced backend developers and covers enterprise-level system design concepts. For basic concepts, refer to the standard system design guides.* 

## Practice Questions

### 1. Design a Distributed Rate Limiter
- **Requirements:** Support 10M RPS, global and per-user limits, burst handling, low latency, high availability, multi-region.
- **Hint:** Think about token bucket/leaky bucket, Redis/memory, sharding, clock skew, and failover.

### 2. Design a Real-Time Collaborative Document Editor (like Google Docs)
- **Requirements:** Multiple users edit same doc, real-time sync, conflict resolution, offline support, versioning, scalability.
- **Hint:** Consider OT/CRDT, WebSockets, operational logs, user presence, and data partitioning.

### 3. Design a Global Notification System
- **Requirements:** Billions of notifications/day, multi-channel (email, SMS, push), retries, deduplication, user preferences, GDPR compliance.
- **Hint:** Event-driven, message queues, idempotency, user profile service, regional failover.

### 4. Design a Scalable File Storage Service (like S3)
- **Requirements:** Store petabytes, versioning, access control, multi-region, high durability, low latency.
- **Hint:** Object storage, metadata DB, erasure coding, CDN, eventual consistency, strong/weak read paths.

### 5. Design a High-Throughput Order Matching Engine (like for a stock exchange)
- **Requirements:** Match buy/sell orders in real-time, low latency, fairness, auditability, high availability.
- **Hint:** Priority queues, event sourcing, partitioning by symbol, failover, replay logs.

### 6. Design a Multi-Tenant SaaS Platform
- **Requirements:** Data isolation, tenant onboarding, scaling, billing, custom configs, RBAC, API rate limits.
- **Hint:** Shared vs. isolated DB, tenant ID in schema, feature flags, per-tenant throttling, audit logs.

### 7. Design a Real-Time Analytics Dashboard
- **Requirements:** Ingest millions of events/sec, aggregate in real-time, low-latency queries, alerting, historical data.
- **Hint:** Stream processing (Kafka, Flink), time-series DB, windowed aggregations, pre-computed views, alert rules engine.

### 8. Design a Secure API Gateway
- **Requirements:** AuthN/Z, rate limiting, request/response transformation, logging, DDoS protection, multi-cloud.
- **Hint:** JWT/OAuth2, WAF, circuit breakers, plugin architecture, distributed tracing.

### 9. Design a Social Feed System (like Twitter)
- **Requirements:** Personalized feeds, billions of users, fan-out, ranking, spam detection, real-time updates.
- **Hint:** Push vs. pull, feed pre-compute, sharding, ML ranking, anti-abuse pipeline, cache invalidation.

### 10. Design a Payment Processing System
- **Requirements:** Idempotency, fraud detection, PCI compliance, multi-currency, settlements, high reliability.
- **Hint:** Transaction logs, double-entry bookkeeping, compensating transactions, risk scoring, secure vaults. 