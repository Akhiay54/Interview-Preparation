# Android Developer Interview Questions - Complete Guide with Answers
## Kotlin, Compose, CMP (Compose Multiplatform), KMP (Kotlin Multiplatform)
### Updated for Android 16 (2025)

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
16. [Android 16 New Features](#android-16-new-features)
17. [Interview Tips and Strategies](#interview-tips-and-strategies)
18. [Additional Resources](#additional-resources)

---

## Kotlin Fundamentals

### Basic Concepts

1. **What is Kotlin and why was it created?**
   
   **Answer:** Kotlin is a statically typed programming language developed by JetBrains in 2011, officially released in 2016. It was created to address Java's limitations while maintaining 100% interoperability.
   
   **Key reasons for creation:**
   - **Conciseness**: Reduce boilerplate code
   - **Safety**: Null safety and type safety
   - **Interoperability**: Seamless Java integration
   - **Tool-friendly**: Excellent IDE support
   - **Multiplatform**: Target JVM, Android, Native, JS, and WASM
   
   ```kotlin
   // Java vs Kotlin comparison
   // Java
   public class Person {
       private String name;
       private int age;
       
       public Person(String name, int age) {
           this.name = name;
           this.age = age;
       }
       // getters, setters, equals, hashCode, toString...
   }
   
   // Kotlin
   data class Person(val name: String, val age: Int)
   ```

2. **Explain the difference between `val` and `var`**
   
   **Answer:**
   - **`val`**: Immutable reference (read-only), similar to `final` in Java
   - **`var`**: Mutable reference (read-write)
   
   ```kotlin
   val name = "John"        // Immutable
   // name = "Jane"         // Compilation error
   
   var age = 25            // Mutable
   age = 26                // OK
   
   val list = mutableListOf(1, 2, 3)
   list.add(4)             // OK - object is mutable, reference is immutable
   // list = mutableListOf(5, 6) // Error - can't reassign reference
   ```

3. **What is null safety in Kotlin? Explain `?`, `!!`, `?.`, and `?:`**
   
   **Answer:** Kotlin's null safety prevents NullPointerException at compile time by distinguishing nullable and non-nullable types.
   
   **Operators:**
   - **`?`**: Nullable type declaration
   - **`!!`**: Not-null assertion (throws exception if null)
   - **`?.`**: Safe call operator (returns null if receiver is null)
   - **`?:`**: Elvis operator (provides default value if null)
   
   ```kotlin
   var name: String = "John"    // Non-nullable
   var nullableName: String? = null  // Nullable
   
   // Safe call
   val length = nullableName?.length  // Returns null if nullableName is null
   
   // Elvis operator
   val displayName = nullableName ?: "Unknown"
   
   // Not-null assertion
   val definiteLength = nullableName!!.length  // Throws KotlinNullPointerException if null
   
   // Safe call with let
   nullableName?.let { name ->
       println("Name is $name")
   }
   ```

4. **What are data classes and what methods do they auto-generate?**
   
   **Answer:** Data classes are classes whose primary purpose is to hold data. They automatically generate utility methods.
   
   **Auto-generated methods:**
   - **`equals()`**: Structural equality
   - **`hashCode()`**: Hash code generation
   - **`toString()`**: String representation
   - **`copy()`**: Create copy with modified properties
   - **`componentN()`**: Destructuring declarations
   
   ```kotlin
   data class User(val name: String, val age: Int, val email: String)
   
   val user1 = User("John", 25, "john@example.com")
   val user2 = User("John", 25, "john@example.com")
   
   println(user1 == user2)  // true (structural equality)
   println(user1.toString()) // User(name=John, age=25, email=john@example.com)
   
   // Copy with modifications
   val olderUser = user1.copy(age = 26)
   
   // Destructuring
   val (name, age, email) = user1
   ```

5. **Explain the difference between `==` and `===` in Kotlin**
   
   **Answer:**
   - **`==`**: Structural equality (calls `equals()` method)
   - **`===`**: Referential equality (same object reference)
   
   ```kotlin
   val str1 = "Hello"
   val str2 = "Hello"
   val str3 = String("Hello".toCharArray())
   
   println(str1 == str2)   // true (structural equality)
   println(str1 === str2)  // true (string interning)
   println(str1 == str3)   // true (structural equality)
   println(str1 === str3)  // false (different objects)
   
   data class Person(val name: String)
   val person1 = Person("John")
   val person2 = Person("John")
   
   println(person1 == person2)  // true (data class equals)
   println(person1 === person2) // false (different objects)
   ```

6. **What is string interpolation and how is it used?**
   
   **Answer:** String interpolation allows embedding expressions inside string literals using `$` symbol.
   
   ```kotlin
   val name = "John"
   val age = 25
   
   // Simple interpolation
   val greeting = "Hello, $name!"
   
   // Expression interpolation
   val message = "Next year, $name will be ${age + 1} years old"
   
   // Complex expressions
   val user = User("John", 25, "john@example.com")
   val info = "User: ${user.name.uppercase()}, Age: ${if (user.age >= 18) "Adult" else "Minor"}"
   
   // Escape dollar sign
   val price = "Price: \$${100.50}"
   ```

7. **Explain when expressions vs if expressions**
   
   **Answer:** Both `when` and `if` are expressions in Kotlin (they return values), but `when` is more powerful for multiple conditions.
   
   ```kotlin
   // If expression
   val max = if (a > b) a else b
   
   val result = if (score >= 90) {
       "A"
   } else if (score >= 80) {
       "B"
   } else {
       "C"
   }
   
   // When expression
   val grade = when (score) {
       in 90..100 -> "A"
       in 80..89 -> "B"
       in 70..79 -> "C"
       else -> "F"
   }
   
   // When with type checking
   fun describe(obj: Any): String = when (obj) {
       1 -> "One"
       "Hello" -> "Greeting"
       is Long -> "Long number"
       !is String -> "Not a string"
       else -> "Unknown"
   }
   
   // When without argument
   when {
       x.isOdd() -> print("x is odd")
       x.isEven() -> print("x is even")
       else -> print("x is funny")
   }
   ```

8. **What are sealed classes and when would you use them?**
   
   **Answer:** Sealed classes restrict class hierarchy - all subclasses must be declared in the same file. They're perfect for representing restricted class hierarchies.
   
   ```kotlin
   sealed class Result<out T>
   data class Success<T>(val data: T) : Result<T>()
   data class Error(val exception: Throwable) : Result<Nothing>()
   object Loading : Result<Nothing>()
   
   // Usage with when expression (exhaustive)
   fun handleResult(result: Result<String>) = when (result) {
       is Success -> println("Data: ${result.data}")
       is Error -> println("Error: ${result.exception.message}")
       Loading -> println("Loading...")
       // No else needed - compiler knows all cases are covered
   }
   
   // API Response example
   sealed class ApiResponse<T> {
       data class Success<T>(val data: T) : ApiResponse<T>()
       data class Error<T>(val code: Int, val message: String) : ApiResponse<T>()
       class Loading<T> : ApiResponse<T>()
   }
   ```

9. **Explain the difference between `object`, `class`, and `data class`**
   
   **Answer:**
   - **`class`**: Regular class for creating multiple instances
   - **`data class`**: Class optimized for holding data with auto-generated methods
   - **`object`**: Singleton instance (created lazily on first access)
   
   ```kotlin
   // Regular class
   class Person(val name: String) {
       fun greet() = "Hello, I'm $name"
   }
   
   // Data class
   data class User(val id: Int, val name: String, val email: String)
   
   // Object (Singleton)
   object DatabaseManager {
       fun connect() = println("Connected to database")
       fun disconnect() = println("Disconnected from database")
   }
   
   // Object expression (anonymous object)
   val clickListener = object : View.OnClickListener {
       override fun onClick(v: View?) {
           println("Clicked!")
       }
   }
   
   // Companion object
   class MyClass {
       companion object {
           const val CONSTANT = "value"
           fun staticMethod() = "static"
       }
   }
   ```

10. **What is type inference in Kotlin?**
    
    **Answer:** Type inference allows the compiler to automatically determine types based on context, reducing explicit type declarations.
    
    ```kotlin
    // Explicit types
    val name: String = "John"
    val age: Int = 25
    val isActive: Boolean = true
    
    // Type inference
    val name = "John"        // String inferred
    val age = 25            // Int inferred
    val isActive = true     // Boolean inferred
    
    // Collection inference
    val numbers = listOf(1, 2, 3)  // List<Int>
    val mixed = listOf("a", 1, true)  // List<Any>
    
    // Function return type inference
    fun add(a: Int, b: Int) = a + b  // Return type Int inferred
    
    // Generic type inference
    val map = mapOf("key" to "value")  // Map<String, String>
    
    // Lambda parameter inference
    val doubled = numbers.map { it * 2 }  // 'it' is Int
    
    // When type inference fails
    val emptyList = emptyList<String>()  // Need explicit type
    ```

### Functions and Lambdas

11. **What are higher-order functions?**
    
    **Answer:** Higher-order functions are functions that take other functions as parameters or return functions. They enable functional programming patterns.
    
    ```kotlin
    // Function taking another function as parameter
    fun calculate(x: Int, y: Int, operation: (Int, Int) -> Int): Int {
        return operation(x, y)
    }
    
    // Usage
    val sum = calculate(5, 3) { a, b -> a + b }
    val product = calculate(5, 3) { a, b -> a * b }
    
    // Function returning a function
    fun createMultiplier(factor: Int): (Int) -> Int {
        return { number -> number * factor }
    }
    
    val doubler = createMultiplier(2)
    val result = doubler(5) // 10
    
    // Built-in higher-order functions
    val numbers = listOf(1, 2, 3, 4, 5)
    val doubled = numbers.map { it * 2 }
    val evens = numbers.filter { it % 2 == 0 }
    val sum = numbers.reduce { acc, n -> acc + n }
    ```

12. **Explain lambda expressions and their syntax**
    
    **Answer:** Lambda expressions are anonymous functions that can be treated as values. They have concise syntax for functional programming.
    
    ```kotlin
    // Basic lambda syntax
    val lambda: (Int, Int) -> Int = { x, y -> x + y }
    
    // Lambda with single parameter (can use 'it')
    val numbers = listOf(1, 2, 3, 4, 5)
    val doubled = numbers.map { it * 2 }
    
    // Lambda with multiple parameters
    val combined = numbers.mapIndexed { index, value -> 
        "Index: $index, Value: $value" 
    }
    
    // Lambda with no parameters
    val greeting: () -> String = { "Hello, World!" }
    
    // Lambda with explicit parameter types
    val calculator: (Int, Int) -> Int = { x: Int, y: Int -> x + y }
    
    // Multi-line lambda
    val processor = { input: String ->
        val trimmed = input.trim()
        val uppercase = trimmed.uppercase()
        "Processed: $uppercase"
    }
    
    // Lambda as last parameter (trailing lambda syntax)
    numbers.filter { it > 3 }
    // Instead of: numbers.filter({ it > 3 })
    ```

13. **What is the difference between lambda and anonymous functions?**
    
    **Answer:** Both are ways to define functions without names, but they have different syntax and behavior:
    
    ```kotlin
    // Lambda expression
    val lambda = { x: Int, y: Int -> x + y }
    
    // Anonymous function
    val anonymousFunction = fun(x: Int, y: Int): Int { return x + y }
    
    // Key differences:
    
    // 1. Return behavior
    fun testReturn() {
        val numbers = listOf(1, 2, 3, 4, 5)
        
        // Lambda - return exits the enclosing function
        numbers.forEach { 
            if (it == 3) return // Returns from testReturn()
            println(it)
        }
        
        // Anonymous function - return exits only the anonymous function
        numbers.forEach(fun(value) {
            if (value == 3) return // Returns from anonymous function only
            println(value)
        })
    }
    
    // 2. Type inference
    val lambda = { x: Int -> x * 2 } // Type inferred
    val anonymous = fun(x: Int) = x * 2 // Explicit return type can be specified
    
    // 3. Syntax flexibility
    val multiLineLambda = { x: Int ->
        val doubled = x * 2
        doubled + 1
    }
    
    val multiLineAnonymous = fun(x: Int): Int {
        val doubled = x * 2
        return doubled + 1
    }
    ```

14. **What are extension functions and how do you create them?**
    
    **Answer:** Extension functions allow you to add new functions to existing classes without modifying their source code.
    
    ```kotlin
    // Basic extension function
    fun String.isPalindrome(): Boolean {
        val cleaned = this.lowercase().replace(Regex("[^a-z0-9]"), "")
        return cleaned == cleaned.reversed()
    }
    
    // Usage
    val text = "A man a plan a canal Panama"
    println(text.isPalindrome()) // true
    
    // Extension function with parameters
    fun List<Int>.secondLargest(): Int? {
        return this.distinct().sortedDescending().getOrNull(1)
    }
    
    // Extension properties
    val String.lastChar: Char?
        get() = if (isEmpty()) null else this[length - 1]
    
    // Extension functions on generic types
    fun <T> List<T>.second(): T? {
        return if (size >= 2) this[1] else null
    }
    
    // Extension functions with receivers
    fun StringBuilder.appendLine(value: Any?): StringBuilder {
        append(value)
        append('\n')
        return this
    }
    
    // Nullable receiver
    fun String?.isNullOrEmpty(): Boolean {
        return this == null || this.isEmpty()
    }
    
    // Extension functions in classes (member extensions)
    class StringProcessor {
        fun String.processSpecial(): String {
            return "Processed: $this"
        }
        
        fun process(input: String): String {
            return input.processSpecial() // Can call extension inside class
        }
    }
    ```

15. **Explain function types and function literals**
    
    **Answer:** Function types represent the signature of functions, and function literals are ways to create function instances.
    
    ```kotlin
    // Function type declarations
    typealias Operation = (Int, Int) -> Int
    typealias Predicate<T> = (T) -> Boolean
    typealias Callback = () -> Unit
    
    // Function types as parameters
    fun processNumbers(
        numbers: List<Int>,
        operation: (Int) -> Int,
        filter: (Int) -> Boolean = { true }
    ): List<Int> {
        return numbers.filter(filter).map(operation)
    }
    
    // Function literals (different ways to create functions)
    
    // 1. Lambda expression
    val add: (Int, Int) -> Int = { x, y -> x + y }
    
    // 2. Anonymous function
    val multiply = fun(x: Int, y: Int): Int { return x * y }
    
    // 3. Function reference
    fun subtract(x: Int, y: Int): Int = x - y
    val subtractRef: (Int, Int) -> Int = ::subtract
    
    // 4. Member function reference
    class Calculator {
        fun divide(x: Int, y: Int): Int = x / y
    }
    val calc = Calculator()
    val divideRef: (Int, Int) -> Int = calc::divide
    
    // Function types with receivers
    val stringBuilder: StringBuilder.() -> Unit = {
        append("Hello")
        append(" World")
    }
    
    // Usage
    val sb = StringBuilder()
    sb.stringBuilder()
    
    // Higher-order function with function type
    fun <T, R> T.let(block: (T) -> R): R = block(this)
    fun <T> T.also(block: (T) -> Unit): T {
        block(this)
        return this
    }
    ```

16. **What is the `it` keyword in lambdas?**
    
    **Answer:** `it` is an implicit parameter name for single-parameter lambdas, making code more concise.
    
    ```kotlin
    // Single parameter lambda - can use 'it'
    val numbers = listOf(1, 2, 3, 4, 5)
    val doubled = numbers.map { it * 2 }
    
    // Equivalent to:
    val doubled2 = numbers.map { number -> number * 2 }
    
    // Multiple examples with 'it'
    val strings = listOf("hello", "world", "kotlin")
    
    // Filter using 'it'
    val longStrings = strings.filter { it.length > 5 }
    
    // Transform using 'it'
    val uppercased = strings.map { it.uppercase() }
    
    // Chain operations with 'it'
    val processed = strings
        .filter { it.isNotEmpty() }
        .map { it.capitalize() }
        .sortedBy { it.length }
    
    // When NOT to use 'it'
    
    // Multiple parameters - can't use 'it'
    val indexed = strings.mapIndexed { index, value -> 
        "$index: $value" 
    }
    
    // Complex lambda - explicit names are clearer
    val complexOperation = numbers.map { number ->
        val squared = number * number
        val doubled = squared * 2
        doubled + 1
    }
    
    // Nested lambdas - 'it' can be confusing
    val nestedResult = numbers.map { outer ->
        strings.map { inner -> "$outer-$inner" }
    }
    
    // Custom parameter name for clarity
    val users = listOf(User("John", 25), User("Jane", 30))
    val names = users.map { user -> user.name } // Clearer than 'it'
    ```

17. **How do you handle multiple parameters in lambdas?**
    
    **Answer:** Multiple parameters in lambdas are handled with explicit parameter names separated by commas.
    
    ```kotlin
    // Multiple parameters with explicit names
    val numbers = listOf(1, 2, 3, 4, 5)
    val strings = listOf("a", "b", "c")
    
    // Two parameters
    val combined = numbers.zip(strings) { number, letter -> 
        "$number$letter" 
    }
    
    // MapIndexed with index and value
    val indexed = numbers.mapIndexed { index, value -> 
        "Position $index: $value" 
    }
    
    // Fold with accumulator and current value
    val sum = numbers.fold(0) { acc, current -> acc + current }
    
    // Reduce with two elements
    val product = numbers.reduce { acc, current -> acc * current }
    
    // Custom higher-order function
    fun <T, R> combine(
        first: T, 
        second: T, 
        operation: (T, T) -> R
    ): R = operation(first, second)
    
    val result = combine(10, 20) { a, b -> a + b }
    
    // Destructuring in lambdas
    val pairs = listOf(1 to "one", 2 to "two", 3 to "three")
    val formatted = pairs.map { (number, word) -> 
        "Number: $number, Word: $word" 
    }
    
    // Data class destructuring
    data class Person(val name: String, val age: Int)
    val people = listOf(Person("John", 25), Person("Jane", 30))
    val descriptions = people.map { (name, age) -> 
        "$name is $age years old" 
    }
    
    // Ignoring parameters with underscore
    val onlyFirst = pairs.map { (number, _) -> number }
    val onlySecond = pairs.map { (_, word) -> word }
    ```

18. **What are inline functions and when should you use them?**
    
    **Answer:** Inline functions copy the function body to the call site during compilation, eliminating function call overhead.
    
    ```kotlin
    // Inline function definition
    inline fun <T> myLet(value: T, block: (T) -> Unit) {
        block(value)
    }
    
    // Usage
    myLet(5) { println(it) }
    
    // Compiled equivalent (approximately):
    // println(5)
    
    // Benefits of inline functions:
    
    // 1. Performance - no function call overhead
    inline fun measureTime(block: () -> Unit): Long {
        val start = System.currentTimeMillis()
        block()
        return System.currentTimeMillis() - start
    }
    
    // 2. Non-local returns allowed
    inline fun forEach(items: List<Int>, action: (Int) -> Unit) {
        for (item in items) {
            action(item)
        }
    }
    
    fun processItems() {
        val numbers = listOf(1, 2, 3, 4, 5)
        forEach(numbers) { 
            if (it == 3) return // Returns from processItems()
            println(it)
        }
    }
    
    // When to use inline functions:
    
    // 1. Higher-order functions with lambda parameters
    inline fun <T> List<T>.customFilter(predicate: (T) -> Boolean): List<T> {
        val result = mutableListOf<T>()
        for (item in this) {
            if (predicate(item)) {
                result.add(item)
            }
        }
        return result
    }
    
    // 2. Reified type parameters
    inline fun <reified T> isInstanceOf(value: Any): Boolean {
        return value is T
    }
    
    // Usage
    val isString = isInstanceOf<String>("hello") // true
    val isInt = isInstanceOf<Int>("hello") // false
    
    // When NOT to use inline:
    
    // 1. Large functions (increases bytecode size)
    // 2. Functions that don't take function parameters
    // 3. Recursive functions
    // 4. Functions stored in variables or passed as arguments
    ```

19. **Explain `noinline` and `crossinline` keywords**
    
    **Answer:** These keywords modify the behavior of lambda parameters in inline functions.
    
    ```kotlin
    // noinline - prevents inlining of specific lambda parameters
    inline fun processData(
        data: String,
        inlinedProcessor: (String) -> String,
        noinline nonInlinedProcessor: (String) -> String
    ): String {
        val processed = inlinedProcessor(data)
        
        // Can store noinline lambda in variable
        val storedLambda = nonInlinedProcessor
        
        // Can pass noinline lambda to other functions
        return anotherFunction(processed, storedLambda)
    }
    
    fun anotherFunction(data: String, processor: (String) -> String): String {
        return processor(data)
    }
    
    // crossinline - prevents non-local returns in lambda
    inline fun runInBackground(
        crossinline action: () -> Unit
    ) {
        Thread {
            action() // Cannot contain non-local returns
        }.start()
    }
    
    fun testCrossinline() {
        runInBackground {
            // return // Compilation error - non-local return not allowed
            println("Running in background")
        }
    }
    
    // Practical example combining both
    inline fun <T> measureAndProcess(
        data: T,
        crossinline processor: (T) -> T, // Will be inlined but no non-local returns
        noinline logger: (String) -> Unit = { println(it) } // Won't be inlined
    ): T {
        val start = System.currentTimeMillis()
        
        // Store logger for later use
        val storedLogger = logger
        
        // Process in background thread
        val result = processor(data)
        
        val duration = System.currentTimeMillis() - start
        storedLogger("Processing took ${duration}ms")
        
        return result
    }
    
    // Usage
    val result = measureAndProcess("hello") { input ->
        input.uppercase()
    }
    ```

20. **What is function composition?**
    
    **Answer:** Function composition is combining simple functions to create more complex ones, following the mathematical concept f(g(x)).
    
    ```kotlin
    // Basic function composition
    fun <A, B, C> compose(f: (B) -> C, g: (A) -> B): (A) -> C {
        return { x -> f(g(x)) }
    }
    
    // Example functions
    val addOne: (Int) -> Int = { it + 1 }
    val multiplyByTwo: (Int) -> Int = { it * 2 }
    val toString: (Int) -> String = { it.toString() }
    
    // Compose functions
    val addOneThenMultiply = compose(multiplyByTwo, addOne)
    val processNumber = compose(toString, addOneThenMultiply)
    
    println(processNumber(5)) // "12" (5 + 1 = 6, 6 * 2 = 12, "12")
    
    // Infix function for composition
    infix fun <A, B, C> ((B) -> C).compose(g: (A) -> B): (A) -> C {
        return { x -> this(g(x)) }
    }
    
    // Usage with infix
    val pipeline = toString compose multiplyByTwo compose addOne
    println(pipeline(5)) // "12"
    
    // Practical example - data processing pipeline
    data class User(val name: String, val age: Int)
    data class ProcessedUser(val displayName: String, val category: String)
    
    val validateUser: (User) -> User = { user ->
        require(user.name.isNotBlank()) { "Name cannot be blank" }
        require(user.age >= 0) { "Age must be positive" }
        user
    }
    
    val normalizeUser: (User) -> User = { user ->
        user.copy(name = user.name.trim().lowercase())
    }
    
    val categorizeUser: (User) -> ProcessedUser = { user ->
        val category = when {
            user.age < 18 -> "Minor"
            user.age < 65 -> "Adult"
            else -> "Senior"
        }
        ProcessedUser(user.name.capitalize(), category)
    }
    
    // Compose the pipeline
    val userProcessor = categorizeUser compose normalizeUser compose validateUser
    
    // Usage
    val rawUser = User("  JOHN DOE  ", 25)
    val processedUser = userProcessor(rawUser)
    println(processedUser) // ProcessedUser(displayName=John doe, category=Adult)
    
    // Extension function approach
    fun <T> T.pipe(transform: (T) -> T): T = transform(this)
    
    val result = User("John", 25)
        .pipe(validateUser)
        .pipe(normalizeUser)
        .let(categorizeUser)
    ```

### Collections

21. **Explain the difference between List, MutableList, and ArrayList**
    
    **Answer:** These represent different levels of mutability and implementation in Kotlin collections:
    
    ```kotlin
    // List - Immutable interface (read-only)
    val immutableList: List<String> = listOf("a", "b", "c")
    // immutableList.add("d") // Compilation error - no add method
    
    // MutableList - Mutable interface (read-write)
    val mutableList: MutableList<String> = mutableListOf("a", "b", "c")
    mutableList.add("d") // OK
    mutableList.removeAt(0) // OK
    
    // ArrayList - Concrete implementation of MutableList
    val arrayList: ArrayList<String> = arrayListOf("a", "b", "c")
    arrayList.add("d") // OK
    arrayList.ensureCapacity(100) // ArrayList-specific method
    
    // Conversion between types
    val list = listOf(1, 2, 3)
    val mutableCopy = list.toMutableList()
    val immutableCopy = mutableCopy.toList()
    
    // Performance characteristics
    val arrayList = ArrayList<Int>() // Resizable array, O(1) access
    val linkedList = LinkedList<Int>() // Doubly-linked list, O(1) insertion/deletion
    
    // Best practices
    fun processItems(items: List<String>) { // Accept immutable interface
        items.forEach { println(it) }
    }
    
    fun createItems(): List<String> { // Return immutable interface
        return listOf("item1", "item2", "item3")
    }
    ```

22. **What are the main collection types in Kotlin?**
    
    **Answer:** Kotlin provides several collection types, each with immutable and mutable variants:
    
    ```kotlin
    // Lists - Ordered collections
    val list = listOf(1, 2, 3, 3) // Immutable
    val mutableList = mutableListOf(1, 2, 3) // Mutable
    val arrayList = arrayListOf(1, 2, 3) // ArrayList implementation
    
    // Sets - Unique elements
    val set = setOf(1, 2, 3, 3) // [1, 2, 3] - duplicates removed
    val mutableSet = mutableSetOf(1, 2, 3)
    val hashSet = hashSetOf(1, 2, 3) // HashSet implementation
    val linkedSet = linkedSetOf(1, 2, 3) // Maintains insertion order
    
    // Maps - Key-value pairs
    val map = mapOf("a" to 1, "b" to 2)
    val mutableMap = mutableMapOf("a" to 1, "b" to 2)
    val hashMap = hashMapOf("a" to 1, "b" to 2)
    val linkedMap = linkedMapOf("a" to 1, "b" to 2) // Maintains insertion order
    
    // Arrays - Fixed-size collections
    val array = arrayOf(1, 2, 3)
    val intArray = intArrayOf(1, 2, 3) // Primitive array
    val stringArray = Array(5) { "item$it" } // Generated array
    
    // Ranges
    val range = 1..10 // IntRange
    val charRange = 'a'..'z'
    val downTo = 10 downTo 1
    val step = 1..10 step 2 // [1, 3, 5, 7, 9]
    
    // Sequences - Lazy collections
    val sequence = sequenceOf(1, 2, 3)
    val generatedSequence = generateSequence(1) { it + 1 }
    
    // Collection hierarchy
    interface Collection<out E> : Iterable<E>
    interface List<out E> : Collection<E>
    interface Set<out E> : Collection<E>
    interface Map<K, out V>
    
    interface MutableCollection<E> : Collection<E>
    interface MutableList<E> : List<E>, MutableCollection<E>
    interface MutableSet<E> : Set<E>, MutableCollection<E>
    interface MutableMap<K, V> : Map<K, V>
    ```

23. **Explain `map`, `filter`, `reduce`, and `fold` operations**
    
    **Answer:** These are fundamental functional programming operations for transforming collections:
    
    ```kotlin
    val numbers = listOf(1, 2, 3, 4, 5)
    
    // MAP - Transform each element
    val doubled = numbers.map { it * 2 } // [2, 4, 6, 8, 10]
    val strings = numbers.map { "Number: $it" } // ["Number: 1", "Number: 2", ...]
    
    // FILTER - Select elements based on predicate
    val evens = numbers.filter { it % 2 == 0 } // [2, 4]
    val greaterThanThree = numbers.filter { it > 3 } // [4, 5]
    
    // REDUCE - Combine elements into single value (no initial value)
    val sum = numbers.reduce { acc, element -> acc + element } // 15
    val product = numbers.reduce { acc, element -> acc * element } // 120
    // Note: reduce throws exception on empty collections
    
    // FOLD - Combine elements with initial value
    val sumWithInitial = numbers.fold(0) { acc, element -> acc + element } // 15
    val concatenated = numbers.fold("") { acc, element -> acc + element } // "12345"
    val sumWithOffset = numbers.fold(100) { acc, element -> acc + element } // 115
    
    // Advanced examples
    data class Person(val name: String, val age: Int)
    val people = listOf(
        Person("John", 25),
        Person("Jane", 30),
        Person("Bob", 35)
    )
    
    // Map with object transformation
    val names = people.map { it.name } // ["John", "Jane", "Bob"]
    val ageGroups = people.map { person ->
        when {
            person.age < 30 -> "Young"
            person.age < 40 -> "Middle"
            else -> "Senior"
        }
    }
    
    // Filter with complex conditions
    val adults = people.filter { it.age >= 18 }
    val youngAdults = people.filter { it.age in 18..30 }
    
    // Reduce for complex operations
    val oldestPerson = people.reduce { acc, person ->
        if (person.age > acc.age) person else acc
    }
    
    // Fold for aggregations
    val totalAge = people.fold(0) { acc, person -> acc + person.age }
    val namesSummary = people.fold("People: ") { acc, person -> 
        acc + person.name + " " 
    }
    
    // Chaining operations
    val processedData = numbers
        .filter { it > 2 }
        .map { it * 2 }
        .fold(0) { acc, element -> acc + element }
    ```

24. **What is the difference between `forEach` and `map`?**
    
    **Answer:** `forEach` performs side effects without returning values, while `map` transforms elements and returns a new collection:
    
    ```kotlin
    val numbers = listOf(1, 2, 3, 4, 5)
    
    // forEach - Side effects, returns Unit
    numbers.forEach { println(it) } // Prints each number
    val forEachResult = numbers.forEach { it * 2 } // forEachResult is Unit
    
    // map - Transformation, returns new collection
    val doubled = numbers.map { it * 2 } // Returns [2, 4, 6, 8, 10]
    numbers.map { println(it) } // Returns [Unit, Unit, Unit, Unit, Unit]
    
    // Key differences:
    
    // 1. Return type
    val list = listOf("a", "b", "c")
    val mapResult: List<String> = list.map { it.uppercase() } // Returns List<String>
    val forEachResult: Unit = list.forEach { println(it) } // Returns Unit
    
    // 2. Purpose
    // forEach - for side effects (logging, updating external state)
    val mutableList = mutableListOf<Int>()
    numbers.forEach { mutableList.add(it * 2) } // Side effect
    
    // map - for transformations (creating new data)
    val transformed = numbers.map { it * 2 } // Pure transformation
    
    // 3. Performance
    // forEach - slightly more efficient for side effects only
    numbers.forEach { processItem(it) } // No collection creation
    
    // map - creates new collection
    val processed = numbers.map { processItem(it) } // Creates new list
    
    // 4. Functional programming style
    // forEach - imperative style
    numbers.forEach { number ->
        if (number > 3) {
            println("Large number: $number")
        }
    }
    
    // map + filter - functional style
    val largeNumbers = numbers
        .filter { it > 3 }
        .map { "Large number: $it" }
    
    // 5. Chaining
    // forEach - cannot be chained (returns Unit)
    numbers.forEach { it * 2 } // Cannot chain further
    
    // map - can be chained
    val result = numbers
        .map { it * 2 }
        .filter { it > 5 }
        .map { "Result: $it" }
    
    // When to use each:
    // Use forEach when:
    // - Performing side effects (logging, updating UI, etc.)
    // - You don't need to transform the collection
    // - You want to execute code for each element
    
    // Use map when:
    // - Transforming elements
    // - Creating new collections
    // - Chaining operations
    // - Following functional programming principles
    ```

25. **How do you create immutable collections?**
    
    **Answer:** Kotlin provides several ways to create immutable collections with different levels of immutability:
    
    ```kotlin
    // 1. Using collection factory functions
    val immutableList = listOf(1, 2, 3) // Read-only List
    val immutableSet = setOf("a", "b", "c") // Read-only Set
    val immutableMap = mapOf("key1" to "value1", "key2" to "value2") // Read-only Map
    
    // 2. Converting from mutable collections
    val mutableList = mutableListOf(1, 2, 3)
    val immutableCopy = mutableList.toList() // Creates immutable copy
    
    val mutableSet = mutableSetOf("a", "b")
    val immutableSetCopy = mutableSet.toSet()
    
    val mutableMap = mutableMapOf("key" to "value")
    val immutableMapCopy = mutableMap.toMap()
    
    // 3. Using builders
    val builtList = buildList {
        add(1)
        add(2)
        add(3)
    } // Returns immutable List
    
    val builtSet = buildSet {
        add("a")
        add("b")
        add("c")
    } // Returns immutable Set
    
    val builtMap = buildMap {
        put("key1", "value1")
        put("key2", "value2")
    } // Returns immutable Map
    
    // 4. Empty collections
    val emptyList = emptyList<String>()
    val emptySet = emptySet<Int>()
    val emptyMap = emptyMap<String, Int>()
    
    // 5. Single element collections
    val singletonList = listOf("only item")
    val singletonSet = setOf("only item")
    val singletonMap = mapOf("key" to "value")
    
    // 6. Immutable collections with data classes
    data class ImmutableData(
        val items: List<String> = emptyList(),
        val properties: Map<String, String> = emptyMap()
    )
    
    val data = ImmutableData(
        items = listOf("item1", "item2"),
        properties = mapOf("prop1" to "value1")
    )
    
    // 7. Deep immutability considerations
    data class Person(val name: String, val age: Int)
    val people = listOf(
        Person("John", 25),
        Person("Jane", 30)
    ) // List is immutable, but Person objects could be mutable if not data class
    
    // 8. Persistent collections (third-party libraries)
    // Using kotlinx.collections.immutable
    /*
    val persistentList = persistentListOf(1, 2, 3)
    val newList = persistentList.add(4) // Returns new list, original unchanged
    
    val persistentMap = persistentMapOf("a" to 1, "b" to 2)
    val newMap = persistentMap.put("c", 3) // Returns new map
    */
    
    // 9. Best practices for immutability
    class ImmutableRepository {
        private val _items = mutableListOf<String>()
        val items: List<String> get() = _items.toList() // Return immutable copy
        
        fun addItem(item: String) {
            _items.add(item)
        }
    }
    
    // 10. Defensive copying
    class SafeContainer(items: List<String>) {
        private val _items = items.toList() // Defensive copy
        val items: List<String> get() = _items.toList() // Return copy
    }
    
    // Benefits of immutable collections:
    // - Thread safety
    // - Predictable behavior
    // - Easier reasoning about code
    // - Functional programming support
    // - No accidental modifications
    ```

26. **Explain `flatMap` and when to use it**
    
    **Answer:** `flatMap` transforms each element to a collection and then flattens the result into a single collection:
    
    ```kotlin
    // Basic flatMap example
    val numbers = listOf(1, 2, 3)
    val doubled = numbers.flatMap { listOf(it, it * 2) }
    // Result: [1, 2, 2, 4, 3, 6]
    
    // Comparison with map
    val mapped = numbers.map { listOf(it, it * 2) }
    // Result: [[1, 2], [2, 4], [3, 6]] - List of Lists
    
    val flatMapped = numbers.flatMap { listOf(it, it * 2) }
    // Result: [1, 2, 2, 4, 3, 6] - Flattened List
    
    // Real-world examples
    
    // 1. Processing nested data structures
    data class Department(val name: String, val employees: List<String>)
    val departments = listOf(
        Department("Engineering", listOf("John", "Jane", "Bob")),
        Department("Marketing", listOf("Alice", "Charlie")),
        Department("Sales", listOf("David", "Eve"))
    )
    
    // Get all employees across departments
    val allEmployees = departments.flatMap { it.employees }
    // Result: ["John", "Jane", "Bob", "Alice", "Charlie", "David", "Eve"]
    
    // 2. String processing
    val sentences = listOf("Hello world", "Kotlin is great", "FlatMap rocks")
    val allWords = sentences.flatMap { it.split(" ") }
    // Result: ["Hello", "world", "Kotlin", "is", "great", "FlatMap", "rocks"]
    
    // 3. Optional/nullable processing
    val nullableStrings = listOf("1", "2", null, "4", "not a number")
    val validNumbers = nullableStrings.flatMap { str ->
        str?.toIntOrNull()?.let { listOf(it) } ?: emptyList()
    }
    // Result: [1, 2, 4]
    
    // 4. File processing
    data class Directory(val name: String, val files: List<String>)
    val directories = listOf(
        Directory("src", listOf("Main.kt", "Utils.kt")),
        Directory("test", listOf("MainTest.kt", "UtilsTest.kt"))
    )
    
    val allFiles = directories.flatMap { it.files }
    // Result: ["Main.kt", "Utils.kt", "MainTest.kt", "UtilsTest.kt"]
    
    // 5. Generating combinations
    val colors = listOf("Red", "Blue")
    val sizes = listOf("Small", "Large")
    val combinations = colors.flatMap { color ->
        sizes.map { size -> "$color $size" }
    }
    // Result: ["Red Small", "Red Large", "Blue Small", "Blue Large"]
    
    // 6. Parsing and extracting data
    val jsonStrings = listOf(
        """{"users": ["john", "jane"]}""",
        """{"users": ["bob", "alice"]}"""
    )
    // Simulated JSON parsing
    val extractedUsers = jsonStrings.flatMap { json ->
        // In real code, you'd use a JSON parser
        when {
            json.contains("john") -> listOf("john", "jane")
            json.contains("bob") -> listOf("bob", "alice")
            else -> emptyList()
        }
    }
    
    // 7. Flattening nested collections
    val nestedLists = listOf(
        listOf(1, 2, 3),
        listOf(4, 5),
        listOf(6, 7, 8, 9)
    )
    val flattened = nestedLists.flatMap { it }
    // Result: [1, 2, 3, 4, 5, 6, 7, 8, 9]
    
    // 8. Conditional flattening
    val items = listOf("apple", "banana", "cherry")
    val conditionalFlattening = items.flatMap { item ->
        if (item.length > 5) {
            listOf(item, item.uppercase())
        } else {
            listOf(item)
        }
    }
    // Result: ["apple", "banana", "BANANA", "cherry", "CHERRY"]
    
    // When to use flatMap:
    // - When you need to transform elements to collections and flatten
    // - Processing nested data structures
    // - Combining multiple collections
    // - Filtering and transforming in one operation
    // - Generating combinations or permutations
    // - Parsing complex data formats
    ```

27. **What are sequences and how do they differ from collections?**
    
    **Answer:** Sequences are lazy collections that evaluate elements on-demand, providing better performance for chained operations:
    
    ```kotlin
    // Creating sequences
    val sequence = sequenceOf(1, 2, 3, 4, 5)
    val listSequence = listOf(1, 2, 3, 4, 5).asSequence()
    val generatedSequence = generateSequence(1) { it + 1 } // Infinite sequence
    
    // Key differences between Collections and Sequences:
    
    // 1. EVALUATION STRATEGY
    val numbers = listOf(1, 2, 3, 4, 5)
    
    // Collection - Eager evaluation (immediate)
    val collectionResult = numbers
        .map { 
            println("Mapping $it")
            it * 2 
        }
        .filter { 
            println("Filtering $it")
            it > 5 
        }
    // Output: All mapping operations, then all filtering operations
    
    // Sequence - Lazy evaluation (on-demand)
    val sequenceResult = numbers.asSequence()
        .map { 
            println("Mapping $it")
            it * 2 
        }
        .filter { 
            println("Filtering $it")
            it > 5 
        }
        .toList() // Terminal operation triggers evaluation
    // Output: Mapping and filtering happen element by element
    
    // 2. PERFORMANCE COMPARISON
    val largeList = (1..1_000_000).toList()
    
    // Collection - Creates intermediate collections
    val collectionChain = largeList
        .map { it * 2 }        // Creates 1M element list
        .filter { it > 100 }   // Creates another filtered list
        .take(10)              // Creates final 10 element list
    
    // Sequence - No intermediate collections
    val sequenceChain = largeList.asSequence()
        .map { it * 2 }        // No intermediate collection
        .filter { it > 100 }   // No intermediate collection
        .take(10)              // No intermediate collection
        .toList()              // Only creates final 10 element list
    
    // 3. INFINITE SEQUENCES
    val infiniteSequence = generateSequence(1) { it + 1 }
    val first10Even = infiniteSequence
        .filter { it % 2 == 0 }
        .take(10)
        .toList()
    // Result: [2, 4, 6, 8, 10, 12, 14, 16, 18, 20]
    
    // 4. SEQUENCE OPERATIONS
    
    // Intermediate operations (lazy)
    val intermediateSequence = sequenceOf(1, 2, 3, 4, 5)
        .map { it * 2 }
        .filter { it > 4 }
        .drop(1)
        .take(2)
    // No evaluation yet!
    
    // Terminal operations (trigger evaluation)
    val list = intermediateSequence.toList()
    val firstElement = intermediateSequence.first()
    val sum = intermediateSequence.sum()
    
    // 5. PRACTICAL EXAMPLES
    
    // File processing
    fun processLargeFile(lines: Sequence<String>): List<String> {
        return lines
            .filter { it.isNotBlank() }
            .map { it.trim() }
            .filter { it.startsWith("ERROR") }
            .take(100)
            .toList()
    }
    
    // Database-like operations
    data class User(val id: Int, val name: String, val age: Int, val active: Boolean)
    val users = generateSequence(1) { it + 1 }
        .take(1000)
        .map { User(it, "User$it", (18..80).random(), it % 10 != 0) }
    
    val activeAdults = users
        .filter { it.active }
        .filter { it.age >= 18 }
        .take(50)
        .toList()
    
    // 6. CUSTOM SEQUENCES
    fun fibonacciSequence(): Sequence<Long> = sequence {
        var a = 0L
        var b = 1L
        yield(a)
        yield(b)
        while (true) {
            val next = a + b
            yield(next)
            a = b
            b = next
        }
    }
    
    val first20Fibonacci = fibonacciSequence().take(20).toList()
    
    // 7. SEQUENCE BUILDER
    val customSequence = sequence {
        yield(1)
        yieldAll(listOf(2, 3, 4))
        yieldAll(generateSequence(5) { it + 1 }.take(5))
    }
    
    // 8. WHEN TO USE SEQUENCES
    
    // Use Sequences when:
    // - Chaining multiple operations
    // - Working with large datasets
    // - Only need a subset of results
    // - Want to avoid intermediate collections
    // - Processing infinite data streams
    
    // Use Collections when:
    // - Need random access to elements
    // - Performing single operations
    // - Working with small datasets
    // - Need to reuse results multiple times
    // - Require collection-specific operations
    
    // 9. PERFORMANCE MEASUREMENT
    fun measurePerformance() {
        val data = (1..1_000_000).toList()
        
        // Collection approach
        val collectionTime = measureTimeMillis {
            val result = data
                .map { it * 2 }
                .filter { it > 1000 }
                .take(100)
        }
        
        // Sequence approach
        val sequenceTime = measureTimeMillis {
            val result = data.asSequence()
                .map { it * 2 }
                .filter { it > 1000 }
                .take(100)
                .toList()
        }
        
        println("Collection time: ${collectionTime}ms")
        println("Sequence time: ${sequenceTime}ms")
    }
    ```

28. **Explain lazy evaluation in Kotlin sequences**
    
    **Answer:** Lazy evaluation means computations are deferred until their results are actually needed, providing efficiency benefits:
    
    ```kotlin
    // 1. LAZY EVALUATION CONCEPT
    val numbers = listOf(1, 2, 3, 4, 5)
    
    // This sequence is not evaluated yet
    val lazySequence = numbers.asSequence()
        .map { 
            println("Processing $it") // This won't print yet
            it * 2 
        }
        .filter { it > 4 }
    
    println("Sequence created") // This prints first
    
    // Evaluation happens only when terminal operation is called
    val result = lazySequence.toList() // Now processing happens
    
    // 2. ELEMENT-BY-ELEMENT PROCESSING
    val sequence = sequenceOf(1, 2, 3, 4, 5)
        .map { 
            println("Mapping $it")
            it * 2 
        }
        .filter { 
            println("Filtering $it")
            it > 4 
        }
    
    println("Getting first element:")
    val first = sequence.first()
    // Output shows element-by-element processing:
    // Mapping 1, Filtering 2, Mapping 2, Filtering 4, Mapping 3, Filtering 6
    
    // 3. SHORT-CIRCUITING OPERATIONS
    val shortCircuit = (1..1000).asSequence()
        .map { 
            println("Processing $it")
            it * 2 
        }
        .filter { it > 10 }
        .take(3) // Only processes until 3 elements are found
        .toList()
    
    // Only processes elements 1, 2, 3, 4, 5, 6 (stops when 3 results found)
    
    // 4. INFINITE SEQUENCES WITH LAZY EVALUATION
    val infiniteNumbers = generateSequence(1) { it + 1 }
    val evenNumbers = infiniteNumbers
        .filter { it % 2 == 0 }
        .map { it * 2 }
    
    // No computation happens until we request elements
    val first5Even = evenNumbers.take(5).toList() // [4, 8, 12, 16, 20]
    
    // 5. CUSTOM LAZY SEQUENCES
    fun lazyFibonacci(): Sequence<Int> = sequence {
        var a = 0
        var b = 1
        while (true) {
            yield(a)
            val next = a + b
            a = b
            b = next
        }
    }
    
    val fibonacci = lazyFibonacci()
    val first10Fib = fibonacci.take(10).toList()
    
    // 6. LAZY PROPERTIES
    class ExpensiveComputation {
        val expensiveValue: String by lazy {
            println("Computing expensive value...")
            Thread.sleep(1000) // Simulate expensive computation
            "Computed Result"
        }
    }
    
    val computation = ExpensiveComputation()
    println("Object created")
    println(computation.expensiveValue) // Computation happens here
    println(computation.expensiveValue) // Uses cached result
    
    // 7. LAZY INITIALIZATION MODES
    val threadSafeValue by lazy(LazyThreadSafetyMode.SYNCHRONIZED) {
        "Thread-safe initialization"
    }
    
    val publicationValue by lazy(LazyThreadSafetyMode.PUBLICATION) {
        "Publication mode - first successful initialization wins"
    }
    
    val noneValue by lazy(LazyThreadSafetyMode.NONE) {
        "No thread safety - use only in single-threaded scenarios"
    }
    
    // 8. PRACTICAL EXAMPLES
    
    // Database query simulation
    class DatabaseQuery {
        fun getUsers(): Sequence<User> = sequence {
            println("Connecting to database...")
            for (i in 1..1000) {
                println("Fetching user $i")
                yield(User(i, "User$i", (18..80).random()))
            }
        }
    }
    
    val db = DatabaseQuery()
    val activeUsers = db.getUsers()
        .filter { it.age >= 21 }
        .take(5) // Only fetches first 5 matching users
        .toList()
    
    // File processing
    fun processLogFile(filename: String): Sequence<String> = sequence {
        println("Opening file: $filename")
        // Simulate reading file line by line
        for (i in 1..1000) {
            yield("Log line $i")
        }
    }
    
    val errorLines = processLogFile("app.log")
        .filter { it.contains("ERROR") }
        .take(10) // Only processes until 10 errors found
        .toList()
    
    // 9. PERFORMANCE BENEFITS
    fun demonstratePerformance() {
        val largeDataset = (1..1_000_000).toList()
        
        // Eager evaluation - processes all elements
        val eagerResult = largeDataset
            .map { it * 2 }      // Processes 1M elements
            .filter { it > 100 } // Processes all mapped elements
            .take(5)             // Takes first 5 from filtered result
        
        // Lazy evaluation - processes only what's needed
        val lazyResult = largeDataset.asSequence()
            .map { it * 2 }      // Processes only as needed
            .filter { it > 100 } // Processes only as needed
            .take(5)             // Stops after finding 5 elements
            .toList()
        
        println("Both produce same result: ${eagerResult == lazyResult}")
    }
    
    // 10. COMBINING LAZY EVALUATION WITH COROUTINES
    suspend fun lazyAsyncSequence(): Sequence<String> = sequence {
        for (i in 1..10) {
            delay(100) // Simulate async work
            yield("Item $i")
        }
    }
    
    // Usage in coroutine
    runBlocking {
        val asyncSequence = lazyAsyncSequence()
        val first3 = asyncSequence.take(3).toList()
        // Only processes first 3 items with delays
    }
    
    // Benefits of lazy evaluation:
    // - Memory efficiency (no intermediate collections)
    // - Performance improvement for large datasets
    // - Ability to work with infinite sequences
    // - Short-circuiting for early termination
    // - Deferred computation until needed
    ```

29. **How do you sort collections in Kotlin?**
    
    **Answer:** Kotlin provides multiple ways to sort collections with different sorting strategies:
    
    ```kotlin
    // 1. BASIC SORTING
    val numbers = listOf(3, 1, 4, 1, 5, 9, 2, 6)
    
    // sorted() - returns new sorted list
    val sortedNumbers = numbers.sorted() // [1, 1, 2, 3, 4, 5, 6, 9]
    val sortedDescending = numbers.sortedDescending() // [9, 6, 5, 4, 3, 2, 1, 1]
    
    // Mutable sorting
    val mutableNumbers = mutableListOf(3, 1, 4, 1, 5, 9, 2, 6)
    mutableNumbers.sort() // Sorts in place
    mutableNumbers.sortDescending() // Sorts in place (descending)
    
    // 2. SORTING WITH CUSTOM COMPARATOR
    val strings = listOf("apple", "Banana", "cherry", "Date")
    
    // Case-insensitive sorting
    val sortedIgnoreCase = strings.sortedWith(String.CASE_INSENSITIVE_ORDER)
    // Result: ["apple", "Banana", "cherry", "Date"]
    
    // Custom comparator
    val sortedByLength = strings.sortedWith(compareBy { it.length })
    // Result: ["Date", "apple", "cherry", "Banana"]
    
    // Multiple criteria sorting
    val sortedByLengthThenAlpha = strings.sortedWith(
        compareBy<String> { it.length }.thenBy { it.lowercase() }
    )
    
    // 3. SORTING OBJECTS
    data class Person(val name: String, val age: Int, val city: String)
    val people = listOf(
        Person("John", 25, "New York"),
        Person("Jane", 30, "London"),
        Person("Bob", 25, "Paris"),
        Person("Alice", 35, "New York")
    )
    
    // Sort by single property
    val sortedByAge = people.sortedBy { it.age }
    val sortedByName = people.sortedBy { it.name }
    
    // Sort by multiple properties
    val sortedByAgeAndName = people.sortedWith(
        compareBy<Person> { it.age }.thenBy { it.name }
    )
    
    // Sort with custom logic
    val sortedByAgeDesc = people.sortedByDescending { it.age }
    val sortedByAgeDescNameAsc = people.sortedWith(
        compareByDescending<Person> { it.age }.thenBy { it.name }
    )
    
    // 4. ADVANCED SORTING TECHNIQUES
    
    // Natural order with nulls
    val nullableNumbers = listOf(3, null, 1, 4, null, 2)
    val sortedWithNulls = nullableNumbers.sortedWith(nullsFirst(naturalOrder()))
    val sortedNullsLast = nullableNumbers.sortedWith(nullsLast(naturalOrder()))
    
    // Custom comparator for complex sorting
    val complexSort = people.sortedWith(
        compareBy<Person> { it.city }
            .thenByDescending { it.age }
            .thenBy { it.name }
    )
    
    // 5. SORTING MAPS
    val map = mapOf("c" to 3, "a" to 1, "b" to 2)
    
    // Sort by keys
    val sortedByKeys = map.toSortedMap() // TreeMap sorted by keys
    val sortedByKeysDesc = map.toList().sortedByDescending { it.first }.toMap()
    
    // Sort by values
    val sortedByValues = map.toList().sortedBy { it.second }.toMap()
    val sortedByValuesDesc = map.toList().sortedByDescending { it.second }.toMap()
    
    // 6. SORTING WITH CUSTOM TYPES
    enum class Priority { LOW, MEDIUM, HIGH }
    
    data class Task(val name: String, val priority: Priority, val dueDate: LocalDate)
    
    val tasks = listOf(
        Task("Task 1", Priority.HIGH, LocalDate.now().plusDays(1)),
        Task("Task 2", Priority.LOW, LocalDate.now().plusDays(3)),
        Task("Task 3", Priority.MEDIUM, LocalDate.now().plusDays(2))
    )
    
    // Sort by enum ordinal
    val sortedByPriority = tasks.sortedBy { it.priority }
    
    // Custom enum sorting
    val priorityOrder = mapOf(Priority.HIGH to 1, Priority.MEDIUM to 2, Priority.LOW to 3)
    val customPrioritySort = tasks.sortedBy { priorityOrder[it.priority] }
    
    // 7. PERFORMANCE CONSIDERATIONS
    
    // For large collections, consider:
    val largeList = (1..1_000_000).shuffled()
    
    // Use sortedWith for complex comparisons
    val efficientSort = largeList.sortedWith(compareBy { it })
    
    // For multiple sorts on same data, sort once and reuse
    val onceSorted = largeList.sorted()
    val filtered1 = onceSorted.filter { it > 500_000 }
    val filtered2 = onceSorted.filter { it < 100_000 }
    
    // 8. SORTING WITH SEQUENCES (for large datasets)
    val largeSortedSequence = (1..1_000_000).asSequence()
        .shuffled()
        .sorted()
        .take(100)
        .toList()
    
    // 9. STABLE SORTING
    // Kotlin's sort is stable (maintains relative order of equal elements)
    data class Student(val name: String, val grade: Int, val id: Int)
    val students = listOf(
        Student("Alice", 85, 1),
        Student("Bob", 85, 2),
        Student("Charlie", 90, 3),
        Student("David", 85, 4)
    )
    
    // Students with same grade maintain their original relative order
    val sortedByGrade = students.sortedBy { it.grade }
    
    // 10. SORTING COLLECTIONS IN PLACE
    val mutableList = mutableListOf(3, 1, 4, 1, 5, 9, 2, 6)
    
    // Sort in place
    mutableList.sort()
    
    // Sort with custom comparator in place
    val mutablePeople = people.toMutableList()
    mutablePeople.sortWith(compareBy { it.age })
    
    // 11. SORTING ARRAYS
    val array = arrayOf(3, 1, 4, 1, 5, 9, 2, 6)
    array.sort() // Sorts in place
    
    val sortedArray = array.sortedArray() // Returns new sorted array
    
    // 12. PRACTICAL EXAMPLES
    
    // Sorting file names naturally
    val fileNames = listOf("file1.txt", "file10.txt", "file2.txt", "file20.txt")
    val naturalSort = fileNames.sortedWith(compareBy { 
        it.replace(Regex("\\d+")) { matchResult ->
            matchResult.value.padStart(10, '0')
        }
    })
    
    // Sorting by multiple criteria with weights
    fun sortByWeightedCriteria(items: List<Person>): List<Person> {
        return items.sortedWith(compareBy<Person> { 
            // Primary: age (weighted more heavily)
            it.age * 10 
        }.thenBy { 
            // Secondary: name length
            it.name.length 
        })
    }
    
    // Best practices:
    // - Use sortedBy/sortedWith for immutable sorting
    // - Use sort/sortWith for in-place sorting of mutable collections
    // - Chain comparators for multiple criteria
    // - Consider performance for large datasets
    // - Use stable sorting when order matters for equal elements
    ```

30. **What is the difference between `groupBy` and `partition`?**
    
    **Answer:** `groupBy` creates multiple groups based on a key function, while `partition` splits a collection into exactly two groups based on a predicate:
    
    ```kotlin
    val numbers = listOf(1, 2, 3, 4, 5, 6, 7, 8, 9, 10)
    
    // PARTITION - splits into exactly 2 groups (true/false)
    val (evens, odds) = numbers.partition { it % 2 == 0 }
    // evens: [2, 4, 6, 8, 10]
    // odds: [1, 3, 5, 7, 9]
    
    // Returns Pair<List<T>, List<T>>
    val partitionResult: Pair<List<Int>, List<Int>> = numbers.partition { it % 2 == 0 }
    val evenNumbers = partitionResult.first
    val oddNumbers = partitionResult.second
    
    // GROUPBY - creates multiple groups based on key
    val groupedByRemainder = numbers.groupBy { it % 3 }
    // Result: {1=[1, 4, 7, 10], 2=[2, 5, 8], 0=[3, 6, 9]}
    
    // Returns Map<K, List<T>>
    val groupResult: Map<Int, List<Int>> = numbers.groupBy { it % 3 }
    
    // DETAILED COMPARISON
    
    // 1. Number of groups
    // partition: Always 2 groups (true/false)
    val partitioned = numbers.partition { it > 5 }
    // Result: Pair([6, 7, 8, 9, 10], [1, 2, 3, 4, 5])
    
    // groupBy: Variable number of groups
    val grouped = numbers.groupBy { 
        when {
            it <= 3 -> "small"
            it <= 7 -> "medium"
            else -> "large"
        }
    }
    // Result: {"small"=[1, 2, 3], "medium"=[4, 5, 6, 7], "large"=[8, 9, 10]}
    
    // 2. Return type
    // partition: Pair<List<T>, List<T>>
    val (positives, negatives) = listOf(-2, -1, 0, 1, 2).partition { it >= 0 }
    
    // groupBy: Map<K, List<T>>
    val groupedBySign = listOf(-2, -1, 0, 1, 2).groupBy { 
        when {
            it > 0 -> "positive"
            it < 0 -> "negative"
            else -> "zero"
        }
    }
    
    // 3. PRACTICAL EXAMPLES
    
    data class Person(val name: String, val age: Int, val department: String)
    val employees = listOf(
        Person("John", 25, "Engineering"),
        Person("Jane", 30, "Marketing"),
        Person("Bob", 35, "Engineering"),
        Person("Alice", 28, "Sales"),
        Person("Charlie", 32, "Marketing"),
        Person("David", 27, "Sales")
    )
    
    // partition: Split into adults and young adults
    val (adults, youngAdults) = employees.partition { it.age >= 30 }
    // adults: [Jane, Bob, Charlie]
    // youngAdults: [John, Alice, David]
    
    // groupBy: Group by department
    val byDepartment = employees.groupBy { it.department }
    // Result: {
    //   "Engineering" = [John, Bob],
    //   "Marketing" = [Jane, Charlie],
    //   "Sales" = [Alice, David]
    // }
    
    // 4. ADVANCED USAGE
    
    // partition with complex predicate
    val words = listOf("apple", "banana", "cherry", "date", "elderberry")
    val (longWords, shortWords) = words.partition { it.length > 5 }
    
    // groupBy with complex key
    val groupedByFirstLetter = words.groupBy { it.first().uppercase() }
    val groupedByLength = words.groupBy { it.length }
    
    // 5. CHAINING OPERATIONS
    
    // After partition
    val processedEvens = numbers.partition { it % 2 == 0 }.first
        .map { it * 2 }
        .filter { it > 10 }
    
    // After groupBy
    val processedGroups = numbers.groupBy { it % 3 }
        .mapValues { (_, values) -> values.map { it * 2 } }
        .filterValues { it.size > 2 }
    
    // 6. PERFORMANCE CONSIDERATIONS
    
    // partition: Single pass through collection
    val largeList = (1..1_000_000).toList()
    val (evenLarge, oddLarge) = largeList.partition { it % 2 == 0 }
    
    // groupBy: Single pass, but creates map structure
    val groupedLarge = largeList.groupBy { it % 10 }
    
    // 7. WORKING WITH RESULTS
    
    // partition results
    val (passed, failed) = listOf(85, 92, 67, 78, 95, 43, 88).partition { it >= 70 }
    println("Passed: ${passed.size}, Failed: ${failed.size}")
    
    // groupBy results
    val gradeGroups = listOf(85, 92, 67, 78, 95, 43, 88).groupBy { 
        when {
            it >= 90 -> "A"
            it >= 80 -> "B"
            it >= 70 -> "C"
            else -> "F"
        }
    }
    gradeGroups.forEach { (grade, scores) ->
        println("Grade $grade: ${scores.size} students")
    }
    
    // 8. EMPTY COLLECTIONS HANDLING
    
    // partition: Both lists can be empty
    val emptyList = emptyList<Int>()
    val (empty1, empty2) = emptyList.partition { it > 0 }
    // Both empty1 and empty2 are empty lists
    
    // groupBy: Returns empty map
    val emptyGroups = emptyList.groupBy { it % 2 }
    // Returns empty map: {}
    
    // 9. NULLABLE HANDLING
    
    val nullableNumbers = listOf(1, null, 2, null, 3, 4)
    
    // partition with nulls
    val (nonNulls, nulls) = nullableNumbers.partition { it != null }
    
    // groupBy with nulls
    val groupedWithNulls = nullableNumbers.groupBy { it ?: -1 }
    
    // 10. WHEN TO USE EACH
    
    // Use partition when:
    // - You need exactly 2 groups
    // - Binary classification (true/false, pass/fail, etc.)
    // - Simple boolean condition
    // - Want to destructure into two variables
    
    // Use groupBy when:
    // - You need multiple groups
    // - Grouping by categories, types, or ranges
    // - Complex classification logic
    // - Want to access groups by key
    // - Need to process each group differently
    
    // 11. COMBINING BOTH
    
    // First group, then partition each group
    val departmentGroups = employees.groupBy { it.department }
    val departmentAgeGroups = departmentGroups.mapValues { (_, people) ->
        people.partition { it.age >= 30 }
    }
    
    // First partition, then group each partition
    val (senior, junior) = employees.partition { it.age >= 30 }
    val seniorByDept = senior.groupBy { it.department }
    val juniorByDept = junior.groupBy { it.department }
    ```

### Object-Oriented Programming

31. **Explain inheritance in Kotlin**
    
    **Answer:** Inheritance in Kotlin allows classes to inherit properties and methods from other classes. All classes inherit from `Any` by default, and classes are final by default.
    
    ```kotlin
    // Base class (must be open to be inherited)
    open class Animal(val name: String) {
        open fun makeSound() {
            println("$name makes a sound")
        }
        
        fun sleep() {
            println("$name is sleeping")
        }
    }
    
    // Derived class
    class Dog(name: String, val breed: String) : Animal(name) {
        override fun makeSound() {
            println("$name barks")
        }
        
        fun wagTail() {
            println("$name wags tail")
        }
    }
    
    // Usage
    val dog = Dog("Buddy", "Golden Retriever")
    dog.makeSound() // "Buddy barks"
    dog.sleep()     // "Buddy is sleeping"
    dog.wagTail()   // "Buddy wags tail"
    
    // Multiple inheritance levels
    open class Mammal(name: String) : Animal(name) {
        open fun giveBirth() {
            println("$name gives birth to live young")
        }
    }
    
    class Cat(name: String) : Mammal(name) {
        override fun makeSound() {
            println("$name meows")
        }
        
        override fun giveBirth() {
            super.giveBirth() // Call parent implementation
            println("$name has kittens")
        }
    }
    
    // Constructor inheritance
    open class Vehicle(val brand: String, val model: String) {
        init {
            println("Vehicle created: $brand $model")
        }
        
        open fun start() {
            println("$brand $model is starting")
        }
    }
    
    class Car(brand: String, model: String, val doors: Int) : Vehicle(brand, model) {
        init {
            println("Car created with $doors doors")
        }
        
        override fun start() {
            super.start()
            println("Engine started")
        }
    }
    
    // Inheritance with properties
    open class Person(open val name: String, open val age: Int) {
        open fun introduce() {
            println("Hi, I'm $name and I'm $age years old")
        }
    }
    
    class Student(
        override val name: String,
        override val age: Int,
        val studentId: String
    ) : Person(name, age) {
        override fun introduce() {
            super.introduce()
            println("My student ID is $studentId")
        }
    }
    ```

32. **What is the `open` keyword?**
    
    **Answer:** The `open` keyword makes classes, methods, and properties available for inheritance and overriding. By default, all classes in Kotlin are final.
    
    ```kotlin
    // Classes
    open class BaseClass {
        // This class can be inherited
    }
    
    class FinalClass : BaseClass() {
        // This class cannot be inherited (final by default)
    }
    
    // Methods
    open class Parent {
        open fun overridableMethod() {
            println("Can be overridden")
        }
        
        fun finalMethod() {
            println("Cannot be overridden")
        }
    }
    
    class Child : Parent() {
        override fun overridableMethod() {
            println("Overridden method")
        }
        
        // override fun finalMethod() // Compilation error
    }
    
    // Properties
    open class Shape {
        open val name: String = "Shape"
        open var color: String = "White"
        
        val id: Int = 1 // Final property
    }
    
    class Circle : Shape() {
        override val name: String = "Circle"
        override var color: String = "Red"
        
        // override val id: Int = 2 // Compilation error
    }
    
    // Abstract classes (implicitly open)
    abstract class AbstractClass {
        abstract fun abstractMethod()
        
        open fun openMethod() {
            println("Can be overridden")
        }
        
        fun finalMethod() {
            println("Cannot be overridden")
        }
    }
    
    // Interfaces (implicitly open)
    interface MyInterface {
        fun interfaceMethod() // Implicitly open
        
        fun defaultImplementation() {
            println("Default implementation")
        }
    }
    
    // Sealing inheritance
    class SealedChild : Parent() {
        final override fun overridableMethod() {
            println("Final override - cannot be overridden further")
        }
    }
    
    // When to use open:
    // - When designing for inheritance
    // - Creating base classes for frameworks
    // - Template method pattern
    // - Polymorphic behavior needed
    ```

33. **How do you override methods and properties?**
    
    **Answer:** Use the `override` keyword to override methods and properties from parent classes or interfaces. The parent member must be marked as `open` or `abstract`.
    
    ```kotlin
    // Method overriding
    open class Animal {
        open fun makeSound() {
            println("Animal makes a sound")
        }
        
        open fun move() {
            println("Animal moves")
        }
    }
    
    class Dog : Animal() {
        override fun makeSound() {
            println("Dog barks")
        }
        
        override fun move() {
            super.move() // Call parent implementation
            println("Dog runs")
        }
    }
    
    // Property overriding
    open class Vehicle {
        open val maxSpeed: Int = 100
        open var fuel: String = "Gasoline"
    }
    
    class ElectricCar : Vehicle() {
        override val maxSpeed: Int = 200
        override var fuel: String = "Electric"
            get() = "Battery: $field"
            set(value) {
                field = "Battery: $value"
            }
    }
    
    // Interface implementation
    interface Flyable {
        fun fly()
        
        fun land() {
            println("Landing")
        }
    }
    
    interface Swimmable {
        fun swim()
    }
    
    class Duck : Animal(), Flyable, Swimmable {
        override fun makeSound() {
            println("Duck quacks")
        }
        
        override fun fly() {
            println("Duck flies")
        }
        
        override fun swim() {
            println("Duck swims")
        }
        
        override fun land() {
            super.land()
            println("Duck lands on water")
        }
    }
    
    // Abstract class overriding
    abstract class Shape {
        abstract val area: Double
        abstract fun draw()
        
        open fun describe() {
            println("This is a shape with area $area")
        }
    }
    
    class Rectangle(val width: Double, val height: Double) : Shape() {
        override val area: Double
            get() = width * height
        
        override fun draw() {
            println("Drawing rectangle")
        }
        
        override fun describe() {
            super.describe()
            println("Rectangle: ${width}x${height}")
        }
    }
    
    // Multiple interface conflicts
    interface A {
        fun method() {
            println("A")
        }
    }
    
    interface B {
        fun method() {
            println("B")
        }
    }
    
    class C : A, B {
        override fun method() {
            super<A>.method()
            super<B>.method()
            println("C")
        }
    }
    
    // Property overriding with different types
    open class Base {
        open val property: Any = "Base"
    }
    
    class Derived : Base() {
        override val property: String = "Derived" // More specific type
    }
    
    // Constructor property overriding
    open class Person(open val name: String)
    
    class Student(override val name: String, val grade: Int) : Person(name)
    
    // Lateinit property overriding
    open class Component {
        open lateinit var context: String
    }
    
    class AndroidComponent : Component() {
        override lateinit var context: String
    }
    ```

34. **What are abstract classes vs interfaces?**
    
    **Answer:** Abstract classes provide partial implementation and single inheritance, while interfaces define contracts and support multiple inheritance.
    
    ```kotlin
    // ABSTRACT CLASS
    abstract class Animal(val name: String) {
        // Concrete property
        val id: Int = generateId()
        
        // Abstract property
        abstract val species: String
        
        // Concrete method
        fun sleep() {
            println("$name is sleeping")
        }
        
        // Abstract method
        abstract fun makeSound()
        
        // Open method (can be overridden)
        open fun move() {
            println("$name is moving")
        }
        
        // Private method
        private fun generateId(): Int = (1..1000).random()
    }
    
    // INTERFACE
    interface Flyable {
        // Abstract property
        val wingSpan: Double
        
        // Abstract method
        fun fly()
        
        // Default implementation
        fun takeOff() {
            println("Taking off with wingspan $wingSpan")
        }
        
        // Property with default implementation
        val canFly: Boolean
            get() = wingSpan > 0
    }
    
    interface Swimmable {
        fun swim()
        
        fun dive() {
            println("Diving underwater")
        }
    }
    
    // Implementation
    class Bird(name: String, override val wingSpan: Double) : Animal(name), Flyable, Swimmable {
        override val species: String = "Bird"
        
        override fun makeSound() {
            println("$name chirps")
        }
        
        override fun fly() {
            println("$name flies with $wingSpan wingspan")
        }
        
        override fun swim() {
            println("$name swims")
        }
        
        override fun move() {
            super.move()
            if (canFly) fly()
        }
    }
    
    // KEY DIFFERENCES:
    
    // 1. INHERITANCE
    // Abstract class: Single inheritance
    abstract class Vehicle(val brand: String) {
        abstract fun start()
    }
    
    // Interface: Multiple inheritance
    interface Engine {
        fun start()
    }
    
    interface GPS {
        fun navigate()
    }
    
    class Car(brand: String) : Vehicle(brand), Engine, GPS {
        override fun start() {
            println("Car engine started")
        }
        
        override fun navigate() {
            println("GPS navigation active")
        }
    }
    
    // 2. STATE AND CONSTRUCTORS
    // Abstract class: Can have state and constructors
    abstract class DatabaseConnection(val url: String) {
        protected var isConnected: Boolean = false
        
        init {
            println("Database connection initialized")
        }
        
        abstract fun connect()
        
        fun disconnect() {
            isConnected = false
            println("Disconnected from $url")
        }
    }
    
    // Interface: No state or constructors
    interface Repository {
        // val url: String // Cannot have backing field
        val url: String
            get() = "default-url" // Only computed properties
        
        fun save(data: Any)
        fun load(): Any
    }
    
    // 3. ACCESS MODIFIERS
    // Abstract class: All access modifiers
    abstract class Service {
        protected abstract fun internalMethod()
        public abstract fun publicMethod()
        private fun privateMethod() {}
    }
    
    // Interface: Only public (implicitly)
    interface API {
        fun method() // Implicitly public
        // protected fun protectedMethod() // Compilation error
    }
    
    // 4. PRACTICAL EXAMPLES
    
    // Use abstract class for shared implementation
    abstract class HttpClient {
        protected val timeout: Int = 30
        
        abstract fun request(url: String): String
        
        fun requestWithRetry(url: String, retries: Int = 3): String {
            repeat(retries) {
                try {
                    return request(url)
                } catch (e: Exception) {
                    if (it == retries - 1) throw e
                }
            }
            throw RuntimeException("Max retries exceeded")
        }
    }
    
    // Use interface for contracts
    interface JsonParser {
        fun <T> parse(json: String, type: Class<T>): T
        fun toJson(obj: Any): String
    }
    
    interface Logger {
        fun log(message: String)
        
        fun logError(message: String) {
            log("ERROR: $message")
        }
    }
    
    // Implementation combining both
    class RestClient : HttpClient(), JsonParser, Logger {
        override fun request(url: String): String {
            log("Making request to $url")
            return "Response from $url"
        }
        
        override fun <T> parse(json: String, type: Class<T>): T {
            log("Parsing JSON to ${type.simpleName}")
            // Implementation
            return type.newInstance()
        }
        
        override fun toJson(obj: Any): String {
            log("Converting ${obj::class.simpleName} to JSON")
            return "{}"
        }
        
        override fun log(message: String) {
            println("[RestClient] $message")
        }
    }
    
    // WHEN TO USE EACH:
    
    // Use Abstract Class when:
    // - You have shared implementation
    // - You need constructors with parameters
    // - You want to control access modifiers
    // - You're modeling "is-a" relationships
    // - You need to maintain state
    
    // Use Interface when:
    // - You're defining contracts/capabilities
    // - You need multiple inheritance
    // - You're modeling "can-do" relationships
    // - You want maximum flexibility
    // - You're designing for testability
    ```

35. **Explain delegation in Kotlin**
    
    **Answer:** Delegation allows an object to delegate some of its responsibilities to another object. Kotlin supports class delegation and property delegation.
    
    ```kotlin
    // CLASS DELEGATION
    
    // Interface to delegate
    interface Printer {
        fun print(message: String)
    }
    
    // Implementation to delegate to
    class ConsolePrinter : Printer {
        override fun print(message: String) {
            println("Console: $message")
        }
    }
    
    class FilePrinter : Printer {
        override fun print(message: String) {
            println("File: $message")
        }
    }
    
    // Delegating class
    class Logger(printer: Printer) : Printer by printer {
        fun logInfo(message: String) {
            print("INFO: $message") // Delegates to printer
        }
        
        fun logError(message: String) {
            print("ERROR: $message") // Delegates to printer
        }
    }
    
    // Usage
    val consoleLogger = Logger(ConsolePrinter())
    val fileLogger = Logger(FilePrinter())
    
    consoleLogger.print("Direct print") // "Console: Direct print"
    consoleLogger.logInfo("Info message") // "Console: INFO: Info message"
    
    // PROPERTY DELEGATION
    
    // Built-in delegates
    class User {
        // Lazy initialization
        val expensiveProperty: String by lazy {
            println("Computing expensive property")
            "Computed value"
        }
        
        // Observable property
        var name: String by Delegates.observable("Initial") { property, oldValue, newValue ->
            println("${property.name} changed from $oldValue to $newValue")
        }
        
        // Vetoable property
        var age: Int by Delegates.vetoable(0) { property, oldValue, newValue ->
            newValue >= 0 // Only allow non-negative ages
        }
        
        // Not null property
        var email: String by Delegates.notNull()
    }
    
    // CUSTOM PROPERTY DELEGATES
    
    // Custom delegate for validation
    class ValidatedDelegate<T>(
        private var value: T,
        private val validator: (T) -> Boolean
    ) {
        operator fun getValue(thisRef: Any?, property: KProperty<*>): T {
            return value
        }
        
        operator fun setValue(thisRef: Any?, property: KProperty<*>, value: T) {
            if (validator(value)) {
                this.value = value
            } else {
                throw IllegalArgumentException("Invalid value: $value")
            }
        }
    }
    
    // Usage of custom delegate
    class Person {
        var name: String by ValidatedDelegate("") { it.isNotBlank() }
        var age: Int by ValidatedDelegate(0) { it >= 0 }
    }
    
    // ADVANCED DELEGATION PATTERNS
    
    // Multiple interface delegation
    interface Speaker {
        fun speak()
    }
    
    interface Walker {
        fun walk()
    }
    
    class Human : Speaker, Walker {
        override fun speak() = println("Human speaks")
        override fun walk() = println("Human walks")
    }
    
    class Robot(
        private val speaker: Speaker,
        private val walker: Walker
    ) : Speaker by speaker, Walker by walker {
        
        fun performTasks() {
            speak() // Delegates to speaker
            walk()  // Delegates to walker
        }
    }
    
    // Delegation with override
    class SmartLogger(private val printer: Printer) : Printer by printer {
        override fun print(message: String) {
            val timestamp = System.currentTimeMillis()
            printer.print("[$timestamp] $message")
        }
    }
    
    // Map delegation
    class Settings(private val map: MutableMap<String, Any>) {
        var theme: String by map
        var fontSize: Int by map
        var autoSave: Boolean by map
    }
    
    // Usage
    val settingsMap = mutableMapOf<String, Any>(
        "theme" to "dark",
        "fontSize" to 12,
        "autoSave" to true
    )
    val settings = Settings(settingsMap)
    println(settings.theme) // "dark"
    settings.fontSize = 14
    println(settingsMap["fontSize"]) // 14
    
    // DELEGATION FOR COMPOSITION OVER INHERITANCE
    
    // Instead of inheritance
    abstract class Vehicle {
        abstract fun start()
        abstract fun stop()
    }
    
    class Car : Vehicle() {
        override fun start() = println("Car started")
        override fun stop() = println("Car stopped")
    }
    
    // Use delegation for composition
    interface Engine {
        fun start()
        fun stop()
    }
    
    class GasEngine : Engine {
        override fun start() = println("Gas engine started")
        override fun stop() = println("Gas engine stopped")
    }
    
    class ElectricEngine : Engine {
        override fun start() = println("Electric engine started")
        override fun stop() = println("Electric engine stopped")
    }
    
    class ModernCar(engine: Engine) : Engine by engine {
        fun drive() {
            start() // Delegates to engine
            println("Driving...")
            stop()  // Delegates to engine
        }
    }
    
    // PROPERTY DELEGATION PATTERNS
    
    // Preference delegation
    class PreferenceDelegate<T>(
        private val key: String,
        private val defaultValue: T,
        private val preferences: SharedPreferences
    ) {
        operator fun getValue(thisRef: Any?, property: KProperty<*>): T {
            return when (defaultValue) {
                is String -> preferences.getString(key, defaultValue) as T
                is Int -> preferences.getInt(key, defaultValue) as T
                is Boolean -> preferences.getBoolean(key, defaultValue) as T
                else -> defaultValue
            }
        }
        
        operator fun setValue(thisRef: Any?, property: KProperty<*>, value: T) {
            preferences.edit().apply {
                when (value) {
                    is String -> putString(key, value)
                    is Int -> putInt(key, value)
                    is Boolean -> putBoolean(key, value)
                }
                apply()
            }
        }
    }
    
    // Benefits of delegation:
    // - Composition over inheritance
    // - Code reuse without inheritance
    // - Flexible object behavior
    // - Cleaner property management
    // - Separation of concerns
    ```

36. **What is the `by` keyword used for?**
    
    **Answer:** The `by` keyword is used for delegation in Kotlin - both class delegation and property delegation.
    
    ```kotlin
    // 1. CLASS DELEGATION
    
    interface Repository {
        fun save(data: String)
        fun load(): String
    }
    
    class DatabaseRepository : Repository {
        override fun save(data: String) {
            println("Saving to database: $data")
        }
        
        override fun load(): String {
            return "Data from database"
        }
    }
    
    // Delegate interface implementation
    class CachedRepository(
        private val repository: Repository
    ) : Repository by repository {
        
        private var cache: String? = null
        
        override fun load(): String {
            return cache ?: repository.load().also { cache = it }
        }
        
        fun clearCache() {
            cache = null
        }
    }
    
    // 2. PROPERTY DELEGATION
    
    class User {
        // Lazy delegation
        val expensiveComputation: String by lazy {
            println("Computing...")
            Thread.sleep(1000)
            "Expensive result"
        }
        
        // Observable delegation
        var name: String by Delegates.observable("Unknown") { prop, old, new ->
            println("Name changed from $old to $new")
        }
        
        // Vetoable delegation
        var age: Int by Delegates.vetoable(0) { prop, old, new ->
            new >= 0 // Only allow non-negative ages
        }
        
        // Not null delegation
        var email: String by Delegates.notNull()
    }
    
    // 3. CUSTOM PROPERTY DELEGATES
    
    // Simple custom delegate
    class StringDelegate(private var value: String = "") {
        operator fun getValue(thisRef: Any?, property: KProperty<*>): String {
            println("Getting ${property.name}: $value")
            return value
        }
        
        operator fun setValue(thisRef: Any?, property: KProperty<*>, value: String) {
            println("Setting ${property.name} to $value")
            this.value = value
        }
    }
    
    class Example {
        var delegatedProperty: String by StringDelegate()
    }
    
    // 4. MAP DELEGATION
    
    class Person(private val map: Map<String, Any>) {
        val name: String by map
        val age: Int by map
        val email: String by map
    }
    
    // Usage
    val personData = mapOf(
        "name" to "John",
        "age" to 30,
        "email" to "john@example.com"
    )
    val person = Person(personData)
    println(person.name) // "John"
    
    // Mutable map delegation
    class MutablePerson(private val map: MutableMap<String, Any>) {
        var name: String by map
        var age: Int by map
        var email: String by map
    }
    
    // 5. MULTIPLE INTERFACE DELEGATION
    
    interface Clickable {
        fun click()
    }
    
    interface Focusable {
        fun focus()
        fun blur()
    }
    
    class Button : Clickable, Focusable {
        override fun click() = println("Button clicked")
        override fun focus() = println("Button focused")
        override fun blur() = println("Button blurred")
    }
    
    class EnhancedButton(
        clickable: Clickable,
        focusable: Focusable
    ) : Clickable by clickable, Focusable by focusable {
        
        fun performAction() {
            focus()
            click()
            blur()
        }
    }
    
    // 6. DELEGATION WITH OVERRIDE
    
    interface Logger {
        fun log(message: String)
    }
    
    class ConsoleLogger : Logger {
        override fun log(message: String) {
            println("Console: $message")
        }
    }
    
    class TimestampLogger(private val logger: Logger) : Logger by logger {
        override fun log(message: String) {
            val timestamp = System.currentTimeMillis()
            logger.log("[$timestamp] $message")
        }
    }
    
    // 7. ADVANCED PROPERTY DELEGATION
    
    // Synchronized property
    class SynchronizedDelegate<T>(private var value: T) {
        private val lock = Any()
        
        operator fun getValue(thisRef: Any?, property: KProperty<*>): T {
            synchronized(lock) {
                return value
            }
        }
        
        operator fun setValue(thisRef: Any?, property: KProperty<*>, value: T) {
            synchronized(lock) {
                this.value = value
            }
        }
    }
    
    // Validated property
    class ValidatedDelegate<T>(
        private var value: T,
        private val validator: (T) -> Boolean
    ) {
        operator fun getValue(thisRef: Any?, property: KProperty<*>): T = value
        
        operator fun setValue(thisRef: Any?, property: KProperty<*>, value: T) {
            if (validator(value)) {
                this.value = value
            } else {
                throw IllegalArgumentException("Invalid value: $value")
            }
        }
    }
    
    class Product {
        var name: String by ValidatedDelegate("") { it.isNotBlank() }
        var price: Double by ValidatedDelegate(0.0) { it >= 0.0 }
        var quantity: Int by SynchronizedDelegate(0)
    }
    
    // 8. PROVIDER DELEGATES
    
    class ProviderDelegate<T>(private val provider: () -> T) {
        operator fun getValue(thisRef: Any?, property: KProperty<*>): T {
            return provider()
        }
    }
    
    fun <T> provided(provider: () -> T) = ProviderDelegate(provider)
    
    class Service {
        val currentTime: Long by provided { System.currentTimeMillis() }
        val randomNumber: Int by provided { (1..100).random() }
    }
    
    // 9. DELEGATION PATTERNS
    
    // Delegation chain
    class LoggingRepository(
        private val repository: Repository
    ) : Repository by repository {
        
        override fun save(data: String) {
            println("Logging: About to save $data")
            repository.save(data)
            println("Logging: Save completed")
        }
        
        override fun load(): String {
            println("Logging: About to load")
            val result = repository.load()
            println("Logging: Load completed")
            return result
        }
    }
    
    // Usage examples
    fun demonstrateDelegation() {
        // Class delegation
        val dbRepo = DatabaseRepository()
        val cachedRepo = CachedRepository(dbRepo)
        val loggingRepo = LoggingRepository(cachedRepo)
        
        loggingRepo.save("test data")
        println(loggingRepo.load())
        
        // Property delegation
        val user = User()
        user.name = "John" // Triggers observable
        user.age = 25      // Triggers vetoable
        user.email = "john@example.com"
        
        println(user.expensiveComputation) // Triggers lazy computation
    }
    
    // Benefits of 'by' keyword:
    // - Composition over inheritance
    // - Code reuse and modularity
    // - Cleaner property management
    // - Separation of concerns
    // - Flexible object behavior
    // - Reduced boilerplate code
    ```

37. **How do you implement multiple interfaces?**
    
    **Answer:** Kotlin supports multiple interface inheritance. A class can implement multiple interfaces by separating them with commas.
    
    ```kotlin
    // 1. BASIC MULTIPLE INTERFACE IMPLEMENTATION
    
    interface Drawable {
        fun draw()
        
        fun getColor(): String {
            return "Default color"
        }
    }
    
    interface Clickable {
        fun click()
        
        fun showRipple() {
            println("Showing ripple effect")
        }
    }
    
    interface Focusable {
        fun focus()
        fun blur()
        
        val canReceiveFocus: Boolean
            get() = true
    }
    
    // Implementing multiple interfaces
    class Button : Drawable, Clickable, Focusable {
        override fun draw() {
            println("Drawing button")
        }
        
        override fun click() {
            println("Button clicked")
        }
        
        override fun focus() {
            println("Button focused")
        }
        
        override fun blur() {
            println("Button blurred")
        }
        
        override val canReceiveFocus: Boolean = true
    }
    
    // 2. HANDLING METHOD CONFLICTS
    
    interface A {
        fun method() {
            println("Method from A")
        }
        
        fun uniqueA() {
            println("Unique to A")
        }
    }
    
    interface B {
        fun method() {
            println("Method from B")
        }
        
        fun uniqueB() {
            println("Unique to B")
        }
    }
    
    class ConflictResolver : A, B {
        override fun method() {
            super<A>.method()
            super<B>.method()
            println("Resolved in ConflictResolver")
        }
    }
    
    // 3. REAL-WORLD EXAMPLE: MULTIMEDIA PLAYER
    
    interface MediaPlayer {
        fun play()
        fun pause()
        fun stop()
        
        val isPlaying: Boolean
        val duration: Long
    }
    
    interface VolumeControl {
        fun setVolume(level: Int)
        fun mute()
        fun unmute()
        
        val currentVolume: Int
        val isMuted: Boolean
    }
    
    interface PlaylistManager {
        fun addToPlaylist(item: String)
        fun removeFromPlaylist(item: String)
        fun nextTrack()
        fun previousTrack()
        
        val currentTrack: String?
        val playlistSize: Int
    }
    
    class MusicPlayer : MediaPlayer, VolumeControl, PlaylistManager {
        private var _isPlaying = false
        private var _currentVolume = 50
        private var _isMuted = false
        private val playlist = mutableListOf<String>()
        private var currentTrackIndex = 0
        
        // MediaPlayer implementation
        override fun play() {
            _isPlaying = true
            println("Playing: ${currentTrack ?: "No track selected"}")
        }
        
        override fun pause() {
            _isPlaying = false
            println("Paused")
        }
        
        override fun stop() {
            _isPlaying = false
            currentTrackIndex = 0
            println("Stopped")
        }
        
        override val isPlaying: Boolean
            get() = _isPlaying
        
        override val duration: Long
            get() = 180000 // 3 minutes in milliseconds
        
        // VolumeControl implementation
        override fun setVolume(level: Int) {
            _currentVolume = level.coerceIn(0, 100)
            println("Volume set to $_currentVolume")
        }
        
        override fun mute() {
            _isMuted = true
            println("Muted")
        }
        
        override fun unmute() {
            _isMuted = false
            println("Unmuted")
        }
        
        override val currentVolume: Int
            get() = if (_isMuted) 0 else _currentVolume
        
        override val isMuted: Boolean
            get() = _isMuted
        
        // PlaylistManager implementation
        override fun addToPlaylist(item: String) {
            playlist.add(item)
            println("Added '$item' to playlist")
        }
        
        override fun removeFromPlaylist(item: String) {
            playlist.remove(item)
            println("Removed '$item' from playlist")
        }
        
        override fun nextTrack() {
            if (playlist.isNotEmpty()) {
                currentTrackIndex = (currentTrackIndex + 1) % playlist.size
                println("Next track: ${currentTrack}")
            }
        }
        
        override fun previousTrack() {
            if (playlist.isNotEmpty()) {
                currentTrackIndex = if (currentTrackIndex == 0) playlist.size - 1 else currentTrackIndex - 1
                println("Previous track: ${currentTrack}")
            }
        }
        
        override val currentTrack: String?
            get() = playlist.getOrNull(currentTrackIndex)
        
        override val playlistSize: Int
            get() = playlist.size
    }
    
    // 4. INTERFACE SEGREGATION PRINCIPLE
    
    // Bad: Fat interface
    interface BadWorker {
        fun work()
        fun eat()
        fun sleep()
    }
    
    // Good: Segregated interfaces
    interface Worker {
        fun work()
    }
    
    interface Eater {
        fun eat()
    }
    
    interface Sleeper {
        fun sleep()
    }
    
    class Human : Worker, Eater, Sleeper {
        override fun work() = println("Human working")
        override fun eat() = println("Human eating")
        override fun sleep() = println("Human sleeping")
    }
    
    class Robot : Worker {
        override fun work() = println("Robot working")
        // Robot doesn't need to eat or sleep
    }
    
    // 5. MIXIN PATTERN WITH INTERFACES
    
    interface Loggable {
        fun log(message: String) {
            println("[${this::class.simpleName}] $message")
        }
    }
    
    interface Cacheable {
        fun cache(key: String, value: Any) {
            println("Caching $key: $value")
        }
        
        fun getCached(key: String): Any? {
            println("Getting cached value for $key")
            return null
        }
    }
    
    interface Validatable {
        fun validate(): Boolean {
            println("Validating ${this::class.simpleName}")
            return true
        }
    }
    
    class UserService : Loggable, Cacheable, Validatable {
        fun createUser(name: String): User {
            log("Creating user: $name")
            
            if (!validate()) {
                log("Validation failed")
                throw IllegalArgumentException("Invalid user data")
            }
            
            val user = User(name)
            cache("user_$name", user)
            
            log("User created successfully")
            return user
        }
    }
    
    // 6. FUNCTIONAL INTERFACES (SAM interfaces)
    
    fun interface EventListener {
        fun onEvent(event: String)
    }
    
    fun interface DataProcessor {
        fun process(data: String): String
    }
    
    class EventHandler : EventListener, DataProcessor {
        override fun onEvent(event: String) {
            println("Handling event: $event")
        }
        
        override fun process(data: String): String {
            return "Processed: $data"
        }
    }
    
    // 7. INTERFACE WITH GENERIC TYPES
    
    interface Repository<T> {
        fun save(item: T)
        fun findById(id: String): T?
        fun findAll(): List<T>
        fun delete(id: String)
    }
    
    interface Searchable<T> {
        fun search(query: String): List<T>
    }
    
    interface Sortable<T> {
        fun sort(items: List<T>, comparator: Comparator<T>): List<T>
    }
    
    class UserRepository : Repository<User>, Searchable<User>, Sortable<User> {
        private val users = mutableMapOf<String, User>()
        
        override fun save(item: User) {
            users[item.id] = item
        }
        
        override fun findById(id: String): User? = users[id]
        
        override fun findAll(): List<User> = users.values.toList()
        
        override fun delete(id: String) {
            users.remove(id)
        }
        
        override fun search(query: String): List<User> {
            return users.values.filter { it.name.contains(query, ignoreCase = true) }
        }
        
        override fun sort(items: List<User>, comparator: Comparator<User>): List<User> {
            return items.sortedWith(comparator)
        }
    }
    
    // 8. DELEGATION WITH MULTIPLE INTERFACES
    
    class SmartButton(
        private val drawable: Drawable,
        private val clickable: Clickable,
        private val focusable: Focusable
    ) : Drawable by drawable, Clickable by clickable, Focusable by focusable {
        
        fun performCompleteAction() {
            focus()
            showRipple()
            click()
            draw()
            blur()
        }
    }
    
    // Usage example
    fun demonstrateMultipleInterfaces() {
        val musicPlayer = MusicPlayer()
        
        // Use as MediaPlayer
        musicPlayer.addToPlaylist("Song 1")
        musicPlayer.addToPlaylist("Song 2")
        musicPlayer.play()
        
        // Use as VolumeControl
        musicPlayer.setVolume(75)
        musicPlayer.mute()
        
        // Use as PlaylistManager
        musicPlayer.nextTrack()
        musicPlayer.previousTrack()
        
        // All interfaces work together
        println("Currently playing: ${musicPlayer.currentTrack}")
        println("Volume: ${musicPlayer.currentVolume}")
        println("Is playing: ${musicPlayer.isPlaying}")
    }
    
    // Benefits of multiple interface implementation:
    // - Flexible design and composition
    // - Interface segregation principle
    // - Mixin-like behavior
    // - Contract-based programming
    // - Better testability
    // - Loose coupling
    ```

38. **What are companion objects?**
    
    **Answer:** Companion objects are singleton objects tied to a class, similar to static members in Java. They provide class-level functionality without needing an instance.
    
    ```kotlin
    // 1. BASIC COMPANION OBJECT
    
    class MyClass {
        companion object {
            const val CONSTANT = "constant value"
            
            fun staticMethod() {
                println("This is like a static method")
            }
            
            var counter = 0
                private set
            
            fun incrementCounter() {
                counter++
            }
        }
        
        fun instanceMethod() {
            println("This is an instance method")
            staticMethod() // Can call companion object methods
        }
    }
    
    // Usage
    MyClass.staticMethod() // Called without creating instance
    println(MyClass.CONSTANT) // Access constant
    MyClass.incrementCounter()
    println(MyClass.counter) // 1
    
    // 2. NAMED COMPANION OBJECTS
    
    class Database {
        companion object Factory {
            fun create(url: String): Database {
                println("Creating database connection to $url")
                return Database()
            }
            
            fun createInMemory(): Database {
                println("Creating in-memory database")
                return Database()
            }
        }
    }
    
    // Usage - both ways work
    val db1 = Database.create("localhost:5432")
    val db2 = Database.Factory.create("localhost:5432")
    
    // 3. COMPANION OBJECT WITH INTERFACE
    
    interface JsonSerializer {
        fun toJson(): String
    }
    
    class User(val name: String, val email: String) {
        companion object : JsonSerializer {
            override fun toJson(): String {
                return """{"type": "User", "version": "1.0"}"""
            }
            
            fun fromJson(json: String): User {
                // Parse JSON and create User
                return User("John", "john@example.com")
            }
        }
        
        fun toJson(): String {
            return """{"name": "$name", "email": "$email"}"""
        }
    }
    
    // Usage
    val userMetadata = User.toJson() // Companion object method
    val user = User("Jane", "jane@example.com")
    val userData = user.toJson() // Instance method
    
    // 4. FACTORY PATTERN WITH COMPANION OBJECTS
    
    sealed class Shape {
        abstract val area: Double
        
        companion object {
            fun createCircle(radius: Double): Shape = Circle(radius)
            fun createRectangle(width: Double, height: Double): Shape = Rectangle(width, height)
            fun createSquare(side: Double): Shape = Rectangle(side, side)
        }
    }
    
    data class Circle(val radius: Double) : Shape() {
        override val area: Double
            get() = Math.PI * radius * radius
    }
    
    data class Rectangle(val width: Double, val height: Double) : Shape() {
        override val area: Double
            get() = width * height
    }
    
    // Usage
    val circle = Shape.createCircle(5.0)
    val rectangle = Shape.createRectangle(10.0, 5.0)
    val square = Shape.createSquare(4.0)
    
    // 5. SINGLETON PATTERN
    
    class NetworkManager {
        companion object {
            @Volatile
            private var INSTANCE: NetworkManager? = null
            
            fun getInstance(): NetworkManager {
                return INSTANCE ?: synchronized(this) {
                    INSTANCE ?: NetworkManager().also { INSTANCE = it }
                }
            }
        }
        
        fun makeRequest(url: String) {
            println("Making request to $url")
        }
    }
    
    // Better singleton using object
    object BetterNetworkManager {
        fun makeRequest(url: String) {
            println("Making request to $url")
        }
    }
    
    // 6. CONSTANTS AND CONFIGURATION
    
    class ApiConfig {
        companion object {
            const val BASE_URL = "https://api.example.com"
            const val API_VERSION = "v1"
            const val TIMEOUT = 30000
            
            val HEADERS = mapOf(
                "Content-Type" to "application/json",
                "Accept" to "application/json"
            )
            
            fun getEndpoint(path: String): String {
                return "$BASE_URL/$API_VERSION/$path"
            }
        }
    }
    
    // 7. EXTENSION FUNCTIONS ON COMPANION OBJECTS
    
    class MathUtils {
        companion object {
            fun add(a: Int, b: Int): Int = a + b
        }
    }
    
    // Extension function on companion object
    fun MathUtils.Companion.multiply(a: Int, b: Int): Int = a * b
    
    // Usage
    val sum = MathUtils.add(5, 3)
    val product = MathUtils.multiply(5, 3)
    
    // 8. COMPANION OBJECT WITH GENERICS
    
    class Container<T>(private val value: T) {
        companion object {
            fun <T> empty(): Container<T?> = Container(null)
            
            fun <T> of(value: T): Container<T> = Container(value)
            
            fun <T> fromList(list: List<T>): Container<List<T>> = Container(list)
        }
        
        fun get(): T = value
        
        fun isEmpty(): Boolean = value == null
    }
    
    // Usage
    val emptyContainer = Container.empty<String>()
    val stringContainer = Container.of("Hello")
    val listContainer = Container.fromList(listOf(1, 2, 3))
    
    // 9. ACCESSING PRIVATE MEMBERS
    
    class BankAccount(private val accountNumber: String) {
        private var balance: Double = 0.0
        
        companion object {
            private const val BANK_CODE = "ABC123"
            
            fun validateAccountNumber(accountNumber: String): Boolean {
                return accountNumber.startsWith(BANK_CODE)
            }
            
            fun createAccount(accountNumber: String): BankAccount? {
                return if (validateAccountNumber(accountNumber)) {
                    BankAccount(accountNumber)
                } else {
                    null
                }
            }
        }
        
        fun deposit(amount: Double) {
            balance += amount
        }
        
        fun getBalance(): Double = balance
    }
    
    // 10. COMPANION OBJECT INITIALIZATION
    
    class Logger {
        companion object {
            private val startTime = System.currentTimeMillis()
            
            init {
                println("Logger companion object initialized at $startTime")
            }
            
            fun log(message: String) {
                val elapsed = System.currentTimeMillis() - startTime
                println("[$elapsed ms] $message")
            }
        }
    }
    
    // 11. COMPANION OBJECT WITH DELEGATION
    
    interface Formatter {
        fun format(value: Any): String
    }
    
    class JsonFormatter : Formatter {
        override fun format(value: Any): String = """{"value": "$value"}"""
    }
    
    class DataProcessor {
        companion object : Formatter by JsonFormatter() {
            fun process(data: List<Any>): List<String> {
                return data.map { format(it) }
            }
        }
    }
    
    // 12. PRACTICAL EXAMPLE: UTILITY CLASS
    
    class DateUtils {
        companion object {
            private val formatter = SimpleDateFormat("yyyy-MM-dd HH:mm:ss")
            
            fun formatDate(date: Date): String {
                return formatter.format(date)
            }
            
            fun parseDate(dateString: String): Date? {
                return try {
                    formatter.parse(dateString)
                } catch (e: Exception) {
                    null
                }
            }
            
            fun getCurrentTimestamp(): String {
                return formatDate(Date())
            }
            
            fun isWeekend(date: Date): Boolean {
                val calendar = Calendar.getInstance()
                calendar.time = date
                val dayOfWeek = calendar.get(Calendar.DAY_OF_WEEK)
                return dayOfWeek == Calendar.SATURDAY || dayOfWeek == Calendar.SUNDAY
            }
        }
    }
    
    // Usage examples
    fun demonstrateCompanionObjects() {
        // Factory pattern
        val shapes = listOf(
            Shape.createCircle(5.0),
            Shape.createRectangle(10.0, 5.0),
            Shape.createSquare(4.0)
        )
        
        shapes.forEach { shape ->
            println("Area: ${shape.area}")
        }
        
        // Utility usage
        val now = Date()
        println("Current time: ${DateUtils.formatDate(now)}")
        println("Is weekend: ${DateUtils.isWeekend(now)}")
        
        // Singleton usage
        val networkManager = NetworkManager.getInstance()
        networkManager.makeRequest("https://api.example.com/data")
    }
    
    // Key points about companion objects:
    // - One per class maximum
    // - Initialized when class is first loaded
    // - Can implement interfaces
    // - Can have extension functions
    // - Can access private members of the class
    // - Similar to static members in Java
    // - Can be used for factory patterns
    // - Thread-safe initialization
    ```

39. **Explain visibility modifiers in Kotlin**
    
    **Answer:** Kotlin has four visibility modifiers that control access to declarations: `public`, `private`, `protected`, and `internal`.
    
    ```kotlin
    // 1. PUBLIC (default)
    // Visible everywhere
    
    public class PublicClass {
        public val publicProperty = "visible everywhere"
        public fun publicFunction() = "accessible from anywhere"
    }
    
    // 'public' keyword is optional (default)
    class DefaultClass {
        val defaultProperty = "also public"
        fun defaultFunction() = "also public"
    }
    
    // 2. PRIVATE
    // Visible only within the same file (top-level) or class
    
    // Top-level private
    private class PrivateTopLevelClass {
        private val privateProperty = "only visible in this file"
        private fun privateFunction() = "only visible in this file"
    }
    
    private fun privateTopLevelFunction() = "only visible in this file"
    
    // Class-level private
    class ClassWithPrivateMembers {
        private val privateField = "only visible in this class"
        private fun privateMethod() = "only visible in this class"
        
        fun publicMethod() {
            // Can access private members within the same class
            println(privateField)
            privateMethod()
        }
    }
    
    // 3. PROTECTED
    // Visible within the class and its subclasses
    
    open class BaseClass {
        protected val protectedProperty = "visible to subclasses"
        protected fun protectedMethod() = "visible to subclasses"
        
        private val privateProperty = "not visible to subclasses"
    }
    
    class DerivedClass : BaseClass() {
        fun accessProtected() {
            // Can access protected members from base class
            println(protectedProperty)
            protectedMethod()
            
            // Cannot access private members
            // println(privateProperty) // Compilation error
        }
    }
    
    // 4. INTERNAL
    // Visible within the same module
    
    internal class InternalClass {
        internal val internalProperty = "visible within module"
        internal fun internalFunction() = "visible within module"
    }
    
    internal fun internalTopLevelFunction() = "visible within module"
    
    // 5. VISIBILITY IN DIFFERENT CONTEXTS
    
    // Interface visibility
    interface MyInterface {
        // Interface members are public by default
        fun interfaceMethod()
        
        // Cannot be private in interface
        // private fun privateMethod() // Compilation error
    }
    
    // Abstract class visibility
    abstract class AbstractClass {
        abstract protected fun abstractProtectedMethod()
        abstract internal fun abstractInternalMethod()
        
        // Abstract members cannot be private
        // abstract private fun abstractPrivateMethod() // Compilation error
    }
    
    // 6. CONSTRUCTOR VISIBILITY
    
    class ClassWithPrivateConstructor private constructor(val value: String) {
        companion object {
            fun create(value: String): ClassWithPrivateConstructor {
                return ClassWithPrivateConstructor(value)
            }
        }
    }
    
    // Cannot create instance directly
    // val instance = ClassWithPrivateConstructor("test") // Compilation error
    val instance = ClassWithPrivateConstructor.create("test") // OK
    
    // Internal constructor
    class ClassWithInternalConstructor internal constructor(val value: String) {
        // Can also have multiple constructors with different visibility
        private constructor() : this("default")
        
        companion object {
            fun createDefault(): ClassWithInternalConstructor {
                return ClassWithInternalConstructor()
            }
        }
    }
    
    // 7. PROPERTY VISIBILITY
    
    class PropertyVisibilityExample {
        // Public property with private setter
        var publicGetPrivateSet: String = "initial"
            private set
        
        // Internal property
        internal var internalProperty: String = "internal"
        
        // Protected property
        protected var protectedProperty: String = "protected"
        
        // Private property
        private var privateProperty: String = "private"
        
        fun updateProperty(value: String) {
            publicGetPrivateSet = value // Can set from within class
        }
        
        // Custom getter/setter visibility
        var customVisibility: String = "value"
            internal set  // Internal setter
            get() = field.uppercase() // Public getter
    }
    
    // 8. NESTED CLASS VISIBILITY
    
    class OuterClass {
        private val outerPrivate = "outer private"
        
        // Nested class cannot access outer class private members
        class NestedClass {
            fun method() {
                // Cannot access outerPrivate
                // println(outerPrivate) // Compilation error
            }
        }
        
        // Inner class can access outer class private members
        inner class InnerClass {
            fun method() {
                println(outerPrivate) // OK
            }
        }
        
        // Private nested class
        private class PrivateNestedClass {
            fun method() = "private nested"
        }
        
        fun createPrivateNested(): PrivateNestedClass {
            return PrivateNestedClass()
        }
    }
    
    // 9. VISIBILITY IN INHERITANCE
    
    open class Parent {
        open protected fun protectedMethod() = "parent protected"
        open internal fun internalMethod() = "parent internal"
        private fun privateMethod() = "parent private"
    }
    
    class Child : Parent() {
        // Can override protected with protected or public
        override fun protectedMethod() = "child protected"
        
        // Can override internal with internal or public
        public override fun internalMethod() = "child public"
        
        // Cannot override private methods
        // override fun privateMethod() = "child" // Compilation error
        
        // Cannot make visibility more restrictive
        // private override fun protectedMethod() = "child" // Compilation error
    }
    
    // 10. PRACTICAL EXAMPLES
    
    // Repository pattern with visibility control
    class UserRepository {
        private val users = mutableListOf<User>()
        
        // Public interface
        fun addUser(user: User) {
            validateUser(user)
            users.add(user)
        }
        
        fun getUser(id: String): User? {
            return users.find { it.id == id }
        }
        
        // Internal for testing
        internal fun getUserCount(): Int = users.size
        
        // Private implementation details
        private fun validateUser(user: User) {
            require(user.name.isNotBlank()) { "Name cannot be blank" }
            require(user.email.contains("@")) { "Invalid email" }
        }
    }
    
    // Builder pattern with visibility
    class HttpRequest private constructor(
        val url: String,
        val method: String,
        val headers: Map<String, String>,
        val body: String?
    ) {
        class Builder {
            private var url: String = ""
            private var method: String = "GET"
            private var headers: MutableMap<String, String> = mutableMapOf()
            private var body: String? = null
            
            fun url(url: String) = apply { this.url = url }
            fun method(method: String) = apply { this.method = method }
            fun header(key: String, value: String) = apply { headers[key] = value }
            fun body(body: String) = apply { this.body = body }
            
            fun build(): HttpRequest {
                require(url.isNotBlank()) { "URL is required" }
                return HttpRequest(url, method, headers.toMap(), body)
            }
        }
        
        companion object {
            fun builder(): Builder = Builder()
        }
    }
    
    // 11. MODULE BOUNDARIES (internal)
    
    // In module A
    internal class ModuleAInternal {
        internal fun internalFunction() = "only visible in module A"
    }
    
    // In module B - cannot access ModuleAInternal
    // val instance = ModuleAInternal() // Compilation error
    
    // 12. VISIBILITY BEST PRACTICES
    
    class WellDesignedClass {
        // Expose minimal public API
        fun publicOperation(): String {
            val processed = processData()
            return formatResult(processed)
        }
        
        // Keep implementation details private
        private fun processData(): String {
            return "processed data"
        }
        
        private fun formatResult(data: String): String {
            return "Result: $data"
        }
        
        // Use internal for testing hooks
        internal fun getInternalState(): String {
            return "internal state for testing"
        }
        
        // Use protected for extension points
        protected open fun extensionPoint(): String {
            return "can be overridden"
        }
    }
    
    // Summary of visibility modifiers:
    // - public: Visible everywhere (default)
    // - private: Visible only within same file/class
    // - protected: Visible within class and subclasses
    // - internal: Visible within same module
    // - Choose the most restrictive visibility that works
    // - Use private for implementation details
    // - Use internal for testing and module boundaries
    // - Use protected for inheritance extension points
    ```

40. **What is the difference between `internal` and `protected`?**
    
    **Answer:** `internal` provides module-level visibility while `protected` provides inheritance-based visibility. They serve different purposes in access control.
    
    ```kotlin
    // 1. INTERNAL - MODULE VISIBILITY
    
    // Internal classes, functions, and properties are visible within the same module
    internal class InternalService {
        internal val internalProperty = "visible within module"
        internal fun internalMethod() = "accessible within module"
    }
    
    internal fun internalTopLevelFunction() = "module-level function"
    
    // Usage within the same module
    class SameModuleClass {
        fun useInternalMembers() {
            val service = InternalService() // OK - same module
            println(service.internalProperty) // OK - same module
            service.internalMethod() // OK - same module
            internalTopLevelFunction() // OK - same module
        }
    }
    
    // 2. PROTECTED - INHERITANCE VISIBILITY
    
    open class BaseClass {
        protected val protectedProperty = "visible to subclasses"
        protected fun protectedMethod() = "accessible to subclasses"
        
        // Protected members are NOT visible to other classes in same package/module
        internal fun internalMethod() {
            // Can access protected members within same class
            println(protectedProperty)
            protectedMethod()
        }
    }
    
    class DerivedClass : BaseClass() {
        fun accessProtectedMembers() {
            // Can access protected members from base class
            println(protectedProperty) // OK - inheritance
            protectedMethod() // OK - inheritance
        }
    }
    
    class UnrelatedClass {
        fun tryAccessProtected() {
            val base = BaseClass()
            // Cannot access protected members from unrelated class
            // println(base.protectedProperty) // Compilation error
            // base.protectedMethod() // Compilation error
        }
    }
    
    // 3. KEY DIFFERENCES DEMONSTRATED
    
    // Module A
    internal class ModuleAClass {
        internal fun internalFunction() = "internal"
        protected open fun protectedFunction() = "protected"
    }
    
    class ModuleADerived : ModuleAClass() {
        fun demonstrateDifference() {
            // Can access internal (same module)
            internalFunction() // OK
            
            // Can access protected (inheritance)
            protectedFunction() // OK
        }
    }
    
    class ModuleAUnrelated {
        fun demonstrateAccess() {
            val instance = ModuleAClass()
            
            // Can access internal (same module)
            instance.internalFunction() // OK
            
            // Cannot access protected (no inheritance)
            // instance.protectedFunction() // Compilation error
        }
    }
    
    // Module B (different module)
    class ModuleBClass {
        fun tryAccess() {
            // Cannot access internal from different module
            // val instance = ModuleAClass() // Compilation error
        }
    }
    
    class ModuleBDerived : ModuleAClass() {
        fun demonstrateProtectedAccess() {
            // Cannot access internal (different module)
            // internalFunction() // Compilation error
            
            // Can access protected (inheritance works across modules)
            protectedFunction() // OK
        }
    }
    
    // 4. PRACTICAL EXAMPLE: FRAMEWORK DESIGN
    
    // Base framework class
    open class FrameworkComponent {
        // Internal for framework implementation
        internal var frameworkState: String = "initialized"
        
        // Protected for extension by users
        protected open fun onInitialize() {
            println("Framework component initializing")
        }
        
        protected open fun onDestroy() {
            println("Framework component destroying")
        }
        
        // Public API
        fun initialize() {
            frameworkState = "initializing"
            onInitialize()
            frameworkState = "initialized"
        }
        
        fun destroy() {
            frameworkState = "destroying"
            onDestroy()
            frameworkState = "destroyed"
        }
    }
    
    // Framework internal helper (same module)
    internal class FrameworkHelper {
        fun checkState(component: FrameworkComponent) {
            // Can access internal members
            println("Component state: ${component.frameworkState}")
            
            // Cannot access protected members without inheritance
            // component.onInitialize() // Compilation error
        }
    }
    
    // User code (different module)
    class UserComponent : FrameworkComponent() {
        override fun onInitialize() {
            super.onInitialize()
            println("User component initializing")
        }
        
        override fun onDestroy() {
            super.onDestroy()
            println("User component destroying")
        }
        
        fun userMethod() {
            // Cannot access internal members (different module)
            // println(frameworkState) // Compilation error
            
            // Can access protected members (inheritance)
            onInitialize() // OK
        }
    }
    
    // 5. INTERFACE AND ABSTRACT CLASS DIFFERENCES
    
    interface MyInterface {
        // Cannot have protected members in interface
        // protected fun protectedMethod() // Compilation error
        
        // Can have internal members
        internal fun internalMethod() {
            println("Internal interface method")
        }
    }
    
    abstract class MyAbstractClass {
        // Can have both internal and protected
        internal abstract fun internalAbstractMethod()
        protected abstract fun protectedAbstractMethod()
        
        // Concrete implementations
        internal fun internalConcreteMethod() {
            println("Internal concrete method")
        }
        
        protected fun protectedConcreteMethod() {
            println("Protected concrete method")
        }
    }
    
    // 6. CONSTRUCTOR VISIBILITY
    
    class ConstructorVisibilityExample {
        // Internal constructor
        internal constructor(value: String) {
            println("Internal constructor: $value")
        }
        
        // Protected constructor (only useful in open classes)
        protected constructor(value: Int) {
            println("Protected constructor: $value")
        }
    }
    
    open class OpenClassWithProtectedConstructor {
        protected constructor(value: String) {
            println("Protected constructor: $value")
        }
    }
    
    class DerivedFromProtected : OpenClassWithProtectedConstructor {
        constructor(value: String) : super(value) {
            println("Derived constructor")
        }
    }
    
    // 7. PROPERTY VISIBILITY
    
    class PropertyVisibilityComparison {
        // Internal property
        internal var internalProperty: String = "internal"
        
        // Protected property (only useful in open classes)
        protected var protectedProperty: String = "protected"
        
        // Mixed visibility
        var mixedVisibility: String = "public getter"
            internal set // Internal setter
        
        protected var protectedGetInternalSet: String = "protected"
            internal set // Internal setter
    }
    
    // 8. TESTING IMPLICATIONS
    
    class ServiceClass {
        // Public API
        fun publicMethod(): String {
            return processData()
        }
        
        // Internal for testing
        internal fun processData(): String {
            return validateInput() + formatOutput()
        }
        
        // Private implementation
        private fun validateInput(): String = "validated"
        private fun formatOutput(): String = "formatted"
    }
    
    // Test class (same module)
    class ServiceTest {
        fun testInternalMethods() {
            val service = ServiceClass()
            
            // Can test internal methods
            val result = service.processData()
            assertEquals("validatedformatted", result)
        }
    }
    
    // 9. WHEN TO USE EACH
    
    // Use INTERNAL when:
    // - Sharing implementation details within a module
    // - Creating testing hooks
    // - Building framework internals
    // - Module-specific utilities
    
    class ModuleUtilities {
        internal fun moduleSpecificHelper() = "helper function"
        internal val moduleConfiguration = "config"
    }
    
    // Use PROTECTED when:
    // - Designing for inheritance
    // - Creating extension points
    // - Template method pattern
    // - Framework base classes
    
    open class TemplateClass {
        // Template method (public)
        fun execute() {
            step1()
            step2()
            step3()
        }
        
        // Extension points (protected)
        protected open fun step1() = println("Default step 1")
        protected open fun step2() = println("Default step 2")
        protected open fun step3() = println("Default step 3")
    }
    
    class ConcreteTemplate : TemplateClass() {
        override fun step2() {
            println("Custom step 2")
        }
    }
    
    // 10. SUMMARY TABLE
    /*
    | Aspect | Internal | Protected |
    |--------|----------|-----------|
    | Scope | Module | Class hierarchy |
    | Inheritance | No | Yes |
    | Cross-module | No | Yes (with inheritance) |
    | Interfaces | Yes | No |
    | Top-level | Yes | No |
    | Testing | Excellent | Limited |
    | Framework design | Implementation | Extension points |
    */
    
    // Best practices:
    // - Use internal for module boundaries and testing
    // - Use protected for inheritance-based design
    // - Prefer composition over inheritance when possible
    // - Keep public API minimal
    // - Use internal for implementation details
    // - Use protected for template methods and extension points
    ```

---

## Kotlin Advanced

### Generics and Variance

41. **What are generics in Kotlin?**
    
    **Answer:** Generics allow you to write type-safe code that works with different types while maintaining type safety at compile time.
    
    ```kotlin
    // 1. BASIC GENERICS
    
    // Generic class
    class Box<T>(val value: T) {
        fun get(): T = value
        
        fun isEmpty(): Boolean = value == null
    }
    
    // Usage
    val stringBox = Box("Hello")
    val intBox = Box(42)
    val nullableBox = Box<String?>(null)
    
    // Generic interface
    interface Repository<T> {
        fun save(item: T)
        fun findById(id: String): T?
        fun findAll(): List<T>
    }
    
    // Generic function
    fun <T> swap(a: T, b: T): Pair<T, T> = Pair(b, a)
    
    // Usage
    val swapped = swap("hello", "world") // Pair<String, String>
    val swappedNumbers = swap(1, 2) // Pair<Int, Int>
    
    // 2. MULTIPLE TYPE PARAMETERS
    
    class Pair<A, B>(val first: A, val second: B) {
        fun <C> mapFirst(transform: (A) -> C): Pair<C, B> {
            return Pair(transform(first), second)
        }
        
        fun <C> mapSecond(transform: (B) -> C): Pair<A, C> {
            return Pair(first, transform(second))
        }
    }
    
    // Generic with bounds
    class NumberContainer<T : Number>(val value: T) {
        fun getDoubleValue(): Double = value.toDouble()
        
        fun isPositive(): Boolean = value.toDouble() > 0
    }
    
    // Usage
    val intContainer = NumberContainer(42)
    val doubleContainer = NumberContainer(3.14)
    // val stringContainer = NumberContainer("hello") // Compilation error
    
    // 3. GENERIC CONSTRAINTS
    
    // Single upper bound
    fun <T : Comparable<T>> sort(list: MutableList<T>) {
        list.sortWith { a, b -> a.compareTo(b) }
    }
    
    // Multiple bounds (where clause)
    interface Named {
        val name: String
    }
    
    interface Aged {
        val age: Int
    }
    
    fun <T> processEntity(entity: T) where T : Named, T : Aged {
        println("Processing ${entity.name}, age ${entity.age}")
    }
    
    // Implementation
    class Person(override val name: String, override val age: Int) : Named, Aged
    
    val person = Person("John", 30)
    processEntity(person) // OK
    
    // 4. GENERIC INHERITANCE
    
    // Generic base class
    abstract class Animal<T> {
        abstract fun makeSound(): T
        abstract fun getInfo(): T
    }
    
    // Concrete implementations
    class Dog : Animal<String>() {
        override fun makeSound(): String = "Woof!"
        override fun getInfo(): String = "This is a dog"
    }
    
    class Cat : Animal<String>() {
        override fun makeSound(): String = "Meow!"
        override fun getInfo(): String = "This is a cat"
    }
    
    // Generic with different type
    class Robot : Animal<Int>() {
        override fun makeSound(): Int = 404 // Sound not found
        override fun getInfo(): Int = 200 // OK status
    }
    
    // 5. GENERIC FUNCTIONS WITH RECEIVERS
    
    fun <T> T.toOptional(): Optional<T> = Optional.ofNullable(this)
    
    fun <T> List<T>.second(): T? = if (size >= 2) this[1] else null
    
    fun <T> MutableList<T>.swap(index1: Int, index2: Int) {
        val temp = this[index1]
        this[index1] = this[index2]
        this[index2] = temp
    }
    
    // Usage
    val numbers = mutableListOf(1, 2, 3, 4, 5)
    numbers.swap(0, 4) // [5, 2, 3, 4, 1]
    val secondItem = numbers.second() // 2
    
    // 6. GENERIC BUILDERS
    
    class ListBuilder<T> {
        private val items = mutableListOf<T>()
        
        fun add(item: T) {
            items.add(item)
        }
        
        fun addAll(items: Collection<T>) {
            this.items.addAll(items)
        }
        
        fun build(): List<T> = items.toList()
    }
    
    fun <T> buildList(builder: ListBuilder<T>.() -> Unit): List<T> {
        return ListBuilder<T>().apply(builder).build()
    }
    
    // Usage
    val numbers = buildList<Int> {
        add(1)
        add(2)
        addAll(listOf(3, 4, 5))
    }
    
    // 7. GENERIC SEALED CLASSES
    
    sealed class Result<out T> {
        data class Success<T>(val data: T) : Result<T>()
        data class Error(val exception: Throwable) : Result<Nothing>()
        object Loading : Result<Nothing>()
    }
    
    // Usage
    fun handleResult(result: Result<String>) {
        when (result) {
            is Result.Success -> println("Data: ${result.data}")
            is Result.Error -> println("Error: ${result.exception.message}")
            Result.Loading -> println("Loading...")
        }
    }
    
    // 8. GENERIC DELEGATES
    
    class LazyDelegate<T>(private val initializer: () -> T) {
        private var value: T? = null
        private var initialized = false
        
        operator fun getValue(thisRef: Any?, property: KProperty<*>): T {
            if (!initialized) {
                value = initializer()
                initialized = true
            }
            @Suppress("UNCHECKED_CAST")
            return value as T
        }
    }
    
    fun <T> lazyCustom(initializer: () -> T) = LazyDelegate(initializer)
    
    // Usage
    class Example {
        val expensiveValue: String by lazyCustom {
            println("Computing expensive value")
            "Computed!"
        }
    }
    
    // 9. GENERIC COLLECTIONS
    
    class Stack<T> {
        private val items = mutableListOf<T>()
        
        fun push(item: T) {
            items.add(item)
        }
        
        fun pop(): T? {
            return if (items.isNotEmpty()) {
                items.removeAt(items.size - 1)
            } else {
                null
            }
        }
        
        fun peek(): T? = items.lastOrNull()
        
        fun isEmpty(): Boolean = items.isEmpty()
        
        fun size(): Int = items.size
    }
    
    // Usage
    val stringStack = Stack<String>()
    stringStack.push("first")
    stringStack.push("second")
    println(stringStack.pop()) // "second"
    
    // 10. PRACTICAL EXAMPLES
    
    // Generic repository pattern
    abstract class BaseRepository<T, ID> {
        protected val items = mutableMapOf<ID, T>()
        
        abstract fun getId(item: T): ID
        
        fun save(item: T): T {
            val id = getId(item)
            items[id] = item
            return item
        }
        
        fun findById(id: ID): T? = items[id]
        
        fun findAll(): List<T> = items.values.toList()
        
        fun delete(id: ID): Boolean {
            return items.remove(id) != null
        }
        
        fun count(): Int = items.size
    }
    
    // Concrete implementation
    data class User(val id: String, val name: String, val email: String)
    
    class UserRepository : BaseRepository<User, String>() {
        override fun getId(item: User): String = item.id
        
        fun findByEmail(email: String): User? {
            return items.values.find { it.email == email }
        }
    }
    
    // Generic event system
    interface EventListener<T> {
        fun onEvent(event: T)
    }
    
    class EventBus<T> {
        private val listeners = mutableListOf<EventListener<T>>()
        
        fun register(listener: EventListener<T>) {
            listeners.add(listener)
        }
        
        fun unregister(listener: EventListener<T>) {
            listeners.remove(listener)
        }
        
        fun post(event: T) {
            listeners.forEach { it.onEvent(event) }
        }
    }
    
    // Benefits of generics:
    // - Type safety at compile time
    // - Code reusability
    // - Elimination of type casting
    // - Better IDE support and refactoring
    // - Performance benefits (no boxing/unboxing)
    ```

42. **Explain variance: `in`, `out`, and `*` (star projection)**
    
    **Answer:** Variance controls how generic types with inheritance relationships relate to each other. Kotlin uses `out` (covariance), `in` (contravariance), and `*` (star projection).
    
    ```kotlin
    // 1. COVARIANCE (out)
    // If A is a subtype of B, then Producer<A> is a subtype of Producer<B>
    
    interface Producer<out T> {
        fun produce(): T
        // Cannot have T as parameter type
        // fun consume(item: T) // Compilation error
    }
    
    class StringProducer : Producer<String> {
        override fun produce(): String = "Hello"
    }
    
    class AnyProducer : Producer<Any> {
        override fun produce(): Any = "Any value"
    }
    
    // Covariance allows this assignment
    val stringProducer: Producer<String> = StringProducer()
    val anyProducer: Producer<Any> = stringProducer // OK - covariant
    
    // 2. CONTRAVARIANCE (in)
    // If A is a subtype of B, then Consumer<B> is a subtype of Consumer<A>
    
    interface Consumer<in T> {
        fun consume(item: T)
        // Cannot have T as return type
        // fun produce(): T // Compilation error
    }
    
    class AnyConsumer : Consumer<Any> {
        override fun consume(item: Any) {
            println("Consuming: $item")
        }
    }
    
    class StringConsumer : Consumer<String> {
        override fun consume(item: String) {
            println("Consuming string: $item")
        }
    }
    
    // Contravariance allows this assignment
    val anyConsumer: Consumer<Any> = AnyConsumer()
    val stringConsumer: Consumer<String> = anyConsumer // OK - contravariant
    
    // 3. STAR PROJECTION (*)
    // Represents unknown type with restrictions
    
    interface Container<T> {
        fun get(): T
        fun set(item: T)
    }
    
    // Star projection
    fun processContainer(container: Container<*>) {
        // Can call methods that return T (as Any?)
        val item = container.get() // Returns Any?
        
        // Cannot call methods that take T as parameter
        // container.set("value") // Compilation error
        
        // Can only set null (if T is nullable)
        container.set(null) // OK only if T is nullable
    }
    
    // 4. PRACTICAL EXAMPLES
    
    // Covariant List (read-only)
    interface ReadOnlyList<out T> {
        fun get(index: Int): T
        fun size(): Int
        
        // These would break covariance
        // fun add(item: T) // Compilation error
        // fun set(index: Int, item: T) // Compilation error
    }
    
    class StringList : ReadOnlyList<String> {
        private val items = listOf("a", "b", "c")
        override fun get(index: Int): String = items[index]
        override fun size(): Int = items.size
    }
    
    // Covariance in action
    val stringList: ReadOnlyList<String> = StringList()
    val anyList: ReadOnlyList<Any> = stringList // OK - covariant
    
    // Contravariant Comparator
    interface Comparator<in T> {
        fun compare(a: T, b: T): Int
    }
    
    class AnyComparator : Comparator<Any> {
        override fun compare(a: Any, b: Any): Int {
            return a.toString().compareTo(b.toString())
        }
    }
    
    // Contravariance in action
    val anyComparator: Comparator<Any> = AnyComparator()
    val stringComparator: Comparator<String> = anyComparator // OK - contravariant
    
    // 5. VARIANCE IN FUNCTIONS
    
    // Function types are contravariant in parameters and covariant in return type
    // (T) -> R is equivalent to Function1<in T, out R>
    
    val stringToAny: (String) -> Any = { it }
    val anyToString: (Any) -> String = { it.toString() }
    
    // Contravariant in parameter
    val stringFunction: (String) -> String = anyToString // OK
    
    // Covariant in return type
    val anyFunction: (String) -> Any = stringToAny // OK
    
    // 6. VARIANCE IN COLLECTIONS
    
    fun demonstrateVariance() {
        // Kotlin's built-in collections
        val strings: List<String> = listOf("a", "b", "c")
        val anys: List<Any> = strings // OK - List<out T> is covariant
        
        val mutableStrings: MutableList<String> = mutableListOf("a", "b", "c")
        // val mutableAnys: MutableList<Any> = mutableStrings // Error - MutableList<T> is invariant
        
        // Arrays are invariant
        val stringArray: Array<String> = arrayOf("a", "b", "c")
        // val anyArray: Array<Any> = stringArray // Error - Array<T> is invariant
    }
    
    // 7. USE-SITE VARIANCE
    
    class Box<T>(var value: T)
    
    // Use-site variance with 'out'
    fun copyFrom(from: Box<out Any>) {
        val value = from.value // OK - can read
        // from.value = "new value" // Error - cannot write
    }
    
    // Use-site variance with 'in'
    fun copyTo(to: Box<in String>) {
        to.value = "new value" // OK - can write
        // val value = to.value // Error - cannot read (returns Any?)
    }
    
    // 8. COMPLEX VARIANCE SCENARIOS
    
    // Producer-Consumer with both variance types
    interface Channel<in I, out O> {
        fun send(item: I)
        fun receive(): O
    }
    
    class StringToIntChannel : Channel<String, Int> {
        override fun send(item: String) {
            println("Sending: $item")
        }
        
        override fun receive(): Int = 42
    }
    
    // Variance allows flexible assignment
    val channel: Channel<String, Int> = StringToIntChannel()
    val flexibleChannel: Channel<Any, Number> = channel // OK - contravariant in I, covariant in O
    
    // 9. VARIANCE WITH GENERICS
    
    class Repository<T> {
        private val items = mutableListOf<T>()
        
        // Covariant method
        fun getAll(): List<T> = items.toList()
        
        // Contravariant method
        fun addAll(items: Collection<T>) {
            this.items.addAll(items)
        }
        
        // Use-site variance
        fun copyFrom(other: Repository<out T>) {
            this.items.addAll(other.getAll())
        }
        
        fun copyTo(other: Repository<in T>) {
            other.addAll(this.items)
        }
    }
    
    // 10. STAR PROJECTION PRACTICAL USES
    
    fun processAnyList(list: List<*>) {
        // Can access size and indices
        println("List size: ${list.size}")
        
        // Can get elements (as Any?)
        list.forEach { item ->
            println("Item: $item")
        }
        
        // Can check for null
        val hasNulls = list.any { it == null }
        
        // Cannot add elements
        // if (list is MutableList<*>) {
        //     list.add("item") // Compilation error
        // }
    }
    
    // Star projection with bounds
    fun processNumberList(list: List<out Number>) {
        list.forEach { number ->
            println("Number: ${number.toDouble()}")
        }
    }
    
    // When to use each:
    // - Use 'out' when you only read from the generic type (Producer)
    // - Use 'in' when you only write to the generic type (Consumer)
    // - Use '*' when you don't know or care about the specific type
    // - Use declaration-site variance for APIs you design
    // - Use use-site variance when calling existing APIs
    ```

43. **What is type erasure?**
    
    **Answer:** Type erasure is the process where generic type information is removed at runtime. The JVM doesn't know about generic types, so they're "erased" to their raw types.
    
    ```kotlin
    // 1. BASIC TYPE ERASURE
    
    // At compile time: List<String> and List<Int>
    // At runtime: Both become just List
    
    val stringList: List<String> = listOf("a", "b", "c")
    val intList: List<Int> = listOf(1, 2, 3)
    
    // At runtime, both have the same type
    println(stringList::class) // class kotlin.collections.ArrayList
    println(intList::class)    // class kotlin.collections.ArrayList
    
    // Cannot distinguish between generic types at runtime
    // println(stringList is List<String>) // Compilation error
    println(stringList is List<*>) // OK - star projection
    
    // 2. PROBLEMS CAUSED BY TYPE ERASURE
    
    // Cannot create generic arrays
    // val genericArray = Array<T>(10) { ... } // Compilation error
    
    // Cannot use generic types in is checks
    fun checkType(obj: Any) {
        // if (obj is List<String>) { ... } // Compilation error
        if (obj is List<*>) { // OK
            println("It's a list of something")
        }
    }
    
    // Cannot catch generic exceptions
    // try {
    //     // some code
    // } catch (e: MyException<String>) { // Compilation error
    //     // handle
    // }
    
    // 3. WORKAROUNDS FOR TYPE ERASURE
    
    // Using Class objects
    fun <T> createList(clazz: Class<T>, size: Int): List<T> {
        return (0 until size).map { 
            clazz.newInstance() 
        }
    }
    
    // Using KClass
    fun <T : Any> createListKotlin(clazz: KClass<T>, size: Int): List<T> {
        return (0 until size).map { 
            clazz.createInstance() 
        }
    }
    
    // Type tokens pattern
    abstract class TypeReference<T> {
        val type: Type = (javaClass.genericSuperclass as ParameterizedType).actualTypeArguments[0]
    }
    
    // Usage
    val stringListType = object : TypeReference<List<String>>() {}
    val intListType = object : TypeReference<List<Int>>() {}
    
    // 4. REIFIED TYPE PARAMETERS
    
    // Reified allows access to type information at runtime
    inline fun <reified T> isInstanceOf(obj: Any): Boolean {
        return obj is T
    }
    
    // Usage
    val result1 = isInstanceOf<String>("hello") // true
    val result2 = isInstanceOf<Int>("hello")    // false
    
    // Reified with collections
    inline fun <reified T> List<*>.filterIsInstance(): List<T> {
        return this.filterIsInstance<T>()
    }
    
    val mixedList: List<Any> = listOf("hello", 42, "world", 3.14)
    val strings = mixedList.filterIsInstance<String>() // ["hello", "world"]
    val numbers = mixedList.filterIsInstance<Number>() // [42, 3.14]
    
    // 5. REIFIED IN PRACTICE
    
    // JSON parsing with reified types
    inline fun <reified T> parseJson(json: String): T {
        // In real implementation, you'd use a JSON library
        return when (T::class) {
            String::class -> json as T
            Int::class -> json.toInt() as T
            else -> throw IllegalArgumentException("Unsupported type")
        }
    }
    
    // Usage
    val stringValue = parseJson<String>("\"hello\"")
    val intValue = parseJson<Int>("42")
    
    // Database query with reified types
    class Database {
        inline fun <reified T> query(sql: String): List<T> {
            // Use T::class to determine how to map results
            return when (T::class) {
                String::class -> listOf("result1", "result2") as List<T>
                Int::class -> listOf(1, 2, 3) as List<T>
                else -> emptyList()
            }
        }
    }
    
    // 6. LIMITATIONS OF REIFIED
    
    // Reified only works with inline functions
    // fun <reified T> nonInlineFunction() { } // Compilation error
    
    // Cannot use reified in class type parameters
    // class MyClass<reified T> { } // Compilation error
    
    // Cannot use reified with non-inline lambda parameters
    // inline fun <reified T> processWithLambda(noinline lambda: () -> Unit) {
    //     // T::class // May not work reliably
    // }
    
    // 7. ARRAY CREATION WITH REIFIED
    
    // Cannot create generic arrays normally
    // fun <T> createArray(size: Int): Array<T> {
    //     return Array<T>(size) { ... } // Compilation error
    // }
    
    // With reified, you can create arrays
    inline fun <reified T> createArray(size: Int, noinline init: (Int) -> T): Array<T> {
        return Array(size, init)
    }
    
    // Usage
    val stringArray = createArray<String>(5) { "item$it" }
    val intArray = createArray<Int>(3) { it * 2 }
    
    // 8. TYPE ERASURE AND REFLECTION
    
    fun demonstrateReflection() {
        val stringList = listOf("a", "b", "c")
        val intList = listOf(1, 2, 3)
        
        // Runtime type is the same
        println(stringList.javaClass) // class java.util.ArrayList
        println(intList.javaClass)    // class java.util.ArrayList
        
        // Generic type information is lost
        val field = stringList.javaClass.getDeclaredField("elementData")
        println(field.type) // class [Ljava.lang.Object;
    }
    
    // 9. PRACTICAL EXAMPLES
    
    // Service locator with reified types
    class ServiceLocator {
        private val services = mutableMapOf<KClass<*>, Any>()
        
        fun <T : Any> register(service: T, clazz: KClass<T>) {
            services[clazz] = service
        }
        
        inline fun <reified T : Any> get(): T? {
            return services[T::class] as? T
        }
    }
    
    // Usage
    val locator = ServiceLocator()
    locator.register("Hello", String::class)
    locator.register(42, Int::class)
    
    val stringService = locator.get<String>() // "Hello"
    val intService = locator.get<Int>()       // 42
    
    // Event bus with reified types
    class EventBus {
        private val listeners = mutableMapOf<KClass<*>, MutableList<(Any) -> Unit>>()
        
        inline fun <reified T : Any> subscribe(noinline listener: (T) -> Unit) {
            listeners.getOrPut(T::class) { mutableListOf() }
                .add { listener(it as T) }
        }
        
        inline fun <reified T : Any> publish(event: T) {
            listeners[T::class]?.forEach { it(event) }
        }
    }
    
    // 10. ADVANCED TYPE ERASURE CONCEPTS
    
    // Phantom types - types that exist only at compile time
    sealed class State
    object Draft : State()
    object Published : State()
    
    class Document<S : State> private constructor(val content: String) {
        companion object {
            fun create(content: String): Document<Draft> {
                return Document(content)
            }
        }
        
        fun publish(): Document<Published> {
            // At runtime, this is just Document to Document
            return Document<Published>(content)
        }
        
        // Only available for published documents
        fun archive(): Document<Draft> where S : Published {
            return Document<Draft>(content)
        }
    }
    
    // Type-safe builder with phantom types
    class QueryBuilder<T> {
        private val conditions = mutableListOf<String>()
        
        fun where(condition: String): QueryBuilder<T> {
            conditions.add(condition)
            return this
        }
        
        fun build(): String {
            return "SELECT * FROM table WHERE ${conditions.joinToString(" AND ")}"
        }
    }
    
    // Benefits and limitations:
    // Benefits:
    // - Smaller bytecode (no generic type info)
    // - Backward compatibility with pre-generics code
    // - Performance (no runtime type checks)
    
    // Limitations:
    // - Cannot create generic arrays
    // - Cannot use instanceof with generic types
    // - Cannot catch generic exceptions
    // - Need workarounds for type-dependent operations
    ```

44. **How do you create generic functions?**
    
    **Answer:** Generic functions allow you to write functions that work with different types while maintaining type safety. You declare type parameters in angle brackets before the function name.
    
    ```kotlin
    // 1. BASIC GENERIC FUNCTIONS
    
    // Simple generic function
    fun <T> identity(value: T): T {
        return value
    }
    
    // Usage
    val stringResult = identity("hello")  // T is String
    val intResult = identity(42)          // T is Int
    val boolResult = identity(true)       // T is Boolean
    
    // Multiple type parameters
    fun <A, B> createPair(first: A, second: B): Pair<A, B> {
        return Pair(first, second)
    }
    
    // Usage
    val pair1 = createPair("hello", 42)      // Pair<String, Int>
    val pair2 = createPair(true, 3.14)       // Pair<Boolean, Double>
    
    // 2. GENERIC FUNCTIONS WITH CONSTRAINTS
    
    // Upper bound constraint
    fun <T : Number> addNumbers(a: T, b: T): Double {
        return a.toDouble() + b.toDouble()
    }
    
    // Usage
    val sumInt = addNumbers(5, 10)        // Works with Int
    val sumDouble = addNumbers(3.14, 2.86) // Works with Double
    // val sumString = addNumbers("a", "b") // Compilation error
    
    // Multiple constraints
    fun <T> processComparable(item: T): String where T : Comparable<T>, T : CharSequence {
        return "Item: $item, length: ${item.length}"
    }
    
    // Usage
    val result = processComparable("hello") // String implements both Comparable and CharSequence
    
    // 3. GENERIC EXTENSION FUNCTIONS
    
    // Extension function on generic type
    fun <T> T.toOptional(): Optional<T> {
        return Optional.ofNullable(this)
    }
    
    // Extension function on generic collections
    fun <T> List<T>.second(): T? {
        return if (size >= 2) this[1] else null
    }
    
    fun <T> MutableList<T>.swap(index1: Int, index2: Int) {
        val temp = this[index1]
        this[index1] = this[index2]
        this[index2] = temp
    }
    
    // Usage
    val numbers = mutableListOf(1, 2, 3, 4, 5)
    numbers.swap(0, 4)  // [5, 2, 3, 4, 1]
    val secondNumber = numbers.second()  // 2
    
    // 4. GENERIC FUNCTIONS WITH RECEIVERS
    
    // Generic function with receiver
    fun <T> T.applyIf(condition: Boolean, block: T.() -> T): T {
        return if (condition) this.block() else this
    }
    
    // Usage
    val result = "hello"
        .applyIf(true) { uppercase() }  // "HELLO"
        .applyIf(false) { reversed() }  // Still "HELLO"
    
    // Generic builder function
    fun <T> buildList(builder: MutableList<T>.() -> Unit): List<T> {
        return mutableListOf<T>().apply(builder)
    }
    
    // Usage
    val numbers = buildList<Int> {
        add(1)
        add(2)
        add(3)
    }
    
    // 5. GENERIC FUNCTIONS WITH VARIANCE
    
    // Covariant parameter
    fun <T> copyList(source: List<out T>): List<T> {
        return source.toList()
    }
    
    // Contravariant parameter
    fun <T> fillList(target: MutableList<in T>, items: List<T>) {
        target.addAll(items)
    }
    
    // Usage
    val strings: List<String> = listOf("a", "b", "c")
    val anys: MutableList<Any> = mutableListOf()
    
    fillList(anys, strings)  // OK - MutableList<Any> can accept String items
    
    // 6. REIFIED GENERIC FUNCTIONS
    
    // Reified type parameter for runtime type access
    inline fun <reified T> isInstanceOf(obj: Any): Boolean {
        return obj is T
    }
    
    inline fun <reified T> filterByType(list: List<Any>): List<T> {
        return list.filterIsInstance<T>()
    }
    
    // Usage
    val mixedList = listOf("hello", 42, "world", 3.14, true)
    val strings = filterByType<String>(mixedList)  // ["hello", "world"]
    val numbers = filterByType<Number>(mixedList)  // [42, 3.14]
    
    // 7. GENERIC HIGHER-ORDER FUNCTIONS
    
    // Generic function that takes function as parameter
    fun <T, R> List<T>.mapNotNull(transform: (T) -> R?): List<R> {
        return this.mapNotNull(transform)
    }
    
    // Generic function that returns function
    fun <T> createPredicate(value: T): (T) -> Boolean {
        return { it == value }
    }
    
    // Usage
    val isHello = createPredicate("hello")
    val result = isHello("hello")  // true
    
    // Complex generic higher-order function
    fun <T, R> List<T>.foldIndexed(
        initial: R,
        operation: (index: Int, acc: R, element: T) -> R
    ): R {
        var accumulator = initial
        for ((index, element) in this.withIndex()) {
            accumulator = operation(index, accumulator, element)
        }
        return accumulator
    }
    
    // 8. GENERIC FUNCTIONS WITH MULTIPLE BOUNDS
    
    // Function with multiple type parameter bounds
    fun <T> merge(first: T, second: T): T 
        where T : Comparable<T>, 
              T : Appendable {
        return if (first >= second) {
            first.append(second.toString())
            first
        } else {
            second.append(first.toString())
            second
        }
    }
    
    // Custom interface for demonstration
    interface Mergeable<T> {
        fun merge(other: T): T
    }
    
    fun <T : Mergeable<T>> mergeAll(items: List<T>): T? {
        return items.reduceOrNull { acc, item -> acc.merge(item) }
    }
    
    // 9. GENERIC FUNCTIONS WITH STAR PROJECTION
    
    // Function accepting any generic type
    fun processAnyContainer(container: Container<*>) {
        // Can only read as Any?
        val value = container.get()
        println("Container contains: $value")
        
        // Cannot write (except null if nullable)
        // container.set("value")  // Compilation error
    }
    
    // Function with bounded star projection
    fun processNumberContainer(container: Container<out Number>) {
        val number = container.get()
        println("Number: ${number.toDouble()}")
    }
    
    // 10. PRACTICAL GENERIC FUNCTIONS
    
    // Retry function with generic return type
    suspend fun <T> retry(
        times: Int = 3,
        delay: Long = 1000,
        block: suspend () -> T
    ): T {
        repeat(times - 1) {
            try {
                return block()
            } catch (e: Exception) {
                kotlinx.coroutines.delay(delay)
            }
        }
        return block() // Last attempt without catching
    }
    
    // Usage
    val result = retry<String> {
        // Some operation that might fail
        apiCall()
    }
    
    // Generic caching function
    class Cache<K, V> {
        private val cache = mutableMapOf<K, V>()
        
        fun <R> computeIfAbsent(key: K, compute: (K) -> V): V {
            return cache.getOrPut(key) { compute(key) }
        }
    }
    
    // Generic validation function
    fun <T> validate(value: T, vararg validators: (T) -> Boolean): Boolean {
        return validators.all { it(value) }
    }
    
    // Usage
    val isValidEmail = validate(
        "user@example.com",
        { it.contains("@") },
        { it.contains(".") },
        { it.length > 5 }
    )
    
    // Generic transformation pipeline
    fun <T> T.transform(vararg transformations: (T) -> T): T {
        return transformations.fold(this) { acc, transform -> transform(acc) }
    }
    
    // Usage
    val result = "hello"
        .transform(
            { it.uppercase() },
            { it.reversed() },
            { "[$it]" }
        )  // "[OLLEH]"
    
    // Generic event handling
    class EventHandler<T> {
        private val handlers = mutableListOf<(T) -> Unit>()
        
        fun subscribe(handler: (T) -> Unit) {
            handlers.add(handler)
        }
        
        fun emit(event: T) {
            handlers.forEach { it(event) }
        }
    }
    
    // Generic result handling
    fun <T, R> T.mapResult(transform: (T) -> R): R {
        return try {
            transform(this)
        } catch (e: Exception) {
            throw RuntimeException("Failed to transform $this", e)
        }
    }
    
    // Best practices for generic functions:
    // 1. Use descriptive type parameter names (T, K, V, etc.)
    // 2. Add constraints when necessary
    // 3. Use reified for runtime type access
    // 4. Consider variance for flexibility
    // 5. Keep type parameters minimal
    // 6. Document type parameter meanings
    // 7. Use star projection for unknown types
    ```

45. **What are reified type parameters?**
    
    **Answer:** Reified type parameters allow you to access type information at runtime in inline functions, overcoming the limitations of type erasure.
    
    ```kotlin
    // 1. BASIC REIFIED CONCEPT
    
    // Without reified - type information is lost
    fun <T> regularFunction(obj: Any): Boolean {
        // return obj is T  // Compilation error - cannot check erased type
        return false
    }
    
    // With reified - type information is preserved
    inline fun <reified T> reifiedFunction(obj: Any): Boolean {
        return obj is T  // OK - type information available at runtime
    }
    
    // Usage
    val result1 = reifiedFunction<String>("hello")  // true
    val result2 = reifiedFunction<Int>("hello")     // false
    
    // 2. TYPE CHECKING WITH REIFIED
    
    inline fun <reified T> isInstanceOf(obj: Any): Boolean {
        return obj is T
    }
    
    inline fun <reified T> castOrNull(obj: Any): T? {
        return obj as? T
    }
    
    inline fun <reified T> requireInstanceOf(obj: Any): T {
        return obj as T
    }
    
    // Usage examples
    val mixedList = listOf("hello", 42, true, 3.14)
    
    mixedList.forEach { item ->
        when {
            isInstanceOf<String>(item) -> println("String: $item")
            isInstanceOf<Int>(item) -> println("Int: $item")
            isInstanceOf<Boolean>(item) -> println("Boolean: $item")
            isInstanceOf<Double>(item) -> println("Double: $item")
        }
    }
    
    // 3. COLLECTION FILTERING WITH REIFIED
    
    inline fun <reified T> List<*>.filterIsInstance(): List<T> {
        return this.filterIsInstance<T>()
    }
    
    inline fun <reified T> List<*>.firstInstanceOf(): T? {
        return this.firstOrNull { it is T } as? T
    }
    
    inline fun <reified T> List<*>.countInstancesOf(): Int {
        return this.count { it is T }
    }
    
    // Usage
    val mixedItems = listOf("hello", 42, "world", 3.14, true)
    val strings = mixedItems.filterIsInstance<String>()  // ["hello", "world"]
    val firstNumber = mixedItems.firstInstanceOf<Number>()  // 42
    val numberCount = mixedItems.countInstancesOf<Number>()  // 2
    
    // 4. JSON PARSING WITH REIFIED
    
    // Simulated JSON parser using reified types
    inline fun <reified T> parseJson(json: String): T {
        return when (T::class) {
            String::class -> json.removeSurrounding("\"") as T
            Int::class -> json.toInt() as T
            Boolean::class -> json.toBoolean() as T
            Double::class -> json.toDouble() as T
            List::class -> parseJsonList<Any>(json) as T
            else -> throw IllegalArgumentException("Unsupported type: ${T::class}")
        }
    }
    
    inline fun <reified T> parseJsonList(json: String): List<T> {
        // Simplified JSON array parsing
        val items = json.removeSurrounding("[", "]")
            .split(",")
            .map { it.trim() }
        
        return items.map { parseJson<T>(it) }
    }
    
    // Usage
    val stringValue = parseJson<String>("\"hello\"")  // "hello"
    val intValue = parseJson<Int>("42")               // 42
    val boolValue = parseJson<Boolean>("true")        // true
    
    // 5. GENERIC ARRAY CREATION WITH REIFIED
    
    // Cannot create generic arrays normally
    // fun <T> createArray(size: Int): Array<T> = Array<T>(size) { ... }  // Error
    
    // With reified, array creation is possible
    inline fun <reified T> createArray(size: Int, init: (Int) -> T): Array<T> {
        return Array(size, init)
    }
    
    inline fun <reified T> arrayOfNulls(size: Int): Array<T?> {
        return arrayOfNulls<T>(size)
    }
    
    // Usage
    val stringArray = createArray<String>(5) { "item$it" }
    val intArray = createArray<Int>(3) { it * 2 }
    val nullableArray = arrayOfNulls<String>(3)
    
    // 6. REFLECTION WITH REIFIED
    
    inline fun <reified T> getClassName(): String {
        return T::class.simpleName ?: "Unknown"
    }
    
    inline fun <reified T> getClassInfo(): String {
        val kClass = T::class
        return buildString {
            append("Class: ${kClass.simpleName}\n")
            append("Qualified name: ${kClass.qualifiedName}\n")
            append("Is data class: ${kClass.isData}\n")
            append("Is sealed: ${kClass.isSealed}\n")
            append("Constructors: ${kClass.constructors.size}\n")
            append("Properties: ${kClass.memberProperties.size}\n")
        }
    }
    
    // Usage
    println(getClassName<String>())  // "String"
    println(getClassInfo<List<String>>())  // Detailed class information
    
    // 7. DEPENDENCY INJECTION WITH REIFIED
    
    class ServiceContainer {
        private val services = mutableMapOf<KClass<*>, Any>()
        
        fun <T : Any> register(service: T) {
            services[service::class] = service
        }
        
        inline fun <reified T : Any> get(): T {
            return services[T::class] as? T 
                ?: throw IllegalArgumentException("Service ${T::class.simpleName} not registered")
        }
        
        inline fun <reified T : Any> getOrNull(): T? {
            return services[T::class] as? T
        }
    }
    
    // Usage
    class UserService
    class EmailService
    
    val container = ServiceContainer()
    container.register(UserService())
    container.register(EmailService())
    
    val userService = container.get<UserService>()
    val emailService = container.get<EmailService>()
    
    // 8. GENERIC BUILDERS WITH REIFIED
    
    inline fun <reified T> buildSet(builder: MutableSet<T>.() -> Unit): Set<T> {
        return mutableSetOf<T>().apply(builder)
    }
    
    inline fun <reified K, reified V> buildMap(builder: MutableMap<K, V>.() -> Unit): Map<K, V> {
        return mutableMapOf<K, V>().apply(builder)
    }
    
    // Usage
    val stringSet = buildSet<String> {
        add("hello")
        add("world")
    }
    
    val stringToIntMap = buildMap<String, Int> {
        put("one", 1)
        put("two", 2)
    }
    
    // 9. SERIALIZATION WITH REIFIED
    
    // Simulated serialization framework
    interface Serializer<T> {
        fun serialize(obj: T): String
        fun deserialize(data: String): T
    }
    
    class JsonSerializer<T> : Serializer<T> {
        override fun serialize(obj: T): String = obj.toString()
        override fun deserialize(data: String): T {
            throw NotImplementedError("Deserialization not implemented")
        }
    }
    
    object SerializerRegistry {
        private val serializers = mutableMapOf<KClass<*>, Serializer<*>>()
        
        fun <T : Any> register(serializer: Serializer<T>, clazz: KClass<T>) {
            serializers[clazz] = serializer
        }
        
        inline fun <reified T : Any> get(): Serializer<T> {
            return serializers[T::class] as? Serializer<T>
                ?: throw IllegalArgumentException("No serializer for ${T::class.simpleName}")
        }
    }
    
    inline fun <reified T : Any> serialize(obj: T): String {
        return SerializerRegistry.get<T>().serialize(obj)
    }
    
    // 10. ADVANCED REIFIED PATTERNS
    
    // Generic event system with reified types
    class EventBus {
        private val subscribers = mutableMapOf<KClass<*>, MutableList<(Any) -> Unit>>()
        
        inline fun <reified T : Any> subscribe(noinline handler: (T) -> Unit) {
            subscribers.getOrPut(T::class) { mutableListOf() }
                .add { handler(it as T) }
        }
        
        inline fun <reified T : Any> publish(event: T) {
            subscribers[T::class]?.forEach { it(event) }
        }
        
        inline fun <reified T : Any> unsubscribe() {
            subscribers.remove(T::class)
        }
    }
    
    // Usage
    data class UserLoginEvent(val userId: String)
    data class UserLogoutEvent(val userId: String)
    
    val eventBus = EventBus()
    
    eventBus.subscribe<UserLoginEvent> { event ->
        println("User logged in: ${event.userId}")
    }
    
    eventBus.subscribe<UserLogoutEvent> { event ->
        println("User logged out: ${event.userId}")
    }
    
    eventBus.publish(UserLoginEvent("user123"))
    eventBus.publish(UserLogoutEvent("user123"))
    
    // Generic validation with reified types
    inline fun <reified T> validate(obj: Any, validator: (T) -> Boolean): Boolean {
        return if (obj is T) {
            validator(obj)
        } else {
            false
        }
    }
    
    // Usage
    val isValidString = validate<String>("hello") { it.length > 3 }  // true
    val isValidInt = validate<Int>(42) { it > 0 }  // true
    val isValidWrongType = validate<String>(42) { it.length > 3 }  // false
    
    // Limitations of reified:
    // 1. Only works with inline functions
    // 2. Cannot be used in class type parameters
    // 3. Cannot be used with noinline lambda parameters
    // 4. May increase bytecode size due to inlining
    // 5. Limited to compile-time accessible types
    
    // Best practices:
    // 1. Use reified when you need runtime type information
    // 2. Keep reified functions small and focused
    // 3. Consider performance implications of inlining
    // 4. Use reified for type-safe APIs
    // 5. Combine with reflection carefully
    ```

46. **Explain PECS (Producer Extends Consumer Super) principle**
    
    **Answer:** PECS (Producer Extends Consumer Super) is a principle for using wildcards in generics. "Producer Extends" means use `? extends T` (or `out T` in Kotlin) when you're reading from a structure, and "Consumer Super" means use `? super T` (or `in T` in Kotlin) when you're writing to a structure.
    
    ```kotlin
    // 1. PRODUCER EXTENDS (out T - Covariance)
    // Use when you're READING/PRODUCING values from a collection
    
    interface Producer<out T> {
        fun produce(): T  // Can return T
        // fun consume(item: T)  // Cannot accept T as parameter
    }
    
    // Example: Reading from a collection
    fun copyFromSource(source: List<out Number>) {
        // Can read Number or its subtypes
        source.forEach { number ->
            println("Number: ${number.toDouble()}")
        }
        
        // Cannot add to source (it's read-only for us)
        // source.add(42)  // Compilation error if it were MutableList
    }
    
    // Usage
    val integers: List<Int> = listOf(1, 2, 3)
    val doubles: List<Double> = listOf(1.1, 2.2, 3.3)
    
    copyFromSource(integers)  // OK - Int is subtype of Number
    copyFromSource(doubles)   // OK - Double is subtype of Number
    
    // 2. CONSUMER SUPER (in T - Contravariance)
    // Use when you're WRITING/CONSUMING values to a collection
    
    interface Consumer<in T> {
        fun consume(item: T)  // Can accept T
        // fun produce(): T  // Cannot return T
    }
    
    // Example: Writing to a collection
    fun copyToDestination(destination: MutableList<in String>, items: List<String>) {
        // Can add String items to destination
        destination.addAll(items)
        
        // Cannot read specific type from destination (returns Any?)
        // val item: String = destination[0]  // Compilation error
    }
    
    // Usage
    val strings = listOf("hello", "world")
    val anyList: MutableList<Any> = mutableListOf()
    val charSequenceList: MutableList<CharSequence> = mutableListOf()
    
    copyToDestination(anyList, strings)          // OK - Any is supertype of String
    copyToDestination(charSequenceList, strings) // OK - CharSequence is supertype of String
    
    // 3. PRACTICAL PECS EXAMPLES
    
    // Collections.copy() equivalent
    fun <T> copy(destination: MutableList<in T>, source: List<out T>) {
        for (item in source) {
            destination.add(item)
        }
    }
    
    // Usage
    val numbers: List<Int> = listOf(1, 2, 3)
    val anyDestination: MutableList<Any> = mutableListOf()
    
    copy(anyDestination, numbers)  // OK - follows PECS
    
    // Comparator example (Consumer)
    fun <T> sort(list: MutableList<T>, comparator: Comparator<in T>) {
        list.sortWith(comparator)
    }
    
    val strings = mutableListOf("banana", "apple", "cherry")
    val anyComparator = Comparator<Any> { a, b -> a.toString().compareTo(b.toString()) }
    
    sort(strings, anyComparator)  // OK - Comparator<Any> can compare Strings
    
    // PECS Memory Aids:
    // - PRODUCER extends: Use "out" when you GET values out
    // - CONSUMER super: Use "in" when you PUT values in
    // - If you're reading, use "out" (covariance)
    // - If you're writing, use "in" (contravariance)
    // - If you're doing both, use invariant (no variance)
    ```

47. **What is contravariance and covariance?**
    
    **Answer:** Covariance and contravariance describe how subtyping relationships between types relate to subtyping relationships between their generic containers.
    
    ```kotlin
    // 1. BASIC CONCEPTS
    
    // Type hierarchy: Dog -> Animal -> Any
    open class Animal(val name: String)
    class Dog(name: String) : Animal(name)
    
    // COVARIANCE (out) - preserves subtype relationship
    // If Dog <: Animal, then Producer<Dog> <: Producer<Animal>
    interface Producer<out T> {
        fun produce(): T
    }
    
    val dogProducer: Producer<Dog> = object : Producer<Dog> {
        override fun produce(): Dog = Dog("Buddy")
    }
    
    // Covariance allows this assignment
    val animalProducer: Producer<Animal> = dogProducer  // OK
    
    // CONTRAVARIANCE (in) - reverses subtype relationship
    // If Dog <: Animal, then Consumer<Animal> <: Consumer<Dog>
    interface Consumer<in T> {
        fun consume(item: T)
    }
    
    val animalConsumer: Consumer<Animal> = object : Consumer<Animal> {
        override fun consume(item: Animal) {
            println("Consuming animal: ${item.name}")
        }
    }
    
    // Contravariance allows this assignment
    val dogConsumer: Consumer<Dog> = animalConsumer  // OK
    
    // 2. WHY COVARIANCE WORKS
    
    // Covariant example - reading is safe
    fun feedFromProducer(producer: Producer<Animal>) {
        val animal = producer.produce()  // We get an Animal (or subtype)
        println("Fed animal: ${animal.name}")
    }
    
    // Safe to pass Dog producer - Dog is-a Animal
    feedFromProducer(dogProducer)  // Safe: Dog can be treated as Animal
    
    // 3. WHY CONTRAVARIANCE WORKS
    
    // Contravariant example - writing is safe
    fun feedToConsumer(consumer: Consumer<Dog>) {
        val dog = Dog("Rex")
        consumer.consume(dog)  // We provide a Dog
    }
    
    // Safe to pass Animal consumer - it can handle any Animal (including Dog)
    feedToConsumer(animalConsumer)  // Safe: Animal consumer can handle Dog
    
    // Function types are contravariant in parameters, covariant in return type
    val animalToString: (Animal) -> String = { "Animal: ${it.name}" }
    val dogToString: (Dog) -> String = animalToString  // OK - contravariant in parameter
    ```

48. **How do you handle generic constraints?**
    
    **Answer:** Generic constraints limit the types that can be used as type arguments using upper bounds, multiple bounds, and where clauses.
    
    ```kotlin
    // 1. SINGLE UPPER BOUND CONSTRAINTS
    
    // Basic upper bound - T must be Number or its subtype
    fun <T : Number> addNumbers(a: T, b: T): Double {
        return a.toDouble() + b.toDouble()
    }
    
    // Generic class with constraint
    class NumberContainer<T : Number>(val value: T) {
        fun getDoubleValue(): Double = value.toDouble()
        fun isPositive(): Boolean = value.toDouble() > 0
    }
    
    // 2. MULTIPLE CONSTRAINTS WITH WHERE CLAUSE
    
    interface Named {
        val name: String
    }
    
    interface Aged {
        val age: Int
    }
    
    // Function with multiple constraints
    fun <T> processEntity(entity: T): String 
        where T : Named, 
              T : Aged {
        return "Entity: ${entity.name}, Age: ${entity.age}"
    }
    
    // Implementation
    class Person(
        override val name: String,
        override val age: Int
    ) : Named, Aged
    
    // Usage
    val person = Person("John", 30)
    println(processEntity(person))    // OK - implements all interfaces
    
    // 3. COMPARABLE CONSTRAINTS
    
    fun <T : Comparable<T>> sort(items: MutableList<T>) {
        items.sortWith { a, b -> a.compareTo(b) }
    }
    
    fun <T : Comparable<T>> findMax(items: List<T>): T? {
        return items.maxOrNull()
    }
    
    // 4. COLLECTION CONSTRAINTS
    
    fun <T, C : Collection<T>> processCollection(collection: C): String {
        return "Collection type: ${collection::class.simpleName}, size: ${collection.size}"
    }
    
    fun <T, C : MutableCollection<T>> addToCollection(collection: C, item: T): C {
        collection.add(item)
        return collection
    }
    ```

49. **What are higher-kinded types?**
    
    **Answer:** Higher-kinded types are types that take other types as parameters. While Kotlin doesn't have native support, you can simulate them using patterns and libraries like Arrow.
    
    ```kotlin
    // 1. SIMULATING HKT WITH TYPE CLASSES
    
    // Kind wrapper to simulate higher-kinded types
    interface Kind<out F, out A>
    
    // Type class for Functor
    interface Functor<F> {
        fun <A, B> Kind<F, A>.map(f: (A) -> B): Kind<F, B>
    }
    
    // 2. OPTION/MAYBE IMPLEMENTATION
    
    sealed class Option<out A> : Kind<ForOption, A> {
        companion object
    }
    
    object None : Option<Nothing>()
    data class Some<A>(val value: A) : Option<A>()
    
    class ForOption private constructor()
    
    fun <A> Kind<ForOption, A>.fix(): Option<A> = this as Option<A>
    
    // Functor instance for Option
    object OptionFunctor : Functor<ForOption> {
        override fun <A, B> Kind<ForOption, A>.map(f: (A) -> B): Kind<ForOption, B> =
            when (val option = fix()) {
                is None -> None
                is Some -> Some(f(option.value))
            }
    }
    
    // Usage
    val some = Some(42)
    with(OptionFunctor) {
        val mapped = some.map { it * 2 }  // Some(84)
    }
    ```

50. **Explain type aliases and their use cases**
    
    **Answer:** Type aliases create alternative names for existing types, making code more readable and maintainable without runtime overhead.
    
    ```kotlin
    // 1. BASIC TYPE ALIASES
    
    // Simple type aliases
    typealias UserId = String
    typealias Age = Int
    typealias Score = Double
    
    // Function type aliases
    typealias EventHandler = (String) -> Unit
    typealias Validator<T> = (T) -> Boolean
    typealias Mapper<T, R> = (T) -> R
    
    // Collection type aliases
    typealias UserList = List<User>
    typealias UserMap = Map<String, User>
    
    // Usage
    fun processUser(id: UserId, age: Age): Score {
        return age * 10.0
    }
    
    val handler: EventHandler = { event -> println("Event: $event") }
    
    // 2. COMPLEX GENERIC TYPE ALIASES
    
    // Nested generic types
    typealias Repository<T> = Map<String, List<T>>
    typealias Cache<K, V> = MutableMap<K, V>
    typealias ResultCallback<T> = (Result<T>) -> Unit
    
    // Function types with multiple parameters
    typealias BiFunction<T, U, R> = (T, U) -> R
    typealias Predicate<T> = (T) -> Boolean
    
    // Suspend function aliases
    typealias AsyncOperation<T> = suspend () -> T
    typealias AsyncCallback<T> = suspend (T) -> Unit
    
    // 3. DOMAIN-SPECIFIC TYPE ALIASES
    
    // Financial domain
    typealias Money = BigDecimal
    typealias Currency = String
    typealias AccountNumber = String
    
    data class Transaction(
        val from: AccountNumber,
        val to: AccountNumber,
        val amount: Money,
        val currency: Currency
    )
    
    // Web development
    typealias HttpStatus = Int
    typealias Headers = Map<String, String>
    typealias QueryParams = Map<String, List<String>>
    
    // Benefits:
    // - Improved code readability
    // - Better domain modeling
    // - Easier refactoring
    // - Self-documenting code
    // - No runtime overhead
    ```

### Coroutines

51. **What are Kotlin coroutines?**
    
    **Answer:** Coroutines are a concurrency design pattern that allows you to write asynchronous, non-blocking code in a sequential manner. They provide a way to perform long-running tasks without blocking threads.
    
    ```kotlin
    // 1. BASIC COROUTINE CONCEPTS
    
    // Traditional callback approach (callback hell)
    fun fetchUserData(userId: String, callback: (User?) -> Unit) {
        networkCall(userId) { user ->
            if (user != null) {
                fetchUserPosts(user.id) { posts ->
                    if (posts != null) {
                        callback(user.copy(posts = posts))
                    } else {
                        callback(null)
                    }
                }
            } else {
                callback(null)
            }
        }
    }
    
    // Coroutine approach (sequential and readable)
    suspend fun fetchUserData(userId: String): User? {
        val user = networkCall(userId) ?: return null
        val posts = fetchUserPosts(user.id) ?: return null
        return user.copy(posts = posts)
    }
    
    // 2. COROUTINE ADVANTAGES
    
    // Lightweight - millions can run concurrently
    fun demonstrateLightweight() {
        runBlocking {
            val jobs = List(100_000) {
                launch {
                    delay(1000)
                    println("Coroutine $it completed")
                }
            }
            jobs.forEach { it.join() }
        }
    }
    
    // Non-blocking - doesn't block the calling thread
    suspend fun nonBlockingExample() {
        println("Before delay")
        delay(1000) // Suspends coroutine, doesn't block thread
        println("After delay")
    }
    
    // 3. COROUTINE BUILDING BLOCKS
    
    // Suspend functions - can be paused and resumed
    suspend fun downloadFile(url: String): ByteArray {
        delay(1000) // Simulate network delay
        return "file content".toByteArray()
    }
    
    // Coroutine builders - create and start coroutines
    fun coroutineBuilders() {
        // launch - fire and forget
        GlobalScope.launch {
            println("Background task")
        }
        
        // async - returns a result
        val deferred = GlobalScope.async {
            downloadFile("https://example.com/file.txt")
        }
        
        // runBlocking - blocks current thread
        runBlocking {
            val result = deferred.await()
            println("Downloaded ${result.size} bytes")
        }
    }
    
    // 4. PRACTICAL EXAMPLE: API CALLS
    
    class UserRepository {
        private val apiService = ApiService()
        
        suspend fun getUser(id: String): User? = try {
            apiService.getUser(id)
        } catch (e: Exception) {
            null
        }
        
        suspend fun getUserPosts(userId: String): List<Post> = try {
            apiService.getUserPosts(userId)
        } catch (e: Exception) {
            emptyList()
        }
        
        suspend fun getUserWithPosts(id: String): UserWithPosts? {
            val user = getUser(id) ?: return null
            val posts = getUserPosts(id)
            return UserWithPosts(user, posts)
        }
        
        // Concurrent API calls
        suspend fun getUserData(id: String): UserData? {
            return try {
                // Run calls concurrently
                val userDeferred = async { getUser(id) }
                val postsDeferred = async { getUserPosts(id) }
                val friendsDeferred = async { getUserFriends(id) }
                
                val user = userDeferred.await() ?: return null
                val posts = postsDeferred.await()
                val friends = friendsDeferred.await()
                
                UserData(user, posts, friends)
            } catch (e: Exception) {
                null
            }
        }
    }
    
    // 5. ANDROID-SPECIFIC USAGE
    
    class MainActivity : AppCompatActivity() {
        private val userRepository = UserRepository()
        
        override fun onCreate(savedInstanceState: Bundle?) {
            super.onCreate(savedInstanceState)
            
            // Launch coroutine in lifecycle scope
            lifecycleScope.launch {
                val userData = userRepository.getUserData("user123")
                userData?.let { updateUI(it) }
            }
        }
        
        private fun updateUI(userData: UserData) {
            // Update UI on main thread
            textView.text = userData.user.name
            postsAdapter.submitList(userData.posts)
        }
    }
    
    // ViewModel with coroutines
    class UserViewModel : ViewModel() {
        private val userRepository = UserRepository()
        
        private val _userData = MutableLiveData<UserData?>()
        val userData: LiveData<UserData?> = _userData
        
        fun loadUser(id: String) {
            viewModelScope.launch {
                _userData.value = userRepository.getUserData(id)
            }
        }
    }
    
    // 6. COROUTINE STATES
    
    fun demonstrateCoroutineStates() {
        val job = GlobalScope.launch {
            println("Coroutine started")
            delay(1000)
            println("Coroutine completed")
        }
        
        println("Job state: ${job.isActive}")   // true
        println("Job completed: ${job.isCompleted}") // false
        println("Job cancelled: ${job.isCancelled}") // false
        
        // Cancel the job
        job.cancel()
        println("Job cancelled: ${job.isCancelled}") // true
    }
    
    // 7. ERROR HANDLING IN COROUTINES
    
    suspend fun robustNetworkCall(): Result<String> = try {
        val response = apiCall()
        Result.success(response)
    } catch (e: Exception) {
        Result.failure(e)
    }
    
    fun handleCoroutineErrors() {
        GlobalScope.launch {
            try {
                val result = robustNetworkCall()
                result.fold(
                    onSuccess = { data -> println("Success: $data") },
                    onFailure = { error -> println("Error: ${error.message}") }
                )
            } catch (e: Exception) {
                println("Unexpected error: ${e.message}")
            }
        }
    }
    
    // 8. COROUTINE PERFORMANCE
    
    // Sequential execution
    suspend fun sequentialExecution(): Long {
        val startTime = System.currentTimeMillis()
        
        val result1 = apiCall1() // Takes 1 second
        val result2 = apiCall2() // Takes 1 second
        val result3 = apiCall3() // Takes 1 second
        
        return System.currentTimeMillis() - startTime // ~3 seconds
    }
    
    // Concurrent execution
    suspend fun concurrentExecution(): Long {
        val startTime = System.currentTimeMillis()
        
        val deferred1 = async { apiCall1() }
        val deferred2 = async { apiCall2() }
        val deferred3 = async { apiCall3() }
        
        val result1 = deferred1.await()
        val result2 = deferred2.await()
        val result3 = deferred3.await()
        
        return System.currentTimeMillis() - startTime // ~1 second
    }
    
    // 9. REAL-WORLD PATTERNS
    
    // Producer-Consumer pattern
    class DataProcessor {
        private val channel = Channel<String>(Channel.UNLIMITED)
        
        fun startProducer() = GlobalScope.launch {
            repeat(100) { i ->
                channel.send("Data $i")
                delay(100)
            }
            channel.close()
        }
        
        fun startConsumer() = GlobalScope.launch {
            for (data in channel) {
                processData(data)
            }
        }
        
        private suspend fun processData(data: String) {
            delay(50) // Simulate processing
            println("Processed: $data")
        }
    }
    
    // Retry mechanism with coroutines
    suspend fun <T> retryWithExponentialBackoff(
        maxRetries: Int = 3,
        initialDelay: Long = 1000,
        factor: Double = 2.0,
        block: suspend () -> T
    ): T {
        var currentDelay = initialDelay
        repeat(maxRetries - 1) {
            try {
                return block()
            } catch (e: Exception) {
                delay(currentDelay)
                currentDelay = (currentDelay * factor).toLong()
            }
        }
        return block() // Last attempt
    }
    
    // Usage
    suspend fun reliableApiCall(): String {
        return retryWithExponentialBackoff {
            apiCall() // May throw exception
        }
    }
    
    // Benefits of coroutines:
    // 1. Lightweight - millions can run concurrently
    // 2. Sequential code for asynchronous operations
    // 3. Built-in cancellation support
    // 4. Structured concurrency
    // 5. No callback hell
    // 6. Easy error handling
    // 7. Great Android integration
    ```

52. **Explain the difference between `suspend` functions and regular functions**
    
    **Answer:** Suspend functions are special functions that can be paused and resumed without blocking the thread, while regular functions execute completely before returning.
    
    ```kotlin
    // 1. BASIC DIFFERENCES
    
    // Regular function - blocks the thread
    fun regularFunction(): String {
        Thread.sleep(1000) // Blocks the calling thread for 1 second
        return "Result"
    }
    
    // Suspend function - suspends the coroutine
    suspend fun suspendFunction(): String {
        delay(1000) // Suspends coroutine for 1 second, doesn't block thread
        return "Result"
    }
    
    // 2. CALLING CONVENTIONS
    
    fun demonstrateCallingConventions() {
        // Regular function can be called from anywhere
        val result1 = regularFunction()
        
        // Suspend function can only be called from:
        // 1. Another suspend function
        // 2. Inside a coroutine
        
        GlobalScope.launch {
            val result2 = suspendFunction() // OK - inside coroutine
        }
        
        runBlocking {
            val result3 = suspendFunction() // OK - inside runBlocking
        }
        
        // val result4 = suspendFunction() // ERROR - cannot call from regular function
    }
    
    // 3. THREAD BEHAVIOR
    
    fun threadBehaviorDemo() {
        println("Thread behavior demo")
        
        // Regular function blocks thread
        thread {
            println("Regular function start - Thread: ${Thread.currentThread().name}")
            regularFunction() // Thread is blocked here
            println("Regular function end - Thread: ${Thread.currentThread().name}")
        }
        
        // Suspend function doesn't block thread
        GlobalScope.launch {
            println("Suspend function start - Thread: ${Thread.currentThread().name}")
            suspendFunction() // Coroutine is suspended, thread is free
            println("Suspend function end - Thread: ${Thread.currentThread().name}")
        }
    }
    
    // 4. COMPILATION DIFFERENCES
    
    // Suspend function is compiled with an additional Continuation parameter
    // suspend fun example() -> fun example(continuation: Continuation<Unit>)
    
    suspend fun compilationExample(value: Int): String {
        delay(100)
        return "Value: $value"
    }
    
    // Compiled roughly to:
    // fun compilationExample(value: Int, continuation: Continuation<String>): Any
    
    // 5. PRACTICAL EXAMPLES
    
    class NetworkService {
        // Regular function - blocking
        fun fetchDataBlocking(url: String): String {
            // This would block the calling thread
            Thread.sleep(2000) // Simulate network call
            return "Data from $url"
        }
        
        // Suspend function - non-blocking
        suspend fun fetchData(url: String): String {
            delay(2000) // Simulate network call without blocking
            return "Data from $url"
        }
        
        // Suspend function with actual HTTP call
        suspend fun fetchDataFromApi(url: String): String = withContext(Dispatchers.IO) {
            // Actual network implementation would go here
            URL(url).readText()
        }
    }
    
    // 6. SEQUENTIAL VS CONCURRENT WITH SUSPEND FUNCTIONS
    
    class DataService {
        suspend fun getData1(): String {
            delay(1000)
            return "Data 1"
        }
        
        suspend fun getData2(): String {
            delay(1000)
            return "Data 2"
        }
        
        // Sequential execution - takes 2 seconds
        suspend fun getDataSequential(): Pair<String, String> {
            val data1 = getData1() // Wait 1 second
            val data2 = getData2() // Wait another 1 second
            return data1 to data2
        }
        
        // Concurrent execution - takes 1 second
        suspend fun getDataConcurrent(): Pair<String, String> {
            val deferred1 = async { getData1() } // Start immediately
            val deferred2 = async { getData2() } // Start immediately
            return deferred1.await() to deferred2.await() // Wait for both
        }
    }
    
    // 7. ERROR HANDLING DIFFERENCES
    
    fun errorHandlingRegular() {
        try {
            val result = regularFunction()
            println("Success: $result")
        } catch (e: Exception) {
            println("Error: ${e.message}")
        }
    }
    
    suspend fun errorHandlingSuspend() {
        try {
            val result = suspendFunction()
            println("Success: $result")
        } catch (e: Exception) {
            println("Error: ${e.message}")
        }
    }
    
    // 8. CANCELLATION SUPPORT
    
    // Regular function cannot be cancelled
    fun longRunningRegular(): String {
        repeat(10) {
            Thread.sleep(1000) // Cannot be interrupted cooperatively
            println("Working... $it")
        }
        return "Done"
    }
    
    // Suspend function supports cancellation
    suspend fun longRunningSuspend(): String {
        repeat(10) {
            delay(1000) // Checks for cancellation
            println("Working... $it")
        }
        return "Done"
    }
    
    fun demonstrateCancellation() {
        val job = GlobalScope.launch {
            try {
                longRunningSuspend()
            } catch (e: CancellationException) {
                println("Cancelled!")
            }
        }
        
        // Cancel after 3 seconds
        GlobalScope.launch {
            delay(3000)
            job.cancel()
        }
    }
    
    // 9. ANDROID SPECIFIC EXAMPLES
    
    class UserRepository {
        // Regular function - would block UI thread if called from main thread
        fun getUserBlocking(id: String): User {
            Thread.sleep(2000) // Simulate database/network call
            return User(id, "John Doe")
        }
        
        // Suspend function - can be called from main thread safely
        suspend fun getUser(id: String): User = withContext(Dispatchers.IO) {
            delay(2000) // Simulate database/network call
            User(id, "John Doe")
        }
    }
    
    class MainActivity : AppCompatActivity() {
        private val userRepository = UserRepository()
        
        override fun onCreate(savedInstanceState: Bundle?) {
            super.onCreate(savedInstanceState)
            
            // BAD: This would block the UI thread
            // val user = userRepository.getUserBlocking("123")
            
            // GOOD: This doesn't block the UI thread
            lifecycleScope.launch {
                val user = userRepository.getUser("123")
                updateUI(user)
            }
        }
        
        private fun updateUI(user: User) {
            // Update UI elements
        }
    }
    
    // 10. PERFORMANCE IMPLICATIONS
    
    fun performanceComparison() {
        val startTime = System.currentTimeMillis()
        
        // Regular functions with threads (expensive)
        val threads = List(1000) {
            thread {
                Thread.sleep(1000)
                println("Thread $it completed")
            }
        }
        threads.forEach { it.join() }
        
        val threadsTime = System.currentTimeMillis() - startTime
        println("Threads took: ${threadsTime}ms")
        
        // Suspend functions with coroutines (lightweight)
        runBlocking {
            val jobs = List(1000) {
                launch {
                    delay(1000)
                    println("Coroutine $it completed")
                }
            }
            jobs.forEach { it.join() }
        }
        
        val coroutinesTime = System.currentTimeMillis() - startTime - threadsTime
        println("Coroutines took: ${coroutinesTime}ms")
    }
    
    // Key differences summary:
    // 1. Suspend functions can be paused and resumed
    // 2. They don't block threads
    // 3. Can only be called from coroutines or other suspend functions
    // 4. Support cooperative cancellation
    // 5. Are compiled with continuation-passing style
    // 6. Enable sequential-looking asynchronous code
    // 7. Are much more lightweight than threads
    ```

53. **What is a CoroutineScope?**
    
    **Answer:** CoroutineScope defines the scope in which coroutines run and provides structured concurrency by ensuring all child coroutines complete before the scope completes.
    
    ```kotlin
    // 1. BASIC COROUTINE SCOPE CONCEPT
    
    // CoroutineScope defines the lifecycle of coroutines
    interface CoroutineScope {
        val coroutineContext: CoroutineContext
    }
    
    // Creating a basic scope
    fun basicScopeExample() {
        val scope = CoroutineScope(Dispatchers.Default)
        
        scope.launch {
            println("Coroutine in custom scope")
            delay(1000)
            println("Coroutine completed")
        }
    }
    
    // 2. BUILT-IN SCOPES
    
    fun builtInScopes() {
        // GlobalScope - application lifetime (use sparingly)
        GlobalScope.launch {
            println("Global scope coroutine")
        }
        
        // MainScope - for UI components
        val mainScope = MainScope()
        mainScope.launch {
            println("Main scope coroutine")
        }
        
        // Custom scope with specific context
        val customScope = CoroutineScope(
            Dispatchers.IO + SupervisorJob() + CoroutineName("CustomScope")
        )
        customScope.launch {
            println("Custom scope coroutine")
        }
    }
    
    // 3. STRUCTURED CONCURRENCY
    
    suspend fun structuredConcurrencyExample() = coroutineScope {
        println("Parent scope started")
        
        launch {
            delay(1000)
            println("Child 1 completed")
        }
        
        launch {
            delay(2000)
            println("Child 2 completed")
        }
        
        async {
            delay(1500)
            "Child 3 result"
        }
        
        println("All children will complete before this scope ends")
    }
    
    // 4. ANDROID LIFECYCLE SCOPES
    
    class MainActivity : AppCompatActivity() {
        override fun onCreate(savedInstanceState: Bundle?) {
            super.onCreate(savedInstanceState)
            
            // lifecycleScope - tied to activity lifecycle
            lifecycleScope.launch {
                // Automatically cancelled when activity is destroyed
                loadData()
            }
            
            // Launch when activity is at least STARTED
            lifecycleScope.launchWhenStarted {
                // Paused when activity goes to background
                updateUI()
            }
            
            // Launch when activity is RESUMED
            lifecycleScope.launchWhenResumed {
                // Only runs when activity is in foreground
                startLocationUpdates()
            }
        }
        
        private suspend fun loadData() {
            delay(1000)
            println("Data loaded")
        }
        
        private suspend fun updateUI() {
            delay(500)
            println("UI updated")
        }
        
        private suspend fun startLocationUpdates() {
            println("Location updates started")
        }
    }
    
    // ViewModel scope
    class UserViewModel : ViewModel() {
        fun loadUser(id: String) {
            // viewModelScope - automatically cancelled when ViewModel is cleared
            viewModelScope.launch {
                try {
                    val user = userRepository.getUser(id)
                    _userLiveData.value = user
                } catch (e: Exception) {
                    _errorLiveData.value = e.message
                }
            }
        }
    }
    
    // 5. CUSTOM SCOPE IMPLEMENTATION
    
    class NetworkManager : CoroutineScope {
        private val job = SupervisorJob()
        
        override val coroutineContext: CoroutineContext
            get() = Dispatchers.IO + job + CoroutineName("NetworkManager")
        
        fun fetchData(url: String) = launch {
            try {
                val data = httpClient.get(url)
                processData(data)
            } catch (e: Exception) {
                handleError(e)
            }
        }
        
        fun shutdown() {
            job.cancel() // Cancels all coroutines in this scope
        }
        
        private suspend fun processData(data: String) {
            delay(100)
            println("Data processed: $data")
        }
        
        private fun handleError(e: Exception) {
            println("Error: ${e.message}")
        }
    }
    
    // 6. SCOPE CANCELLATION
    
    class ScopeCancellationExample {
        private val scope = CoroutineScope(Dispatchers.Default + SupervisorJob())
        
        fun startWork() {
            scope.launch {
                repeat(10) { i ->
                    if (isActive) { // Check if coroutine is still active
                        delay(1000)
                        println("Work iteration $i")
                    } else {
                        println("Work cancelled")
                        return@launch
                    }
                }
            }
        }
        
        fun cancelWork() {
            scope.cancel() // Cancels all coroutines in the scope
        }
        
        fun cancelWithMessage() {
            scope.cancel(CancellationException("User requested cancellation"))
        }
    }
    
    // 7. EXCEPTION HANDLING IN SCOPES
    
    class ExceptionHandlingExample {
        fun demonstrateExceptionHandling() {
            // Scope with exception handler
            val handler = CoroutineExceptionHandler { context, exception ->
                println("Caught exception: ${exception.message}")
            }
            
            val scope = CoroutineScope(Dispatchers.Default + handler)
            
            scope.launch {
                throw RuntimeException("Something went wrong")
            }
            
            // With SupervisorJob - one child failure doesn't affect others
            val supervisorScope = CoroutineScope(
                Dispatchers.Default + SupervisorJob() + handler
            )
            
            supervisorScope.launch {
                throw RuntimeException("Child 1 failed")
            }
            
            supervisorScope.launch {
                delay(1000)
                println("Child 2 completed successfully")
            }
        }
    }
    
    // 8. SCOPE NESTING AND INHERITANCE
    
    suspend fun scopeNestingExample() = coroutineScope {
        println("Outer scope")
        
        launch {
            println("Child coroutine 1")
            
            // Nested scope
            coroutineScope {
                launch {
                    delay(1000)
                    println("Nested child 1")
                }
                
                launch {
                    delay(2000)
                    println("Nested child 2")
                }
            }
            
            println("Child coroutine 1 completed")
        }
        
        launch {
            delay(3000)
            println("Child coroutine 2")
        }
        
        println("Outer scope will wait for all children")
    }
    
    // 9. TESTING WITH SCOPES
    
    class TestingScopeExample {
        @Test
        fun testCoroutineScope() = runTest {
            val testScope = TestScope()
            
            testScope.launch {
                delay(1000)
                println("Test coroutine")
            }
            
            testScope.advanceTimeBy(1000) // Advance virtual time
            // Assertions here
        }
        
        @Test
        fun testWithTestDispatcher() = runTest {
            val testDispatcher = StandardTestDispatcher()
            val scope = CoroutineScope(testDispatcher)
            
            scope.launch {
                delay(1000)
                println("Test with custom dispatcher")
            }
            
            advanceUntilIdle() // Run all pending coroutines
        }
    }
    
    // 10. BEST PRACTICES FOR SCOPES
    
    class BestPracticesExample {
        // DON'T: Use GlobalScope for regular operations
        fun badExample() {
            GlobalScope.launch {
                // This coroutine will outlive the component
                loadData()
            }
        }
        
        // DO: Use appropriate lifecycle-aware scopes
        class GoodActivity : AppCompatActivity() {
            override fun onCreate(savedInstanceState: Bundle?) {
                super.onCreate(savedInstanceState)
                
                lifecycleScope.launch {
                    // Automatically cancelled when activity is destroyed
                    loadData()
                }
            }
        }
        
        // DO: Create custom scopes for specific components
        class DataManager {
            private val scope = CoroutineScope(
                Dispatchers.IO + 
                SupervisorJob() + 
                CoroutineName("DataManager")
            )
            
            fun startDataSync() = scope.launch {
                // Data synchronization logic
            }
            
            fun cleanup() {
                scope.cancel()
            }
        }
        
        // DO: Use coroutineScope for structured concurrency
        suspend fun processDataStructured() = coroutineScope {
            val deferred1 = async { fetchData1() }
            val deferred2 = async { fetchData2() }
            
            // Both operations must complete before function returns
            combineData(deferred1.await(), deferred2.await())
        }
    }
    
    // Key points about CoroutineScope:
    // 1. Provides structured concurrency
    // 2. Ensures all child coroutines complete before scope completes
    // 3. Enables proper cancellation propagation
    // 4. Tied to component lifecycle in Android
    // 5. Prevents coroutine leaks
    // 6. Supports exception handling at scope level
    // 7. Can be customized with different contexts
    ```

54. **Explain different coroutine dispatchers (Main, IO, Default, Unconfined)**
    
    **Answer:** Dispatchers determine which thread or thread pool coroutines run on. Each dispatcher is optimized for different types of work.
    
    ```kotlin
    // 1. DISPATCHERS.MAIN - UI Thread
    
    // For UI operations and lightweight work
    fun mainDispatcherExample() {
        // In Android, runs on the main/UI thread
        GlobalScope.launch(Dispatchers.Main) {
            // Safe to update UI here
            textView.text = "Updated from coroutine"
            progressBar.visibility = View.GONE
            
            // Don't do heavy work here - will block UI
            // heavyComputation() // BAD - blocks UI
        }
    }
    
    // Main.immediate - optimization for same-thread dispatch
    fun mainImmediateExample() {
        GlobalScope.launch(Dispatchers.Main.immediate) {
            // If already on main thread, executes immediately
            // Otherwise, dispatches to main thread
            updateUI()
        }
    }
    
    // 2. DISPATCHERS.IO - I/O Operations
    
    // For disk reads, network calls, database operations
    suspend fun ioDispatcherExample() {
        withContext(Dispatchers.IO) {
            // File operations
            val fileContent = File("data.txt").readText()
            
            // Network calls
            val response = httpClient.get("https://api.example.com/data")
            
            // Database operations
            val users = database.userDao().getAllUsers()
            
            // Any blocking I/O operation
            val result = blockingIOOperation()
        }
    }
    
    class NetworkService {
        suspend fun fetchData(url: String): String = withContext(Dispatchers.IO) {
            // Network call on IO dispatcher
            URL(url).readText()
        }
        
        suspend fun saveToFile(data: String, filename: String) = withContext(Dispatchers.IO) {
            // File operation on IO dispatcher
            File(filename).writeText(data)
        }
    }
    
    // 3. DISPATCHERS.DEFAULT - CPU-Intensive Work
    
    // For computational work, data processing, algorithms
    suspend fun defaultDispatcherExample() {
        withContext(Dispatchers.Default) {
            // CPU-intensive calculations
            val result = heavyComputation()
            
            // Data processing
            val processedData = processLargeDataset(data)
            
            // Sorting large collections
            val sortedList = largeList.sorted()
            
            // Image processing
            val processedImage = processImage(bitmap)
        }
    }
    
    class DataProcessor {
        suspend fun processData(data: List<Int>): List<Int> = withContext(Dispatchers.Default) {
            data.map { it * it } // CPU-intensive operation
                .filter { it > 100 }
                .sorted()
        }
        
        suspend fun calculatePrimes(limit: Int): List<Int> = withContext(Dispatchers.Default) {
            (2..limit).filter { isPrime(it) }
        }
        
        private fun isPrime(n: Int): Boolean {
            if (n < 2) return false
            for (i in 2..sqrt(n.toDouble()).toInt()) {
                if (n % i == 0) return false
            }
            return true
        }
    }
    
    // 4. DISPATCHERS.UNCONFINED - Not Recommended for General Use
    
    // Starts in caller thread, resumes in any thread
    fun unconfinedDispatcherExample() {
        GlobalScope.launch(Dispatchers.Unconfined) {
            println("Before delay - Thread: ${Thread.currentThread().name}")
            delay(1000)
            println("After delay - Thread: ${Thread.currentThread().name}")
            // Thread might be different after suspension
        }
    }
    
    // Mainly used for testing or specific advanced scenarios
    @Test
    fun testWithUnconfined() = runTest {
        launch(Dispatchers.Unconfined) {
            // Executes immediately without thread switching
            doSomething()
        }
    }
    
    // 5. CUSTOM DISPATCHERS
    
    class CustomDispatcherExample {
        // Single thread dispatcher
        private val singleThreadDispatcher = Executors.newSingleThreadExecutor().asCoroutineDispatcher()
        
        // Fixed thread pool dispatcher
        private val fixedThreadPoolDispatcher = Executors.newFixedThreadPool(4).asCoroutineDispatcher()
        
        // Limited parallelism dispatcher
        private val limitedParallelismDispatcher = Dispatchers.Default.limitedParallelism(2)
        
        suspend fun useCustomDispatchers() {
            // Single thread for sequential operations
            withContext(singleThreadDispatcher) {
                sequentialOperation1()
                sequentialOperation2()
            }
            
            // Fixed thread pool for parallel work
            withContext(fixedThreadPoolDispatcher) {
                parallelOperation()
            }
            
            // Limited parallelism
            withContext(limitedParallelismDispatcher) {
                // Only 2 threads from Default pool
                intensiveOperation()
            }
        }
        
        fun cleanup() {
            singleThreadDispatcher.close()
            fixedThreadPoolDispatcher.close()
        }
    }
    
    // 6. DISPATCHER SWITCHING
    
    class DispatcherSwitchingExample {
        suspend fun complexOperation() {
            // Start with background computation
            val computedData = withContext(Dispatchers.Default) {
                heavyComputation()
            }
            
            // Switch to IO for network call
            val networkData = withContext(Dispatchers.IO) {
                fetchFromNetwork(computedData)
            }
            
            // Switch to Main for UI update
            withContext(Dispatchers.Main) {
                updateUI(networkData)
            }
        }
        
        suspend fun optimizedDataFlow() {
            // Concurrent operations on appropriate dispatchers
            val computationDeferred = async(Dispatchers.Default) {
                heavyComputation()
            }
            
            val networkDeferred = async(Dispatchers.IO) {
                fetchFromNetwork()
            }
            
            // Wait for both and update UI
            val results = awaitAll(computationDeferred, networkDeferred)
            withContext(Dispatchers.Main) {
                updateUI(results)
            }
        }
    }
    
    // 7. ANDROID-SPECIFIC DISPATCHER USAGE
    
    class AndroidDispatcherExample : ViewModel() {
        private val repository = UserRepository()
        
        fun loadUserData(userId: String) {
            viewModelScope.launch {
                try {
                    // Show loading on Main thread
                    _loading.value = true
                    
                    // Fetch data on IO thread
                    val userData = withContext(Dispatchers.IO) {
                        repository.getUserData(userId)
                    }
                    
                    // Process data on Default thread if needed
                    val processedData = withContext(Dispatchers.Default) {
                        processUserData(userData)
                    }
                    
                    // Update UI on Main thread (viewModelScope uses Main by default)
                    _userData.value = processedData
                    _loading.value = false
                    
                } catch (e: Exception) {
                    _error.value = e.message
                    _loading.value = false
                }
            }
        }
    }
    
    // 8. PERFORMANCE CONSIDERATIONS
    
    class PerformanceExample {
        // DON'T: Use wrong dispatcher for the task
        suspend fun badExample() {
            withContext(Dispatchers.Main) {
                // BAD: Heavy computation on Main thread
                heavyComputation() // Blocks UI
            }
            
            withContext(Dispatchers.Default) {
                // BAD: File I/O on Default thread
                File("data.txt").readText() // Wastes CPU thread
            }
        }
        
        // DO: Use appropriate dispatcher
        suspend fun goodExample() {
            // CPU work on Default
            val result = withContext(Dispatchers.Default) {
                heavyComputation()
            }
            
            // I/O work on IO
            val data = withContext(Dispatchers.IO) {
                File("data.txt").readText()
            }
            
            // UI update on Main
            withContext(Dispatchers.Main) {
                updateUI(result, data)
            }
        }
    }
    
    // 9. TESTING DISPATCHERS
    
    class TestingDispatchersExample {
        @Test
        fun testWithTestDispatcher() = runTest {
            val testDispatcher = StandardTestDispatcher()
            
            launch(testDispatcher) {
                delay(1000)
                println("Test completed")
            }
            
            advanceTimeBy(1000)
            // Test assertions
        }
        
        @Test
        fun testMainDispatcher() = runTest {
            // Use TestDispatcher for Main in tests
            Dispatchers.setMain(StandardTestDispatcher())
            
            launch(Dispatchers.Main) {
                // Test main thread operations
            }
            
            Dispatchers.resetMain()
        }
    }
    
    // 10. DISPATCHER GUIDELINES
    
    /*
    DISPATCHER SELECTION GUIDE:
    
    Dispatchers.Main:
    - UI updates
    - Lightweight operations
    - Calling suspend functions
    - LiveData updates
    
    Dispatchers.IO:
    - Network requests
    - File operations
    - Database operations
    - Any blocking I/O
    
    Dispatchers.Default:
    - CPU-intensive work
    - Data processing
    - Sorting/filtering large collections
    - Mathematical calculations
    - Image/video processing
    
    Dispatchers.Unconfined:
    - Testing scenarios
    - Advanced use cases
    - Generally avoid in production
    
    THREAD POOL SIZES:
    - Main: 1 thread (UI thread)
    - IO: 64 threads (or more based on system)
    - Default: Number of CPU cores
    - Unconfined: No dedicated threads
    */
    
    class DispatcherBestPractices {
        // Use withContext for switching dispatchers
        suspend fun correctDispatcherSwitch() {
            val data = withContext(Dispatchers.IO) {
                fetchData()
            }
            
            val processed = withContext(Dispatchers.Default) {
                processData(data)
            }
            
            withContext(Dispatchers.Main) {
                updateUI(processed)
            }
        }
        
        // Avoid unnecessary dispatcher switches
        suspend fun avoidUnnecessarySwitches() {
            withContext(Dispatchers.IO) {
                val data1 = fetchData1()
                val data2 = fetchData2() // Stay on same dispatcher
                val data3 = fetchData3() // Stay on same dispatcher
                combineData(data1, data2, data3)
            }
        }
    }
    ```

55. **What is the difference between `launch` and `async`?**
    
    **Answer:** `launch` is used for fire-and-forget operations that don't return a value, while `async` is used for concurrent operations that return a result.
    
    ```kotlin
    // 1. BASIC DIFFERENCES
    
    fun basicDifferences() {
        // launch - returns Job, no result value
        val job: Job = GlobalScope.launch {
            delay(1000)
            println("Launch completed")
            // No return value
        }
        
        // async - returns Deferred<T>, has result value
        val deferred: Deferred<String> = GlobalScope.async {
            delay(1000)
            "Async result" // Returns a value
        }
        
        // Getting the result
        runBlocking {
            job.join() // Wait for completion, no result
            val result = deferred.await() // Wait for completion and get result
            println("Result: $result")
        }
    }
    
    // 2. RETURN TYPES AND USAGE
    
    suspend fun returnTypesExample() {
        // launch returns Job
        val job = launch {
            performBackgroundTask()
        }
        
        // Can check job status
        println("Job active: ${job.isActive}")
        println("Job completed: ${job.isCompleted}")
        
        // Can cancel job
        job.cancel()
        
        // async returns Deferred<T>
        val deferred = async {
            computeValue()
        }
        
        // Deferred extends Job, so has all Job capabilities
        println("Deferred active: ${deferred.isActive}")
        
        // Plus ability to get result
        val result = deferred.await()
        println("Computed value: $result")
    }
    
    // 3. CONCURRENT OPERATIONS
    
    suspend fun concurrentOperationsExample() {
        // Using launch - fire and forget multiple operations
        val job1 = launch {
            delay(1000)
            println("Task 1 completed")
        }
        
        val job2 = launch {
            delay(2000)
            println("Task 2 completed")
        }
        
        // Wait for all to complete
        joinAll(job1, job2)
        
        // Using async - concurrent operations with results
        val deferred1 = async {
            delay(1000)
            "Result 1"
        }
        
        val deferred2 = async {
            delay(2000)
            "Result 2"
        }
        
        // Get all results
        val results = awaitAll(deferred1, deferred2)
        println("All results: $results")
    }
    
    // 4. PRACTICAL EXAMPLES
    
    class NetworkService {
        suspend fun fetchUser(id: String): User {
            delay(1000) // Simulate network call
            return User(id, "User $id")
        }
        
        suspend fun fetchUserPosts(userId: String): List<Post> {
            delay(1500) // Simulate network call
            return listOf(Post("Post 1"), Post("Post 2"))
        }
        
        suspend fun fetchUserFriends(userId: String): List<User> {
            delay(800) // Simulate network call
            return listOf(User("friend1", "Friend 1"))
        }
        
        // Using launch for independent operations
        fun startBackgroundSync(userIds: List<String>) {
            userIds.forEach { userId ->
                GlobalScope.launch {
                    try {
                        val user = fetchUser(userId)
                        saveUserToCache(user)
                        println("User $userId synced")
                    } catch (e: Exception) {
                        println("Failed to sync user $userId: ${e.message}")
                    }
                }
            }
        }
        
        // Using async for dependent operations
        suspend fun getUserDashboard(userId: String): UserDashboard {
            // Start all operations concurrently
            val userDeferred = async { fetchUser(userId) }
            val postsDeferred = async { fetchUserPosts(userId) }
            val friendsDeferred = async { fetchUserFriends(userId) }
            
            // Wait for all results
            val user = userDeferred.await()
            val posts = postsDeferred.await()
            val friends = friendsDeferred.await()
            
            return UserDashboard(user, posts, friends)
        }
        
        // Sequential vs Concurrent comparison
        suspend fun sequentialFetch(userId: String): UserDashboard {
            // Takes ~3.3 seconds (1000 + 1500 + 800)
            val user = fetchUser(userId)
            val posts = fetchUserPosts(userId)
            val friends = fetchUserFriends(userId)
            
            return UserDashboard(user, posts, friends)
        }
        
        suspend fun concurrentFetch(userId: String): UserDashboard {
            // Takes ~1.5 seconds (max of 1000, 1500, 800)
            return coroutineScope {
                val userDeferred = async { fetchUser(userId) }
                val postsDeferred = async { fetchUserPosts(userId) }
                val friendsDeferred = async { fetchUserFriends(userId) }
                
                UserDashboard(
                    userDeferred.await(),
                    postsDeferred.await(),
                    friendsDeferred.await()
                )
            }
        }
    }
    
    // 5. ERROR HANDLING DIFFERENCES
    
    suspend fun errorHandlingExample() {
        // launch - exceptions are handled by CoroutineExceptionHandler
        val job = launch {
            throw RuntimeException("Launch error")
        }
        
        // Exception is not propagated to caller
        job.join() // Completes normally even though coroutine failed
        
        // async - exceptions are stored in Deferred
        val deferred = async {
            throw RuntimeException("Async error")
        }
        
        try {
            deferred.await() // Exception is thrown here
        } catch (e: Exception) {
            println("Caught async exception: ${e.message}")
        }
    }
    
    // 6. EXCEPTION PROPAGATION
    
    class ExceptionPropagationExample {
        suspend fun launchExceptionHandling() = coroutineScope {
            val handler = CoroutineExceptionHandler { _, exception ->
                println("Caught in handler: ${exception.message}")
            }
            
            // Exception handled by handler
            launch(handler) {
                throw RuntimeException("Launch exception")
            }
            
            // This continues normally
            println("After launch with exception")
        }
        
        suspend fun asyncExceptionHandling() = coroutineScope {
            val deferred = async {
                throw RuntimeException("Async exception")
            }
            
            // Exception not thrown until await() is called
            println("Async started, no exception yet")
            
            try {
                deferred.await()
            } catch (e: Exception) {
                println("Exception caught at await: ${e.message}")
            }
        }
    }
    
    // 7. CANCELLATION BEHAVIOR
    
    suspend fun cancellationExample() {
        val job = launch {
            repeat(10) { i ->
                delay(1000)
                println("Launch iteration $i")
            }
        }
        
        val deferred = async {
            repeat(10) { i ->
                delay(1000)
                println("Async iteration $i")
                i
            }
        }
        
        delay(3000)
        
        // Cancel both
        job.cancel()
        deferred.cancel()
        
        // job.join() // Completes immediately after cancellation
        
        try {
            val result = deferred.await() // Throws CancellationException
        } catch (e: CancellationException) {
            println("Async was cancelled")
        }
    }
    
    // 8. PERFORMANCE CONSIDERATIONS
    
    class PerformanceExample {
        // When you don't need the result, use launch
        fun fireAndForgetOperations() {
            repeat(1000) { i ->
                GlobalScope.launch {
                    logEvent("Event $i")
                }
            }
        }
        
        // When you need results, use async
        suspend fun gatherResults(): List<String> {
            val deferreds = (1..1000).map { i ->
                async {
                    processItem(i)
                }
            }
            return deferreds.awaitAll()
        }
        
        // Mixed usage
        suspend fun mixedOperations() {
            // Start background logging (don't wait for result)
            launch {
                logAnalytics()
            }
            
            // Get data we need
            val userData = async { fetchUserData() }
            val settingsData = async { fetchSettingsData() }
            
            // Use the data we need
            val user = userData.await()
            val settings = settingsData.await()
            
            updateUI(user, settings)
        }
    }
    
    // 9. TESTING DIFFERENCES
    
    class TestingExample {
        @Test
        fun testLaunch() = runTest {
            val job = launch {
                delay(1000)
                performOperation()
            }
            
            advanceTimeBy(1000)
            job.join()
            
            // Verify operation was performed
            verify { performOperation() }
        }
        
        @Test
        fun testAsync() = runTest {
            val deferred = async {
                delay(1000)
                computeValue()
            }
            
            advanceTimeBy(1000)
            val result = deferred.await()
            
            assertEquals("expected", result)
        }
    }
    
    // 10. REAL-WORLD PATTERNS
    
    class RealWorldPatterns {
        // Pattern 1: Fire-and-forget logging
        fun logUserAction(action: String) {
            GlobalScope.launch {
                analyticsService.log(action)
            }
        }
        
        // Pattern 2: Parallel data fetching
        suspend fun loadDashboard(): Dashboard = coroutineScope {
            val newsDeferred = async { newsService.getLatestNews() }
            val weatherDeferred = async { weatherService.getCurrentWeather() }
            val stocksDeferred = async { stockService.getPortfolio() }
            
            Dashboard(
                news = newsDeferred.await(),
                weather = weatherDeferred.await(),
                stocks = stocksDeferred.await()
            )
        }
        
        // Pattern 3: Background processing with status updates
        suspend fun processLargeFile(file: File) = coroutineScope {
            // Start background processing
            val processingJob = launch {
                processFileInBackground(file)
            }
            
            // Start status updates
            val statusJob = launch {
                while (processingJob.isActive) {
                    updateProgress()
                    delay(1000)
                }
            }
            
            // Wait for processing to complete
            processingJob.join()
            statusJob.cancel()
        }
        
        // Pattern 4: Timeout with async
        suspend fun fetchWithTimeout(): String? = try {
            withTimeout(5000) {
                async { fetchData() }.await()
            }
        } catch (e: TimeoutCancellationException) {
            null
        }
    }
    
    // DECISION GUIDE:
    /*
    Use LAUNCH when:
    - Fire-and-forget operations
    - Side effects (logging, analytics)
    - Independent background tasks
    - You don't need a return value
    - Exception handling via CoroutineExceptionHandler
    
    Use ASYNC when:
    - You need a return value
    - Concurrent operations that produce results
    - Parallel computations
    - Exception handling via try-catch on await()
    - Building complex data from multiple sources
    */
    ```
61. **What is `SupervisorJob` and when to use it?**

**Answer:** `SupervisorJob` is a special type of job that doesn't cancel its children when one of them fails. It's useful when you want to isolate failures and prevent them from propagating to sibling coroutines.

```kotlin
// Regular Job - failure propagates to siblings
val job = Job()
val child1 = launch(job) { 
    delay(100)
    throw Exception("Child 1 failed")
}
val child2 = launch(job) { 
    delay(200)
    println("Child 2 completed") // This won't execute
}
// Both children are cancelled when child1 fails

// SupervisorJob - failure doesn't propagate
val supervisorJob = SupervisorJob()
val child1 = launch(supervisorJob) { 
    delay(100)
    throw Exception("Child 1 failed")
}
val child2 = launch(supervisorJob) { 
    delay(200)
    println("Child 2 completed") // This will execute
}
// Only child1 is cancelled, child2 continues

// Real-world example: Multiple API calls
class UserService {
    suspend fun loadUserData(userId: String): UserData = coroutineScope {
        val supervisorJob = SupervisorJob()
        
        val profileDeferred = async(supervisorJob) { 
            apiService.getUserProfile(userId) 
        }
        val postsDeferred = async(supervisorJob) { 
            apiService.getUserPosts(userId) 
        }
        val friendsDeferred = async(supervisorJob) { 
            apiService.getUserFriends(userId) 
        }
        
        try {
            UserData(
                profile = profileDeferred.await(),
                posts = postsDeferred.await(),
                friends = friendsDeferred.await()
            )
        } catch (e: Exception) {
            // Handle partial failures gracefully
            UserData(
                profile = profileDeferred.awaitOrNull(),
                posts = postsDeferred.awaitOrNull(),
                friends = friendsDeferred.awaitOrNull()
            )
        }
    }
}
```

**Use cases:**
- Multiple independent operations where one failure shouldn't affect others
- UI updates from multiple data sources
- Parallel API calls where partial success is acceptable
- Background tasks that should continue even if some fail

62. **Explain flow in Kotlin coroutines**

**Answer:** Flow is Kotlin's reactive streams API that emits multiple values over time. It's designed for asynchronous data streams and follows the cold stream pattern.

```kotlin
// Basic Flow creation
fun simpleFlow(): Flow<Int> = flow {
    for (i in 1..5) {
        delay(100)
        emit(i)
    }
}

// Flow collection
fun collectFlow() {
    lifecycleScope.launch {
        simpleFlow()
            .collect { value ->
                println("Received: $value")
            }
    }
}

// Flow with different operators
fun advancedFlow(): Flow<String> = flow {
    emit("Hello")
    delay(1000)
    emit("World")
    delay(1000)
    emit("!")
}
.map { it.uppercase() }
.filter { it.length > 2 }

// Real-world example: API pagination
class NewsRepository {
    fun getNewsFlow(page: Int): Flow<List<News>> = flow {
        var currentPage = page
        while (true) {
            val news = apiService.getNews(currentPage)
            emit(news)
            if (news.isEmpty()) break
            currentPage++
            delay(5000) // Wait before next page
        }
    }
}

// Flow with error handling
fun safeFlow(): Flow<Result<String>> = flow {
    try {
        emit(Result.success("Success"))
        delay(100)
        throw Exception("Error")
    } catch (e: Exception) {
        emit(Result.failure(e))
    }
}
.catch { e ->
    emit(Result.failure(e))
}
```

**Key characteristics:**
- Cold streams (start emitting only when collected)
- Sequential by default
- Built-in backpressure handling
- Cancellable and cooperative
- Thread-safe

63. **What is the difference between hot and cold flows?**

**Answer:** The main difference is when and how many times the flow starts emitting values.

**Cold Flows (Regular Flow):**
```kotlin
// Cold flow - starts emitting only when collected
fun coldFlow(): Flow<Int> = flow {
    println("Starting cold flow")
    for (i in 1..3) {
        delay(100)
        emit(i)
        println("Emitted: $i")
    }
}

// Multiple collectors get separate streams
fun demonstrateColdFlow() {
    lifecycleScope.launch {
        val flow = coldFlow()
        
        // First collector
        launch {
            flow.collect { value ->
                println("Collector 1: $value")
            }
        }
        
        // Second collector (starts fresh)
        launch {
            flow.collect { value ->
                println("Collector 2: $value")
            }
        }
    }
}
// Output:
// Starting cold flow
// Emitted: 1
// Collector 1: 1
// Emitted: 2
// Collector 1: 2
// Starting cold flow  // Second collector starts fresh
// Emitted: 1
// Collector 2: 1
// Emitted: 2
// Collector 2: 2
```

**Hot Flows (StateFlow, SharedFlow):**
```kotlin
// Hot flow - emits regardless of collectors
val hotFlow = MutableStateFlow(0)

fun demonstrateHotFlow() {
    lifecycleScope.launch {
        // Start emitting
        launch {
            repeat(5) {
                delay(100)
                hotFlow.value = it
            }
        }
        
        // First collector
        launch {
            hotFlow.collect { value ->
                println("Collector 1: $value")
            }
        }
        
        // Second collector (joins existing stream)
        delay(200)
        launch {
            hotFlow.collect { value ->
                println("Collector 2: $value")
            }
        }
    }
}
// Output:
// Collector 1: 0
// Collector 1: 1
// Collector 1: 2
// Collector 2: 2  // Joins at current value
// Collector 1: 3
// Collector 2: 3
```

**Key differences:**
- **Cold flows:** Start fresh for each collector, like a function call
- **Hot flows:** Share the same stream across collectors, like a broadcast
- **Cold flows:** Good for one-time data streams (API calls, file reading)
- **Hot flows:** Good for ongoing state updates (UI state, sensor data)

64. **How do you handle backpressure in flows?**

**Answer:** Backpressure occurs when a producer emits values faster than a consumer can process them. Kotlin Flow provides several strategies to handle this.

```kotlin
// 1. Buffer - stores emitted values
fun bufferedFlow(): Flow<Int> = flow {
    for (i in 1..1000) {
        emit(i)
        delay(10) // Fast emission
    }
}
.buffer(100) // Buffer 100 values

// 2. Conflate - keeps only the latest value
fun conflatedFlow(): Flow<Int> = flow {
    for (i in 1..1000) {
        emit(i)
        delay(10)
    }
}
.conflate() // Only latest value is processed

// 3. CollectLatest - cancels previous collection
fun collectLatestFlow(): Flow<Int> = flow {
    for (i in 1..1000) {
        emit(i)
        delay(10)
    }
}

// Usage
lifecycleScope.launch {
    collectLatestFlow()
        .collectLatest { value ->
            delay(100) // Slow processing
            println("Processing: $value")
        }
}

// 4. Sample - takes samples at regular intervals
fun sampledFlow(): Flow<Int> = flow {
    for (i in 1..1000) {
        emit(i)
        delay(10)
    }
}
.sample(100) // Sample every 100ms

// 5. Throttle - limits emission rate
fun throttledFlow(): Flow<Int> = flow {
    for (i in 1..1000) {
        emit(i)
        delay(10)
    }
}
.throttle(100) // Max one emission per 100ms

// Real-world example: Sensor data processing
class SensorManager {
    private val sensorFlow = MutableSharedFlow<SensorData>()
    
    fun processSensorData(): Flow<ProcessedData> = sensorFlow
        .buffer(50) // Buffer sensor readings
        .conflate() // Keep only latest if processing is slow
        .map { sensorData ->
            // Heavy processing
            processSensorData(sensorData)
        }
        .flowOn(Dispatchers.Default) // Process on background thread
}
```

**Strategy selection:**
- **Buffer:** When you need all values but processing is slower
- **Conflate:** When only the latest value matters
- **CollectLatest:** When you want to cancel previous processing
- **Sample:** When you need periodic updates
- **Throttle:** When you want to limit emission rate

65. **What are flow operators (`map`, `filter`, `collect`, etc.)?**

**Answer:** Flow operators are functions that transform, filter, or combine flows. They create new flows without modifying the original.

```kotlin
// 1. Transform operators
fun transformOperators() {
    val flow = flowOf(1, 2, 3, 4, 5)
    
    // Map - transform each value
    flow.map { it * 2 }
        .collect { println("Mapped: $it") }
    
    // Transform - more flexible transformation
    flow.transform { value ->
        emit("Number: $value")
        emit("Double: ${value * 2}")
    }
    .collect { println("Transformed: $it") }
    
    // FlatMap - transform to another flow
    flow.flatMapConcat { value ->
        flow {
            emit(value)
            emit(value * 10)
        }
    }
    .collect { println("FlatMapped: $it") }
}

// 2. Filter operators
fun filterOperators() {
    val flow = flowOf(1, 2, 3, 4, 5, 6, 7, 8, 9, 10)
    
    // Filter - keep only matching values
    flow.filter { it % 2 == 0 }
        .collect { println("Even: $it") }
    
    // Take - take first N values
    flow.take(3)
        .collect { println("First 3: $it") }
    
    // Drop - skip first N values
    flow.drop(5)
        .collect { println("After 5: $it") }
    
    // Distinct - remove duplicates
    flowOf(1, 1, 2, 2, 3, 3)
        .distinctUntilChanged()
        .collect { println("Distinct: $it") }
}

// 3. Combine operators
fun combineOperators() {
    val flow1 = flowOf(1, 2, 3)
    val flow2 = flowOf("A", "B", "C")
    
    // Combine - combine latest values
    flow1.combine(flow2) { number, letter ->
        "$number$letter"
    }
    .collect { println("Combined: $it") }
    
    // Zip - pair values in order
    flow1.zip(flow2) { number, letter ->
        "$number$letter"
    }
    .collect { println("Zipped: $it") }
    
    // Merge - merge multiple flows
    merge(flow1, flow2.map { it.toString() })
        .collect { println("Merged: $it") }
}

// 4. Terminal operators
fun terminalOperators() {
    val flow = flowOf(1, 2, 3, 4, 5)
    
    // Collect - consume all values
    flow.collect { println("Collected: $it") }
    
    // First - get first value
    val first = flow.first()
    println("First: $first")
    
    // Last - get last value
    val last = flow.last()
    println("Last: $last")
    
    // Count - count total values
    val count = flow.count()
    println("Count: $count")
    
    // Reduce - accumulate values
    val sum = flow.reduce { acc, value -> acc + value }
    println("Sum: $sum")
    
    // Fold - accumulate with initial value
    val result = flow.fold("") { acc, value -> "$acc$value" }
    println("Folded: $result")
}

// 5. Error handling operators
fun errorHandlingOperators() {
    val flow = flow {
        emit(1)
        throw Exception("Error")
        emit(2)
    }
    
    // Catch - handle exceptions
    flow.catch { e ->
        emit(-1) // Emit fallback value
        println("Caught: ${e.message}")
    }
    .collect { println("With catch: $it") }
    
    // OnEach - side effects
    flow.onEach { println("About to emit: $it") }
        .onStart { println("Flow started") }
        .onCompletion { println("Flow completed") }
        .collect { println("Collected: $it") }
}

// Real-world example: Data processing pipeline
class DataProcessor {
    fun processUserData(): Flow<ProcessedUser> = userFlow
        .filter { it.isActive } // Only active users
        .map { user -> user.copy(name = user.name.trim()) } // Clean data
        .transform { user ->
            // Enrich with additional data
            val profile = getUserProfile(user.id)
            emit(user.copy(profile = profile))
        }
        .buffer(10) // Handle backpressure
        .flowOn(Dispatchers.IO) // Process on IO thread
}
```

66. **Explain `StateFlow` and `SharedFlow`**

**Answer:** `StateFlow` and `SharedFlow` are hot flows that emit values to multiple collectors. They're commonly used for state management and event broadcasting.

**StateFlow:**
```kotlin
// StateFlow - holds current state and emits updates
class UserViewModel : ViewModel() {
    private val _userState = MutableStateFlow<UserState>(UserState.Loading)
    val userState: StateFlow<UserState> = _userState.asStateFlow()
    
    fun loadUser(userId: String) {
        viewModelScope.launch {
            _userState.value = UserState.Loading
            try {
                val user = userRepository.getUser(userId)
                _userState.value = UserState.Success(user)
            } catch (e: Exception) {
                _userState.value = UserState.Error(e.message)
            }
        }
    }
}

// StateFlow characteristics
fun stateFlowCharacteristics() {
    val stateFlow = MutableStateFlow(0)
    
    // 1. Always has a value
    println("Initial value: ${stateFlow.value}")
    
    // 2. Emits current value to new collectors
    lifecycleScope.launch {
        stateFlow.collect { value ->
            println("Collector 1: $value")
        }
    }
    
    // 3. Only emits when value changes
    stateFlow.value = 1 // Emits
    stateFlow.value = 1 // No emission (same value)
    stateFlow.value = 2 // Emits
    
    // 4. Replays last value to new collectors
    lifecycleScope.launch {
        delay(1000)
        stateFlow.collect { value ->
            println("Late collector: $value") // Gets current value (2)
        }
    }
}

// SharedFlow - event stream without current value
class EventManager {
    private val _events = MutableSharedFlow<Event>()
    val events: SharedFlow<Event> = _events.asSharedFlow()
    
    suspend fun emitEvent(event: Event) {
        _events.emit(event)
    }
}

// SharedFlow characteristics
fun sharedFlowCharacteristics() {
    val sharedFlow = MutableSharedFlow<Int>()
    
    // 1. No initial value
    // println(sharedFlow.value) // Compilation error
    
    // 2. Emits to all active collectors
    lifecycleScope.launch {
        sharedFlow.collect { value ->
            println("Collector 1: $value")
        }
    }
    
    lifecycleScope.launch {
        sharedFlow.collect { value ->
            println("Collector 2: $value")
        }
    }
    
    // 3. New collectors don't get previous values
    lifecycleScope.launch {
        delay(1000)
        sharedFlow.collect { value ->
            println("Late collector: $value") // Won't get previous values
        }
    }
    
    // 4. Can configure replay
    val replayFlow = MutableSharedFlow<Int>(replay = 3)
    replayFlow.emit(1)
    replayFlow.emit(2)
    replayFlow.emit(3)
    
    // New collector gets last 3 values
    lifecycleScope.launch {
        replayFlow.collect { value ->
            println("Replay collector: $value") // Gets 1, 2, 3
        }
    }
}

// Real-world examples
class NewsApp {
    // StateFlow for UI state
    private val _uiState = MutableStateFlow<NewsUiState>(NewsUiState.Loading)
    val uiState: StateFlow<NewsUiState> = _uiState.asStateFlow()
    
    // SharedFlow for one-time events
    private val _navigationEvents = MutableSharedFlow<NavigationEvent>()
    val navigationEvents: SharedFlow<NavigationEvent> = _navigationEvents.asSharedFlow()
    
    fun loadNews() {
        viewModelScope.launch {
            _uiState.value = NewsUiState.Loading
            try {
                val news = newsRepository.getNews()
                _uiState.value = NewsUiState.Success(news)
            } catch (e: Exception) {
                _uiState.value = NewsUiState.Error(e.message)
            }
        }
    }
    
    fun navigateToDetail(newsId: String) {
        viewModelScope.launch {
            _navigationEvents.emit(NavigationEvent.NavigateToDetail(newsId))
        }
    }
}

// Usage in Compose
@Composable
fun NewsScreen(viewModel: NewsViewModel) {
    val uiState by viewModel.uiState.collectAsState()
    val navigationEvents = viewModel.navigationEvents.collectAsState(initial = null)
    
    LaunchedEffect(navigationEvents.value) {
        navigationEvents.value?.let { event ->
            when (event) {
                is NavigationEvent.NavigateToDetail -> {
                    // Navigate to detail screen
                }
            }
        }
    }
    
    when (uiState) {
        is NewsUiState.Loading -> LoadingSpinner()
        is NewsUiState.Success -> NewsList(uiState.news)
        is NewsUiState.Error -> ErrorMessage(uiState.message)
    }
}
```

**Key differences:**
- **StateFlow:** Has current value, replays to new collectors, good for state
- **SharedFlow:** No current value, no replay by default, good for events
- **StateFlow:** Use for UI state, configuration, persistent data
- **SharedFlow:** Use for one-time events, user actions, notifications

67. **What is `Channel` in coroutines?**

**Answer:** `Channel` is a communication primitive that allows coroutines to send and receive values. It's similar to a queue or pipe.

```kotlin
// Basic Channel usage
fun basicChannel() {
    val channel = Channel<Int>()
    
    // Producer
    lifecycleScope.launch {
        for (i in 1..5) {
            delay(100)
            channel.send(i)
            println("Sent: $i")
        }
        channel.close() // Signal completion
    }
    
    // Consumer
    lifecycleScope.launch {
        for (value in channel) {
            println("Received: $value")
        }
        println("Channel closed")
    }
}

// Channel types
fun channelTypes() {
    // 1. Rendezvous (default) - no buffer
    val rendezvousChannel = Channel<Int>()
    
    // 2. Buffered - fixed size buffer
    val bufferedChannel = Channel<Int>(capacity = 5)
    
    // 3. Unlimited - unlimited buffer
    val unlimitedChannel = Channel<Int>(Channel.UNLIMITED)
    
    // 4. Conflated - keeps only latest value
    val conflatedChannel = Channel<Int>(Channel.CONFLATED)
}

// Producer-consumer pattern
class DataProcessor {
    private val dataChannel = Channel<Data>(capacity = 10)
    
    fun startProcessing() {
        // Producer
        lifecycleScope.launch {
            repeat(100) {
                val data = generateData()
                dataChannel.send(data)
            }
            dataChannel.close()
        }
        
        // Consumer
        lifecycleScope.launch {
            for (data in dataChannel) {
                processData(data)
            }
        }
    }
}

// Channel with multiple consumers
fun multipleConsumers() {
    val channel = Channel<String>()
    
    // Producer
    lifecycleScope.launch {
        repeat(10) {
            channel.send("Message $it")
            delay(100)
        }
        channel.close()
    }
    
    // Multiple consumers
    repeat(3) { consumerId ->
        lifecycleScope.launch {
            for (message in channel) {
                println("Consumer $consumerId: $message")
            }
        }
    }
}

// Channel for coordination
class WorkCoordinator {
    private val workChannel = Channel<Work>()
    private val resultChannel = Channel<Result>()
    
    fun startWork() {
        // Worker
        lifecycleScope.launch {
            for (work in workChannel) {
                val result = performWork(work)
                resultChannel.send(result)
            }
        }
        
        // Coordinator
        lifecycleScope.launch {
            repeat(5) {
                val work = Work("Task $it")
                workChannel.send(work)
                
                val result = resultChannel.receive()
                println("Work completed: ${result.status}")
            }
            
            workChannel.close()
            resultChannel.close()
        }
    }
}

// Channel vs Flow
fun channelVsFlow() {
    // Channel - for one-time communication
    val channel = Channel<Int>()
    lifecycleScope.launch {
        channel.send(1)
        channel.send(2)
        channel.close()
    }
    
    // Flow - for continuous streams
    val flow = flow {
        emit(1)
        emit(2)
    }
    
    // Channel is hot, Flow is cold
    // Channel can be closed, Flow completes naturally
    // Channel is for communication, Flow is for data streams
}

// Real-world example: Image processing pipeline
class ImageProcessor {
    private val imageChannel = Channel<Image>(capacity = 5)
    private val processedChannel = Channel<ProcessedImage>()
    
    fun processImages() {
        // Image loader
        lifecycleScope.launch {
            val images = loadImages()
            for (image in images) {
                imageChannel.send(image)
            }
            imageChannel.close()
        }
        
        // Image processor
        repeat(3) { // 3 parallel processors
            lifecycleScope.launch {
                for (image in imageChannel) {
                    val processed = processImage(image)
                    processedChannel.send(processed)
                }
            }
        }
        
        // Result collector
        lifecycleScope.launch {
            for (processed in processedChannel) {
                saveProcessedImage(processed)
            }
        }
    }
}
```

**Key characteristics:**
- **Suspending:** `send()` and `receive()` are suspending functions
- **FIFO:** First-in-first-out order
- **Closeable:** Can be closed to signal completion
- **Thread-safe:** Safe for concurrent access
- **Different types:** Rendezvous, buffered, unlimited, conflated

68. **How do you test coroutines?**

**Answer:** Testing coroutines requires special handling due to their asynchronous nature. Kotlin provides testing utilities and best practices.

```kotlin
// 1. Using runTest (recommended for unit tests)
@Test
fun testCoroutine() = runTest {
    val result = async { 
        delay(1000)
        "Hello"
    }.await()
    
    assertEquals("Hello", result)
}

// 2. Using TestCoroutineDispatcher
@Test
fun testWithDispatcher() = runTest {
    val dispatcher = StandardTestDispatcher()
    Dispatchers.setMain(dispatcher)
    
    val viewModel = MyViewModel()
    viewModel.loadData()
    
    // Advance time to execute pending coroutines
    advanceTimeBy(1000)
    
    assertEquals("Expected data", viewModel.data.value)
    
    Dispatchers.resetMain()
}

// 3. Testing flows
@Test
fun testFlow() = runTest {
    val flow = flow {
        emit(1)
        delay(100)
        emit(2)
    }
    
    val values = mutableListOf<Int>()
    flow.toList(values)
    
    assertEquals(listOf(1, 2), values)
}

// 4. Testing StateFlow
@Test
fun testStateFlow() = runTest {
    val stateFlow = MutableStateFlow(0)
    
    val values = mutableListOf<Int>()
    val job = launch {
        stateFlow.toList(values)
    }
    
    stateFlow.value = 1
    stateFlow.value = 2
    
    job.cancel()
    
    assertEquals(listOf(0, 1, 2), values)
}

// 5. Testing with timeouts
@Test
fun testTimeout() = runTest {
    val result = withTimeoutOrNull(1000) {
        delay(500)
        "Success"
    }
    
    assertEquals("Success", result)
    
    val timeoutResult = withTimeoutOrNull(100) {
        delay(500)
        "Success"
    }
    
    assertNull(timeoutResult)
}

// 6. Testing exceptions
@Test
fun testException() = runTest {
    val exception = assertThrows<Exception> {
        async {
            delay(100)
            throw Exception("Test exception")
        }.await()
    }
    
    assertEquals("Test exception", exception.message)
}

// 7. Testing with mock dispatchers
@Test
fun testWithMockDispatcher() = runTest {
    val testDispatcher = StandardTestDispatcher()
    
    val result = withContext(testDispatcher) {
        delay(1000)
        "Result"
    }
    
    assertEquals("Result", result)
}

// 8. Testing ViewModel coroutines
class TestViewModel : ViewModel() {
    private val _data = MutableStateFlow<String?>(null)
    val data: StateFlow<String?> = _data.asStateFlow()
    
    fun loadData() {
        viewModelScope.launch {
            delay(1000)
            _data.value = "Loaded data"
        }
    }
}

@Test
fun testViewModel() = runTest {
    val viewModel = TestViewModel()
    
    val values = mutableListOf<String?>()
    val job = launch {
        viewModel.data.toList(values)
    }
    
    viewModel.loadData()
    advanceTimeBy(1000)
    
    job.cancel()
    
    assertEquals(listOf(null, "Loaded data"), values)
}

// 9. Testing with fake repositories
class FakeUserRepository : UserRepository {
    private val users = mutableListOf<User>()
    
    override suspend fun getUsers(): List<User> {
        delay(100) // Simulate network delay
        return users
    }
    
    fun addUser(user: User) {
        users.add(user)
    }
}

@Test
fun testUserRepository() = runTest {
    val repository = FakeUserRepository()
    val user = User("John", "john@example.com")
    repository.addUser(user)
    
    val users = repository.getUsers()
    
    assertEquals(1, users.size)
    assertEquals(user, users[0])
}

// 10. Testing complex scenarios
@Test
fun testComplexScenario() = runTest {
    val channel = Channel<Int>()
    val results = mutableListOf<Int>()
    
    // Producer
    launch {
        repeat(5) {
            channel.send(it)
            delay(100)
        }
        channel.close()
    }
    
    // Consumer
    launch {
        for (value in channel) {
            results.add(value)
        }
    }
    
    // Wait for completion
    advanceUntilIdle()
    
    assertEquals(listOf(0, 1, 2, 3, 4), results)
}

// 11. Testing with custom test rules
@get:Rule
val mainCoroutineRule = MainCoroutineRule()

class MainCoroutineRule : TestWatcher() {
    private val testDispatcher = StandardTestDispatcher()
    
    override fun starting(description: Description) {
        super.starting(description)
        Dispatchers.setMain(testDispatcher)
    }
    
    override fun finished(description: Description) {
        super.finished(description)
        Dispatchers.resetMain()
    }
}

// 12. Testing error handling
@Test
fun testErrorHandling() = runTest {
    val flow = flow {
        emit(1)
        throw Exception("Error")
    }
    .catch { e ->
        emit(-1)
    }
    
    val values = flow.toList()
    
    assertEquals(listOf(1, -1), values)
}
```

**Best practices:**
- Use `runTest` for unit tests
- Use `TestCoroutineDispatcher` for controlling time
- Test both success and failure scenarios
- Use fake repositories for testing
- Test timeouts and cancellations
- Use `advanceTimeBy()` and `advanceUntilIdle()` to control execution

69. **What is coroutine context and how do you combine contexts?**

**Answer:** Coroutine context is a set of elements that define the behavior of a coroutine. It includes dispatcher, job, exception handler, and other elements.

```kotlin
// Basic coroutine context
fun basicContext() {
    // Dispatcher - defines thread pool
    val dispatcher = Dispatchers.IO
    
    // Job - controls lifecycle
    val job = Job()
    
    // Exception handler - handles uncaught exceptions
    val exceptionHandler = CoroutineExceptionHandler { _, exception ->
        println("Caught exception: ${exception.message}")
    }
    
    // Combine contexts
    val context = dispatcher + job + exceptionHandler
    
    lifecycleScope.launch(context) {
        // Coroutine runs on IO dispatcher with custom job and exception handler
        println("Running on: ${Thread.currentThread().name}")
    }
}

// Context elements
fun contextElements() {
    // 1. Dispatcher
    val ioContext = Dispatchers.IO
    val mainContext = Dispatchers.Main
    val defaultContext = Dispatchers.Default
    val unconfinedContext = Dispatchers.Unconfined
    
    // 2. Job
    val parentJob = Job()
    val childJob = Job(parentJob)
    
    // 3. CoroutineName
    val namedContext = CoroutineName("MyCoroutine")
    
    // 4. CoroutineExceptionHandler
    val handler = CoroutineExceptionHandler { _, exception ->
        println("Exception: ${exception.message}")
    }
}

// Combining contexts
fun combineContexts() {
    // Using + operator
    val context1 = Dispatchers.IO + CoroutineName("IOOperation")
    val context2 = context1 + CoroutineExceptionHandler { _, _ -> }
    
    // Using coroutineScope
    lifecycleScope.launch(context2) {
        println("Name: ${coroutineContext[CoroutineName]}")
        println("Dispatcher: ${coroutineContext[ContinuationInterceptor]}")
    }
}

// Context inheritance
fun contextInheritance() {
    val parentContext = Dispatchers.Main + CoroutineName("Parent")
    
    lifecycleScope.launch(parentContext) {
        println("Parent: ${coroutineContext[CoroutineName]}")
        
        // Child inherits parent context
        launch {
            println("Child: ${coroutineContext[CoroutineName]}")
        }
        
        // Override specific elements
        launch(Dispatchers.IO) {
            println("Child with IO: ${coroutineContext[ContinuationInterceptor]}")
        }
    }
}

// Custom context elements
class CustomContextElement : CoroutineContext.Element {
    companion object Key : CoroutineContext.Key<CustomContextElement>
    override val key: CoroutineContext.Key<*> = Key
    
    val customValue = "Custom"
}

fun customContext() {
    val customContext = CustomContextElement()
    
    lifecycleScope.launch(customContext) {
        val element = coroutineContext[CustomContextElement]
        println("Custom value: ${element?.customValue}")
    }
}

// Context-aware functions
fun contextAwareFunction() {
    lifecycleScope.launch(Dispatchers.IO + CoroutineName("Network")) {
        val result = withContext(Dispatchers.Default + CoroutineName("Processing")) {
            // This block runs on Default dispatcher
            heavyComputation()
        }
        
        // Back to IO dispatcher
        saveResult(result)
    }
}

// Real-world example: API client with context
class ApiClient {
    suspend fun makeRequest(
        endpoint: String,
        context: CoroutineContext = Dispatchers.IO
    ): Response = withContext(context + CoroutineName("API-$endpoint")) {
        val startTime = System.currentTimeMillis()
        
        try {
            val response = apiService.request(endpoint)
            val duration = System.currentTimeMillis() - startTime
            println("API call to $endpoint took ${duration}ms")
            response
        } catch (e: Exception) {
            val duration = System.currentTimeMillis() - startTime
            println("API call to $endpoint failed after ${duration}ms")
            throw e
        }
    }
}

// Context for different use cases
class ContextExamples {
    // Network operations
    val networkContext = Dispatchers.IO + 
        CoroutineName("Network") + 
        CoroutineExceptionHandler { _, exception ->
            // Handle network errors
            println("Network error: ${exception.message}")
        }
    
    // Database operations
    val databaseContext = Dispatchers.IO + 
        CoroutineName("Database") + 
        CoroutineExceptionHandler { _, exception ->
            // Handle database errors
            println("Database error: ${exception.message}")
        }
    
    // UI operations
    val uiContext = Dispatchers.Main + 
        CoroutineName("UI") + 
        CoroutineExceptionHandler { _, exception ->
            // Handle UI errors
            println("UI error: ${exception.message}")
        }
    
    // Background processing
    val backgroundContext = Dispatchers.Default + 
        CoroutineName("Background") + 
        CoroutineExceptionHandler { _, exception ->
            // Handle background errors
            println("Background error: ${exception.message}")
        }
}

// Context composition patterns
class ContextComposition {
    // Base contexts
    private val baseContext = CoroutineExceptionHandler { _, exception ->
        println("Base error handler: ${exception.message}")
    }
    
    private val loggingContext = CoroutineName("Logging")
    
    // Composed contexts
    val networkContext = Dispatchers.IO + baseContext + loggingContext
    val uiContext = Dispatchers.Main + baseContext + loggingContext
    val backgroundContext = Dispatchers.Default + baseContext + loggingContext
    
    fun executeWithContext(
        context: CoroutineContext,
        block: suspend () -> Unit
    ) {
        lifecycleScope.launch(context) {
            block()
        }
    }
}
```

**Key concepts:**
- **Context elements:** Dispatcher, Job, CoroutineName, CoroutineExceptionHandler
- **Context combination:** Use `+` operator to combine elements
- **Context inheritance:** Children inherit parent context
- **Context override:** Can override specific elements in child coroutines
- **Custom elements:** Can create custom context elements

70. **Explain coroutine cancellation and cooperation**

**Answer:** Coroutine cancellation is cooperative, meaning coroutines must check for cancellation and respond appropriately.

```kotlin
// Basic cancellation
fun basicCancellation() {
    val job = lifecycleScope.launch {
        repeat(1000) { i ->
            delay(100)
            println("Job: $i")
        }
    }
    
    // Cancel after 1 second
    lifecycleScope.launch {
        delay(1000)
        job.cancel()
        println("Job cancelled")
    }
}

// Cooperative cancellation
fun cooperativeCancellation() {
    val job = lifecycleScope.launch {
        repeat(1000) { i ->
            // Check for cancellation
            if (isActive) {
                delay(100)
                println("Job: $i")
            } else {
                println("Job cancelled, stopping")
                break
            }
        }
    }
    
    lifecycleScope.launch {
        delay(1000)
        job.cancel()
    }
}

// Cancellation with cleanup
fun cancellationWithCleanup() {
    val job = lifecycleScope.launch {
        try {
            repeat(1000) { i ->
                delay(100)
                println("Job: $i")
            }
        } catch (e: CancellationException) {
            println("Job cancelled, cleaning up")
            // Perform cleanup
            cleanup()
            throw e // Re-throw to maintain cancellation
        }
    }
    
    lifecycleScope.launch {
        delay(1000)
        job.cancel()
    }
}

// Using ensureActive()
fun ensureActiveExample() {
    val job = lifecycleScope.launch {
        repeat(1000) { i ->
            ensureActive() // Throws CancellationException if cancelled
            delay(100)
            println("Job: $i")
        }
    }
    
    lifecycleScope.launch {
        delay(1000)
        job.cancel()
    }
}

// Cancellation in different dispatchers
fun cancellationInDispatchers() {
    val job = lifecycleScope.launch(Dispatchers.IO) {
        repeat(1000) { i ->
            ensureActive()
            // CPU-intensive work
            performHeavyComputation()
            println("Heavy computation: $i")
        }
    }
    
    lifecycleScope.launch {
        delay(1000)
        job.cancel()
    }
}

// Cancellation with timeout
fun cancellationWithTimeout() {
    lifecycleScope.launch {
        try {
            withTimeout(1000) {
                repeat(1000) { i ->
                    delay(100)
                    println("Timeout job: $i")
                }
            }
        } catch (e: TimeoutCancellationException) {
            println("Job timed out")
        }
    }
}

// Cancellation with withTimeoutOrNull
fun cancellationWithTimeoutOrNull() {
    lifecycleScope.launch {
        val result = withTimeoutOrNull(1000) {
            delay(500)
            "Success"
        }
        
        println("Result: $result") // "Success"
        
        val timeoutResult = withTimeoutOrNull(100) {
            delay(500)
            "Success"
        }
        
        println("Timeout result: $result") // null
    }
}

// Cancellation in flows
fun cancellationInFlows() {
    val flow = flow {
        repeat(1000) { i ->
            delay(100)
            emit(i)
        }
    }
    
    lifecycleScope.launch {
        flow.collect { value ->
            println("Flow value: $value")
            if (value > 5) {
                cancel() // Cancel the collection
            }
        }
    }
}

// Cancellation with SupervisorJob
fun cancellationWithSupervisor() {
    val supervisorJob = SupervisorJob()
    
    val child1 = lifecycleScope.launch(supervisorJob) {
        repeat(1000) { i ->
            delay(100)
            println("Child 1: $i")
        }
    }
    
    val child2 = lifecycleScope.launch(supervisorJob) {
        repeat(1000) { i ->
            delay(100)
            println("Child 2: $i")
        }
    }
    
    // Cancel only child1
    lifecycleScope.launch {
        delay(1000)
        child1.cancel()
        println("Child 1 cancelled")
    }
}

// Cancellation in ViewModel
class CancellableViewModel : ViewModel() {
    private var dataJob: Job? = null
    
    fun loadData() {
        // Cancel previous job
        dataJob?.cancel()
        
        dataJob = viewModelScope.launch {
            try {
                val data = repository.getData()
                _data.value = data
            } catch (e: CancellationException) {
                println("Data loading cancelled")
                throw e
            }
        }
    }
    
    override fun onCleared() {
        super.onCleared()
        dataJob?.cancel()
    }
}

// Cancellation with custom scope
class CustomScope {
    private val scope = CoroutineScope(Dispatchers.Main + SupervisorJob())
    
    fun startWork() {
        scope.launch {
            repeat(1000) { i ->
                ensureActive()
                delay(100)
                println("Custom scope work: $i")
            }
        }
    }
    
    fun cancelAll() {
        scope.cancel()
    }
}

// Real-world example: File download with cancellation
class FileDownloader {
    suspend fun downloadFile(
        url: String,
        onProgress: (Float) -> Unit
    ): File = withContext(Dispatchers.IO) {
        val connection = URL(url).openConnection() as HttpURLConnection
        val file = File.createTempFile("download", ".tmp")
        
        try {
            val inputStream = connection.inputStream
            val outputStream = FileOutputStream(file)
            val buffer = ByteArray(8192)
            var bytesRead: Int
            var totalBytes = 0L
            val contentLength = connection.contentLength.toLong()
            
            while (inputStream.read(buffer).also { bytesRead = it } != -1) {
                ensureActive() // Check for cancellation
                outputStream.write(buffer, 0, bytesRead)
                totalBytes += bytesRead
                
                if (contentLength > 0) {
                    val progress = totalBytes.toFloat() / contentLength
                    onProgress(progress)
                }
            }
            
            file
        } catch (e: CancellationException) {
            file.delete() // Clean up partial download
            throw e
        } finally {
            connection.disconnect()
        }
    }
}
```

**Key principles:**
- **Cooperative:** Coroutines must check for cancellation
- **Use `isActive` or `ensureActive()`:** To check for cancellation
- **Cleanup in catch blocks:** Handle `CancellationException`
- **Timeout functions:** `withTimeout()`, `withTimeoutOrNull()`
- **SupervisorJob:** Prevents cancellation propagation
- **Scope cancellation:** Cancel all coroutines in a scope

### Advanced Features
71. **What are delegated properties?**

**Answer:** Delegated properties allow you to delegate the getter/setter logic to another class, reducing boilerplate code and enabling powerful patterns like lazy initialization, observable properties, and more.

```kotlin
// Basic delegated property syntax
class Example {
    var property: String by Delegate()
}

// Custom delegate class
class Delegate {
    private var storedValue: String = ""
    
    operator fun getValue(thisRef: Any?, property: KProperty<*>): String {
        println("Getting value of ${property.name}")
        return storedValue
    }
    
    operator fun setValue(thisRef: Any?, property: KProperty<*>, value: String) {
        println("Setting value of ${property.name} to $value")
        storedValue = value
    }
}

// Usage
val example = Example()
example.property = "Hello" // Calls setValue
println(example.property)  // Calls getValue
```

**Built-in delegates:**
- `lazy` - lazy initialization
- `observable` - property change notifications
- `vetoable` - property change validation
- `notNull` - non-null assertion
- `by map` - delegate to map entries

72. **Explain `lazy` delegation**

**Answer:** `lazy` is a built-in delegate that initializes a property only when it's first accessed, providing lazy initialization with thread safety.

```kotlin
// Basic lazy usage
class DataManager {
    val expensiveData: List<String> by lazy {
        println("Initializing expensive data...")
        // Expensive computation
        (1..1000).map { "Item $it" }
    }
}

// Thread-safe lazy (default)
val threadSafeLazy: String by lazy {
    println("Thread-safe initialization")
    "Initialized value"
}

// Non-thread-safe lazy
val nonThreadSafeLazy: String by lazy(LazyThreadSafetyMode.NONE) {
    println("Non-thread-safe initialization")
    "Initialized value"
}

// Custom lazy with parameters
class ConfigManager {
    private val config by lazy {
        loadConfiguration()
    }
    
    fun getConfig(): Configuration {
        return config // Initialized on first access
    }
    
    private fun loadConfiguration(): Configuration {
        // Expensive loading operation
        return Configuration()
    }
}

// Lazy with dependencies
class UserManager {
    private val userRepository by lazy { UserRepository() }
    private val userService by lazy { UserService(userRepository) }
    private val userViewModel by lazy { UserViewModel(userService) }
    
    fun getUserViewModel(): UserViewModel = userViewModel
}

// Lazy in Android ViewModels
class MyViewModel : ViewModel() {
    private val repository by lazy { 
        UserRepository(database, apiService) 
    }
    
    private val userFlow by lazy {
        repository.getUsers()
    }
    
    fun getUsers(): Flow<List<User>> = userFlow
}
```

**Key characteristics:**
- **Thread-safe by default:** Uses double-checked locking
- **Initialized once:** Value is computed only on first access
- **Cached:** Subsequent accesses return the same value
- **Suspending:** Can be used with suspend functions in coroutines

73. **What is `lateinit` and how is it different from nullable types?**

**Answer:** `lateinit` allows you to declare a non-null property that will be initialized later, avoiding null checks while maintaining type safety.

```kotlin
// lateinit vs nullable
class Example {
    // lateinit - must be initialized before use
    lateinit var lateInitProperty: String
    
    // nullable - can be null
    var nullableProperty: String? = null
    
    fun initialize() {
        lateInitProperty = "Initialized"
        nullableProperty = "Also initialized"
    }
    
    fun useProperties() {
        // lateinit - no null check needed
        println(lateInitProperty.length)
        
        // nullable - null check required
        nullableProperty?.let { 
            println(it.length) 
        }
    }
}

// lateinit in Android
class MainActivity : AppCompatActivity() {
    lateinit var binding: ActivityMainBinding
    lateinit var viewModel: MainViewModel
    
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        
        // Initialize lateinit properties
        binding = ActivityMainBinding.inflate(layoutInflater)
        viewModel = ViewModelProvider(this)[MainViewModel::class.java]
        
        setContentView(binding.root)
    }
    
    override fun onResume() {
        super.onResume()
        // Safe to use - guaranteed to be initialized
        binding.textView.text = "Hello"
        viewModel.loadData()
    }
}

// Checking if lateinit is initialized
class LateInitExample {
    lateinit var property: String
    
    fun isInitialized(): Boolean {
        return ::property.isInitialized
    }
    
    fun safeUse() {
        if (::property.isInitialized) {
            println(property.length)
        } else {
            println("Property not initialized")
        }
    }
}

// lateinit with dependency injection
class UserService @Inject constructor() {
    lateinit var userRepository: UserRepository
    lateinit var analyticsService: AnalyticsService
    
    fun initialize(repository: UserRepository, analytics: AnalyticsService) {
        userRepository = repository
        analyticsService = analytics
    }
    
    fun getUser(id: String): User {
        // Safe to use after initialization
        return userRepository.getUser(id)
    }
}

// lateinit vs lazy
class Comparison {
    // lateinit - mutable, must be initialized manually
    lateinit var lateInitVar: String
    
    // lazy - immutable, initialized automatically on first access
    val lazyVar: String by lazy {
        "Computed value"
    }
    
    fun initialize() {
        lateInitVar = "Manual initialization"
        // lazyVar is initialized automatically when accessed
    }
}
```

**Key differences:**
- **lateinit:** Mutable, manual initialization, no null checks needed
- **nullable:** Can be null, requires null checks, explicit initialization
- **lateinit:** Better performance, cleaner code
- **nullable:** More explicit about null possibility

74. **How do you create custom delegated properties?**

**Answer:** Custom delegated properties are created by implementing the `getValue` and `setValue` operator functions.

```kotlin
// Basic custom delegate
class LoggingDelegate<T> {
    private var value: T? = null
    
    operator fun getValue(thisRef: Any?, property: KProperty<*>): T {
        println("Getting ${property.name}: $value")
        return value ?: throw IllegalStateException("Property not initialized")
    }
    
    operator fun setValue(thisRef: Any?, property: KProperty<*>, newValue: T) {
        println("Setting ${property.name} from $value to $newValue")
        value = newValue
    }
}

// Usage
class Example {
    var loggedProperty: String by LoggingDelegate()
}

// Observable delegate
class ObservableDelegate<T>(
    private var value: T,
    private val onChange: (T, T) -> Unit
) {
    operator fun getValue(thisRef: Any?, property: KProperty<*>): T = value
    
    operator fun setValue(thisRef: Any?, property: KProperty<*>, newValue: T) {
        val oldValue = value
        value = newValue
        onChange(oldValue, newValue)
    }
}

// Usage
class User {
    var name: String by ObservableDelegate("") { oldValue, newValue ->
        println("Name changed from '$oldValue' to '$newValue'")
    }
}

// Validation delegate
class ValidatedDelegate<T>(
    private var value: T,
    private val validator: (T) -> Boolean
) {
    operator fun getValue(thisRef: Any?, property: KProperty<*>): T = value
    
    operator fun setValue(thisRef: Any?, property: KProperty<*>, newValue: T) {
        if (validator(newValue)) {
            value = newValue
        } else {
            throw IllegalArgumentException("Invalid value: $newValue")
        }
    }
}

// Usage
class Form {
    var age: Int by ValidatedDelegate(0) { it in 0..150 }
    var email: String by ValidatedDelegate("") { it.contains("@") }
}

// Cached delegate
class CachedDelegate<T>(
    private val compute: () -> T
) {
    private var cachedValue: T? = null
    private var isComputed = false
    
    operator fun getValue(thisRef: Any?, property: KProperty<*>): T {
        if (!isComputed) {
            cachedValue = compute()
            isComputed = true
        }
        return cachedValue!!
    }
}

// Usage
class DataProcessor {
    val processedData: List<String> by CachedDelegate {
        // Expensive computation
        (1..1000).map { "Processed $it" }
    }
}

// Thread-safe delegate
class ThreadSafeDelegate<T>(
    private var value: T
) {
    private val lock = Any()
    
    operator fun getValue(thisRef: Any?, property: KProperty<*>): T {
        synchronized(lock) {
            return value
        }
    }
    
    operator fun setValue(thisRef: Any?, property: KProperty<*>, newValue: T) {
        synchronized(lock) {
            value = newValue
        }
    }
}

// Android-specific delegates
class SharedPreferencesDelegate(
    private val context: Context,
    private val key: String,
    private val defaultValue: String
) {
    private val prefs by lazy { 
        context.getSharedPreferences("app_prefs", Context.MODE_PRIVATE) 
    }
    
    operator fun getValue(thisRef: Any?, property: KProperty<*>): String {
        return prefs.getString(key, defaultValue) ?: defaultValue
    }
    
    operator fun setValue(thisRef: Any?, property: KProperty<*>, value: String) {
        prefs.edit().putString(key, value).apply()
    }
}

// Usage in Activity
class MainActivity : AppCompatActivity() {
    var userToken: String by SharedPreferencesDelegate(this, "user_token", "")
    var isFirstLaunch: Boolean by SharedPreferencesDelegate(this, "first_launch", "true")
}
```

75. **What are property delegates (`by` keyword)?**

**Answer:** Property delegates use the `by` keyword to delegate property access to another object, enabling powerful patterns and reducing boilerplate code.

```kotlin
// by keyword syntax
class Example {
    // Delegate property access to another object
    var property: String by Delegate()
    
    // Equivalent to:
    // private val delegate = Delegate()
    // var property: String
    //     get() = delegate.getValue(this, ::property)
    //     set(value) = delegate.setValue(this, ::property, value)
}

// Built-in delegates
class BuiltInDelegates {
    // 1. lazy - lazy initialization
    val lazyProperty: String by lazy {
        "Expensive computation result"
    }
    
    // 2. observable - property change notifications
    var observableProperty: String by Delegates.observable("") { 
        property, oldValue, newValue ->
        println("${property.name} changed from '$oldValue' to '$newValue'")
    }
    
    // 3. vetoable - property change validation
    var vetoableProperty: Int by Delegates.vetoable(0) { 
        property, oldValue, newValue ->
        newValue >= 0 // Only allow non-negative values
    }
    
    // 4. notNull - non-null assertion
    var notNullProperty: String by Delegates.notNull()
    
    // 5. by map - delegate to map entries
    val map = mutableMapOf<String, Any>()
    var mapProperty: String by map
}

// Custom delegate with by
class CustomDelegate {
    private var storedValue: String = ""
    
    operator fun getValue(thisRef: Any?, property: KProperty<*>): String {
        return storedValue
    }
    
    operator fun setValue(thisRef: Any?, property: KProperty<*>, value: String) {
        storedValue = value
    }
}

// Usage with by
class User {
    var name: String by CustomDelegate()
}

// Delegate to different types
class DelegateExamples {
    // Delegate to another property
    private var _internalValue: String = ""
    var publicValue: String by this::_internalValue
    
    // Delegate to function
    var computedValue: String by lazy { computeValue() }
    
    private fun computeValue(): String {
        return "Computed at ${System.currentTimeMillis()}"
    }
    
    // Delegate to map
    private val properties = mutableMapOf<String, Any>()
    var dynamicProperty: String by properties
}

// Android ViewBinding delegate
class ViewBindingDelegate<T : ViewBinding>(
    private val bindingClass: Class<T>
) {
    private var binding: T? = null
    
    operator fun getValue(thisRef: Activity, property: KProperty<*>): T {
        if (binding == null) {
            binding = bindingClass.getMethod("inflate", LayoutInflater::class.java)
                .invoke(null, thisRef.layoutInflater) as T
        }
        return binding!!
    }
}

// Usage
class MainActivity : AppCompatActivity() {
    private val binding by ViewBindingDelegate(ActivityMainBinding::class.java)
    
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(binding.root)
    }
}

// Delegate for ViewModel
class ViewModelDelegate<T : ViewModel>(
    private val viewModelClass: Class<T>
) {
    operator fun getValue(thisRef: FragmentActivity, property: KProperty<*>): T {
        return ViewModelProvider(thisRef)[viewModelClass]
    }
}

// Usage
class MainActivity : AppCompatActivity() {
    private val viewModel by ViewModelDelegate(MainViewModel::class.java)
}
```

**Key benefits:**
- **Code reuse:** Share property logic across multiple properties
- **Encapsulation:** Hide implementation details
- **Type safety:** Compile-time checking
- **Performance:** Optimized property access
- **Readability:** Clear intent with `by` keyword
76. **Explain reflection in Kotlin**

**Answer:** Reflection allows you to inspect and manipulate classes, properties, and functions at runtime. Kotlin provides a reflection API through the `kotlin-reflect` library.

```kotlin
// Basic reflection
import kotlin.reflect.full.*

class ReflectionExample {
    val property: String = "Hello"
    fun function() = "World"
}

fun basicReflection() {
    val example = ReflectionExample()
    val kClass = example::class
    
    // Get class name
    println("Class name: ${kClass.simpleName}")
    
    // Get properties
    kClass.memberProperties.forEach { property ->
        println("Property: ${property.name} = ${property.get(example)}")
    }
    
    // Get functions
    kClass.memberFunctions.forEach { function ->
        println("Function: ${function.name}")
    }
}

// Property reflection
class User(val name: String, val age: Int)

fun propertyReflection() {
    val user = User("John", 30)
    val kClass = user::class
    
    // Get all properties
    kClass.memberProperties.forEach { property ->
        val value = property.get(user)
        println("${property.name}: $value")
    }
    
    // Get specific property
    val nameProperty = kClass.memberProperties.find { it.name == "name" }
    nameProperty?.let { property ->
        val name = property.get(user) as String
        println("Name: $name")
    }
}

// Function reflection
class Calculator {
    fun add(a: Int, b: Int) = a + b
    fun multiply(a: Int, b: Int) = a * b
}

fun functionReflection() {
    val calculator = Calculator()
    val kClass = calculator::class
    
    // Get function by name
    val addFunction = kClass.memberFunctions.find { it.name == "add" }
    addFunction?.let { function ->
        val result = function.call(calculator, 5, 3)
        println("Result: $result")
    }
    
    // Get function parameters
    kClass.memberFunctions.forEach { function ->
        println("Function: ${function.name}")
        function.parameters.forEach { parameter ->
            println("  Parameter: ${parameter.name} (${parameter.type})")
        }
    }
}

// Constructor reflection
class Person(val name: String, val age: Int)

fun constructorReflection() {
    val kClass = Person::class
    
    // Get primary constructor
    val primaryConstructor = kClass.primaryConstructor
    primaryConstructor?.let { constructor ->
        val person = constructor.call("Alice", 25)
        println("Created person: ${person.name}")
    }
    
    // Get all constructors
    kClass.constructors.forEach { constructor ->
        println("Constructor: ${constructor.parameters.size} parameters")
    }
}

// Annotation reflection
@Target(AnnotationTarget.CLASS)
annotation class MyAnnotation(val value: String)

@MyAnnotation("Test class")
class AnnotatedClass

fun annotationReflection() {
    val kClass = AnnotatedClass::class
    
    // Get annotations
    kClass.annotations.forEach { annotation ->
        when (annotation) {
            is MyAnnotation -> println("Annotation value: ${annotation.value}")
        }
    }
    
    // Check if class has annotation
    val hasAnnotation = kClass.hasAnnotation<MyAnnotation>()
    println("Has annotation: $hasAnnotation")
}

// Dynamic property access
class DynamicAccess {
    private val properties = mutableMapOf<String, Any>()
    
    fun setProperty(name: String, value: Any) {
        properties[name] = value
    }
    
    fun getProperty(name: String): Any? {
        return properties[name]
    }
}

fun dynamicPropertyAccess() {
    val obj = DynamicAccess()
    obj.setProperty("name", "John")
    obj.setProperty("age", 30)
    
    // Access properties dynamically
    val name = obj.getProperty("name")
    val age = obj.getProperty("age")
    
    println("Name: $name, Age: $age")
}

// Real-world example: JSON serialization
class JsonSerializer {
    fun serialize(obj: Any): String {
        val kClass = obj::class
        val properties = kClass.memberProperties
        
        val jsonProperties = properties.map { property ->
            val value = property.get(obj)
            "\"${property.name}\": \"$value\""
        }
        
        return "{${jsonProperties.joinToString(",")}}"
    }
}

// Usage
data class User(val name: String, val email: String)

fun jsonSerialization() {
    val user = User("John", "john@example.com")
    val serializer = JsonSerializer()
    val json = serializer.serialize(user)
    println(json) // {"name": "John","email": "john@example.com"}
}
```

77. **What are annotations and how do you create custom ones?**

**Answer:** Annotations are metadata that can be attached to code elements. They provide information to the compiler, runtime, or other tools.

```kotlin
// Basic annotation syntax
@Target(AnnotationTarget.CLASS)
@Retention(AnnotationRetention.RUNTIME)
annotation class MyAnnotation(val value: String)

// Usage
@MyAnnotation("Test class")
class TestClass

// Annotation targets
@Target(
    AnnotationTarget.CLASS,
    AnnotationTarget.FUNCTION,
    AnnotationTarget.PROPERTY,
    AnnotationTarget.CONSTRUCTOR,
    AnnotationTarget.PROPERTY_GETTER,
    AnnotationTarget.PROPERTY_SETTER,
    AnnotationTarget.FIELD,
    AnnotationTarget.LOCAL_VARIABLE,
    AnnotationTarget.VALUE_PARAMETER,
    AnnotationTarget.TYPE_PARAMETER,
    AnnotationTarget.TYPE
)
annotation class MultiTargetAnnotation

// Retention policies
@Retention(AnnotationRetention.SOURCE) // Compile-time only
annotation class SourceAnnotation

@Retention(AnnotationRetention.BINARY) // Available in compiled class
annotation class BinaryAnnotation

@Retention(AnnotationRetention.RUNTIME) // Available at runtime
annotation class RuntimeAnnotation

// Custom annotation with parameters
@Target(AnnotationTarget.CLASS, AnnotationTarget.FUNCTION)
@Retention(AnnotationRetention.RUNTIME)
annotation class ApiEndpoint(
    val path: String,
    val method: String = "GET",
    val requiresAuth: Boolean = false
)

// Usage
@ApiEndpoint("/users", "GET", true)
class UserController {
    @ApiEndpoint("/users/{id}", "GET")
    fun getUser(id: String) {
        // Implementation
    }
}

// Validation annotation
@Target(AnnotationTarget.PROPERTY)
@Retention(AnnotationRetention.RUNTIME)
annotation class Validate(
    val minLength: Int = 0,
    val maxLength: Int = Int.MAX_VALUE,
    val pattern: String = ""
)

// Usage
class UserForm {
    @Validate(minLength = 2, maxLength = 50)
    var name: String = ""
    
    @Validate(pattern = "^[A-Za-z0-9._%+-]+@[A-Za-z0-9.-]+\\.[A-Za-z]{2,}$")
    var email: String = ""
}

// Android-specific annotations
@Target(AnnotationTarget.CLASS)
@Retention(AnnotationRetention.RUNTIME)
annotation class ViewModel

@Target(AnnotationTarget.CLASS)
@Retention(AnnotationRetention.RUNTIME)
annotation class Repository

// Usage
@ViewModel
class MainViewModel : ViewModel()

@Repository
class UserRepository

// Dependency injection annotation
@Target(AnnotationTarget.CONSTRUCTOR, AnnotationTarget.FUNCTION, AnnotationTarget.PROPERTY)
@Retention(AnnotationRetention.RUNTIME)
annotation class Inject

// Usage
class UserService @Inject constructor(
    private val repository: UserRepository
)

// Testing annotations
@Target(AnnotationTarget.FUNCTION)
@Retention(AnnotationRetention.RUNTIME)
annotation class Test(
    val description: String = "",
    val timeout: Long = 0
)

// Usage
class TestClass {
    @Test(description = "Test user creation", timeout = 5000)
    fun testCreateUser() {
        // Test implementation
    }
}

// Serialization annotations
@Target(AnnotationTarget.PROPERTY)
@Retention(AnnotationRetention.RUNTIME)
annotation class SerializedName(val name: String)

@Target(AnnotationTarget.PROPERTY)
@Retention(AnnotationRetention.RUNTIME)
annotation class Exclude

// Usage
data class User(
    @SerializedName("user_name")
    val name: String,
    
    @Exclude
    val password: String,
    
    @SerializedName("email_address")
    val email: String
)

// Processing annotations at runtime
fun processAnnotations() {
    val kClass = UserForm::class
    
    kClass.memberProperties.forEach { property ->
        val validateAnnotation = property.findAnnotation<Validate>()
        validateAnnotation?.let { annotation ->
            println("Property ${property.name} has validation:")
            println("  Min length: ${annotation.minLength}")
            println("  Max length: ${annotation.maxLength}")
            println("  Pattern: ${annotation.pattern}")
        }
    }
}

// Annotation processor example
@Target(AnnotationTarget.CLASS)
@Retention(AnnotationRetention.SOURCE)
annotation class GenerateBuilder

@GenerateBuilder
data class Person(
    val name: String,
    val age: Int,
    val email: String
)

// The annotation processor would generate:
// class PersonBuilder {
//     private var name: String = ""
//     private var age: Int = 0
//     private var email: String = ""
//     
//     fun name(name: String) = apply { this.name = name }
//     fun age(age: Int) = apply { this.age = age }
//     fun email(email: String) = apply { this.email = email }
//     
//     fun build() = Person(name, age, email)
// }
```

78. **How do you use Kotlin with Java (interoperability)?**

**Answer:** Kotlin is designed to be fully interoperable with Java, allowing seamless use of Java libraries and gradual migration from Java to Kotlin.

```kotlin
// Calling Java from Kotlin
// Java class: User.java
public class User {
    private String name;
    private int age;
    
    public User(String name, int age) {
        this.name = name;
        this.age = age;
    }
    
    public String getName() { return name; }
    public void setName(String name) { this.name = name; }
    public int getAge() { return age; }
    public void setAge(int age) { this.age = age; }
}

// Kotlin usage
fun useJavaClass() {
    val user = User("John", 30)
    println("Name: ${user.name}") // Property syntax
    println("Age: ${user.age}")
    
    user.name = "Jane" // Setter syntax
    user.age = 25
}

// Java static methods
// Java: Utils.java
public class Utils {
    public static String formatName(String firstName, String lastName) {
        return firstName + " " + lastName;
    }
}

// Kotlin usage
fun useJavaStatic() {
    val fullName = Utils.formatName("John", "Doe")
    println(fullName)
}

// Java collections
fun useJavaCollections() {
    val javaList = ArrayList<String>()
    javaList.add("Item 1")
    javaList.add("Item 2")
    
    // Kotlin extension functions work on Java collections
    javaList.forEach { println(it) }
    val filtered = javaList.filter { it.contains("1") }
}

// Java SAM (Single Abstract Method) interfaces
// Java: OnClickListener.java
public interface OnClickListener {
    void onClick(View view);
}

// Kotlin usage
fun useJavaSAM() {
    val listener = OnClickListener { view ->
        println("Clicked: ${view.id}")
    }
}

// Calling Kotlin from Java
// Kotlin class
class KotlinUser(
    val name: String,
    val email: String,
    private val password: String
) {
    fun getDisplayName(): String = "$name ($email)"
    
    companion object {
        fun create(name: String, email: String): KotlinUser {
            return KotlinUser(name, email, "")
        }
    }
}

// Java usage
// User user = new KotlinUser("John", "john@example.com", "password");
// String displayName = user.getDisplayName();
// KotlinUser created = KotlinUser.Companion.create("John", "john@example.com");

// Null safety in Java interop
fun nullSafetyInterop() {
    // Java method that might return null
    val javaString: String? = getJavaString()
    
    // Safe call
    val length = javaString?.length ?: 0
    
    // Not-null assertion (use carefully)
    val forcedLength = javaString!!.length
}

// Platform types
fun platformTypes() {
    // Java method returns String (platform type)
    val javaString = getJavaString() // Type is String! (platform type)
    
    // Can be used as nullable or non-null
    val nullable: String? = javaString
    val nonNull: String = javaString // Assumes non-null
}

// Java arrays
fun javaArrays() {
    // Java array
    val javaArray = arrayOf("a", "b", "c")
    
    // Convert to Kotlin list
    val kotlinList = javaArray.toList()
    
    // Convert Kotlin list to Java array
    val kotlinList2 = listOf("x", "y", "z")
    val javaArray2 = kotlinList2.toTypedArray()
}

// Java exceptions
fun javaExceptions() {
    try {
        // Java method that throws checked exception
        throwIOException()
    } catch (e: IOException) {
        // Kotlin doesn't require catching checked exceptions
        println("Caught: ${e.message}")
    }
}

// Java generics
fun javaGenerics() {
    // Java generic class
    val javaList = ArrayList<String>()
    javaList.add("Item")
    
    // Kotlin type inference
    val firstItem = javaList[0] // Type is String
}

// Android-specific interop
class MainActivity : AppCompatActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        
        // Java View
        val textView = TextView(this)
        textView.text = "Hello from Kotlin"
        
        // Java OnClickListener
        textView.setOnClickListener { view ->
            Toast.makeText(this, "Clicked!", Toast.LENGTH_SHORT).show()
        }
    }
}

// Java static fields
// Java: Constants.java
public class Constants {
    public static final String API_BASE_URL = "https://api.example.com";
    public static final int TIMEOUT = 30000;
}

// Kotlin usage
fun useJavaConstants() {
    val url = Constants.API_BASE_URL
    val timeout = Constants.TIMEOUT
}

// Java enums
// Java: Status.java
public enum Status {
    ACTIVE, INACTIVE, PENDING
}

// Kotlin usage
fun useJavaEnum() {
    val status = Status.ACTIVE
    when (status) {
        Status.ACTIVE -> println("Active")
        Status.INACTIVE -> println("Inactive")
        Status.PENDING -> println("Pending")
    }
}
```

79. **What is the `@JvmStatic` annotation?**

**Answer:** `@JvmStatic` is an annotation that makes a function or property in a companion object accessible as a static member from Java code.

```kotlin
// Without @JvmStatic
class Utils {
    companion object {
        fun formatName(firstName: String, lastName: String): String {
            return "$firstName $lastName"
        }
        
        val DEFAULT_TIMEOUT = 30000
    }
}

// Java usage without @JvmStatic
// String name = Utils.Companion.formatName("John", "Doe");
// int timeout = Utils.Companion.getDEFAULT_TIMEOUT();

// With @JvmStatic
class Utils {
    companion object {
        @JvmStatic
        fun formatName(firstName: String, lastName: String): String {
            return "$firstName $lastName"
        }
        
        @JvmStatic
        val DEFAULT_TIMEOUT = 30000
    }
}

// Java usage with @JvmStatic
// String name = Utils.formatName("John", "Doe");
// int timeout = Utils.DEFAULT_TIMEOUT;

// Real-world example: API client
class ApiClient {
    companion object {
        @JvmStatic
        fun create(baseUrl: String): ApiClient {
            return ApiClient(baseUrl)
        }
        
        @JvmStatic
        val DEFAULT_TIMEOUT = 30000L
        
        @JvmStatic
        fun getDefaultHeaders(): Map<String, String> {
            return mapOf(
                "Content-Type" to "application/json",
                "Accept" to "application/json"
            )
        }
    }
    
    private constructor(baseUrl: String) {
        // Private constructor
    }
}

// Java usage
// ApiClient client = ApiClient.create("https://api.example.com");
// long timeout = ApiClient.DEFAULT_TIMEOUT;
// Map<String, String> headers = ApiClient.getDefaultHeaders();

// Android ViewModel factory
class UserViewModel : ViewModel() {
    companion object {
        @JvmStatic
        fun provideFactory(repository: UserRepository): ViewModelProvider.Factory {
            return object : ViewModelProvider.Factory {
                override fun <T : ViewModel> create(modelClass: Class<T>): T {
                    return UserViewModel(repository) as T
                }
            }
        }
    }
    
    private constructor(repository: UserRepository) : super() {
        // Private constructor
    }
}

// Java usage in Activity
// ViewModelProvider.Factory factory = UserViewModel.provideFactory(repository);
// UserViewModel viewModel = new ViewModelProvider(this, factory).get(UserViewModel.class);

// Utility functions
class StringUtils {
    companion object {
        @JvmStatic
        fun isEmpty(str: String?): Boolean {
            return str.isNullOrEmpty()
        }
        
        @JvmStatic
        fun isEmail(email: String): Boolean {
            return email.contains("@")
        }
        
        @JvmStatic
        fun capitalize(str: String): String {
            return str.capitalize()
        }
    }
}

// Java usage
// boolean empty = StringUtils.isEmpty("test");
// boolean email = StringUtils.isEmail("test@example.com");
// String capitalized = StringUtils.capitalize("hello");

// Constants class
class AppConstants {
    companion object {
        @JvmStatic
        val APP_NAME = "MyApp"
        
        @JvmStatic
        val VERSION = "1.0.0"
        
        @JvmStatic
        val BUILD_NUMBER = 100
        
        @JvmStatic
        fun getApiUrl(environment: String): String {
            return when (environment) {
                "dev" -> "https://dev-api.example.com"
                "staging" -> "https://staging-api.example.com"
                "prod" -> "https://api.example.com"
                else -> "https://api.example.com"
            }
        }
    }
}

// Java usage
// String appName = AppConstants.APP_NAME;
// String version = AppConstants.VERSION;
// int buildNumber = AppConstants.BUILD_NUMBER;
// String apiUrl = AppConstants.getApiUrl("prod");

// Database helper
class DatabaseHelper {
    companion object {
        @JvmStatic
        val DATABASE_NAME = "app_database"
        
        @JvmStatic
        val DATABASE_VERSION = 1
        
        @JvmStatic
        fun getInstance(context: Context): DatabaseHelper {
            return DatabaseHelper(context)
        }
        
        @JvmStatic
        fun isDatabaseExists(context: Context): Boolean {
            val dbFile = context.getDatabasePath(DATABASE_NAME)
            return dbFile.exists()
        }
    }
    
    private constructor(context: Context) {
        // Private constructor
    }
}

// Java usage
// DatabaseHelper db = DatabaseHelper.getInstance(context);
// boolean exists = DatabaseHelper.isDatabaseExists(context);
// String dbName = DatabaseHelper.DATABASE_NAME;
```

**Key benefits:**
- **Cleaner Java interop:** No need for `Companion` reference
- **Familiar syntax:** Java developers can use familiar static syntax
- **Better performance:** Direct static access
- **IDE support:** Better autocomplete and navigation in Java

80. **Explain Kotlin's type system (Nothing, Unit, Any)**

**Answer:** Kotlin has a rich type system with special types that serve specific purposes in the language.

```kotlin
// Any - root of the type hierarchy
fun anyType() {
    // Any is the supertype of all non-nullable types
    val anyValue: Any = "Hello"
    val anyNumber: Any = 42
    val anyBoolean: Any = true
    
    // Any has three methods: equals(), hashCode(), toString()
    println(anyValue.toString())
    println(anyValue.hashCode())
    println(anyValue.equals("Hello"))
}

// Any? - nullable root type
fun nullableAny() {
    val nullableAny: Any? = null
    val stringOrNull: Any? = "Hello"
    
    // Can hold any value including null
    val mixed: Any? = if (System.currentTimeMillis() % 2 == 0L) "Even" else null
}

// Unit - equivalent to void in Java
fun unitType() {
    // Functions that don't return a value return Unit
    fun printMessage(message: String): Unit {
        println(message)
    }
    
    // Unit is the default return type
    fun defaultReturn() {
        println("No explicit return type")
    }
    
    // Unit is a singleton object
    val unitValue: Unit = Unit
    println(unitValue) // kotlin.Unit
    
    // Unit in generic contexts
    fun processData(data: String): Unit {
        // Process data
    }
    
    // Unit in lambdas
    val lambda: (String) -> Unit = { message ->
        println(message)
    }
}

// Nothing - represents "no value" or "never returns"
fun nothingType() {
    // Functions that never return normally
    fun throwException(): Nothing {
        throw RuntimeException("This function never returns")
    }
    
    fun infiniteLoop(): Nothing {
        while (true) {
            // Infinite loop
        }
    }
    
    // Nothing is a subtype of every type
    val impossible: String = throwException() // Compiles because Nothing <: String
    
    // Nothing in generic contexts
    fun fail(): Nothing {
        throw Exception("Fail")
    }
    
    // Nothing with nullable types
    val nullableString: String? = fail() // Compiles because Nothing <: String?
}

// Practical examples with Nothing
fun practicalNothing() {
    // Error handling
    fun requirePositive(value: Int): Int {
        return if (value > 0) value else throw IllegalArgumentException("Value must be positive")
    }
    
    // Type-safe builders
    fun buildString(init: StringBuilder.() -> Unit): String {
        val builder = StringBuilder()
        builder.init()
        return builder.toString()
    }
    
    // Exhaustive when expressions
    fun processStatus(status: Status): String {
        return when (status) {
            Status.ACTIVE -> "Active"
            Status.INACTIVE -> "Inactive"
            Status.PENDING -> "Pending"
            // No else needed because when is exhaustive
        }
    }
}

// Type hierarchy
fun typeHierarchy() {
    // Any is the root
    val any: Any = "Hello"
    
    // String is a subtype of Any
    val string: String = "Hello"
    
    // Nothing is a subtype of everything
    val nothing: Nothing = throw Exception()
    
    // Unit is a subtype of Any
    val unit: Unit = Unit
    
    // Nullable types
    val nullableString: String? = null
    val nullableAny: Any? = null
}

// Generic type bounds
fun genericBounds() {
    // Upper bound
    fun <T : Any> processNonNull(value: T): String {
        return value.toString()
    }
    
    // Multiple bounds
    fun <T> processComparable(value: T) where T : Comparable<T>, T : Any {
        // T must be both Comparable and Any
    }
    
    // Nothing as lower bound
    fun <T> safeCast(value: Any): T? {
        return value as? T
    }
}

// Real-world examples
class TypeSystemExamples {
    // Unit in callbacks
    fun setOnClickListener(listener: (View) -> Unit) {
        // listener returns Unit
    }
    
    // Nothing in error handling
    fun validateUser(user: User?): User {
        return user ?: throw IllegalArgumentException("User cannot be null")
    }
    
    // Any in collections
    fun processMixedList(items: List<Any>) {
        items.forEach { item ->
            when (item) {
                is String -> println("String: $item")
                is Int -> println("Int: $item")
                is Boolean -> println("Boolean: $item")
                else -> println("Unknown type: $item")
            }
        }
    }
    
    // Nothing in sealed classes
    sealed class Result<out T> {
        data class Success<T>(val data: T) : Result<T>()
        data class Error(val message: String) : Result<Nothing>()
    }
    
    fun processResult(result: Result<String>) {
        when (result) {
            is Result.Success -> println("Success: ${result.data}")
            is Result.Error -> println("Error: ${result.message}")
            // No else needed because when is exhaustive
        }
    }
}

// Type inference with special types
fun typeInference() {
    // Unit inference
    val unitValue = println("Hello") // Type is Unit
    
    // Nothing inference
    val nothingValue = throw Exception() // Type is Nothing
    
    // Any inference
    val anyValue = if (true) "String" else 42 // Type is Any
    
    // Nullable inference
    val nullableValue = if (true) "String" else null // Type is String?
}

// Type safety examples
fun typeSafety() {
    // Unit ensures void functions are handled
    fun voidFunction() {
        // Returns Unit implicitly
    }
    
    val result = voidFunction() // result is Unit
    
    // Nothing ensures exhaustive handling
    sealed class Direction {
        object North : Direction()
        object South : Direction()
        object East : Direction()
        object West : Direction()
    }
    
    fun getDirectionName(direction: Direction): String {
        return when (direction) {
            Direction.North -> "North"
            Direction.South -> "South"
            Direction.East -> "East"
            Direction.West -> "West"
            // Compiler ensures all cases are handled
        }
    }
}
```

**Key characteristics:**
- **Any:** Root of non-nullable type hierarchy
- **Any?:** Root of nullable type hierarchy  
- **Unit:** Represents "no meaningful value" (like void)
- **Nothing:** Represents "no value" or "never returns"
- **Type safety:** Compiler ensures type safety at compile time
- **Type inference:** Compiler can infer types automatically

---

## Android Core

### Components and Lifecycle
81. **Explain the Android application components**

**Answer:** Android has four main application components that serve as entry points for different parts of your app.

```kotlin
// 1. Activity - UI component
class MainActivity : AppCompatActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)
    }
}

// 2. Service - Background component
class MyService : Service() {
    override fun onBind(intent: Intent?): IBinder? = null
    
    override fun onStartCommand(intent: Intent?, flags: Int, startId: Int): Int {
        // Perform background work
        return START_STICKY
    }
}

// 3. BroadcastReceiver - System event listener
class MyReceiver : BroadcastReceiver() {
    override fun onReceive(context: Context, intent: Intent) {
        when (intent.action) {
            Intent.ACTION_BOOT_COMPLETED -> {
                // Handle boot completion
            }
            "com.example.CUSTOM_ACTION" -> {
                // Handle custom action
            }
        }
    }
}

// 4. ContentProvider - Data sharing component
class MyContentProvider : ContentProvider() {
    override fun onCreate(): Boolean {
        // Initialize provider
        return true
    }
    
    override fun query(
        uri: Uri,
        projection: Array<out String>?,
        selection: String?,
        selectionArgs: Array<out String>?,
        sortOrder: String?
    ): Cursor? {
        // Query data
        return null
    }
    
    override fun insert(uri: Uri, values: ContentValues?): Uri? {
        // Insert data
        return null
    }
    
    override fun update(
        uri: Uri,
        values: ContentValues?,
        selection: String?,
        selectionArgs: Array<out String>?
    ): Int {
        // Update data
        return 0
    }
    
    override fun delete(
        uri: Uri,
        selection: String?,
        selectionArgs: Array<out String>?
    ): Int {
        // Delete data
        return 0
    }
    
    override fun getType(uri: Uri): String? {
        // Return MIME type
        return null
    }
}

// Component registration in AndroidManifest.xml
/*
<manifest>
    <application>
        <!-- Activity -->
        <activity android:name=".MainActivity">
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />
                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
        </activity>
        
        <!-- Service -->
        <service android:name=".MyService" />
        
        <!-- BroadcastReceiver -->
        <receiver android:name=".MyReceiver">
            <intent-filter>
                <action android:name="android.intent.action.BOOT_COMPLETED" />
            </intent-filter>
        </receiver>
        
        <!-- ContentProvider -->
        <provider 
            android:name=".MyContentProvider"
            android:authorities="com.example.provider"
            android:exported="false" />
    </application>
</manifest>
*/
```

**Key characteristics:**
- **Activity:** UI screens, user interaction
- **Service:** Background processing, no UI
- **BroadcastReceiver:** System event handling
- **ContentProvider:** Data sharing between apps

82. **What is the Activity lifecycle?**

**Answer:** The Activity lifecycle consists of callback methods that are called as the Activity transitions between different states.

```kotlin
class LifecycleActivity : AppCompatActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)
        
        // Initialize UI components
        // Restore saved state if available
        savedInstanceState?.let { bundle ->
            val savedData = bundle.getString("key")
        }
    }
    
    override fun onStart() {
        super.onStart()
        // Activity becomes visible to user
        // Start animations, refresh data
    }
    
    override fun onResume() {
        super.onResume()
        // Activity is in foreground and interactive
        // Resume animations, start sensors
    }
    
    override fun onPause() {
        super.onPause()
        // Activity is partially visible
        // Pause animations, stop sensors
        // Save critical data
    }
    
    override fun onStop() {
        super.onStop()
        // Activity is no longer visible
        // Stop heavy operations
    }
    
    override fun onDestroy() {
        super.onDestroy()
        // Activity is being destroyed
        // Clean up resources, unregister listeners
    }
    
    override fun onSaveInstanceState(outState: Bundle) {
        super.onSaveInstanceState(outState)
        // Save state before configuration change
        outState.putString("key", "value")
    }
    
    override fun onRestoreInstanceState(savedInstanceState: Bundle) {
        super.onRestoreInstanceState(savedInstanceState)
        // Restore state after configuration change
        val savedData = savedInstanceState.getString("key")
    }
}

// Lifecycle-aware components
class LifecycleAwareComponent : LifecycleObserver {
    @OnLifecycleEvent(Lifecycle.Event.ON_RESUME)
    fun onResume() {
        // Handle resume event
    }
    
    @OnLifecycleEvent(Lifecycle.Event.ON_PAUSE)
    fun onPause() {
        // Handle pause event
    }
}

// Usage in Activity
class MainActivity : AppCompatActivity() {
    private val component = LifecycleAwareComponent()
    
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        lifecycle.addObserver(component)
    }
}
```

**Lifecycle states:**
- **Created:** Activity is created but not visible
- **Started:** Activity is visible but not in foreground
- **Resumed:** Activity is visible and interactive
- **Paused:** Activity is partially visible
- **Stopped:** Activity is not visible
- **Destroyed:** Activity is destroyed

83. **Describe Fragment lifecycle**

**Answer:** Fragments have their own lifecycle that is tied to their host Activity's lifecycle.

```kotlin
class MyFragment : Fragment() {
    override fun onAttach(context: Context) {
        super.onAttach(context)
        // Fragment is attached to Activity
        // Initialize callbacks
    }
    
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        // Fragment is created
        // Initialize non-UI components
    }
    
    override fun onCreateView(
        inflater: LayoutInflater,
        container: ViewGroup?,
        savedInstanceState: Bundle?
    ): View? {
        // Create and return the fragment's view
        return inflater.inflate(R.layout.fragment_my, container, false)
    }
    
    override fun onViewCreated(view: View, savedInstanceState: Bundle?) {
        super.onViewCreated(view, savedInstanceState)
        // View is created and ready
        // Initialize UI components
        setupViews(view)
    }
    
    override fun onActivityCreated(savedInstanceState: Bundle?) {
        super.onActivityCreated(savedInstanceState)
        // Activity's onCreate() has completed
        // Access Activity's views
    }
    
    override fun onStart() {
        super.onStart()
        // Fragment becomes visible
    }
    
    override fun onResume() {
        super.onResume()
        // Fragment is interactive
    }
    
    override fun onPause() {
        super.onPause()
        // Fragment is no longer interactive
    }
    
    override fun onStop() {
        super.onStop()
        // Fragment is no longer visible
    }
    
    override fun onDestroyView() {
        super.onDestroyView()
        // View is being destroyed
        // Clean up UI references
    }
    
    override fun onDestroy() {
        super.onDestroy()
        // Fragment is being destroyed
        // Clean up resources
    }
    
    override fun onDetach() {
        super.onDetach()
        // Fragment is detached from Activity
        // Clear callbacks
    }
    
    private fun setupViews(view: View) {
        // Initialize UI components
    }
}

// Fragment with ViewBinding
class ModernFragment : Fragment() {
    private var _binding: FragmentMyBinding? = null
    private val binding get() = _binding!!
    
    override fun onCreateView(
        inflater: LayoutInflater,
        container: ViewGroup?,
        savedInstanceState: Bundle?
    ): View {
        _binding = FragmentMyBinding.inflate(inflater, container, false)
        return binding.root
    }
    
    override fun onViewCreated(view: View, savedInstanceState: Bundle?) {
        super.onViewCreated(view, savedInstanceState)
        binding.textView.text = "Hello Fragment!"
    }
    
    override fun onDestroyView() {
        super.onDestroyView()
        _binding = null
    }
}
```

**Fragment lifecycle states:**
- **Attached:** Fragment is attached to Activity
- **Created:** Fragment is created
- **ViewCreated:** Fragment's view is created
- **ActivityCreated:** Activity's onCreate() completed
- **Started:** Fragment is visible
- **Resumed:** Fragment is interactive
- **Paused:** Fragment is not interactive
- **Stopped:** Fragment is not visible
- **DestroyedView:** Fragment's view is destroyed
- **Destroyed:** Fragment is destroyed
- **Detached:** Fragment is detached from Activity

84. **What is the difference between Activity and Fragment?**

**Answer:** Activities and Fragments serve different purposes in Android development, with Fragments being more modular and reusable.

```kotlin
// Activity - Full screen component
class MainActivity : AppCompatActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)
        
        // Activity manages its own lifecycle
        // Can contain multiple fragments
        if (savedInstanceState == null) {
            supportFragmentManager.beginTransaction()
                .add(R.id.fragment_container, MyFragment())
                .commit()
        }
    }
}

// Fragment - Modular UI component
class MyFragment : Fragment() {
    override fun onCreateView(
        inflater: LayoutInflater,
        container: ViewGroup?,
        savedInstanceState: Bundle?
    ): View? {
        return inflater.inflate(R.layout.fragment_my, container, false)
    }
}

// Key differences:

// 1. Lifecycle dependency
class LifecycleComparison {
    // Activity has independent lifecycle
    class IndependentActivity : AppCompatActivity() {
        override fun onCreate(savedInstanceState: Bundle?) {
            super.onCreate(savedInstanceState)
            // Activity can exist independently
        }
    }
    
    // Fragment lifecycle depends on Activity
    class DependentFragment : Fragment() {
        override fun onAttach(context: Context) {
            super.onAttach(context)
            // Fragment needs Activity to exist
        }
    }
}

// 2. UI flexibility
class UIFlexibility {
    // Activity - typically full screen
    class FullScreenActivity : AppCompatActivity() {
        override fun onCreate(savedInstanceState: Bundle?) {
            super.onCreate(savedInstanceState)
            setContentView(R.layout.activity_full_screen)
        }
    }
    
    // Fragment - can be any size
    class FlexibleFragment : Fragment() {
        override fun onCreateView(
            inflater: LayoutInflater,
            container: ViewGroup?,
            savedInstanceState: Bundle?
        ): View? {
            return inflater.inflate(R.layout.fragment_flexible, container, false)
        }
    }
}

// 3. Reusability
class ReusabilityExample {
    // Fragment can be reused in different activities
    class ReusableFragment : Fragment() {
        companion object {
            fun newInstance(param: String): ReusableFragment {
                return ReusableFragment().apply {
                    arguments = Bundle().apply {
                        putString("param", param)
                    }
                }
            }
        }
    }
    
    // Usage in different activities
    class Activity1 : AppCompatActivity() {
        override fun onCreate(savedInstanceState: Bundle?) {
            super.onCreate(savedInstanceState)
            setContentView(R.layout.activity_1)
            
            supportFragmentManager.beginTransaction()
                .add(R.id.container, ReusableFragment.newInstance("param1"))
                .commit()
        }
    }
    
    class Activity2 : AppCompatActivity() {
        override fun onCreate(savedInstanceState: Bundle?) {
            super.onCreate(savedInstanceState)
            setContentView(R.layout.activity_2)
            
            supportFragmentManager.beginTransaction()
                .add(R.id.container, ReusableFragment.newInstance("param2"))
                .commit()
        }
    }
}

// 4. Communication patterns
class CommunicationPatterns {
    // Activity-to-Fragment communication
    class HostActivity : AppCompatActivity() {
        fun sendDataToFragment(data: String) {
            val fragment = supportFragmentManager
                .findFragmentById(R.id.fragment_container) as? MyFragment
            fragment?.updateData(data)
        }
    }
    
    class MyFragment : Fragment() {
        fun updateData(data: String) {
            // Update UI with data
        }
    }
    
    // Fragment-to-Activity communication
    interface FragmentCallback {
        fun onFragmentAction(data: String)
    }
    
    class CallbackFragment : Fragment() {
        private var callback: FragmentCallback? = null
        
        override fun onAttach(context: Context) {
            super.onAttach(context)
            callback = context as? FragmentCallback
        }
        
        fun performAction() {
            callback?.onFragmentAction("data from fragment")
        }
    }
}
```

**Key differences:**
- **Lifecycle:** Activity is independent, Fragment depends on Activity
- **UI:** Activity is typically full-screen, Fragment is flexible
- **Reusability:** Fragments are more reusable across activities
- **Communication:** Different patterns for component interaction
- **Back stack:** Activities have their own back stack, Fragments share Activity's

85. **How do you handle configuration changes?**

**Answer:** Configuration changes (like rotation) can destroy and recreate components. Several strategies exist to handle this gracefully.

```kotlin
// 1. Using ViewModel (Recommended)
class ConfigurationChangeViewModel : ViewModel() {
    private val _data = MutableLiveData<String>()
    val data: LiveData<String> = _data
    
    fun loadData() {
        // Data survives configuration changes
        _data.value = "Survived configuration change"
    }
}

class MainActivity : AppCompatActivity() {
    private lateinit var viewModel: ConfigurationChangeViewModel
    
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)
        
        // ViewModel survives configuration changes
        viewModel = ViewModelProvider(this)[ConfigurationChangeViewModel::class.java]
        
        viewModel.data.observe(this) { data ->
            // Update UI with data
        }
    }
}

// 2. Using onSaveInstanceState
class SaveInstanceStateActivity : AppCompatActivity() {
    private var userInput: String = ""
    
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)
        
        // Restore saved state
        savedInstanceState?.let { bundle ->
            userInput = bundle.getString("user_input", "")
            updateUI(userInput)
        }
    }
    
    override fun onSaveInstanceState(outState: Bundle) {
        super.onSaveInstanceState(outState)
        // Save state before configuration change
        outState.putString("user_input", userInput)
    }
    
    private fun updateUI(input: String) {
        // Update UI with saved input
    }
}

// 3. Using Fragment with setRetainInstance (Deprecated)
class RetainedFragment : Fragment() {
    private var retainedData: String = ""
    
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        // Fragment survives configuration changes
        retainInstance = true
    }
    
    fun setData(data: String) {
        retainedData = data
    }
    
    fun getData(): String = retainedData
}

// 4. Using android:configChanges (Not recommended)
class ConfigChangesActivity : AppCompatActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)
    }
    
    override fun onConfigurationChanged(newConfig: Configuration) {
        super.onConfigurationChanged(newConfig)
        // Handle configuration change manually
        when (newConfig.orientation) {
            Configuration.ORIENTATION_LANDSCAPE -> {
                // Handle landscape
            }
            Configuration.ORIENTATION_PORTRAIT -> {
                // Handle portrait
            }
        }
    }
}

// 5. Using SavedStateHandle with ViewModel
class SavedStateViewModel(
    private val savedStateHandle: SavedStateHandle
) : ViewModel() {
    
    var userInput: String
        get() = savedStateHandle["user_input"] ?: ""
        set(value) = savedStateHandle.set("user_input", value)
    
    var complexData: ParcelableData?
        get() = savedStateHandle["complex_data"]
        set(value) = savedStateHandle.set("complex_data", value)
}

// 6. Using DataStore for persistent data
class DataStoreManager(private val context: Context) {
    private val dataStore = context.createDataStore(name = "settings")
    
    suspend fun saveData(key: String, value: String) {
        dataStore.edit { preferences ->
            preferences[stringPreferencesKey(key)] = value
        }
    }
    
    fun getData(key: String): Flow<String?> {
        return dataStore.data.map { preferences ->
            preferences[stringPreferencesKey(key)]
        }
    }
}

// 7. Using SharedPreferences
class SharedPreferencesManager(private val context: Context) {
    private val prefs = context.getSharedPreferences("app_prefs", Context.MODE_PRIVATE)
    
    fun saveData(key: String, value: String) {
        prefs.edit().putString(key, value).apply()
    }
    
    fun getData(key: String): String? {
        return prefs.getString(key, null)
    }
}

// 8. Real-world example: Form handling
class FormActivity : AppCompatActivity() {
    private lateinit var viewModel: FormViewModel
    private lateinit var binding: ActivityFormBinding
    
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        binding = ActivityFormBinding.inflate(layoutInflater)
        setContentView(binding.root)
        
        viewModel = ViewModelProvider(this)[FormViewModel::class.java]
        
        // Restore form data
        binding.nameEditText.setText(viewModel.name)
        binding.emailEditText.setText(viewModel.email)
        
        // Save data on text changes
        binding.nameEditText.addTextChangedListener { text ->
            viewModel.name = text.toString()
        }
        
        binding.emailEditText.addTextChangedListener { text ->
            viewModel.email = text.toString()
        }
    }
}

class FormViewModel(
    private val savedStateHandle: SavedStateHandle
) : ViewModel() {
    
    var name: String
        get() = savedStateHandle["name"] ?: ""
        set(value) = savedStateHandle.set("name", value)
    
    var email: String
        get() = savedStateHandle["email"] ?: ""
        set(value) = savedStateHandle.set("email", value)
}
```

**Best practices:**
- **Use ViewModel:** For UI-related data that survives configuration changes
- **Use SavedStateHandle:** For simple data that needs to survive process death
- **Use DataStore/SharedPreferences:** For persistent data
- **Avoid android:configChanges:** Unless absolutely necessary
- **Handle complex objects:** Use Parcelable for complex data
86. **What is `onSaveInstanceState` and `onRestoreInstanceState`?**

**Answer:** These methods allow you to save and restore the state of your Activity when it's destroyed and recreated due to configuration changes or system pressure.

```kotlin
class StateManagementActivity : AppCompatActivity() {
    private var counter = 0
    private var userInput = ""
    private var scrollPosition = 0
    
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)
        
        // Restore state if available
        savedInstanceState?.let { bundle ->
            counter = bundle.getInt("counter", 0)
            userInput = bundle.getString("user_input", "")
            scrollPosition = bundle.getInt("scroll_position", 0)
            
            // Restore UI state
            updateCounterDisplay()
            updateUserInput()
            restoreScrollPosition()
        }
    }
    
    override fun onSaveInstanceState(outState: Bundle) {
        super.onSaveInstanceState(outState)
        // Save current state
        outState.putInt("counter", counter)
        outState.putString("user_input", userInput)
        outState.putInt("scroll_position", scrollPosition)
    }
    
    override fun onRestoreInstanceState(savedInstanceState: Bundle) {
        super.onRestoreInstanceState(savedInstanceState)
        // Alternative restoration method
        counter = savedInstanceState.getInt("counter", 0)
        userInput = savedInstanceState.getString("user_input", "")
        scrollPosition = savedInstanceState.getInt("scroll_position", 0)
    }
    
    private fun updateCounterDisplay() {
        findViewById<TextView>(R.id.counter_text).text = counter.toString()
    }
    
    private fun updateUserInput() {
        findViewById<EditText>(R.id.input_field).setText(userInput)
    }
    
    private fun restoreScrollPosition() {
        findViewById<ScrollView>(R.id.scroll_view).scrollTo(0, scrollPosition)
    }
}

// Saving complex objects
class ComplexStateActivity : AppCompatActivity() {
    private var user: User? = null
    private var settings: Settings? = null
    
    override fun onSaveInstanceState(outState: Bundle) {
        super.onSaveInstanceState(outState)
        
        // Save Parcelable objects
        user?.let { outState.putParcelable("user", it) }
        settings?.let { outState.putParcelable("settings", it) }
        
        // Save Serializable objects
        outState.putSerializable("data", someSerializableData)
    }
    
    override fun onRestoreInstanceState(savedInstanceState: Bundle) {
        super.onRestoreInstanceState(savedInstanceState)
        
        // Restore Parcelable objects
        user = savedInstanceState.getParcelable("user")
        settings = savedInstanceState.getParcelable("settings")
        
        // Restore Serializable objects
        val data = savedInstanceState.getSerializable("data")
    }
}

// Parcelable data class
@Parcelize
data class User(
    val id: Int,
    val name: String,
    val email: String
) : Parcelable

@Parcelize
data class Settings(
    val theme: String,
    val language: String,
    val notifications: Boolean
) : Parcelable
```

**Key points:**
- **onSaveInstanceState:** Called before Activity is destroyed
- **onRestoreInstanceState:** Called after Activity is recreated
- **Bundle size limit:** ~1MB, avoid saving large data
- **Parcelable preferred:** Over Serializable for better performance

87. **Explain Service types (Started, Bound, Foreground)**

**Answer:** Android services come in three main types, each serving different purposes and having different lifecycle characteristics.

```kotlin
// 1. Started Service - runs independently
class StartedService : Service() {
    override fun onBind(intent: Intent?): IBinder? = null
    
    override fun onStartCommand(intent: Intent?, flags: Int, startId: Int): Int {
        // Perform background work
        performBackgroundTask()
        
        // Return value determines restart behavior
        return START_STICKY // Restart if killed
    }
    
    private fun performBackgroundTask() {
        // Long-running operation
        Thread {
            // Do work
            stopSelf() // Stop service when done
        }.start()
    }
}

// Starting a service
class MainActivity : AppCompatActivity() {
    fun startService() {
        val intent = Intent(this, StartedService::class.java)
        startService(intent)
    }
    
    fun stopService() {
        val intent = Intent(this, StartedService::class.java)
        stopService(intent)
    }
}

// 2. Bound Service - communicates with components
class BoundService : Service() {
    private val binder = LocalBinder()
    
    inner class LocalBinder : Binder() {
        fun getService(): BoundService = this@BoundService
    }
    
    override fun onBind(intent: Intent): IBinder {
        return binder
    }
    
    fun getData(): String {
        return "Data from bound service"
    }
    
    fun performTask(callback: (String) -> Unit) {
        // Perform task and call callback
        callback("Task completed")
    }
}

// Using bound service
class BoundActivity : AppCompatActivity() {
    private var boundService: BoundService? = null
    private var isBound = false
    
    private val connection = object : ServiceConnection {
        override fun onServiceConnected(name: ComponentName?, service: IBinder?) {
            val binder = service as BoundService.LocalBinder
            boundService = binder.getService()
            isBound = true
        }
        
        override fun onServiceDisconnected(name: ComponentName?) {
            isBound = false
        }
    }
    
    override fun onStart() {
        super.onStart()
        bindService(
            Intent(this, BoundService::class.java),
            connection,
            Context.BIND_AUTO_CREATE
        )
    }
    
    override fun onStop() {
        super.onStop()
        if (isBound) {
            unbindService(connection)
            isBound = false
        }
    }
    
    fun useService() {
        boundService?.let { service ->
            val data = service.getData()
            service.performTask { result ->
                // Handle result
            }
        }
    }
}

// 3. Foreground Service - visible to user
class ForegroundService : Service() {
    private val NOTIFICATION_ID = 1
    private val CHANNEL_ID = "ForegroundServiceChannel"
    
    override fun onCreate() {
        super.onCreate()
        createNotificationChannel()
    }
    
    override fun onStartCommand(intent: Intent?, flags: Int, startId: Int): Int {
        val notification = createNotification()
        startForeground(NOTIFICATION_ID, notification)
        
        // Perform work
        performForegroundWork()
        
        return START_STICKY
    }
    
    override fun onBind(intent: Intent?): IBinder? = null
    
    private fun createNotificationChannel() {
        if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.O) {
            val channel = NotificationChannel(
                CHANNEL_ID,
                "Foreground Service Channel",
                NotificationManager.IMPORTANCE_LOW
            )
            val manager = getSystemService(NotificationManager::class.java)
            manager.createNotificationChannel(channel)
        }
    }
    
    private fun createNotification(): Notification {
        val intent = Intent(this, MainActivity::class.java)
        val pendingIntent = PendingIntent.getActivity(
            this, 0, intent,
            PendingIntent.FLAG_IMMUTABLE
        )
        
        return NotificationCompat.Builder(this, CHANNEL_ID)
            .setContentTitle("Foreground Service")
            .setContentText("Service is running")
            .setSmallIcon(R.drawable.ic_notification)
            .setContentIntent(pendingIntent)
            .build()
    }
    
    private fun performForegroundWork() {
        // Perform work that should be visible to user
    }
}

// IntentService (Deprecated, use WorkManager instead)
class MyIntentService : IntentService("MyIntentService") {
    override fun onHandleIntent(intent: Intent?) {
        // Handle intent on background thread
        intent?.let { handleIntent(it) }
    }
    
    private fun handleIntent(intent: Intent) {
        when (intent.action) {
            "ACTION_DOWNLOAD" -> downloadFile(intent.getStringExtra("url"))
            "ACTION_UPLOAD" -> uploadFile(intent.getStringExtra("file"))
        }
    }
}

// Modern approach using WorkManager
class ModernBackgroundWork : Worker(
    context,
    WorkerParameters.Builder().build()
) {
    override fun doWork(): Result {
        return try {
            // Perform background work
            performWork()
            Result.success()
        } catch (e: Exception) {
            Result.failure()
        }
    }
    
    private fun performWork() {
        // Background work implementation
    }
}

// Usage
class WorkManagerExample {
    fun scheduleWork() {
        val workRequest = OneTimeWorkRequestBuilder<ModernBackgroundWork>()
            .setConstraints(
                Constraints.Builder()
                    .setRequiredNetworkType(NetworkType.CONNECTED)
                    .build()
            )
            .build()
        
        WorkManager.getInstance(context).enqueue(workRequest)
    }
}
```

**Service types comparison:**
- **Started Service:** Independent, no direct communication
- **Bound Service:** Communicates with bound components
- **Foreground Service:** Visible to user, survives system pressure
- **WorkManager:** Modern replacement for background work

88. **What is BroadcastReceiver and its types?**

**Answer:** BroadcastReceiver is a component that responds to system-wide broadcast announcements. It can be registered statically or dynamically.

```kotlin
// 1. Static BroadcastReceiver (in AndroidManifest.xml)
class StaticReceiver : BroadcastReceiver() {
    override fun onReceive(context: Context, intent: Intent) {
        when (intent.action) {
            Intent.ACTION_BOOT_COMPLETED -> {
                // Handle boot completion
                startAppService(context)
            }
            Intent.ACTION_POWER_CONNECTED -> {
                // Handle power connection
                showChargingNotification(context)
            }
            Intent.ACTION_POWER_DISCONNECTED -> {
                // Handle power disconnection
                hideChargingNotification(context)
            }
        }
    }
    
    private fun startAppService(context: Context) {
        val intent = Intent(context, MyService::class.java)
        context.startService(intent)
    }
    
    private fun showChargingNotification(context: Context) {
        // Show charging notification
    }
    
    private fun hideChargingNotification(context: Context) {
        // Hide charging notification
    }
}

// AndroidManifest.xml registration
/*
<receiver android:name=".StaticReceiver">
    <intent-filter>
        <action android:name="android.intent.action.BOOT_COMPLETED" />
        <action android:name="android.intent.action.POWER_CONNECTED" />
        <action android:name="android.intent.action.POWER_DISCONNECTED" />
        <category android:name="android.intent.category.DEFAULT" />
    </intent-filter>
</receiver>
*/

// 2. Dynamic BroadcastReceiver
class DynamicReceiverActivity : AppCompatActivity() {
    private lateinit var receiver: DynamicReceiver
    
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)
        
        // Register receiver
        receiver = DynamicReceiver()
        val filter = IntentFilter().apply {
            addAction(Intent.ACTION_AIRPLANE_MODE_CHANGED)
            addAction(ConnectivityManager.CONNECTIVITY_ACTION)
        }
        registerReceiver(receiver, filter)
    }
    
    override fun onDestroy() {
        super.onDestroy()
        // Unregister receiver
        unregisterReceiver(receiver)
    }
}

class DynamicReceiver : BroadcastReceiver() {
    override fun onReceive(context: Context, intent: Intent) {
        when (intent.action) {
            Intent.ACTION_AIRPLANE_MODE_CHANGED -> {
                val isAirplaneModeOn = intent.getBooleanExtra("state", false)
                handleAirplaneModeChange(isAirplaneModeOn)
            }
            ConnectivityManager.CONNECTIVITY_ACTION -> {
                handleConnectivityChange(context)
            }
        }
    }
    
    private fun handleAirplaneModeChange(isOn: Boolean) {
        if (isOn) {
            // Handle airplane mode on
        } else {
            // Handle airplane mode off
        }
    }
    
    private fun handleConnectivityChange(context: Context) {
        val connectivityManager = context.getSystemService(Context.CONNECTIVITY_SERVICE) as ConnectivityManager
        val networkInfo = connectivityManager.activeNetworkInfo
        
        if (networkInfo?.isConnected == true) {
            // Network is connected
        } else {
            // Network is disconnected
        }
    }
}

// 3. Local BroadcastReceiver (app-specific)
class LocalBroadcastExample {
    private lateinit var localReceiver: LocalReceiver
    private lateinit var localBroadcastManager: LocalBroadcastManager
    
    fun setupLocalBroadcast() {
        localReceiver = LocalReceiver()
        localBroadcastManager = LocalBroadcastManager.getInstance(context)
        
        val filter = IntentFilter("com.example.LOCAL_ACTION")
        localBroadcastManager.registerReceiver(localReceiver, filter)
    }
    
    fun sendLocalBroadcast() {
        val intent = Intent("com.example.LOCAL_ACTION")
        intent.putExtra("data", "Local broadcast data")
        localBroadcastManager.sendBroadcast(intent)
    }
    
    fun cleanup() {
        localBroadcastManager.unregisterReceiver(localReceiver)
    }
}

class LocalReceiver : BroadcastReceiver() {
    override fun onReceive(context: Context, intent: Intent) {
        val data = intent.getStringExtra("data")
        // Handle local broadcast
    }
}

// 4. Ordered BroadcastReceiver
class OrderedBroadcastExample {
    fun sendOrderedBroadcast() {
        val intent = Intent("com.example.ORDERED_ACTION")
        sendOrderedBroadcast(intent, null)
    }
}

class OrderedReceiver1 : BroadcastReceiver() {
    override fun onReceive(context: Context, intent: Intent) {
        // Process first
        val result = "Processed by Receiver1"
        setResultData(result)
    }
}

class OrderedReceiver2 : BroadcastReceiver() {
    override fun onReceive(context: Context, intent: Intent) {
        // Process second, can access result from previous receiver
        val previousResult = resultData
        // Continue processing
    }
}

// 5. Custom BroadcastReceiver with permissions
class SecureReceiver : BroadcastReceiver() {
    override fun onReceive(context: Context, intent: Intent) {
        // Handle secure broadcast
    }
}

// Sending secure broadcast
fun sendSecureBroadcast() {
    val intent = Intent("com.example.SECURE_ACTION")
    intent.putExtra("sensitive_data", "secret")
    sendBroadcast(intent, "com.example.CUSTOM_PERMISSION")
}

// 6. Modern approach using LiveData/Flow
class ModernBroadcastHandler {
    private val _networkState = MutableLiveData<Boolean>()
    val networkState: LiveData<Boolean> = _networkState
    
    private val receiver = object : BroadcastReceiver() {
        override fun onReceive(context: Context, intent: Intent) {
            if (intent.action == ConnectivityManager.CONNECTIVITY_ACTION) {
                val isConnected = checkNetworkConnectivity(context)
                _networkState.value = isConnected
            }
        }
    }
    
    private fun checkNetworkConnectivity(context: Context): Boolean {
        val connectivityManager = context.getSystemService(Context.CONNECTIVITY_SERVICE) as ConnectivityManager
        val networkInfo = connectivityManager.activeNetworkInfo
        return networkInfo?.isConnected == true
    }
}
```

**BroadcastReceiver types:**
- **Static:** Registered in manifest, survives app lifecycle
- **Dynamic:** Registered in code, tied to component lifecycle
- **Local:** App-specific, more secure and efficient
- **Ordered:** Processed in order, can modify results
- **Modern:** Use LiveData/Flow for app-internal communication

89. **How do you handle implicit vs explicit intents?**

**Answer:** Intents can be explicit (targeting specific component) or implicit (describing action to be performed). Each has different use cases and handling patterns.

```kotlin
// 1. Explicit Intent - targets specific component
class ExplicitIntentExample {
    fun startSpecificActivity() {
        // Explicit intent targeting specific activity
        val intent = Intent(this, TargetActivity::class.java)
        intent.putExtra("data", "Hello from explicit intent")
        startActivity(intent)
    }
    
    fun startSpecificService() {
        // Explicit intent targeting specific service
        val intent = Intent(this, MyService::class.java)
        intent.putExtra("action", "start_work")
        startService(intent)
    }
    
    fun bindSpecificService() {
        // Explicit intent for service binding
        val intent = Intent(this, BoundService::class.java)
        bindService(intent, serviceConnection, Context.BIND_AUTO_CREATE)
    }
}

// 2. Implicit Intent - describes action to perform
class ImplicitIntentExample {
    fun openWebPage(url: String) {
        // Implicit intent to open web page
        val intent = Intent(Intent.ACTION_VIEW, Uri.parse(url))
        if (intent.resolveActivity(packageManager) != null) {
            startActivity(intent)
        }
    }
    
    fun shareText(text: String) {
        // Implicit intent to share text
        val intent = Intent(Intent.ACTION_SEND).apply {
            type = "text/plain"
            putExtra(Intent.EXTRA_TEXT, text)
        }
        startActivity(Intent.createChooser(intent, "Share via"))
    }
    
    fun takePhoto() {
        // Implicit intent to take photo
        val intent = Intent(MediaStore.ACTION_IMAGE_CAPTURE)
        if (intent.resolveActivity(packageManager) != null) {
            startActivityForResult(intent, REQUEST_IMAGE_CAPTURE)
        }
    }
    
    fun pickImage() {
        // Implicit intent to pick image
        val intent = Intent(Intent.ACTION_PICK, MediaStore.Images.Media.EXTERNAL_CONTENT_URI)
        startActivityForResult(intent, REQUEST_IMAGE_PICK)
    }
    
    fun sendEmail(to: String, subject: String, body: String) {
        // Implicit intent to send email
        val intent = Intent(Intent.ACTION_SENDTO).apply {
            data = Uri.parse("mailto:$to")
            putExtra(Intent.EXTRA_SUBJECT, subject)
            putExtra(Intent.EXTRA_TEXT, body)
        }
        if (intent.resolveActivity(packageManager) != null) {
            startActivity(intent)
        }
    }
}

// 3. Handling implicit intents in your app
class IntentHandlerActivity : AppCompatActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)
        
        // Handle incoming intent
        handleIncomingIntent(intent)
    }
    
    private fun handleIncomingIntent(intent: Intent?) {
        intent?.let { incomingIntent ->
            when (incomingIntent.action) {
                Intent.ACTION_VIEW -> {
                    // Handle view action
                    val data = incomingIntent.data
                    data?.let { uri ->
                        handleViewAction(uri)
                    }
                }
                Intent.ACTION_SEND -> {
                    // Handle send action
                    val sharedText = incomingIntent.getStringExtra(Intent.EXTRA_TEXT)
                    sharedText?.let { text ->
                        handleSharedText(text)
                    }
                }
                "com.example.CUSTOM_ACTION" -> {
                    // Handle custom action
                    val customData = incomingIntent.getStringExtra("custom_data")
                    handleCustomAction(customData)
                }
            }
        }
    }
    
    private fun handleViewAction(uri: Uri) {
        // Handle view action based on URI
        when (uri.scheme) {
            "http", "https" -> openWebPage(uri.toString())
            "content" -> openContent(uri)
            else -> handleUnknownUri(uri)
        }
    }
    
    private fun handleSharedText(text: String) {
        // Handle shared text
        findViewById<EditText>(R.id.text_input).setText(text)
    }
    
    private fun handleCustomAction(data: String?) {
        // Handle custom action
        data?.let { processCustomData(it) }
    }
}

// 4. Intent filters in AndroidManifest.xml
/*
<activity android:name=".IntentHandlerActivity">
    <intent-filter>
        <action android:name="android.intent.action.VIEW" />
        <category android:name="android.intent.category.DEFAULT" />
        <data android:scheme="http" />
        <data android:scheme="https" />
    </intent-filter>
    
    <intent-filter>
        <action android:name="android.intent.action.SEND" />
        <category android:name="android.intent.category.DEFAULT" />
        <data android:mimeType="text/plain" />
    </intent-filter>
    
    <intent-filter>
        <action android:name="com.example.CUSTOM_ACTION" />
        <category android:name="android.intent.category.DEFAULT" />
    </intent-filter>
</activity>
*/

// 5. Intent resolution and fallbacks
class IntentResolutionExample {
    fun resolveIntent(intent: Intent): Boolean {
        val resolveInfo = packageManager.resolveActivity(intent, 0)
        return resolveInfo != null
    }
    
    fun startActivityWithFallback(intent: Intent) {
        if (resolveIntent(intent)) {
            startActivity(intent)
        } else {
            // Show fallback or error message
            showNoAppFoundDialog()
        }
    }
    
    fun getAvailableApps(intent: Intent): List<ResolveInfo> {
        return packageManager.queryIntentActivities(intent, 0)
    }
    
    fun showAppChooser(intent: Intent) {
        val chooser = Intent.createChooser(intent, "Select app")
        startActivity(chooser)
    }
}

// 6. Deep linking with implicit intents
class DeepLinkHandler {
    fun createDeepLink(productId: String): Uri {
        return Uri.parse("myapp://product/$productId")
    }
    
    fun handleDeepLink(uri: Uri) {
        when (uri.host) {
            "product" -> {
                val productId = uri.lastPathSegment
                openProduct(productId)
            }
            "category" -> {
                val categoryId = uri.lastPathSegment
                openCategory(categoryId)
            }
            "search" -> {
                val query = uri.getQueryParameter("q")
                performSearch(query)
            }
        }
    }
}

// 7. Intent extras and data handling
class IntentDataExample {
    fun createIntentWithData(): Intent {
        return Intent(this, TargetActivity::class.java).apply {
            // Add extras
            putExtra("string_data", "Hello")
            putExtra("int_data", 42)
            putExtra("boolean_data", true)
            putExtra("parcelable_data", User("John", 30))
            
            // Add data URI
            data = Uri.parse("content://com.example.data/123")
            
            // Add flags
            flags = Intent.FLAG_ACTIVITY_NEW_TASK or Intent.FLAG_ACTIVITY_CLEAR_TOP
        }
    }
    
    fun handleIntentData(intent: Intent) {
        // Extract extras
        val stringData = intent.getStringExtra("string_data")
        val intData = intent.getIntExtra("int_data", 0)
        val booleanData = intent.getBooleanExtra("boolean_data", false)
        val user = intent.getParcelableExtra<User>("parcelable_data")
        
        // Extract data URI
        val dataUri = intent.data
        
        // Process data
        processIntentData(stringData, intData, booleanData, user, dataUri)
    }
}
```

**Key differences:**
- **Explicit:** Targets specific component, more secure
- **Implicit:** Describes action, allows user choice
- **Resolution:** Check if intent can be handled
- **Fallbacks:** Provide alternatives when no app found
- **Deep linking:** Use custom URI schemes

90. **What is ContentProvider and when to use it?**

**Answer:** ContentProvider is a component that manages access to a structured set of data, enabling data sharing between applications.

```kotlin
// 1. Basic ContentProvider implementation
class MyContentProvider : ContentProvider() {
    private lateinit var databaseHelper: DatabaseHelper
    
    override fun onCreate(): Boolean {
        databaseHelper = DatabaseHelper(context)
        return true
    }
    
    override fun query(
        uri: Uri,
        projection: Array<out String>?,
        selection: String?,
        selectionArgs: Array<out String>?,
        sortOrder: String?
    ): Cursor? {
        val db = databaseHelper.readableDatabase
        
        return when (uriMatcher.match(uri)) {
            USERS -> {
                db.query("users", projection, selection, selectionArgs, null, null, sortOrder)
            }
            USER_ID -> {
                val id = uri.lastPathSegment
                db.query("users", projection, "_id = ?", arrayOf(id), null, null, sortOrder)
            }
            else -> null
        }
    }
    
    override fun insert(uri: Uri, values: ContentValues?): Uri? {
        val db = databaseHelper.writableDatabase
        
        return when (uriMatcher.match(uri)) {
            USERS -> {
                val id = db.insert("users", null, values)
                if (id > 0) {
                    ContentUris.withAppendedId(uri, id)
                } else null
            }
            else -> null
        }
    }
    
    override fun update(
        uri: Uri,
        values: ContentValues?,
        selection: String?,
        selectionArgs: Array<out String>?
    ): Int {
        val db = databaseHelper.writableDatabase
        
        return when (uriMatcher.match(uri)) {
            USERS -> db.update("users", values, selection, selectionArgs)
            USER_ID -> {
                val id = uri.lastPathSegment
                db.update("users", values, "_id = ?", arrayOf(id))
            }
            else -> 0
        }
    }
    
    override fun delete(
        uri: Uri,
        selection: String?,
        selectionArgs: Array<out String>?
    ): Int {
        val db = databaseHelper.writableDatabase
        
        return when (uriMatcher.match(uri)) {
            USERS -> db.delete("users", selection, selectionArgs)
            USER_ID -> {
                val id = uri.lastPathSegment
                db.delete("users", "_id = ?", arrayOf(id))
            }
            else -> 0
        }
    }
    
    override fun getType(uri: Uri): String? {
        return when (uriMatcher.match(uri)) {
            USERS -> "vnd.android.cursor.dir/vnd.com.example.users"
            USER_ID -> "vnd.android.cursor.item/vnd.com.example.users"
            else -> null
        }
    }
    
    companion object {
        private const val AUTHORITY = "com.example.provider"
        private const val USERS = 1
        private const val USER_ID = 2
        
        val CONTENT_URI = Uri.parse("content://$AUTHORITY/users")
        
        private val uriMatcher = UriMatcher(UriMatcher.NO_MATCH).apply {
            addURI(AUTHORITY, "users", USERS)
            addURI(AUTHORITY, "users/#", USER_ID)
        }
    }
}

// 2. Using ContentProvider
class ContentProviderClient {
    fun queryUsers(): List<User> {
        val users = mutableListOf<User>()
        val cursor = contentResolver.query(
            MyContentProvider.CONTENT_URI,
            null, null, null, null
        )
        
        cursor?.use {
            while (it.moveToNext()) {
                val user = User(
                    id = it.getLong(it.getColumnIndex("_id")),
                    name = it.getString(it.getColumnIndex("name")),
                    email = it.getString(it.getColumnIndex("email"))
                )
                users.add(user)
            }
        }
        
        return users
    }
    
    fun insertUser(user: User): Uri? {
        val values = ContentValues().apply {
            put("name", user.name)
            put("email", user.email)
        }
        
        return contentResolver.insert(MyContentProvider.CONTENT_URI, values)
    }
    
    fun updateUser(user: User): Int {
        val values = ContentValues().apply {
            put("name", user.name)
            put("email", user.email)
        }
        
        val uri = ContentUris.withAppendedId(MyContentProvider.CONTENT_URI, user.id)
        return contentResolver.update(uri, values, null, null)
    }
    
    fun deleteUser(userId: Long): Int {
        val uri = ContentUris.withAppendedId(MyContentProvider.CONTENT_URI, userId)
        return contentResolver.delete(uri, null, null)
    }
}

// 3. ContentProvider with file access
class FileContentProvider : ContentProvider() {
    override fun onCreate(): Boolean = true
    
    override fun openFile(uri: Uri, mode: String): ParcelFileDescriptor? {
        val file = File(context.filesDir, uri.lastPathSegment ?: "")
        
        return when (mode) {
            "r" -> ParcelFileDescriptor.open(file, ParcelFileDescriptor.MODE_READ_ONLY)
            "w" -> ParcelFileDescriptor.open(file, ParcelFileDescriptor.MODE_WRITE_ONLY)
            "rw" -> ParcelFileDescriptor.open(file, ParcelFileDescriptor.MODE_READ_WRITE)
            else -> null
        }
    }
    
    override fun getType(uri: Uri): String? {
        return when (uri.lastPathSegment?.substringAfterLast('.')) {
            "jpg", "jpeg" -> "image/jpeg"
            "png" -> "image/png"
            "pdf" -> "application/pdf"
            else -> "application/octet-stream"
        }
    }
    
    // Other required methods...
    override fun query(uri: Uri, projection: Array<out String>?, selection: String?, selectionArgs: Array<out String>?, sortOrder: String?): Cursor? = null
    override fun insert(uri: Uri, values: ContentValues?): Uri? = null
    override fun update(uri: Uri, values: ContentValues?, selection: String?, selectionArgs: Array<out String>?): Int = 0
    override fun delete(uri: Uri, selection: String?, selectionArgs: Array<out String>?): Int = 0
}

// 4. ContentProvider with permissions
class SecureContentProvider : ContentProvider() {
    override fun onCreate(): Boolean = true
    
    override fun query(
        uri: Uri,
        projection: Array<out String>?,
        selection: String?,
        selectionArgs: Array<out String>?,
        sortOrder: String?
    ): Cursor? {
        // Check permissions
        if (context.checkCallingPermission("com.example.READ_DATA") != PackageManager.PERMISSION_GRANTED) {
            throw SecurityException("Permission denied")
        }
        
        // Perform query
        return performQuery(uri, projection, selection, selectionArgs, sortOrder)
    }
    
    private fun performQuery(uri: Uri, projection: Array<out String>?, selection: String?, selectionArgs: Array<out String>?, sortOrder: String?): Cursor? {
        // Implementation
        return null
    }
    
    // Other required methods...
    override fun insert(uri: Uri, values: ContentValues?): Uri? = null
    override fun update(uri: Uri, values: ContentValues?, selection: String?, selectionArgs: Array<out String>?): Int = 0
    override fun delete(uri: Uri, selection: String?, selectionArgs: Array<out String>?): Int = 0
    override fun getType(uri: Uri): String? = null
}

// 5. ContentProvider with custom URI schemes
class CustomUriProvider : ContentProvider() {
    override fun onCreate(): Boolean = true
    
    override fun query(
        uri: Uri,
        projection: Array<out String>?,
        selection: String?,
        selectionArgs: Array<out String>?,
        sortOrder: String?
    ): Cursor? {
        return when (uriMatcher.match(uri)) {
            CUSTOM_DATA -> {
                // Handle custom data query
                createCustomCursor()
            }
            else -> null
        }
    }
    
    private fun createCustomCursor(): Cursor {
        // Create custom cursor implementation
        return object : Cursor {
            // Cursor implementation
            override fun getCount(): Int = 0
            override fun getPosition(): Int = 0
            override fun move(offset: Int): Boolean = false
            override fun moveToPosition(position: Int): Boolean = false
            override fun moveToFirst(): Boolean = false
            override fun moveToLast(): Boolean = false
            override fun moveToNext(): Boolean = false
            override fun moveToPrevious(): Boolean = false
            override fun isFirst(): Boolean = false
            override fun isLast(): Boolean = false
            override fun isBeforeFirst(): Boolean = false
            override fun isAfterLast(): Boolean = false
            override fun getColumnIndex(columnName: String): Int = 0
            override fun getColumnIndexOrThrow(columnName: String): Int = 0
            override fun getColumnName(columnIndex: Int): String = ""
            override fun getColumnNames(): Array<String> = arrayOf()
            override fun getColumnCount(): Int = 0
            override fun getBlob(columnIndex: Int): ByteArray = byteArrayOf()
            override fun getString(columnIndex: Int): String = ""
            override fun copyStringToBuffer(columnIndex: Int, buffer: CharArrayBuffer) {}
            override fun getShort(columnIndex: Int): Short = 0
            override fun getInt(columnIndex: Int): Int = 0
            override fun getLong(columnIndex: Int): Long = 0
            override fun getFloat(columnIndex: Int): Float = 0f
            override fun getDouble(columnIndex: Int): Double = 0.0
            override fun getType(columnIndex: Int): Int = 0
            override fun isNull(columnIndex: Int): Boolean = false
            override fun deactivate() {}
            override fun requery(): Boolean = false
            override fun close() {}
            override fun isClosed(): Boolean = false
            override fun registerContentObserver(observer: ContentObserver) {}
            override fun unregisterContentObserver(observer: ContentObserver) {}
            override fun registerDataSetObserver(observer: DataSetObserver) {}
            override fun unregisterDataSetObserver(observer: DataSetObserver) {}
            override fun setNotificationUri(cr: ContentResolver, uri: Uri) {}
            override fun getNotificationUri(): Uri? = null
            override fun getWantsAllOnMoveCalls(): Boolean = false
            override fun setExtras(extras: Bundle) {}
            override fun getExtras(): Bundle = Bundle()
            override fun respond(extras: Bundle): Bundle = Bundle()
        }
    }
    
    // Other required methods...
    override fun insert(uri: Uri, values: ContentValues?): Uri? = null
    override fun update(uri: Uri, values: ContentValues?, selection: String?, selectionArgs: Array<out String>?): Int = 0
    override fun delete(uri: Uri, selection: String?, selectionArgs: Array<out String>?): Int = 0
    override fun getType(uri: Uri): String? = null
    
    companion object {
        private const val AUTHORITY = "com.example.custom"
        private const val CUSTOM_DATA = 1
        
        private val uriMatcher = UriMatcher(UriMatcher.NO_MATCH).apply {
            addURI(AUTHORITY, "custom", CUSTOM_DATA)
        }
    }
}
```

**When to use ContentProvider:**
- **Data sharing:** Between different apps
- **Structured data:** Database-like access patterns
- **File access:** Sharing files between apps
- **Custom data:** Exposing app-specific data
- **Security:** Controlled access to sensitive data

### Views and Layouts
91. **Explain different layout types (Linear, Relative, Constraint, etc.)**

**Answer:** Android provides several layout types, each with different characteristics for arranging UI elements.

```xml
<!-- 1. LinearLayout - arranges children in a single direction -->
<LinearLayout
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:orientation="vertical"
    android:gravity="center">

    <TextView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Title"
        android:layout_margin="16dp" />

    <EditText
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:hint="Enter text"
        android:layout_margin="16dp" />

    <Button
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Submit"
        android:layout_gravity="center" />

</LinearLayout>

<!-- Horizontal LinearLayout -->
<LinearLayout
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:orientation="horizontal"
    android:gravity="center_vertical">

    <ImageView
        android:layout_width="48dp"
        android:layout_height="48dp"
        android:src="@drawable/icon" />

    <TextView
        android:layout_width="0dp"
        android:layout_height="wrap_content"
        android:layout_weight="1"
        android:text="Item text"
        android:layout_marginStart="16dp" />

    <Button
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Action" />

</LinearLayout>

<!-- 2. RelativeLayout - positions children relative to each other -->
<RelativeLayout
    android:layout_width="match_parent"
    android:layout_height="wrap_content">

    <ImageView
        android:id="@+id/avatar"
        android:layout_width="64dp"
        android:layout_height="64dp"
        android:layout_alignParentStart="true"
        android:layout_centerVertical="true"
        android:src="@drawable/avatar" />

    <TextView
        android:id="@+id/name"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_toEndOf="@id/avatar"
        android:layout_alignTop="@id/avatar"
        android:layout_marginStart="16dp"
        android:text="User Name"
        android:textStyle="bold" />

    <TextView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_below="@id/name"
        android:layout_toEndOf="@id/avatar"
        android:layout_marginStart="16dp"
        android:layout_marginTop="4dp"
        android:text="User description" />

    <Button
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_alignParentEnd="true"
        android:layout_centerVertical="true"
        android:text="Follow" />

</RelativeLayout>

<!-- 3. ConstraintLayout - flexible positioning with constraints -->
<androidx.constraintlayout.widget.ConstraintLayout
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:padding="16dp">

    <ImageView
        android:id="@+id/header_image"
        android:layout_width="0dp"
        android:layout_height="200dp"
        android:src="@drawable/header"
        app:layout_constraintTop_toTopOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintEnd_toEndOf="parent" />

    <TextView
        android:id="@+id/title"
        android:layout_width="0dp"
        android:layout_height="wrap_content"
        android:layout_marginTop="16dp"
        android:text="Article Title"
        android:textSize="24sp"
        android:textStyle="bold"
        app:layout_constraintTop_toBottomOf="@id/header_image"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintEnd_toEndOf="parent" />

    <TextView
        android:id="@+id/author"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_marginTop="8dp"
        android:text="By Author Name"
        android:textColor="@color/gray"
        app:layout_constraintTop_toBottomOf="@id/title"
        app:layout_constraintStart_toStartOf="parent" />

    <TextView
        android:id="@+id/date"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_marginTop="8dp"
        android:text="Jan 1, 2024"
        android:textColor="@color/gray"
        app:layout_constraintTop_toBottomOf="@id/title"
        app:layout_constraintEnd_toEndOf="parent" />

    <TextView
        android:layout_width="0dp"
        android:layout_height="wrap_content"
        android:layout_marginTop="16dp"
        android:text="Article content goes here..."
        app:layout_constraintTop_toBottomOf="@id/author"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintEnd_toEndOf="parent" />

</androidx.constraintlayout.widget.ConstraintLayout>

<!-- 4. FrameLayout - stacks children on top of each other -->
<FrameLayout
    android:layout_width="match_parent"
    android:layout_height="200dp">

    <ImageView
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:src="@drawable/background"
        android:scaleType="centerCrop" />

    <TextView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_gravity="center"
        android:text="Overlay Text"
        android:textColor="@color/white"
        android:textSize="24sp"
        android:background="@drawable/text_background" />

</FrameLayout>

<!-- 5. CoordinatorLayout - for complex scrolling behaviors -->
<androidx.coordinatorlayout.widget.CoordinatorLayout
    android:layout_width="match_parent"
    android:layout_height="match_parent">

    <com.google.android.material.appbar.AppBarLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content">

        <com.google.android.material.appbar.CollapsingToolbarLayout
            android:layout_width="match_parent"
            android:layout_height="200dp"
            app:layout_scrollFlags="scroll|exitUntilCollapsed">

            <ImageView
                android:layout_width="match_parent"
                android:layout_height="match_parent"
                android:src="@drawable/header"
                android:scaleType="centerCrop"
                app:layout_collapseMode="parallax" />

            <androidx.appcompat.widget.Toolbar
                android:id="@+id/toolbar"
                android:layout_width="match_parent"
                android:layout_height="?attr/actionBarSize"
                app:layout_collapseMode="pin" />

        </com.google.android.material.appbar.CollapsingToolbarLayout>

    </com.google.android.material.appbar.AppBarLayout>

    <androidx.recyclerview.widget.RecyclerView
        android:id="@+id/recycler_view"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        app:layout_behavior="@string/appbar_scrolling_view_behavior" />

    <com.google.android.material.floatingactionbutton.FloatingActionButton
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_margin="16dp"
        android:src="@drawable/ic_add"
        app:layout_anchor="@id/recycler_view"
        app:layout_anchorGravity="bottom|end" />

</androidx.coordinatorlayout.widget.CoordinatorLayout>
```

**Layout characteristics:**
- **LinearLayout:** Simple, predictable, good for lists
- **RelativeLayout:** Flexible positioning, complex hierarchies
- **ConstraintLayout:** Most flexible, best performance
- **FrameLayout:** Simple stacking, overlays
- **CoordinatorLayout:** Complex scrolling behaviors

92. **What is the difference between `match_parent` and `wrap_content`?**

**Answer:** These are layout parameters that control how a view sizes itself within its parent container.

```xml
<!-- match_parent - view takes all available space -->
<LinearLayout
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:orientation="vertical">

    <!-- Button takes full width of parent -->
    <Button
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="Full Width Button" />

    <!-- TextView takes full width -->
    <TextView
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="This text will wrap to multiple lines and take full width"
        android:background="@color/light_gray" />

</LinearLayout>

<!-- wrap_content - view sizes to its content -->
<LinearLayout
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:orientation="horizontal"
    android:gravity="center">

    <!-- Button sizes to its text -->
    <Button
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Auto Size" />

    <!-- ImageView sizes to its image -->
    <ImageView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:src="@drawable/icon"
        android:layout_marginStart="16dp" />

</LinearLayout>

<!-- Practical examples -->
<LinearLayout
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:orientation="vertical"
    android:padding="16dp">

    <!-- Header with title and action button -->
    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:orientation="horizontal"
        android:gravity="center_vertical">

        <!-- Title takes remaining space -->
        <TextView
            android:layout_width="0dp"
            android:layout_height="wrap_content"
            android:layout_weight="1"
            android:text="Screen Title"
            android:textSize="20sp"
            android:textStyle="bold" />

        <!-- Button sizes to its content -->
        <Button
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="Action" />

    </LinearLayout>

    <!-- Content area -->
    <ScrollView
        android:layout_width="match_parent"
        android:layout_height="0dp"
        android:layout_weight="1"
        android:layout_marginTop="16dp">

        <TextView
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:text="Content text that can be long and will wrap to multiple lines..." />

    </ScrollView>

    <!-- Footer with centered button -->
    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:orientation="horizontal"
        android:gravity="center"
        android:layout_marginTop="16dp">

        <Button
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="Submit" />

    </LinearLayout>

</LinearLayout>
```

**Key differences:**
- **match_parent:** View fills all available space in parent
- **wrap_content:** View sizes to fit its content
- **Performance:** wrap_content requires measurement, match_parent is faster
- **Use cases:** match_parent for containers, wrap_content for content

93. **How do you optimize layout performance?**

**Answer:** Layout performance optimization involves reducing measurement and layout passes, minimizing view hierarchy depth, and using efficient layouts.

```xml
<!-- 1. Use ConstraintLayout for complex layouts -->
<androidx.constraintlayout.widget.ConstraintLayout
    android:layout_width="match_parent"
    android:layout_height="wrap_content">

    <!-- Flat hierarchy instead of nested LinearLayouts -->
    <ImageView
        android:id="@+id/avatar"
        android:layout_width="48dp"
        android:layout_height="48dp"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toTopOf="parent"
        app:layout_constraintBottom_toBottomOf="parent" />

    <TextView
        android:id="@+id/name"
        android:layout_width="0dp"
        android:layout_height="wrap_content"
        android:layout_marginStart="16dp"
        app:layout_constraintStart_toEndOf="@id/avatar"
        app:layout_constraintEnd_toStartOf="@id/button"
        app:layout_constraintTop_toTopOf="@id/avatar" />

    <TextView
        android:layout_width="0dp"
        android:layout_height="wrap_content"
        android:layout_marginStart="16dp"
        app:layout_constraintStart_toEndOf="@id/avatar"
        app:layout_constraintEnd_toStartOf="@id/button"
        app:layout_constraintTop_toBottomOf="@id/name" />

    <Button
        android:id="@+id/button"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintTop_toTopOf="parent"
        app:layout_constraintBottom_toBottomOf="parent" />

</androidx.constraintlayout.widget.ConstraintLayout>

<!-- 2. Use ViewStub for conditional views -->
<LinearLayout
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:orientation="vertical">

    <TextView
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="Main content" />

    <!-- ViewStub - only inflated when needed -->
    <ViewStub
        android:id="@+id/error_stub"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout="@layout/error_layout" />

</LinearLayout>

<!-- 3. Use merge tag to eliminate unnecessary ViewGroups -->
<merge xmlns:android="http://schemas.android.com/apk/res/android">

    <TextView
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="Item 1" />

    <TextView
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="Item 2" />

</merge>

<!-- 4. Use include for reusable layouts -->
<LinearLayout
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:orientation="vertical">

    <include layout="@layout/header_layout" />
    
    <include
        layout="@layout/content_layout"
        android:layout_width="match_parent"
        android:layout_height="0dp"
        android:layout_weight="1" />
    
    <include layout="@layout/footer_layout" />

</LinearLayout>
```

```kotlin
// 5. Optimize in code
class LayoutOptimizationActivity : AppCompatActivity() {
    
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)
        
        // Use ViewStub for conditional content
        val errorStub = findViewById<ViewStub>(R.id.error_stub)
        errorStub?.setOnInflateListener { _, inflated ->
            // Configure inflated view
            inflated.findViewById<TextView>(R.id.error_text).text = "Error message"
        }
        
        // Inflate only when needed
        if (hasError) {
            errorStub?.inflate()
        }
    }
    
    // 6. Use RecyclerView for lists
    private fun setupRecyclerView() {
        val recyclerView = findViewById<RecyclerView>(R.id.recycler_view)
        recyclerView.apply {
            layoutManager = LinearLayoutManager(this@LayoutOptimizationActivity)
            adapter = MyAdapter()
            
            // Optimize performance
            setHasFixedSize(true)
            setItemViewCacheSize(20)
        }
    }
    
    // 7. Custom View for complex layouts
    class OptimizedCustomView @JvmOverloads constructor(
        context: Context,
        attrs: AttributeSet? = null,
        defStyleAttr: Int = 0
    ) : View(context, attrs, defStyleAttr) {
        
        private val paint = Paint(Paint.ANTI_ALIAS_FLAG)
        private val rect = RectF()
        
        override fun onDraw(canvas: Canvas) {
            super.onDraw(canvas)
            // Custom drawing - more efficient than nested layouts
            canvas.drawRoundRect(rect, 8f, 8f, paint)
        }
        
        override fun onMeasure(widthMeasureSpec: Int, heightMeasureSpec: Int) {
            // Efficient measurement
            val width = MeasureSpec.getSize(widthMeasureSpec)
            val height = MeasureSpec.getSize(heightMeasureSpec)
            setMeasuredDimension(width, height)
        }
    }
}

// 8. Layout performance monitoring
class LayoutPerformanceMonitor {
    fun monitorLayoutPerformance(view: View) {
        view.viewTreeObserver.addOnGlobalLayoutListener(object : ViewTreeObserver.OnGlobalLayoutListener {
            override fun onGlobalLayout() {
                // Log layout performance
                Log.d("Layout", "Layout pass completed")
                view.viewTreeObserver.removeOnGlobalLayoutListener(this)
            }
        })
    }
    
    fun checkLayoutDepth(view: View, depth: Int = 0) {
        if (depth > 10) {
            Log.w("Layout", "Deep view hierarchy detected: $depth levels")
        }
        
        if (view is ViewGroup) {
            for (i in 0 until view.childCount) {
                checkLayoutDepth(view.getChildAt(i), depth + 1)
            }
        }
    }
}

// 9. Use data binding for efficient updates
class DataBindingExample {
    private lateinit var binding: ActivityMainBinding
    
    fun setupDataBinding() {
        binding = DataBindingUtil.setContentView(this, R.layout.activity_main)
        binding.user = User("John", "john@example.com")
        binding.executePendingBindings()
    }
}

// 10. Optimize drawable usage
class DrawableOptimization {
    fun optimizeDrawables() {
        // Use vector drawables when possible
        val vectorDrawable = ContextCompat.getDrawable(context, R.drawable.ic_vector)
        
        // Cache drawables
        val drawableCache = mutableMapOf<Int, Drawable>()
        
        fun getDrawable(resId: Int): Drawable? {
            return drawableCache.getOrPut(resId) {
                ContextCompat.getDrawable(context, resId) ?: return null
            }
        }
    }
}
```

**Optimization techniques:**
- **Use ConstraintLayout:** Reduces view hierarchy depth
- **ViewStub:** Defer inflation of conditional views
- **merge tag:** Eliminate unnecessary ViewGroups
- **include tag:** Reuse layouts efficiently
- **RecyclerView:** For large lists
- **Custom Views:** For complex layouts
- **Data Binding:** Efficient UI updates
- **Vector Drawables:** Scalable and lightweight

94. **What is ViewBinding and how is it different from findViewById?**

**Answer:** ViewBinding is a feature that generates binding classes for XML layouts, providing type-safe access to views without the overhead of reflection.

```kotlin
// 1. ViewBinding setup
// build.gradle.kts
android {
    buildFeatures {
        viewBinding = true
    }
}

// 2. Using ViewBinding
class MainActivity : AppCompatActivity() {
    private lateinit var binding: ActivityMainBinding
    
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        binding = ActivityMainBinding.inflate(layoutInflater)
        setContentView(binding.root)
        
        // Type-safe access to views
        binding.titleTextView.text = "Hello ViewBinding"
        binding.submitButton.setOnClickListener {
            val input = binding.inputEditText.text.toString()
            handleSubmit(input)
        }
    }
    
    private fun handleSubmit(input: String) {
        binding.resultTextView.text = "Result: $input"
    }
}

// 3. ViewBinding vs findViewById
class ComparisonExample : AppCompatActivity() {
    
    // findViewById approach (old way)
    private fun findViewByIdApproach() {
        setContentView(R.layout.activity_main)
        
        val titleTextView = findViewById<TextView>(R.id.title_text_view)
        val inputEditText = findViewById<EditText>(R.id.input_edit_text)
        val submitButton = findViewById<Button>(R.id.submit_button)
        val resultTextView = findViewById<TextView>(R.id.result_text_view)
        
        titleTextView.text = "Hello findViewById"
        submitButton.setOnClickListener {
            val input = inputEditText.text.toString()
            resultTextView.text = "Result: $input"
        }
    }
    
    // ViewBinding approach (new way)
    private lateinit var binding: ActivityMainBinding
    
    private fun viewBindingApproach() {
        binding = ActivityMainBinding.inflate(layoutInflater)
        setContentView(binding.root)
        
        // No findViewById calls needed
        binding.titleTextView.text = "Hello ViewBinding"
        binding.submitButton.setOnClickListener {
            val input = binding.inputEditText.text.toString()
            binding.resultTextView.text = "Result: $input"
        }
    }
}

// 4. ViewBinding in Fragments
class MyFragment : Fragment() {
    private var _binding: FragmentMyBinding? = null
    private val binding get() = _binding!!
    
    override fun onCreateView(
        inflater: LayoutInflater,
        container: ViewGroup?,
        savedInstanceState: Bundle?
    ): View {
        _binding = FragmentMyBinding.inflate(inflater, container, false)
        return binding.root
    }
    
    override fun onViewCreated(view: View, savedInstanceState: Bundle?) {
        super.onViewCreated(view, savedInstanceState)
        
        // Use binding
        binding.textView.text = "Fragment with ViewBinding"
        binding.button.setOnClickListener {
            // Handle click
        }
    }
    
    override fun onDestroyView() {
        super.onDestroyView()
        _binding = null // Prevent memory leaks
    }
}

// 5. ViewBinding with RecyclerView
class MyAdapter : RecyclerView.Adapter<MyAdapter.ViewHolder>() {
    
    class ViewHolder(private val binding: ItemMyBinding) : RecyclerView.ViewHolder(binding.root) {
        fun bind(item: MyItem) {
            binding.titleTextView.text = item.title
            binding.descriptionTextView.text = item.description
            binding.imageView.setImageResource(item.imageRes)
        }
    }
    
    override fun onCreateViewHolder(parent: ViewGroup, viewType: Int): ViewHolder {
        val binding = ItemMyBinding.inflate(
            LayoutInflater.from(parent.context),
            parent,
            false
        )
        return ViewHolder(binding)
    }
    
    override fun onBindViewHolder(holder: ViewHolder, position: Int) {
        holder.bind(items[position])
    }
    
    override fun getItemCount() = items.size
}

// 6. ViewBinding with custom views
class CustomView @JvmOverloads constructor(
    context: Context,
    attrs: AttributeSet? = null,
    defStyleAttr: Int = 0
) : FrameLayout(context, attrs, defStyleAttr) {
    
    private val binding: ViewCustomBinding
    
    init {
        binding = ViewCustomBinding.inflate(LayoutInflater.from(context), this, true)
        setupViews()
    }
    
    private fun setupViews() {
        binding.titleTextView.text = "Custom View"
        binding.actionButton.setOnClickListener {
            // Handle action
        }
    }
    
    fun setTitle(title: String) {
        binding.titleTextView.text = title
    }
}

// 7. ViewBinding with include layouts
class IncludeLayoutActivity : AppCompatActivity() {
    private lateinit var binding: ActivityIncludeBinding
    
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        binding = ActivityIncludeBinding.inflate(layoutInflater)
        setContentView(binding.root)
        
        // Access included layout views
        binding.includeHeader.titleTextView.text = "Header Title"
        binding.includeContent.contentTextView.text = "Content"
        binding.includeFooter.footerButton.setOnClickListener {
            // Handle footer action
        }
    }
}

// 8. ViewBinding with ViewStub
class ViewStubActivity : AppCompatActivity() {
    private lateinit var binding: ActivityViewStubBinding
    
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        binding = ActivityViewStubBinding.inflate(layoutInflater)
        setContentView(binding.root)
        
        // Set up ViewStub
        binding.errorStub.setOnInflateListener { _, inflated ->
            // Access inflated layout
            val errorBinding = ViewStubErrorBinding.bind(inflated)
            errorBinding.errorTextView.text = "Error occurred"
            errorBinding.retryButton.setOnClickListener {
                retryOperation()
            }
        }
    }
    
    private fun showError() {
        binding.errorStub.inflate()
    }
}

// 9. ViewBinding with data binding comparison
class DataBindingComparison {
    
    // ViewBinding - simple view access
    private lateinit var viewBinding: ActivityMainBinding
    
    fun setupViewBinding() {
        viewBinding = ActivityMainBinding.inflate(layoutInflater)
        setContentView(viewBinding.root)
        
        // Manual updates
        viewBinding.titleTextView.text = "Title"
        viewBinding.userNameTextView.text = user.name
    }
    
    // DataBinding - automatic updates
    private lateinit var dataBinding: ActivityMainBinding
    
    fun setupDataBinding() {
        dataBinding = DataBindingUtil.setContentView(this, R.layout.activity_main)
        dataBinding.user = user // Automatic UI updates
    }
}

// 10. ViewBinding best practices
class ViewBindingBestPractices {
    
    // Use lateinit for Activity binding
    private lateinit var binding: ActivityMainBinding
    
    // Use nullable binding for Fragments
    private var _binding: FragmentMyBinding? = null
    private val binding get() = _binding!!
    
    // Clean up Fragment binding
    override fun onDestroyView() {
        super.onDestroyView()
        _binding = null
    }
    
    // Use binding in custom views
    class CustomView : FrameLayout {
        private val binding: ViewCustomBinding
        
        init {
            binding = ViewCustomBinding.inflate(LayoutInflater.from(context), this, true)
        }
    }
}
```

**Key differences:**
- **Type safety:** ViewBinding provides compile-time safety
- **Performance:** No reflection overhead like findViewById
- **Null safety:** Binding handles null checks automatically
- **IDE support:** Better autocomplete and refactoring
- **Memory management:** Requires manual cleanup in Fragments

95. **Explain DataBinding and its benefits**

**Answer:** DataBinding is a library that allows you to bind UI components to data sources declaratively, enabling automatic UI updates when data changes.

```kotlin
// 1. DataBinding setup
// build.gradle.kts
android {
    buildFeatures {
        dataBinding = true
    }
}

// 2. Basic DataBinding usage
class User(
    var name: String,
    var email: String,
    var age: Int
)

// activity_main.xml
/*
<layout xmlns:android="http://schemas.android.com/apk/res/android">
    <data>
        <variable
            name="user"
            type="com.example.User" />
    </data>
    
    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:orientation="vertical"
        android:padding="16dp">
        
        <TextView
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="@{user.name}"
            android:textSize="18sp" />
        
        <TextView
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="@{user.email}"
            android:textColor="@color/gray" />
        
        <TextView
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="@{String.valueOf(user.age)}" />
        
    </LinearLayout>
</layout>
*/

// MainActivity.kt
class MainActivity : AppCompatActivity() {
    private lateinit var binding: ActivityMainBinding
    
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        binding = DataBindingUtil.setContentView(this, R.layout.activity_main)
        
        val user = User("John Doe", "john@example.com", 30)
        binding.user = user
        binding.executePendingBindings()
    }
}

// 3. Two-way DataBinding
// activity_form.xml
/*
<layout>
    <data>
        <variable
            name="user"
            type="com.example.User" />
    </data>
    
    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:orientation="vertical"
        android:padding="16dp">
        
        <EditText
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:text="@={user.name}"
            android:hint="Name" />
        
        <EditText
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:text="@={user.email}"
            android:hint="Email" />
        
        <EditText
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:text="@={String.valueOf(user.age)}"
            android:hint="Age"
            android:inputType="number" />
        
    </LinearLayout>
</layout>
*/

// 4. Observable data classes
@Bindable
data class ObservableUser(
    var name: String = "",
    var email: String = "",
    var age: Int = 0
) {
    fun updateName(newName: String) {
        name = newName
        notifyPropertyChanged(BR.name)
    }
    
    fun updateEmail(newEmail: String) {
        email = newEmail
        notifyPropertyChanged(BR.email)
    }
    
    fun updateAge(newAge: Int) {
        age = newAge
        notifyPropertyChanged(BR.age)
    }
}

// 5. DataBinding with LiveData
class DataBindingViewModel : ViewModel() {
    private val _user = MutableLiveData<User>()
    val user: LiveData<User> = _user
    
    fun updateUser(newUser: User) {
        _user.value = newUser
    }
    
    fun updateName(name: String) {
        _user.value?.let { user ->
            _user.value = user.copy(name = name)
        }
    }
}

// activity_main.xml with LiveData
/*
<layout>
    <data>
        <variable
            name="viewModel"
            type="com.example.DataBindingViewModel" />
    </data>
    
    <LinearLayout>
        <TextView
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="@{viewModel.user.name}" />
        
        <TextView
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="@{viewModel.user.email}" />
        
    </LinearLayout>
</layout>
*/

// MainActivity.kt with LiveData
class MainActivity : AppCompatActivity() {
    private lateinit var binding: ActivityMainBinding
    private val viewModel: DataBindingViewModel by viewModels()
    
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        binding = DataBindingUtil.setContentView(this, R.layout.activity_main)
        
        binding.viewModel = viewModel
        binding.lifecycleOwner = this
        
        // LiveData automatically updates UI
        viewModel.updateUser(User("John", "john@example.com", 30))
    }
}

// 6. DataBinding with expressions
// activity_advanced.xml
/*
<layout>
    <data>
        <variable name="user" type="com.example.User" />
        <variable name="isVisible" type="Boolean" />
    </data>
    
    <LinearLayout>
        <TextView
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="@{user.name != null ? user.name : 'Unknown'}"
            android:visibility="@{isVisible ? View.VISIBLE : View.GONE}" />
        
        <TextView
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="@{user.age > 18 ? 'Adult' : 'Minor'}"
            android:textColor="@{user.age > 18 ? @color/green : @color/red}" />
        
    </LinearLayout>
</layout>
*/

// 7. DataBinding with custom binding adapters
@BindingAdapter("imageUrl")
fun setImageUrl(imageView: ImageView, url: String?) {
    url?.let {
        Glide.with(imageView.context)
            .load(it)
            .into(imageView)
    }
}

@BindingAdapter("android:text")
fun setText(textView: TextView, text: String?) {
    textView.text = text ?: ""
}

@BindingAdapter("android:onClick")
fun setOnClick(button: Button, listener: View.OnClickListener?) {
    button.setOnClickListener(listener)
}

// 8. DataBinding with RecyclerView
class UserAdapter : ListAdapter<User, UserAdapter.ViewHolder>(UserDiffCallback()) {
    
    class ViewHolder(private val binding: ItemUserBinding) : RecyclerView.ViewHolder(binding.root) {
        fun bind(user: User) {
            binding.user = user
            binding.executePendingBindings()
        }
    }
    
    override fun onCreateViewHolder(parent: ViewGroup, viewType: Int): ViewHolder {
        val binding = DataBindingUtil.inflate<ItemUserBinding>(
            LayoutInflater.from(parent.context),
            R.layout.item_user,
            parent,
            false
        )
        return ViewHolder(binding)
    }
    
    override fun onBindViewHolder(holder: ViewHolder, position: Int) {
        holder.bind(getItem(position))
    }
}

// item_user.xml
/*
<layout>
    <data>
        <variable name="user" type="com.example.User" />
    </data>
    
    <LinearLayout>
        <TextView
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="@{user.name}" />
        
        <TextView
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="@{user.email}" />
        
    </LinearLayout>
</layout>
*/

// 9. DataBinding with event handling
// activity_events.xml
/*
<layout>
    <data>
        <variable name="handler" type="com.example.EventHandler" />
    </data>
    
    <LinearLayout>
        <Button
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="Submit"
            android:onClick="@{handler::onSubmitClick}" />
        
        <Button
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="Cancel"
            android:onClick="@{() -> handler.onCancelClick()}" />
        
    </LinearLayout>
</layout>
*/

class EventHandler(private val context: Context) {
    fun onSubmitClick(view: View) {
        Toast.makeText(context, "Submit clicked", Toast.LENGTH_SHORT).show()
    }
    
    fun onCancelClick() {
        Toast.makeText(context, "Cancel clicked", Toast.LENGTH_SHORT).show()
    }
}

// 10. DataBinding performance optimization
class DataBindingOptimization {
    
    // Use executePendingBindings() when needed
    fun updateData() {
        binding.user = newUser
        binding.executePendingBindings() // Force immediate update
    }
    
    // Use lifecycle-aware binding
    fun setupLifecycleAwareBinding() {
        binding.lifecycleOwner = this
        // Binding automatically respects lifecycle
    }
    
    // Use custom binding adapters for complex logic
    @BindingAdapter("formattedDate")
    fun setFormattedDate(textView: TextView, date: Date?) {
        date?.let {
            val formatter = SimpleDateFormat("MMM dd, yyyy", Locale.getDefault())
            textView.text = formatter.format(it)
        }
    }
}
```

**DataBinding benefits:**
- **Automatic UI updates:** UI updates when data changes
- **Type safety:** Compile-time checking of expressions
- **Performance:** Efficient binding with minimal overhead
- **Declarative:** UI logic in XML, business logic in code
- **Lifecycle awareness:** Respects component lifecycle
- **Two-way binding:** Automatic data synchronization
- **Custom adapters:** Extensible binding logic
96. **What is the difference between ViewBinding and DataBinding?**

**Answer:**
- **ViewBinding** generates binding classes for each XML layout, providing type-safe access to views. It does not support binding expressions or automatic UI updates.
- **DataBinding** is more powerful: it allows you to bind UI components directly to data sources using expressions in XML, supports two-way binding, observable data, and custom binding adapters.

| Feature            | ViewBinding         | DataBinding         |
|--------------------|--------------------|---------------------|
| Type-safe access   | Yes                | Yes                 |
| Binding expressions| No                 | Yes                 |
| Two-way binding    | No                 | Yes                 |
| Observable data    | No                 | Yes                 |
| Performance        | Faster, less overhead | Slightly more overhead |
| Setup              | Simple, just enable | More setup, enable and use `<layout>` root |
| Use case           | Most apps, simple UIs | MVVM, dynamic UIs, LiveData, complex logic |

**Example:**
- ViewBinding:
  ```kotlin
  val binding = ActivityMainBinding.inflate(layoutInflater)
  binding.textView.text = "Hello"
  ```
- DataBinding:
  ```xml
  <TextView android:text="@{viewModel.userName}" />
  ```
  ```kotlin
  binding.viewModel = viewModel
  binding.lifecycleOwner = this
  ```

---

97. **How do you handle custom views?**

**Answer:**
Custom views are created by extending `View` or its subclasses and overriding methods like `onDraw`, `onMeasure`, and handling custom attributes.

**Steps:**
1. **Extend a View class:**
   ```kotlin
   class MyCustomView @JvmOverloads constructor(
       context: Context,
       attrs: AttributeSet? = null,
       defStyleAttr: Int = 0
   ) : View(context, attrs, defStyleAttr) {
       // Custom drawing, attributes, etc.
   }
   ```
2. **Override `onDraw` and `onMeasure`:**
   ```kotlin
   override fun onDraw(canvas: Canvas) {
       super.onDraw(canvas)
       // Draw custom shapes, text, etc.
   }
   override fun onMeasure(widthMeasureSpec: Int, heightMeasureSpec: Int) {
       val width = MeasureSpec.getSize(widthMeasureSpec)
       val height = MeasureSpec.getSize(heightMeasureSpec)
       setMeasuredDimension(width, height)
   }
   ```
3. **Handle custom XML attributes:**
    - Define in `res/values/attrs.xml`:
      ```xml
      <declare-styleable name="MyCustomView">
          <attr name="customColor" format="color"/>
      </declare-styleable>
      ```
    - Read in constructor:
      ```kotlin
      val typedArray = context.obtainStyledAttributes(attrs, R.styleable.MyCustomView)
      val color = typedArray.getColor(R.styleable.MyCustomView_customColor, Color.BLACK)
      typedArray.recycle()
      ```
4. **Use in XML:**
   ```xml
   <com.example.MyCustomView
       android:layout_width="100dp"
       android:layout_height="100dp"
       app:customColor="@color/primary" />
   ```

**Best practices:**
- Use `ViewGroup` for compound views.
- Support accessibility.
- Optimize drawing and measurement.

---

98. **What is the View drawing process (measure, layout, draw)?**

**Answer:**
The Android view drawing process consists of three main phases:

1. **Measure:**
    - Each view measures itself and its children to determine their size.
    - `onMeasure(widthMeasureSpec, heightMeasureSpec)` is called recursively.

2. **Layout:**
    - Each view sets the position of its children.
    - `onLayout(changed, left, top, right, bottom)` is called.

3. **Draw:**
    - Each view draws itself on the screen.
    - `onDraw(canvas)` is called.

**Example:**
```kotlin
class CustomView(context: Context) : View(context) {
    override fun onMeasure(widthMeasureSpec: Int, heightMeasureSpec: Int) {
        val width = 200
        val height = 200
        setMeasuredDimension(width, height)
    }
    override fun onLayout(changed: Boolean, left: Int, top: Int, right: Int, bottom: Int) {
        // For ViewGroup: position children here
    }
    override fun onDraw(canvas: Canvas) {
        canvas.drawColor(Color.RED)
    }
}
```

**Key points:**
- The process is recursive: parent measures/layouts/draws children.
- Custom views should call `super` methods and handle their own logic.

---

99. **Explain RecyclerView and its components**

**Answer:**
RecyclerView is a flexible, efficient widget for displaying large data sets in a scrollable list/grid.

**Main components:**
- **Adapter:** Binds data to views.
- **ViewHolder:** Holds references to item views for recycling.
- **LayoutManager:** Arranges items (Linear, Grid, Staggered).
- **ItemDecoration:** Adds custom drawing (dividers, spacing).
- **ItemAnimator:** Animates item changes.

**Example:**
```kotlin
class MyAdapter : RecyclerView.Adapter<MyAdapter.ViewHolder>() {
    class ViewHolder(val binding: ItemBinding) : RecyclerView.ViewHolder(binding.root)
    override fun onCreateViewHolder(parent: ViewGroup, viewType: Int): ViewHolder {
        val binding = ItemBinding.inflate(LayoutInflater.from(parent.context), parent, false)
        return ViewHolder(binding)
    }
    override fun onBindViewHolder(holder: ViewHolder, position: Int) {
        val item = items[position]
        holder.binding.textView.text = item.text
    }
    override fun getItemCount() = items.size
}
```
**Usage:**
```kotlin
val recyclerView = findViewById<RecyclerView>(R.id.recyclerView)
recyclerView.layoutManager = LinearLayoutManager(this)
recyclerView.adapter = MyAdapter()
```

---

100. **What is the difference between RecyclerView and ListView?**

**Answer:**
| Feature         | RecyclerView         | ListView           |
|-----------------|---------------------|--------------------|
| ViewHolder      | Required            | Optional           |
| LayoutManager   | Pluggable           | Fixed (vertical)   |
| Animations      | Built-in, extensible| Limited            |
| ItemDecoration  | Supported           | Not supported      |
| Performance     | Better (recycling, prefetching) | Good, but less flexible |
| Flexibility     | High (grid, staggered, custom) | Low              |
| Modern features | Yes (diff utils, paging, etc.) | No               |

- **RecyclerView** is the recommended, modern, and more flexible solution for lists and grids.
- **ListView** is legacy, less efficient, and not recommended for new apps.


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

## Android 16 New Features

### Core Platform Updates (2025)
471. **What are the key features introduced in Android 16?**
    
    **Answer:** Android 16 introduces several major improvements:
    
    **Performance & Privacy:**
    - **Enhanced Privacy Sandbox**: Improved ad tracking controls
    - **Predictive Performance**: AI-powered app optimization
    - **Advanced Battery Management**: Extended battery life algorithms
    - **Memory Compression**: Better RAM utilization
    
    **Developer Experience:**
    - **Compose 2.0**: New rendering engine and performance improvements
    - **KMP Native Support**: First-class Kotlin Multiplatform integration
    - **Enhanced Tooling**: Improved Android Studio integration
    - **WebAssembly Support**: WASM modules in Android apps
    
    **User Experience:**
    - **Adaptive UI 2.0**: Better foldable and large screen support
    - **Enhanced Accessibility**: AI-powered accessibility features
    - **Improved Notifications**: Smart notification grouping
    - **Advanced Gestures**: New gesture recognition APIs

472. **How does Android 16's new Privacy Sandbox work?**
    
    **Answer:** Privacy Sandbox in Android 16 provides privacy-preserving advertising:
    
    ```kotlin
    // Topics API usage
    class PrivacyManager {
        suspend fun getTopics(): List<Topic> {
            return TopicsManager.getInstance()
                .getTopics()
                .filter { it.isUserConsented() }
        }
        
        fun requestAdTargeting(topics: List<Topic>): AdRequest {
            return AdRequest.Builder()
                .setTopics(topics)
                .setPrivacyMode(PrivacyMode.ENHANCED)
                .build()
        }
    }
    ```

473. **What is Predictive Performance in Android 16?**
    
    **Answer:** Predictive Performance uses AI to optimize app behavior:
    
    ```kotlin
    // Predictive Performance API
    class PerformanceOptimizer {
        fun enablePredictiveOptimization() {
            PredictivePerformanceManager.getInstance().apply {
                enableCpuOptimization()
                enableMemoryOptimization()
                enableNetworkOptimization()
                setLearningMode(LearningMode.AGGRESSIVE)
            }
        }
        
        suspend fun getPerformanceInsights(): PerformanceInsights {
            return PredictivePerformanceManager.getInstance()
                .getInsights()
        }
    }
    ```

474. **How does Android 16 improve Compose performance?**
    
    **Answer:** Compose 2.0 in Android 16 introduces:
    
    - **Skia-based renderer**: Hardware-accelerated rendering
    - **Lazy compilation**: Compile composables on-demand
    - **Smart recomposition**: AI-powered recomposition optimization
    - **Memory pooling**: Reduced GC pressure
    
    ```kotlin
    // New Compose 2.0 features
    @Composable
    fun OptimizedComponent() {
        // Lazy compilation annotation
        @LazyComposable
        val expensiveContent = remember {
            generateComplexContent()
        }
        
        // Smart recomposition
        SmartRecomposition {
            Column {
                expensiveContent()
                // Other content
            }
        }
    }
    ```

475. **What are the new KMP features in Android 16?**
    
    **Answer:** Android 16 provides native KMP support:
    
    ```kotlin
    // Native KMP integration
    expect class PlatformSpecificManager {
        fun performNativeOperation(): String
    }
    
    // Android implementation
    actual class PlatformSpecificManager {
        actual fun performNativeOperation(): String {
            return AndroidNativeAPI.performOperation()
        }
    }
    
    // Shared business logic
    class SharedRepository {
        private val platformManager = PlatformSpecificManager()
        
        suspend fun getData(): Result<String> {
            return try {
                val result = platformManager.performNativeOperation()
                Result.success(result)
            } catch (e: Exception) {
                Result.failure(e)
            }
        }
    }
    ```

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