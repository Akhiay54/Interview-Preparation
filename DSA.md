# 🚀 DSA Patterns Cheat Sheet

An organized collection of **must-know Data Structures & Algorithm patterns**, sub-patterns, and **hand-picked hard-level problems** for each.

Practical reminders to help you identify and solve each pattern more effectively!

---

## 1️⃣ [Sliding Window](#-🧩-1.-Sliding-Window)

**When to use:**  
- Substrings, subarrays, or segments in arrays/strings.  
- Problems with “maximum/minimum/at most/exactly K” in a continuous range.

**Key Tricks:**  
- For **fixed-size windows**, move both pointers together.
- For **variable-size windows**, expand right, shrink left to restore constraints.
- Use hash maps or frequency counters for characters or distinct elements.

---

## 2️⃣ Two Pointers

**When to use:**  
- Sorted arrays/lists, pair sums, partitions, duplicates removal.

**Key Tricks:**  
- One pointer starts at the beginning, one at the end.
- Move inward based on problem condition (sum too big/small, etc.).
- Useful for in-place operations with O(1) extra space.

---

## 3️⃣ Fast & Slow Pointers

**When to use:**  
- Detecting cycles in linked lists, arrays.
- Finding starting point of the cycle.

**Key Tricks:**  
- Use `slow = slow.next` and `fast = fast.next.next`.
- If they meet, a cycle exists.
- Reset one pointer to head to find cycle entry point.

---

## 4️⃣ Binary Search

**When to use:**  
- Monotonic (sorted or conditionally increasing/decreasing) problems.
- Search space reduction, boundaries, or “first true/false” type problems.

**Key Tricks:**  
- Think: “Is this value possible?”
- Adjust mid carefully to avoid infinite loops.
- Use `low + (high - low) // 2` for overflow safety.

---

## 5️⃣ Backtracking

**When to use:**  
- Generate all valid combinations, permutations, or arrangements with constraints.

**Key Tricks:**  
- “Try → Recurse → Undo” structure.
- Prune early by checking constraints.
- Use visited flags or sets to avoid duplicates.

---

## 6️⃣ Dynamic Programming (DP)

### 6.1 1D DP

**When to use:**  
- Sequence-based problems (Fibonacci, stairs).

**Key Tricks:**  
- Define `dp[i]` as the optimal value at position `i`.
- Identify base cases and recurrence.

---

### 6.2 2D DP

**When to use:**  
- Matrix/grid problems, edit distance, unique paths.

**Key Tricks:**  
- Define `dp[i][j]` for each cell.
- Transitions come from neighbors (top, left, diagonal).

---

### 6.3 DP on Subsequences (LIS, LCS)

**When to use:**  
- Pick/not pick elements, subsequence matching.

**Key Tricks:**  
- Typical states: `dp[i][j]` for two sequences.
- Use memoization or tabulation to avoid redundant work.

---

### 6.4 DP on Trees

**When to use:**  
- Optimal subtrees, independent sets.

**Key Tricks:**  
- Do post-order DFS.
- Merge child results into parent’s DP state.

---

### 6.5 DP with Bitmasking

**When to use:**  
- Small sets (N ≤ 20), all subsets, TSP.

**Key Tricks:**  
- Use bits in an integer to represent state.
- `dp[mask][i]` → best result for `mask` ending at `i`.

---

### 6.6 DP + Knapsack

**When to use:**  
- Subset sums, resource allocation.

**Key Tricks:**  
- `dp[i][j]` → best value using first `i` items with capacity `j`.
- Decide: pick or skip current item.

---

### 6.7 DP + Monotonic Queue

**When to use:**  
- DP with sliding window optimization.

**Key Tricks:**  
- Maintain a deque to keep max/min values.
- Useful for large constraints that make normal DP too slow.

---

## 7️⃣ Greedy

**When to use:**  
- Local optimal choices lead to global optimal solution.

**Key Tricks:**  
- Prove why greedy works (no counterexamples).
- Sort input based on benefit/cost.
- Great for intervals, scheduling, resource allocation.

---

## 8️⃣ Bit Manipulation

**When to use:**  
- Subsets, unique elements, low-level tricks.

**Key Tricks:**  
- `n & (n - 1)` removes lowest set bit.
- XOR cancels out pairs.
- Use bitmask to generate all subsets.

---

## 9️⃣ Union Find / Disjoint Set

**When to use:**  
- Connectivity, dynamic components, detecting cycles.

**Key Tricks:**  
- Use path compression & union by rank.
- Ideal for “connected components” or checking redundant edges.

---

## 🔟 BFS & DFS

**When to use:**  
- Graph traversal, shortest unweighted paths, connected components.

**Key Tricks:**  
- BFS → shortest path, levels.
- DFS → explore deeply, backtracking.
- Always mark visited nodes!

---

## 1️⃣1️⃣ Shortest Paths (Dijkstra, Bellman-Ford, Floyd-Warshall)

**When to use:**  
- Weighted graphs, finding minimal cost paths.

**Key Tricks:**  
- Dijkstra: only for non-negative weights.
- Bellman-Ford: handles negative edges & detects cycles.
- Floyd-Warshall: all-pairs shortest path, dynamic programming on graphs.

---

## 1️⃣2️⃣ Topological Sort

**When to use:**  
- DAGs (tasks with dependencies).

**Key Tricks:**  
- Use Kahn’s Algorithm (BFS) or DFS post-order.
- If processed nodes < total → there’s a cycle.

---

## 1️⃣3️⃣ Trees & Tries

**When to use:**  
- Hierarchical data, prefix searches.

**Key Tricks:**  
- Tries → use hashmaps for children nodes.
- Binary tree DP → post-order traversal.
- For LCA → use depth and parent pointers.

---

## 1️⃣4️⃣ Heap / Priority Queue

**When to use:**  
- Find Kth largest/smallest, merge sorted lists.

**Key Tricks:**  
- Min-heap for smallest K elements.
- Max-heap for largest K elements.
- Heapsort: push all, pop all.

---

## 1️⃣5️⃣ Monotonic Stack / Queue

**When to use:**  
- Next greater/smaller element, histograms.

**Key Tricks:**  
- Maintain stack in increasing or decreasing order.
- Think left/right bounds for each element.

---

## 1️⃣6️⃣ Graphs (Bridges, Articulation Points)

**When to use:**  
- Network reliability, critical connections.

**Key Tricks:**  
- Use Tarjan’s Algorithm: DFS + low-link values.
- Bridges → removing edge increases components.
- Articulation point → removing node increases components.

---

## 1️⃣7️⃣ Math & Number Theory

**When to use:**  
- Primes, modular arithmetic, and divisibility.

**Key Tricks:**  
- Sieve of Eratosthenes for primes.
- Euclidean algorithm for GCD.
- Use modulo for large computations.

---

## 1️⃣8️⃣ Combinatorics / Permutations

**When to use:**  
- Count/generate unique permutations, combinations.

**Key Tricks:**  
- Backtrack with used/visited flags.
- Sort to handle duplicates.
- Use factorials for counting.

---

## 1️⃣9️⃣ Misc Puzzles & Probability

**When to use:**  
- Random sampling, reservoir sampling, probability simulations.

**Key Tricks:**  
- Reservoir sampling for unknown-size streams.
- Use prefix sums for weighted randomness.
- Always double-check uniformity of random selection.


---

## 🧩 1. Sliding Window

| Problem | Link |
|---------|------|
| Sliding Window Maximum | [LC 239](https://leetcode.com/problems/sliding-window-maximum/) |
| Minimum Window Substring | [LC 76](https://leetcode.com/problems/minimum-window-substring/) |
| Longest Substring with At Most K Distinct Characters | [LC 340](https://leetcode.com/problems/longest-substring-with-at-most-k-distinct-characters/) |
| Longest Substring Without Repeating Characters | [LC 3](https://leetcode.com/problems/longest-substring-without-repeating-characters/) |
| Permutation in String | [LC 567](https://leetcode.com/problems/permutation-in-string/) |
| Count Number of Nice Subarrays | [LC 1248](https://leetcode.com/problems/count-number-of-nice-subarrays/) |

---

## 🔗 2. Two Pointers

| Problem | Link |
|---------|------|
| Trapping Rain Water | [LC 42](https://leetcode.com/problems/trapping-rain-water/) |
| 4Sum | [LC 18](https://leetcode.com/problems/4sum/) |
| Smallest Range Covering K Lists | [LC 632](https://leetcode.com/problems/smallest-range-covering-elements-from-k-lists/) |
| Remove Nth Node from End | [LC 19](https://leetcode.com/problems/remove-nth-node-from-end-of-list/) |
| Longest Palindromic Substring | [LC 5](https://leetcode.com/problems/longest-palindromic-substring/) |

---

## 🏃 3. Fast & Slow Pointers (Cycle)

| Problem | Link |
|---------|------|
| Linked List Cycle II | [LC 142](https://leetcode.com/problems/linked-list-cycle-ii/) |
| Find Duplicate Number | [LC 287](https://leetcode.com/problems/find-the-duplicate-number/) |
| Circular Array Loop | [LC 457](https://leetcode.com/problems/circular-array-loop/) |
| Happy Number | [LC 202](https://leetcode.com/problems/happy-number/) |
| Linked List Cycle | [LC 141](https://leetcode.com/problems/linked-list-cycle/) |

---

## 🔢 4. Binary Search

| Problem | Link |
|---------|------|
| Median of Two Sorted Arrays | [LC 4](https://leetcode.com/problems/median-of-two-sorted-arrays/) |
| Kth Smallest Number in Multiplication Table | [LC 668](https://leetcode.com/problems/kth-smallest-number-in-multiplication-table/) |
| Aggressive Cows | [GFG](https://practice.geeksforgeeks.org/problems/aggressive-cows/1) |
| Split Array Largest Sum | [LC 410](https://leetcode.com/problems/split-array-largest-sum/) |
| Search in Rotated Sorted Array II | [LC 81](https://leetcode.com/problems/search-in-rotated-sorted-array-ii/) |

---

## 🧵 5. Backtracking

| Problem | Link |
|---------|------|
| N-Queens | [LC 51](https://leetcode.com/problems/n-queens/) |
| Sudoku Solver | [LC 37](https://leetcode.com/problems/sudoku-solver/) |
| Word Search II | [LC 212](https://leetcode.com/problems/word-search-ii/) |
| Expression Add Operators | [LC 282](https://leetcode.com/problems/expression-add-operators/) |
| Palindrome Partitioning II | [LC 132](https://leetcode.com/problems/palindrome-partitioning-ii/) |
| Restore IP Addresses | [LC 93](https://leetcode.com/problems/restore-ip-addresses/) |

---

## 🗂️ 6. Dynamic Programming

### ✅ 1D DP
| Problem | Link |
|---------|------|
| House Robber II | [LC 213](https://leetcode.com/problems/house-robber-ii/) |
| Maximum Product Subarray | [LC 152](https://leetcode.com/problems/maximum-product-subarray/) |
| Jump Game II | [LC 45](https://leetcode.com/problems/jump-game-ii/) |

### ✅ 2D DP
| Problem | Link |
|---------|------|
| Edit Distance | [LC 72](https://leetcode.com/problems/edit-distance/) |
| Unique Paths III | [LC 980](https://leetcode.com/problems/unique-paths-iii/) |
| Longest Increasing Path in Matrix | [LC 329](https://leetcode.com/problems/longest-increasing-path-in-a-matrix/) |
| Cherry Pickup II | [LC 1463](https://leetcode.com/problems/cherry-pickup-ii/) |

### ✅ DP on Subsequences
| Problem | Link |
|---------|------|
| Longest Increasing Subsequence | [LC 300](https://leetcode.com/problems/longest-increasing-subsequence/) |
| Distinct Subsequences | [LC 115](https://leetcode.com/problems/distinct-subsequences/) |
| Palindrome Partitioning II | [LC 132](https://leetcode.com/problems/palindrome-partitioning-ii/) |
| Longest Palindromic Subsequence | [LC 516](https://leetcode.com/problems/longest-palindromic-subsequence/) |

### ✅ DP on Trees
| Problem | Link |
|---------|------|
| House Robber III | [LC 337](https://leetcode.com/problems/house-robber-iii/) |
| Binary Tree Cameras | [LC 968](https://leetcode.com/problems/binary-tree-cameras/) |
| Smallest Sufficient Team | [LC 1125](https://leetcode.com/problems/smallest-sufficient-team/) |

### ✅ DP with Bitmasking
| Problem | Link |
|---------|------|
| Shortest Superstring | [LC 943](https://leetcode.com/problems/find-the-shortest-superstring/) |
| Partition to K Equal Sum Subsets | [LC 698](https://leetcode.com/problems/partition-to-k-equal-sum-subsets/) |
| Traveling Salesman Problem (Classic) | - |

### ✅ DP + Knapsack
| Problem | Link |
|---------|------|
| Partition Equal Subset Sum | [LC 416](https://leetcode.com/problems/partition-equal-subset-sum/) |
| Target Sum | [LC 494](https://leetcode.com/problems/target-sum/) |
| Last Stone Weight II | [LC 1049](https://leetcode.com/problems/last-stone-weight-ii/) |

### ✅ DP + Monotonic Queue
| Problem | Link |
|---------|------|
| Jump Game VI | [LC 1696](https://leetcode.com/problems/jump-game-vi/) |
| Sliding Window Maximum | [LC 239](https://leetcode.com/problems/sliding-window-maximum/) |
| Maximum Number of Robots Within Budget | [LC 2398](https://leetcode.com/problems/maximum-number-of-robots-within-budget/) |

---

## ⚡ 7. Greedy

| Problem | Link |
|---------|------|
| Candy | [LC 135](https://leetcode.com/problems/candy/) |
| Minimum Taps to Water a Garden | [LC 1326](https://leetcode.com/problems/minimum-number-of-taps-to-open-to-water-a-garden/) |
| Minimum Cost to Hire K Workers | [LC 857](https://leetcode.com/problems/minimum-cost-to-hire-k-workers/) |
| Gas Station | [LC 134](https://leetcode.com/problems/gas-station/) |
| Jump Game | [LC 55](https://leetcode.com/problems/jump-game/) |

---

## 🔗 8. Bit Manipulation

| Problem | Link |
|---------|------|
| Single Number II | [LC 137](https://leetcode.com/problems/single-number-ii/) |
| Maximum Product of Word Lengths | [LC 318](https://leetcode.com/problems/maximum-product-of-word-lengths/) |
| Bitwise AND of Numbers Range | [LC 201](https://leetcode.com/problems/bitwise-and-of-numbers-range/) |
| Maximum XOR of Two Numbers | [LC 421](https://leetcode.com/problems/maximum-xor-of-two-numbers-in-an-array/) |

---

## 🌐 9. Union Find

| Problem | Link |
|---------|------|
| Redundant Connection II | [LC 685](https://leetcode.com/problems/redundant-connection-ii/) |
| Satisfiability of Equality Equations | [LC 990](https://leetcode.com/problems/satisfiability-of-equality-equations/) |
| Longest Consecutive Sequence | [LC 128](https://leetcode.com/problems/longest-consecutive-sequence/) |

---

## 🌊 10. BFS & DFS

| Problem | Link |
|---------|------|
| Word Ladder II | [LC 126](https://leetcode.com/problems/word-ladder-ii/) |
| Pacific Atlantic Water Flow | [LC 417](https://leetcode.com/problems/pacific-atlantic-water-flow/) |
| Number of Islands II | [LC 305](https://leetcode.com/problems/number-of-islands-ii/) |
| Surrounded Regions | [LC 130](https://leetcode.com/problems/surrounded-regions/) |
| Cut Off Trees for Golf Event | [LC 675](https://leetcode.com/problems/cut-off-trees-for-golf-event/) |
| Shortest Bridge | [LC 934](https://leetcode.com/problems/shortest-bridge/) |

---

## 🗺️ 11. Shortest Paths (Dijkstra, Bellman-Ford, Floyd-Warshall)

| Problem | Link |
|---------|------|
| Network Delay Time | [LC 743](https://leetcode.com/problems/network-delay-time/) |
| Cheapest Flights Within K Stops | [LC 787](https://leetcode.com/problems/cheapest-flights-within-k-stops/) |
| Path with Maximum Probability | [LC 1514](https://leetcode.com/problems/path-with-maximum-probability/) |
| Find the City With the Smallest Number of Neighbors | [LC 1334](https://leetcode.com/problems/find-the-city-with-the-smallest-number-of-neighbors-at-a-threshold-distance/) |
| Negative Weight Cycle | [GFG Practice](https://practice.geeksforgeeks.org/problems/negative-weight-cycle3504/1) |
| Floyd-Warshall (All Pairs Shortest Path) | Classic Implementation |

---

## 🔝 12. Topological Sort

| Problem | Link |
|---------|------|
| Course Schedule II | [LC 210](https://leetcode.com/problems/course-schedule-ii/) |
| Alien Dictionary | [LC 269](https://leetcode.com/problems/alien-dictionary/) |
| Minimum Height Trees | [LC 310](https://leetcode.com/problems/minimum-height-trees/) |
| Sequence Reconstruction | [LC 444](https://leetcode.com/problems/sequence-reconstruction/) |
| Parallel Courses II | [LC 1494](https://leetcode.com/problems/parallel-courses-ii/) |

---

## 🌲 13. Trees & Tries

| Problem | Link |
|---------|------|
| Word Search II | [LC 212](https://leetcode.com/problems/word-search-ii/) |
| Design Search Autocomplete System | [LC 642](https://leetcode.com/problems/design-search-autocomplete-system/) |
| Replace Words | [LC 648](https://leetcode.com/problems/replace-words/) |
| Concatenated Words | [LC 472](https://leetcode.com/problems/concatenated-words/) |
| Palindrome Pairs | [LC 336](https://leetcode.com/problems/palindrome-pairs/) |

---

## 🧺 14. Heap / Priority Queue

| Problem | Link |
|---------|------|
| Find Median from Data Stream | [LC 295](https://leetcode.com/problems/find-median-from-data-stream/) |
| IPO | [LC 502](https://leetcode.com/problems/ipo/) |
| Smallest Range Covering K Lists | [LC 632](https://leetcode.com/problems/smallest-range-covering-elements-from-k-lists/) |
| Merge K Sorted Lists | [LC 23](https://leetcode.com/problems/merge-k-sorted-lists/) |
| Minimum Cost to Connect Sticks | [LC 1167](https://leetcode.com/problems/minimum-cost-to-connect-sticks/) |

---

## 📏 15. Monotonic Stack / Queue

| Problem | Link |
|---------|------|
| Trapping Rain Water | [LC 42](https://leetcode.com/problems/trapping-rain-water/) |
| Largest Rectangle in Histogram | [LC 84](https://leetcode.com/problems/largest-rectangle-in-histogram/) |
| Maximal Rectangle | [LC 85](https://leetcode.com/problems/maximal-rectangle/) |
| Daily Temperatures | [LC 739](https://leetcode.com/problems/daily-temperatures/) |
| Sum of Subarray Minimums | [LC 907](https://leetcode.com/problems/sum-of-subarray-minimums/) |

---

## 🔗 16. Graph Bridges & Articulation Points

| Problem | Link |
|---------|------|
| Critical Connections in a Network | [LC 1192](https://leetcode.com/problems/critical-connections-in-a-network/) |
| Articulation Point | Classic |
| Bridges in Graph | Classic |
| Redundant Connection | [LC 684](https://leetcode.com/problems/redundant-connection/) |

---

## 🧮 17. Math & Number Theory

| Problem | Link |
|---------|------|
| Greatest Common Divisor Traversal | [LC 2709](https://leetcode.com/problems/greatest-common-divisor-traversal/) |
| Count Primes | [LC 204](https://leetcode.com/problems/count-primes/) |
| Power of Three | [LC 326](https://leetcode.com/problems/power-of-three/) |
| Multiply Strings | [LC 43](https://leetcode.com/problems/multiply-strings/) |
| Basic Calculator II | [LC 227](https://leetcode.com/problems/basic-calculator-ii/) |

---

## 🔢 18. Combinatorics / Permutations

| Problem | Link |
|---------|------|
| Permutations II | [LC 47](https://leetcode.com/problems/permutations-ii/) |
| Combination Sum II | [LC 40](https://leetcode.com/problems/combination-sum-ii/) |
| Subsets II | [LC 90](https://leetcode.com/problems/subsets-ii/) |
| Count Vowels Permutation | [LC 1220](https://leetcode.com/problems/count-vowels-permutation/) |
| Beautiful Arrangement | [LC 526](https://leetcode.com/problems/beautiful-arrangement/) |

---

## 🎲 19. Classic Puzzles & Probability

| Problem | Link |
|---------|------|
| 100 Doors Puzzle | Classic |
| Random Pick with Weight | [LC 528](https://leetcode.com/problems/random-pick-with-weight/) |
| Random Point in Non-overlapping Rectangles | [LC 497](https://leetcode.com/problems/random-point-in-non-overlapping-rectangles/) |
| Random Pick Index | [LC 398](https://leetcode.com/problems/random-pick-index/) |
| Find Median from Data Stream | [LC 295](https://leetcode.com/problems/find-median-from-data-stream/) |

---

## ✅ That’s it!  
You now have a **full, extensive DSA Patterns README** covering:
- Patterns ✅
- Sub-patterns ✅
- Hard-level practice ✅

Happy coding! 🚀


---
