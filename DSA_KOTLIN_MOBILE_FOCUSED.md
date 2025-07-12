# 🚀 DSA for Android Developers - Kotlin Implementation Guide
*Data Structures & Algorithms with Kotlin examples and mobile development focus*

---

## 📋 Table of Contents

1. [Kotlin-Specific DSA Concepts](#kotlin-specific-dsa-concepts)
2. [Data Structures in Kotlin](#data-structures-in-kotlin)
3. [Algorithm Patterns with Kotlin](#algorithm-patterns-with-kotlin)
4. [Mobile Development Algorithms](#mobile-development-algorithms)
5. [Performance Optimization](#performance-optimization)
6. [Interview Questions & Solutions](#interview-questions--solutions)
7. [Practice Problems](#practice-problems)

---

## 🎯 Kotlin-Specific DSA Concepts

### Kotlin Collections vs Java Collections

**Kotlin Collections:**
```kotlin
// Immutable collections (read-only)
val list: List<Int> = listOf(1, 2, 3, 4, 5)
val set: Set<String> = setOf("a", "b", "c")
val map: Map<String, Int> = mapOf("a" to 1, "b" to 2)

// Mutable collections
val mutableList: MutableList<Int> = mutableListOf(1, 2, 3)
val mutableSet: MutableSet<String> = mutableSetOf("a", "b")
val mutableMap: MutableMap<String, Int> = mutableMapOf("a" to 1)
```

**Performance Characteristics:**
```kotlin
// ArrayList (mutable) - O(1) access, O(n) insertion/deletion
val arrayList = ArrayList<Int>()

// LinkedList (mutable) - O(n) access, O(1) insertion/deletion
val linkedList = LinkedList<Int>()

// HashSet - O(1) average case for add/contains
val hashSet = HashSet<Int>()

// TreeSet - O(log n) for add/contains, maintains order
val treeSet = TreeSet<Int>()
```

### Kotlin Sequences for Lazy Evaluation

```kotlin
// Eager evaluation (creates intermediate collections)
val result1 = listOf(1, 2, 3, 4, 5)
    .filter { it % 2 == 0 }  // Creates new list
    .map { it * 2 }          // Creates another new list
    .toList()

// Lazy evaluation (no intermediate collections)
val result2 = listOf(1, 2, 3, 4, 5)
    .asSequence()
    .filter { it % 2 == 0 }  // No intermediate collection
    .map { it * 2 }          // No intermediate collection
    .toList()
```

---

## 🏗️ Data Structures in Kotlin

### 1. Custom Linked List Implementation

```kotlin
data class Node<T>(
    val data: T,
    var next: Node<T>? = null
)

class LinkedList<T> {
    private var head: Node<T>? = null
    private var size = 0
    
    fun add(data: T) {
        val newNode = Node(data)
        if (head == null) {
            head = newNode
        } else {
            var current = head
            while (current?.next != null) {
                current = current.next
            }
            current?.next = newNode
        }
        size++
    }
    
    fun remove(data: T): Boolean {
        if (head == null) return false
        
        if (head?.data == data) {
            head = head?.next
            size--
            return true
        }
        
        var current = head
        while (current?.next != null) {
            if (current.next?.data == data) {
                current.next = current.next?.next
                size--
                return true
            }
            current = current.next
        }
        return false
    }
    
    fun contains(data: T): Boolean {
        var current = head
        while (current != null) {
            if (current.data == data) return true
            current = current.next
        }
        return false
    }
    
    fun size(): Int = size
    
    fun isEmpty(): Boolean = size == 0
}
```

### 2. Binary Tree Implementation

```kotlin
data class TreeNode<T>(
    val data: T,
    var left: TreeNode<T>? = null,
    var right: TreeNode<T>? = null
)

class BinaryTree<T> {
    private var root: TreeNode<T>? = null
    
    fun insert(data: T) {
        root = insertRec(root, data)
    }
    
    private fun insertRec(node: TreeNode<T>?, data: T): TreeNode<T> {
        if (node == null) {
            return TreeNode(data)
        }
        
        // Simple insertion (you can implement BST logic here)
        if (node.left == null) {
            node.left = TreeNode(data)
        } else if (node.right == null) {
            node.right = TreeNode(data)
        } else {
            // Insert in left subtree
            node.left = insertRec(node.left, data)
        }
        
        return node
    }
    
    // In-order traversal
    fun inOrderTraversal(): List<T> {
        val result = mutableListOf<T>()
        inOrderRec(root, result)
        return result
    }
    
    private fun inOrderRec(node: TreeNode<T>?, result: MutableList<T>) {
        if (node != null) {
            inOrderRec(node.left, result)
            result.add(node.data)
            inOrderRec(node.right, result)
        }
    }
    
    // Level-order traversal (BFS)
    fun levelOrderTraversal(): List<List<T>> {
        val result = mutableListOf<List<T>>()
        if (root == null) return result
        
        val queue = LinkedList<TreeNode<T>>()
        queue.add(root!!)
        
        while (queue.isNotEmpty()) {
            val levelSize = queue.size
            val currentLevel = mutableListOf<T>()
            
            repeat(levelSize) {
                val node = queue.removeFirst()
                currentLevel.add(node.data)
                
                node.left?.let { queue.add(it) }
                node.right?.let { queue.add(it) }
            }
            
            result.add(currentLevel)
        }
        
        return result
    }
}
```

### 3. Custom HashMap Implementation

```kotlin
class CustomHashMap<K, V> {
    private data class Entry<K, V>(
        val key: K,
        var value: V,
        var next: Entry<K, V>? = null
    )
    
    private var buckets: Array<Entry<K, V>?> = Array(16) { null }
    private var size = 0
    private val loadFactor = 0.75
    
    fun put(key: K, value: V) {
        val index = getIndex(key)
        var entry = buckets[index]
        
        // Check if key already exists
        while (entry != null) {
            if (entry.key == key) {
                entry.value = value
                return
            }
            entry = entry.next
        }
        
        // Add new entry
        val newEntry = Entry(key, value, buckets[index])
        buckets[index] = newEntry
        size++
        
        // Resize if needed
        if (size > buckets.size * loadFactor) {
            resize()
        }
    }
    
    fun get(key: K): V? {
        val index = getIndex(key)
        var entry = buckets[index]
        
        while (entry != null) {
            if (entry.key == key) {
                return entry.value
            }
            entry = entry.next
        }
        
        return null
    }
    
    fun remove(key: K): Boolean {
        val index = getIndex(key)
        var entry = buckets[index]
        var prev: Entry<K, V>? = null
        
        while (entry != null) {
            if (entry.key == key) {
                if (prev == null) {
                    buckets[index] = entry.next
                } else {
                    prev.next = entry.next
                }
                size--
                return true
            }
            prev = entry
            entry = entry.next
        }
        
        return false
    }
    
    private fun getIndex(key: K): Int {
        return abs(key.hashCode()) % buckets.size
    }
    
    private fun resize() {
        val oldBuckets = buckets
        buckets = Array(oldBuckets.size * 2) { null }
        size = 0
        
        oldBuckets.forEach { entry ->
            var current = entry
            while (current != null) {
                put(current.key, current.value)
                current = current.next
            }
        }
    }
    
    fun size(): Int = size
    fun isEmpty(): Boolean = size == 0
}
```

---

## 🔄 Algorithm Patterns with Kotlin

### 1. Sliding Window Pattern

```kotlin
// Maximum sum of subarray of size k
fun maxSumSubarray(arr: IntArray, k: Int): Int {
    if (arr.size < k) return -1
    
    var windowSum = 0
    var maxSum = 0
    
    // Calculate sum of first window
    for (i in 0 until k) {
        windowSum += arr[i]
    }
    maxSum = windowSum
    
    // Slide window and update max sum
    for (i in k until arr.size) {
        windowSum = windowSum - arr[i - k] + arr[i]
        maxSum = maxOf(maxSum, windowSum)
    }
    
    return maxSum
}

// Longest substring with at most k distinct characters
fun longestSubstringKDistinct(s: String, k: Int): Int {
    val charCount = mutableMapOf<Char, Int>()
    var left = 0
    var maxLength = 0
    
    for (right in s.indices) {
        val char = s[right]
        charCount[char] = charCount.getOrDefault(char, 0) + 1
        
        while (charCount.size > k) {
            val leftChar = s[left]
            charCount[leftChar] = charCount[leftChar]!! - 1
            if (charCount[leftChar] == 0) {
                charCount.remove(leftChar)
            }
            left++
        }
        
        maxLength = maxOf(maxLength, right - left + 1)
    }
    
    return maxLength
}
```

### 2. Two Pointers Pattern

```kotlin
// Find pair with given sum in sorted array
fun findPairWithSum(arr: IntArray, target: Int): Pair<Int, Int>? {
    var left = 0
    var right = arr.size - 1
    
    while (left < right) {
        val sum = arr[left] + arr[right]
        when {
            sum == target -> return Pair(arr[left], arr[right])
            sum < target -> left++
            else -> right--
        }
    }
    
    return null
}

// Remove duplicates from sorted array
fun removeDuplicates(arr: IntArray): Int {
    if (arr.isEmpty()) return 0
    
    var writeIndex = 1
    for (readIndex in 1 until arr.size) {
        if (arr[readIndex] != arr[readIndex - 1]) {
            arr[writeIndex] = arr[readIndex]
            writeIndex++
        }
    }
    
    return writeIndex
}

// Container with most water
fun maxArea(height: IntArray): Int {
    var left = 0
    var right = height.size - 1
    var maxArea = 0
    
    while (left < right) {
        val width = right - left
        val h = minOf(height[left], height[right])
        maxArea = maxOf(maxArea, width * h)
        
        if (height[left] < height[right]) {
            left++
        } else {
            right--
        }
    }
    
    return maxArea
}
```

### 3. Fast & Slow Pointers (Floyd's Cycle Detection)

```kotlin
// Detect cycle in linked list
fun hasCycle(head: Node<Int>?): Boolean {
    if (head == null || head.next == null) return false
    
    var slow = head
    var fast = head
    
    while (fast?.next != null) {
        slow = slow?.next
        fast = fast.next?.next
        
        if (slow == fast) return true
    }
    
    return false
}

// Find starting point of cycle
fun detectCycleStart(head: Node<Int>?): Node<Int>? {
    if (head == null || head.next == null) return null
    
    var slow = head
    var fast = head
    
    // Find meeting point
    while (fast?.next != null) {
        slow = slow?.next
        fast = fast.next?.next
        
        if (slow == fast) break
    }
    
    if (fast?.next == null) return null
    
    // Find cycle start
    slow = head
    while (slow != fast) {
        slow = slow?.next
        fast = fast?.next
    }
    
    return slow
}
```

### 4. Binary Search Variations

```kotlin
// Standard binary search
fun binarySearch(arr: IntArray, target: Int): Int {
    var left = 0
    var right = arr.size - 1
    
    while (left <= right) {
        val mid = left + (right - left) / 2
        when {
            arr[mid] == target -> return mid
            arr[mid] < target -> left = mid + 1
            else -> right = mid - 1
        }
    }
    
    return -1
}

// Find first occurrence
fun findFirstOccurrence(arr: IntArray, target: Int): Int {
    var left = 0
    var right = arr.size - 1
    var result = -1
    
    while (left <= right) {
        val mid = left + (right - left) / 2
        if (arr[mid] == target) {
            result = mid
            right = mid - 1 // Continue searching left
        } else if (arr[mid] < target) {
            left = mid + 1
        } else {
            right = mid - 1
        }
    }
    
    return result
}

// Find peak element
fun findPeakElement(arr: IntArray): Int {
    var left = 0
    var right = arr.size - 1
    
    while (left < right) {
        val mid = left + (right - left) / 2
        if (arr[mid] > arr[mid + 1]) {
            right = mid
        } else {
            left = mid + 1
        }
    }
    
    return left
}
```

### 5. Dynamic Programming with Kotlin

```kotlin
// Fibonacci with memoization
class Fibonacci {
    private val memo = mutableMapOf<Int, Long>()
    
    fun fib(n: Int): Long {
        if (n <= 1) return n.toLong()
        if (memo.containsKey(n)) return memo[n]!!
        
        memo[n] = fib(n - 1) + fib(n - 2)
        return memo[n]!!
    }
}

// Longest Increasing Subsequence
fun lengthOfLIS(nums: IntArray): Int {
    val dp = IntArray(nums.size) { 1 }
    var maxLength = 1
    
    for (i in 1 until nums.size) {
        for (j in 0 until i) {
            if (nums[i] > nums[j]) {
                dp[i] = maxOf(dp[i], dp[j] + 1)
            }
        }
        maxLength = maxOf(maxLength, dp[i])
    }
    
    return maxLength
}

// House Robber
fun rob(nums: IntArray): Int {
    if (nums.isEmpty()) return 0
    if (nums.size == 1) return nums[0]
    
    val dp = IntArray(nums.size)
    dp[0] = nums[0]
    dp[1] = maxOf(nums[0], nums[1])
    
    for (i in 2 until nums.size) {
        dp[i] = maxOf(dp[i - 1], dp[i - 2] + nums[i])
    }
    
    return dp[nums.size - 1]
}
```

---

## 📱 Mobile Development Algorithms

### 1. Image Processing Algorithms

```kotlin
// Image compression algorithm
class ImageCompressor {
    fun compressImage(bitmap: Bitmap, quality: Int): Bitmap {
        val outputStream = ByteArrayOutputStream()
        bitmap.compress(Bitmap.CompressFormat.JPEG, quality, outputStream)
        
        val bytes = outputStream.toByteArray()
        return BitmapFactory.decodeByteArray(bytes, 0, bytes.size)
    }
    
    fun resizeImage(bitmap: Bitmap, maxWidth: Int, maxHeight: Int): Bitmap {
        val ratio = minOf(
            maxWidth.toFloat() / bitmap.width,
            maxHeight.toFloat() / bitmap.height
        )
        
        val newWidth = (bitmap.width * ratio).toInt()
        val newHeight = (bitmap.height * ratio).toInt()
        
        return Bitmap.createScaledBitmap(bitmap, newWidth, newHeight, true)
    }
}
```

### 2. Location-Based Algorithms

```kotlin
// Calculate distance between two points (Haversine formula)
fun calculateDistance(lat1: Double, lon1: Double, lat2: Double, lon2: Double): Double {
    val r = 6371 // Earth's radius in kilometers
    
    val dLat = Math.toRadians(lat2 - lat1)
    val dLon = Math.toRadians(lon2 - lon1)
    
    val a = sin(dLat / 2) * sin(dLat / 2) +
            cos(Math.toRadians(lat1)) * cos(Math.toRadians(lat2)) *
            sin(dLon / 2) * sin(dLon / 2)
    
    val c = 2 * atan2(sqrt(a), sqrt(1 - a))
    
    return r * c
}

// Find nearest location
fun findNearestLocation(
    userLocation: Pair<Double, Double>,
    locations: List<Pair<Double, Double>>
): Pair<Double, Double>? {
    if (locations.isEmpty()) return null
    
    return locations.minByOrNull { location ->
        calculateDistance(
            userLocation.first, userLocation.second,
            location.first, location.second
        )
    }
}
```

### 3. Cache Management Algorithms

```kotlin
// LRU Cache implementation
class LRUCache<K, V>(private val capacity: Int) {
    private data class Node<K, V>(
        val key: K,
        var value: V,
        var prev: Node<K, V>? = null,
        var next: Node<K, V>? = null
    )
    
    private val cache = mutableMapOf<K, Node<K, V>>()
    private var head: Node<K, V>? = null
    private var tail: Node<K, V>? = null
    
    fun get(key: K): V? {
        val node = cache[key] ?: return null
        
        // Move to front
        removeNode(node)
        addToFront(node)
        
        return node.value
    }
    
    fun put(key: K, value: V) {
        if (cache.containsKey(key)) {
            val node = cache[key]!!
            node.value = value
            removeNode(node)
            addToFront(node)
        } else {
            val newNode = Node(key, value)
            cache[key] = newNode
            addToFront(newNode)
            
            if (cache.size > capacity) {
                val lastNode = tail!!
                removeNode(lastNode)
                cache.remove(lastNode.key)
            }
        }
    }
    
    private fun addToFront(node: Node<K, V>) {
        node.next = head
        node.prev = null
        
        head?.prev = node
        head = node
        
        if (tail == null) {
            tail = node
        }
    }
    
    private fun removeNode(node: Node<K, V>) {
        if (node.prev != null) {
            node.prev!!.next = node.next
        } else {
            head = node.next
        }
        
        if (node.next != null) {
            node.next!!.prev = node.prev
        } else {
            tail = node.prev
        }
    }
}
```

### 4. Search and Filter Algorithms

```kotlin
// Fuzzy search implementation
class FuzzySearch {
    fun search(query: String, items: List<String>): List<String> {
        return items.filter { item ->
            isFuzzyMatch(query.lowercase(), item.lowercase())
        }
    }
    
    private fun isFuzzyMatch(query: String, text: String): Boolean {
        var queryIndex = 0
        var textIndex = 0
        
        while (queryIndex < query.length && textIndex < text.length) {
            if (query[queryIndex] == text[textIndex]) {
                queryIndex++
            }
            textIndex++
        }
        
        return queryIndex == query.length
    }
}

// Autocomplete with Trie
class TrieNode {
    val children = mutableMapOf<Char, TrieNode>()
    var isEndOfWord = false
    var suggestions = mutableListOf<String>()
}

class Autocomplete {
    private val root = TrieNode()
    
    fun insert(word: String) {
        var current = root
        for (char in word) {
            current.children.getOrPut(char) { TrieNode() }
            current = current.children[char]!!
            current.suggestions.add(word)
        }
        current.isEndOfWord = true
    }
    
    fun getSuggestions(prefix: String): List<String> {
        var current = root
        for (char in prefix) {
            current = current.children[char] ?: return emptyList()
        }
        return current.suggestions.take(5)
    }
}
```

---

## ⚡ Performance Optimization

### 1. Memory Management

```kotlin
// Object pooling for expensive objects
class ObjectPool<T>(
    private val factory: () -> T,
    private val reset: (T) -> Unit,
    private val maxSize: Int
) {
    private val pool = ArrayDeque<T>()
    
    fun acquire(): T {
        return if (pool.isNotEmpty()) {
            pool.removeFirst()
        } else {
            factory()
        }
    }
    
    fun release(obj: T) {
        if (pool.size < maxSize) {
            reset(obj)
            pool.addLast(obj)
        }
    }
}

// Usage example
val bitmapPool = ObjectPool(
    factory = { Bitmap.createBitmap(100, 100, Bitmap.Config.ARGB_8888) },
    reset = { bitmap -> bitmap.recycle() },
    maxSize = 10
)
```

### 2. Algorithm Optimization

```kotlin
// Optimized sorting for mobile
class OptimizedSort {
    // Hybrid sort (QuickSort + InsertionSort for small arrays)
    fun hybridSort(arr: IntArray, left: Int = 0, right: Int = arr.size - 1) {
        if (right - left < 10) {
            insertionSort(arr, left, right)
        } else {
            quickSort(arr, left, right)
        }
    }
    
    private fun quickSort(arr: IntArray, left: Int, right: Int) {
        if (left < right) {
            val pivot = partition(arr, left, right)
            quickSort(arr, left, pivot - 1)
            quickSort(arr, pivot + 1, right)
        }
    }
    
    private fun partition(arr: IntArray, left: Int, right: Int): Int {
        val pivot = arr[right]
        var i = left - 1
        
        for (j in left until right) {
            if (arr[j] <= pivot) {
                i++
                arr[i] = arr[j].also { arr[j] = arr[i] }
            }
        }
        
        arr[i + 1] = arr[right].also { arr[right] = arr[i + 1] }
        return i + 1
    }
    
    private fun insertionSort(arr: IntArray, left: Int, right: Int) {
        for (i in left + 1..right) {
            val key = arr[i]
            var j = i - 1
            
            while (j >= left && arr[j] > key) {
                arr[j + 1] = arr[j]
                j--
            }
            arr[j + 1] = key
        }
    }
}
```

---

## 🎯 Interview Questions & Solutions

### 1. Two Sum Problem

```kotlin
// Brute force approach
fun twoSumBruteForce(nums: IntArray, target: Int): IntArray {
    for (i in nums.indices) {
        for (j in i + 1 until nums.size) {
            if (nums[i] + nums[j] == target) {
                return intArrayOf(i, j)
            }
        }
    }
    return intArrayOf()
}

// Optimized approach with HashMap
fun twoSumOptimized(nums: IntArray, target: Int): IntArray {
    val map = mutableMapOf<Int, Int>()
    
    for (i in nums.indices) {
        val complement = target - nums[i]
        if (map.containsKey(complement)) {
            return intArrayOf(map[complement]!!, i)
        }
        map[nums[i]] = i
    }
    
    return intArrayOf()
}
```

### 2. Valid Parentheses

```kotlin
fun isValidParentheses(s: String): Boolean {
    val stack = ArrayDeque<Char>()
    val pairs = mapOf(
        ')' to '(',
        '}' to '{',
        ']' to '['
    )
    
    for (char in s) {
        when (char) {
            '(', '{', '[' -> stack.addLast(char)
            ')', '}', ']' -> {
                if (stack.isEmpty() || stack.removeLast() != pairs[char]) {
                    return false
                }
            }
        }
    }
    
    return stack.isEmpty()
}
```

### 3. Merge Two Sorted Lists

```kotlin
fun mergeTwoLists(l1: Node<Int>?, l2: Node<Int>?): Node<Int>? {
    val dummy = Node(0)
    var current = dummy
    var p1 = l1
    var p2 = l2
    
    while (p1 != null && p2 != null) {
        if (p1.data <= p2.data) {
            current.next = p1
            p1 = p1.next
        } else {
            current.next = p2
            p2 = p2.next
        }
        current = current.next!!
    }
    
    current.next = p1 ?: p2
    return dummy.next
}
```

### 4. Reverse Linked List

```kotlin
fun reverseList(head: Node<Int>?): Node<Int>? {
    var prev: Node<Int>? = null
    var current = head
    
    while (current != null) {
        val next = current.next
        current.next = prev
        prev = current
        current = next
    }
    
    return prev
}
```

---

## 📚 Practice Problems

### Easy Level
1. **Valid Anagram** - Check if two strings are anagrams
2. **Climbing Stairs** - Count ways to climb n stairs
3. **Best Time to Buy and Sell Stock** - Find maximum profit
4. **Contains Duplicate** - Check for duplicates in array
5. **Valid Palindrome** - Check if string is palindrome

### Medium Level
1. **Add Two Numbers** - Add two linked lists representing numbers
2. **Longest Substring Without Repeating Characters**
3. **Container With Most Water**
4. **3Sum** - Find all unique triplets that sum to zero
5. **Remove Nth Node From End of List**

### Hard Level
1. **Median of Two Sorted Arrays**
2. **Regular Expression Matching**
3. **Merge k Sorted Lists**
4. **Longest Valid Parentheses**
5. **Trapping Rain Water**

### Mobile-Specific Problems
1. **Image Compression Algorithm**
2. **Location-Based Search**
3. **Cache Management**
4. **Real-time Data Synchronization**
5. **Offline-First Data Management**

---

## 🎯 Tips for Android Developers

### 1. Use Kotlin Features Effectively
```kotlin
// Use extension functions
fun IntArray.binarySearch(target: Int): Int {
    // Implementation
}

// Use data classes for clean code
data class Point(val x: Int, val y: Int)

// Use when expressions
fun getDirection(x: Int, y: Int) = when {
    x > 0 && y > 0 -> "Northeast"
    x > 0 && y < 0 -> "Southeast"
    x < 0 && y > 0 -> "Northwest"
    x < 0 && y < 0 -> "Southwest"
    else -> "Center"
}
```

### 2. Consider Mobile Constraints
- **Memory**: Use object pooling for expensive objects
- **Battery**: Optimize algorithms for efficiency
- **Network**: Implement offline-first patterns
- **Storage**: Use efficient data structures

### 3. Practice with Real-World Scenarios
- Image processing algorithms
- Location-based services
- Real-time data handling
- Cache management
- Search and filtering

---

## 📖 Additional Resources

### Books
- "Cracking the Coding Interview" by Gayle McDowell
- "Introduction to Algorithms" by CLRS
- "Kotlin in Action" by Dmitry Jemerov

### Online Platforms
- [LeetCode](https://leetcode.com/)
- [HackerRank](https://www.hackerrank.com/)
- [Codeforces](https://codeforces.com/)

### Kotlin-Specific Resources
- [Kotlin Documentation](https://kotlinlang.org/docs/)
- [Kotlin Collections Guide](https://kotlinlang.org/docs/collections-overview.html)
- [Android Developer Documentation](https://developer.android.com/)

---

*Happy coding with Kotlin! 🚀* 