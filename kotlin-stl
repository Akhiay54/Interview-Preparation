# 🚀 Kotlin Standard Library for DSA — C++ STL Equivalents

A **clear, practical reference** for mapping **C++ STL** containers, algorithms & idioms to **Kotlin** for Data Structures & Algorithms (CP/interviews).

---

## 📑 Table of Contents

1. Containers

   * Arrays
   * Lists
   * Sets
   * Maps
   * Stacks
   * Queues
   * Priority Queues (Heaps)
   * Pairs
2. Algorithms

   * Sorting
   * Searching
   * Min/Max
   * Reverse
   * Frequency Map
   * Permutations
3. Useful Idioms
4. Advanced DSA Structures

   * Trie
   * Tree (Binary Tree)
   * Graph
   * Tips

---

## ✅ 1. Containers

### 🔢 1.1 Arrays & Vectors

| C++                    | Kotlin                                     |
| ---------------------- | ------------------------------------------ |
| `vector<int>`          | `MutableList<Int>()` or `ArrayList<Int>()` |
| Fixed-size `int arr[]` | `IntArray(n)` or `Array<Int>(n)`           |

```kotlin
// Fixed array
val arr = IntArray(5)      // [0,0,0,0,0]
arr[0] = 10

// Dynamic list (like vector)
val vec = mutableListOf(1, 2, 3)
vec.add(4)
vec.removeAt(1)
```

---

### 📄 1.2 Lists (LinkedList)

| C++         | Kotlin                               |
| ----------- | ------------------------------------ |
| `list<int>` | `LinkedList<Int>` (Java Collections) |
| `vector`    | `MutableList<T>`                     |

```kotlin
val linked = java.util.LinkedList<Int>()
linked.addFirst(1)
linked.addLast(2)
linked.removeFirst()
```

---

### 🔑 1.3 Sets

| C++ STL         | Kotlin                               |
| --------------- | ------------------------------------ |
| `set`           | `mutableSetOf<T>()`                  |
| `unordered_set` | `mutableSetOf<T>()` (HashSet)        |
| `multiset`      | `Map<T, Int>` for frequency counting |

```kotlin
val st = mutableSetOf(1, 2, 3)
st.add(4)
st.remove(2)

// Multiset-like
val multi = mutableMapOf<Int, Int>()
multi[1] = multi.getOrDefault(1, 0) + 1
```

---

### 🗺️ 1.4 Maps

| C++ STL         | Kotlin                     |
| --------------- | -------------------------- |
| `map<int, int>` | `mutableMapOf<Int, Int>()` |
| `unordered_map` | `mutableMapOf` (HashMap)   |
| `multimap`      | `Map<K, MutableList<V>>`   |

```kotlin
val mp = mutableMapOf<String, Int>()
mp["A"] = 5
mp["B"] = mp.getOrDefault("B", 0) + 2
mp.remove("A")

// Multimap-like
val multiMap = mutableMapOf<String, MutableList<Int>>()
multiMap.getOrPut("A") { mutableListOf() }.add(10)
```

---

### 📚 1.5 Stack

| C++     | Kotlin            |
| ------- | ----------------- |
| `stack` | `ArrayDeque<T>()` |

```kotlin
val stack = ArrayDeque<Int>()
stack.addLast(1) // push
stack.removeLast() // pop
stack.last() // top
```

---

### 📥 1.6 Queue

| C++     | Kotlin            |
| ------- | ----------------- |
| `queue` | `ArrayDeque<T>()` |

```kotlin
val queue = ArrayDeque<Int>()
queue.addLast(1) // enqueue
queue.removeFirst() // dequeue
queue.first() // front
```

---

### ⏫ 1.7 Priority Queue (Heap)

| C++                    | Kotlin                                     |
| ---------------------- | ------------------------------------------ |
| `priority_queue` (max) | `PriorityQueue<T>()` (min-heap by default) |
| `priority_queue` (min) | `PriorityQueue<T>(compareBy { it })`       |

```kotlin
import java.util.PriorityQueue

val pq = PriorityQueue<Int>() // Min-heap
pq.add(3)
pq.add(1)
pq.add(2)
pq.poll() // 1

// Max-heap
val maxHeap = PriorityQueue<Int>(compareByDescending { it })
maxHeap.add(1)
maxHeap.add(5)
maxHeap.poll() // 5
```

---

### 🤝 1.8 Pairs

| C++     | Kotlin            |
| ------- | ----------------- |
| `pair`  | `Pair<A, B>`      |
| `tuple` | `Triple<A, B, C>` |

```kotlin
val pair = Pair(1, "A")
val (num, str) = pair

val triple = Triple(1, "B", true)
val (x, y, z) = triple
```

---

## ⚙️ 2. Common Algorithms

### 🏷️ Sorting

```kotlin
val list = mutableListOf(3, 1, 4)
list.sort()           // [1, 3, 4]
list.sortDescending() // [4, 3, 1]
```

---

### 🔍 Binary Search

```kotlin
val list = listOf(1, 3, 5, 7)
val idx = list.binarySearch(5) // index of 5, or negative if not found
```

---

### 🔢 Min & Max

```kotlin
val list = listOf(1, 2, 3)
val mn = list.minOrNull()
val mx = list.maxOrNull()
```

---

### 🔄 Reverse

```kotlin
val arr = mutableListOf(1, 2, 3)
arr.reverse() // [3, 2, 1]
```

---

### 🔁 Frequency Map

```kotlin
val freq = mutableMapOf<Char, Int>()
val str = "hello"
for (c in str) {
  freq[c] = freq.getOrDefault(c, 0) + 1
}
```

---

## 🛠️ 3. Useful Idioms

✅ `getOrPut` for maps with lists
✅ `getOrDefault` for safe gets
✅ `withIndex()` for index + value loop
✅ `forEach`, `map`, `filter` for functional style

---

## 🚀 4. Advanced DSA Structures

### 📚 Trie

```kotlin
class TrieNode {
    val children = HashMap<Char, TrieNode>()
    var isEnd = false
}

class Trie {
    private val root = TrieNode()

    fun insert(word: String) {
        var node = root
        for (c in word) {
            node = node.children.getOrPut(c) { TrieNode() }
        }
        node.isEnd = true
    }

    fun search(word: String): Boolean {
        var node = root
        for (c in word) {
            node = node.children[c] ?: return false
        }
        return node.isEnd
    }
}
```

### 🌳 Tree (Binary Tree)

```kotlin
class TreeNode(val value: Int) {
    var left: TreeNode? = null
    var right: TreeNode? = null
}

fun inorder(node: TreeNode?) {
    if (node == null) return
    inorder(node.left)
    println(node.value)
    inorder(node.right)
}
```

### 🔗 Graph (Adjacency List)

```kotlin
val graph = mutableMapOf<Int, MutableList<Int>>()

fun addEdge(u: Int, v: Int) {
    graph.getOrPut(u) { mutableListOf() }.add(v)
    graph.getOrPut(v) { mutableListOf() }.add(u) // if undirected
}

fun dfs(node: Int, visited: MutableSet<Int>) {
    if (node in visited) return
    visited.add(node)
    println(node)
    for (neighbor in graph[node] ?: emptyList()) {
        dfs(neighbor, visited)
    }
}
```

### 💡 Tips

* Use `Map<Int, MutableList<Int>>` for adjacency list
* For Graphs: `ArrayDeque` for BFS, `MutableSet` for visited
* Use `PriorityQueue` for Dijkstra, custom comparators for A\*
* For Tree problems: Kotlin’s `null` handling is useful (`?.` and `?:`)
* For Tries: `HashMap<Char, TrieNode>` gives fast insert/search

---

## 🔗 References

* Kotlin Official Docs: [https://kotlinlang.org/api/latest/jvm/stdlib/](https://kotlinlang.org/api/latest/jvm/stdlib/)
* C++ STL Reference: [https://en.cppreference.com/](https://en.cppreference.com/)

**Happy DSA with Kotlin! 🚀**
