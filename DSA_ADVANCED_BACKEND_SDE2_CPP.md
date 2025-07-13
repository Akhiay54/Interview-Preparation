# Advanced DSA for Backend SDE2 (C++)
*Comprehensive guide for experienced backend developers (3+ years) targeting SDE2 positions*

---

## Table of Contents
1. Advanced Data Structures
2. Graph Algorithms
3. Advanced Dynamic Programming
4. String Algorithms
5. Concurrency & Parallelism Basics
6. Real-World Backend DSA Problems
7. Practice Problems

---

## 1. Advanced Data Structures

### Trie
```cpp
struct TrieNode {
    TrieNode* children[26] = {};
    bool isEnd = false;
};

class Trie {
public:
    TrieNode* root = new TrieNode();
    void insert(const string& word) {
        TrieNode* node = root;
        for (char c : word) {
            int idx = c - 'a';
            if (!node->children[idx]) node->children[idx] = new TrieNode();
            node = node->children[idx];
        }
        node->isEnd = true;
    }
    bool search(const string& word) {
        TrieNode* node = root;
        for (char c : word) {
            int idx = c - 'a';
            if (!node->children[idx]) return false;
            node = node->children[idx];
        }
        return node->isEnd;
    }
};
```

### Segment Tree
```cpp
class SegmentTree {
    vector<int> tree, arr;
    int n;
public:
    SegmentTree(const vector<int>& input) : arr(input), n(input.size()) {
        tree.resize(4 * n);
        build(1, 0, n - 1);
    }
    void build(int node, int l, int r) {
        if (l == r) tree[node] = arr[l];
        else {
            int m = (l + r) / 2;
            build(2 * node, l, m);
            build(2 * node + 1, m + 1, r);
            tree[node] = tree[2 * node] + tree[2 * node + 1];
        }
    }
    int query(int node, int l, int r, int ql, int qr) {
        if (qr < l || ql > r) return 0;
        if (ql <= l && r <= qr) return tree[node];
        int m = (l + r) / 2;
        return query(2 * node, l, m, ql, qr) + query(2 * node + 1, m + 1, r, ql, qr);
    }
};
```

### Fenwick Tree (Binary Indexed Tree)
```cpp
class FenwickTree {
    vector<int> bit;
    int n;
public:
    FenwickTree(int size) : n(size) { bit.assign(n + 1, 0); }
    void update(int idx, int delta) {
        for (++idx; idx <= n; idx += idx & -idx) bit[idx] += delta;
    }
    int query(int idx) {
        int res = 0;
        for (++idx; idx > 0; idx -= idx & -idx) res += bit[idx];
        return res;
    }
};
```

### LRU Cache
```cpp
class LRUCache {
    int cap;
    list<pair<int, int>> dq;
    unordered_map<int, list<pair<int, int>>::iterator> mp;
public:
    LRUCache(int capacity) : cap(capacity) {}
    int get(int key) {
        if (mp.find(key) == mp.end()) return -1;
        dq.splice(dq.begin(), dq, mp[key]);
        return mp[key]->second;
    }
    void put(int key, int value) {
        if (mp.find(key) != mp.end()) dq.erase(mp[key]);
        dq.push_front({key, value});
        mp[key] = dq.begin();
        if (dq.size() > cap) {
            mp.erase(dq.back().first);
            dq.pop_back();
        }
    }
};
```

### LFU Cache
```cpp
class LFUCache {
    int capacity;
    int minFreq;
    unordered_map<int, pair<int, int>> keyToValFreq; // key -> {value, frequency}
    unordered_map<int, list<int>> freqToKeys; // frequency -> list of keys
    unordered_map<int, list<int>::iterator> keyToIter; // key -> iterator in freqToKeys list
    
public:
    LFUCache(int capacity) : capacity(capacity), minFreq(0) {}
    
    int get(int key) {
        if (keyToValFreq.find(key) == keyToValFreq.end()) return -1;
        
        int freq = keyToValFreq[key].second;
        freqToKeys[freq].erase(keyToIter[key]);
        
        if (freqToKeys[freq].empty()) {
            freqToKeys.erase(freq);
            if (minFreq == freq) minFreq++;
        }
        
        freq++;
        keyToValFreq[key].second = freq;
        freqToKeys[freq].push_front(key);
        keyToIter[key] = freqToKeys[freq].begin();
        
        return keyToValFreq[key].first;
    }
    
    void put(int key, int value) {
        if (capacity <= 0) return;
        
        if (get(key) != -1) {
            keyToValFreq[key].first = value;
            return;
        }
        
        if (keyToValFreq.size() >= capacity) {
            int keyToRemove = freqToKeys[minFreq].back();
            freqToKeys[minFreq].pop_back();
            keyToValFreq.erase(keyToRemove);
            keyToIter.erase(keyToRemove);
        }
        
        keyToValFreq[key] = {value, 1};
        freqToKeys[1].push_front(key);
        keyToIter[key] = freqToKeys[1].begin();
        minFreq = 1;
    }
};
```

---

## 2. Graph Algorithms

### Union-Find (Disjoint Set)
```cpp
struct DSU {
    vector<int> parent, rank;
    DSU(int n) : parent(n), rank(n, 0) { iota(parent.begin(), parent.end(), 0); }
    int find(int x) { return parent[x] == x ? x : parent[x] = find(parent[x]); }
    void unite(int x, int y) {
        x = find(x); y = find(y);
        if (x == y) return;
        if (rank[x] < rank[y]) swap(x, y);
        parent[y] = x;
        if (rank[x] == rank[y]) rank[x]++;
    }
};
```

### Tarjan's SCC
```cpp
class TarjanSCC {
    vector<vector<int>> adj;
    vector<int> disc, low, stack;
    vector<bool> inStack;
    int time;
    
public:
    TarjanSCC(int n) : adj(n), disc(n, -1), low(n), inStack(n, false), time(0) {}
    
    void addEdge(int u, int v) { adj[u].push_back(v); }
    
    void dfs(int u, vector<vector<int>>& sccs) {
        disc[u] = low[u] = time++;
        stack.push_back(u);
        inStack[u] = true;
        
        for (int v : adj[u]) {
            if (disc[v] == -1) {
                dfs(v, sccs);
                low[u] = min(low[u], low[v]);
            } else if (inStack[v]) {
                low[u] = min(low[u], disc[v]);
            }
        }
        
        if (low[u] == disc[u]) {
            vector<int> scc;
            int v;
            do {
                v = stack.back(); stack.pop_back();
                inStack[v] = false;
                scc.push_back(v);
            } while (v != u);
            sccs.push_back(scc);
        }
    }
    
    vector<vector<int>> findSCCs() {
        vector<vector<int>> sccs;
        for (int i = 0; i < adj.size(); i++) {
            if (disc[i] == -1) dfs(i, sccs);
        }
        return sccs;
    }
};
```

### Dijkstra's Algorithm
```cpp
vector<int> dijkstra(int n, vector<vector<pair<int, int>>>& adj, int src) {
    vector<int> dist(n, INT_MAX); dist[src] = 0;
    priority_queue<pair<int, int>, vector<pair<int, int>>, greater<>> pq;
    pq.push({0, src});
    while (!pq.empty()) {
        auto [d, u] = pq.top(); pq.pop();
        if (d > dist[u]) continue;
        for (auto& [v, w] : adj[u]) {
            if (dist[v] > d + w) {
                dist[v] = d + w;
                pq.push({dist[v], v});
            }
        }
    }
    return dist;
}
```

### A* Search
```cpp
struct Node {
    int x, y, g, h, f;
    Node* parent;
    Node(int x, int y, int g, int h, Node* parent = nullptr) 
        : x(x), y(y), g(g), h(h), f(g + h), parent(parent) {}
    bool operator>(const Node& other) const { return f > other.f; }
};

class AStar {
    vector<vector<int>> grid;
    int rows, cols;
    
    int heuristic(int x1, int y1, int x2, int y2) {
        return abs(x1 - x2) + abs(y1 - y2); // Manhattan distance
    }
    
    bool isValid(int x, int y) {
        return x >= 0 && x < rows && y >= 0 && y < cols && grid[x][y] != 1;
    }
    
public:
    AStar(vector<vector<int>>& grid) : grid(grid), rows(grid.size()), cols(grid[0].size()) {}
    
    vector<pair<int, int>> findPath(int startX, int startY, int endX, int endY) {
        priority_queue<Node, vector<Node>, greater<Node>> pq;
        vector<vector<bool>> visited(rows, vector<bool>(cols, false));
        
        Node* start = new Node(startX, startY, 0, heuristic(startX, startY, endX, endY));
        pq.push(*start);
        
        while (!pq.empty()) {
            Node current = pq.top(); pq.pop();
            
            if (visited[current.x][current.y]) continue;
            visited[current.x][current.y] = true;
            
            if (current.x == endX && current.y == endY) {
                // Reconstruct path
                vector<pair<int, int>> path;
                Node* node = &current;
                while (node) {
                    path.push_back({node->x, node->y});
                    node = node->parent;
                }
                reverse(path.begin(), path.end());
                return path;
            }
            
            // Check all 4 directions
            int dx[] = {-1, 0, 1, 0};
            int dy[] = {0, 1, 0, -1};
            
            for (int i = 0; i < 4; i++) {
                int newX = current.x + dx[i];
                int newY = current.y + dy[i];
                
                if (isValid(newX, newY) && !visited[newX][newY]) {
                    int newG = current.g + 1;
                    int newH = heuristic(newX, newY, endX, endY);
                    Node* newNode = new Node(newX, newY, newG, newH, &current);
                    pq.push(*newNode);
                }
            }
        }
        
        return {}; // No path found
    }
};
```

### Edmonds-Karp (Max Flow)
```cpp
class EdmondsKarp {
    vector<vector<int>> capacity, flow;
    vector<vector<int>> adj;
    int n;
    
public:
    EdmondsKarp(int n) : n(n) {
        capacity = vector<vector<int>>(n, vector<int>(n, 0));
        flow = vector<vector<int>>(n, vector<int>(n, 0));
        adj = vector<vector<int>>(n);
    }
    
    void addEdge(int u, int v, int cap) {
        capacity[u][v] = cap;
        adj[u].push_back(v);
        adj[v].push_back(u);
    }
    
    int bfs(int s, int t, vector<int>& parent) {
        fill(parent.begin(), parent.end(), -1);
        parent[s] = -2;
        queue<pair<int, int>> q;
        q.push({s, INT_MAX});
        
        while (!q.empty()) {
            int u = q.front().first;
            int flow = q.front().second;
            q.pop();
            
            for (int v : adj[u]) {
                if (parent[v] == -1 && capacity[u][v] - this->flow[u][v] > 0) {
                    parent[v] = u;
                    int newFlow = min(flow, capacity[u][v] - this->flow[u][v]);
                    if (v == t) return newFlow;
                    q.push({v, newFlow});
                }
            }
        }
        return 0;
    }
    
    int maxFlow(int s, int t) {
        vector<int> parent(n);
        int maxFlow = 0;
        
        while (int flow = bfs(s, t, parent)) {
            maxFlow += flow;
            int v = t;
            while (v != s) {
                int u = parent[v];
                this->flow[u][v] += flow;
                this->flow[v][u] -= flow;
                v = u;
            }
        }
        return maxFlow;
    }
};
```

---

## 3. Advanced Dynamic Programming

### DP on Trees
```cpp
struct TreeNode {
    int val;
    vector<TreeNode*> children;
    TreeNode(int x) : val(x) {}
};

class TreeDP {
public:
    // Largest Independent Set in a tree
    pair<int, int> largestIndependentSet(TreeNode* root) {
        if (!root) return {0, 0};
        
        int include = root->val; // Include current node
        int exclude = 0;         // Exclude current node
        
        for (TreeNode* child : root->children) {
            auto [inc, exc] = largestIndependentSet(child);
            include += exc;  // If we include root, exclude children
            exclude += max(inc, exc); // If we exclude root, take max of include/exclude for children
        }
        
        return {include, exclude};
    }
    
    // Longest path in a tree (diameter)
    int diameter = 0;
    int longestPath(TreeNode* root) {
        if (!root) return 0;
        
        int max1 = 0, max2 = 0;
        for (TreeNode* child : root->children) {
            int path = longestPath(child);
            if (path > max1) {
                max2 = max1;
                max1 = path;
            } else if (path > max2) {
                max2 = path;
            }
        }
        
        diameter = max(diameter, max1 + max2);
        return max1 + 1;
    }
    
    // Minimum vertex cover in a tree
    pair<int, int> minVertexCover(TreeNode* root) {
        if (!root) return {0, 0};
        if (root->children.empty()) return {1, 0};
        
        int include = 1;  // Include current node
        int exclude = 0;  // Exclude current node
        
        for (TreeNode* child : root->children) {
            auto [inc, exc] = minVertexCover(child);
            include += min(inc, exc);  // If we include root, take min for children
            exclude += inc;            // If we exclude root, must include children
        }
        
        return {include, exclude};
    }
};
```

### DP with Bitmask
```cpp
class BitmaskDP {
public:
    // Traveling Salesman Problem (TSP)
    int tsp(vector<vector<int>>& dist) {
        int n = dist.size();
        vector<vector<int>> dp(1 << n, vector<int>(n, INT_MAX));
        
        // Base case: starting from city 0
        dp[1][0] = 0;
        
        for (int mask = 1; mask < (1 << n); mask++) {
            for (int i = 0; i < n; i++) {
                if (!(mask & (1 << i))) continue;
                
                for (int j = 0; j < n; j++) {
                    if (mask & (1 << j)) continue;
                    
                    int newMask = mask | (1 << j);
                    dp[newMask][j] = min(dp[newMask][j], dp[mask][i] + dist[i][j]);
                }
            }
        }
        
        // Return to starting city
        int result = INT_MAX;
        for (int i = 1; i < n; i++) {
            result = min(result, dp[(1 << n) - 1][i] + dist[i][0]);
        }
        return result;
    }
    
    // Assignment Problem (Hungarian Algorithm with bitmask)
    int assignmentProblem(vector<vector<int>>& cost) {
        int n = cost.size();
        vector<int> dp(1 << n, INT_MAX);
        dp[0] = 0;
        
        for (int mask = 0; mask < (1 << n); mask++) {
            int tasks = __builtin_popcount(mask);
            for (int i = 0; i < n; i++) {
                if (!(mask & (1 << i))) {
                    int newMask = mask | (1 << i);
                    dp[newMask] = min(dp[newMask], dp[mask] + cost[tasks][i]);
                }
            }
        }
        return dp[(1 << n) - 1];
    }
    
    // Subset Sum with bitmask
    bool subsetSum(vector<int>& nums, int target) {
        int n = nums.size();
        vector<bool> dp(1 << n, false);
        dp[0] = true;
        
        for (int mask = 0; mask < (1 << n); mask++) {
            if (!dp[mask]) continue;
            
            int sum = 0;
            for (int i = 0; i < n; i++) {
                if (mask & (1 << i)) sum += nums[i];
            }
            
            if (sum == target) return true;
            
            for (int i = 0; i < n; i++) {
                if (!(mask & (1 << i))) {
                    dp[mask | (1 << i)] = true;
                }
            }
        }
        return false;
    }
};
```

### DP Optimizations
```cpp
class DPOptimizations {
public:
    // Convex Hull Trick (for optimizing dp[i] = min(dp[j] + cost(j, i)))
    class ConvexHullTrick {
        struct Line {
            long long m, b;
            Line(long long m, long long b) : m(m), b(b) {}
            long long eval(long long x) const { return m * x + b; }
        };
        
        vector<Line> hull;
        
        bool bad(const Line& l1, const Line& l2, const Line& l3) {
            return (l3.b - l1.b) * (l1.m - l2.m) <= (l2.b - l1.b) * (l1.m - l3.m);
        }
        
    public:
        void add(long long m, long long b) {
            Line l(m, b);
            while (hull.size() >= 2 && bad(hull[hull.size() - 2], hull.back(), l)) {
                hull.pop_back();
            }
            hull.push_back(l);
        }
        
        long long query(long long x) {
            int l = 0, r = hull.size() - 1;
            while (l < r) {
                int mid = (l + r) / 2;
                if (hull[mid].eval(x) < hull[mid + 1].eval(x)) {
                    r = mid;
                } else {
                    l = mid + 1;
                }
            }
            return hull[l].eval(x);
        }
    };
    
    // Monotonic Queue Optimization (for sliding window min/max)
    vector<int> slidingWindowMin(vector<int>& arr, int k) {
        deque<int> dq;
        vector<int> result;
        
        for (int i = 0; i < arr.size(); i++) {
            // Remove elements outside window
            while (!dq.empty() && dq.front() <= i - k) {
                dq.pop_front();
            }
            
            // Remove larger elements from back
            while (!dq.empty() && arr[dq.back()] >= arr[i]) {
                dq.pop_back();
            }
            
            dq.push_back(i);
            
            if (i >= k - 1) {
                result.push_back(arr[dq.front()]);
            }
        }
        return result;
    }
    
    // Divide and Conquer Optimization (for 1D1D DP)
    void solve1D1D(int n, vector<long long>& dp, vector<long long>& cost) {
        function<void(int, int, int, int)> solve = [&](int l, int r, int optL, int optR) {
            if (l > r) return;
            
            int mid = (l + r) / 2;
            pair<long long, int> best = {LLONG_MAX, -1};
            
            for (int k = optL; k <= min(mid, optR); k++) {
                best = min(best, {dp[k] + cost[k] * (mid - k), k});
            }
            
            dp[mid] = best.first;
            int opt = best.second;
            
            solve(l, mid - 1, optL, opt);
            solve(mid + 1, r, opt, optR);
        };
        
        solve(0, n - 1, 0, n - 1);
    }
    
    // Knuth Optimization (for 2D DP with quadrangle inequality)
    int knuthOptimization(vector<vector<int>>& cost) {
        int n = cost.size();
        vector<vector<int>> dp(n, vector<int>(n, INT_MAX));
        vector<vector<int>> opt(n, vector<int>(n));
        
        for (int i = 0; i < n; i++) {
            dp[i][i] = 0;
            opt[i][i] = i;
        }
        
        for (int len = 2; len <= n; len++) {
            for (int i = 0; i <= n - len; i++) {
                int j = i + len - 1;
                for (int k = opt[i][j-1]; k <= min(j-1, opt[i+1][j]); k++) {
                    int val = dp[i][k] + dp[k+1][j] + cost[i][j];
                    if (val < dp[i][j]) {
                        dp[i][j] = val;
                        opt[i][j] = k;
                    }
                }
            }
        }
        return dp[0][n-1];
    }
};
```

---

## 4. String Algorithms

### KMP (Knuth-Morris-Pratt)
```cpp
vector<int> buildLPS(const string& pat) {
    int n = pat.size();
    vector<int> lps(n);
    for (int i = 1, len = 0; i < n;) {
        if (pat[i] == pat[len]) lps[i++] = ++len;
        else if (len) len = lps[len - 1];
        else lps[i++] = 0;
    }
    return lps;
}
```

### Rabin-Karp
```cpp
class RabinKarp {
    const int MOD = 1e9 + 7;
    const int BASE = 31;
    
public:
    // Find all occurrences of pattern in text
    vector<int> findPattern(const string& text, const string& pattern) {
        vector<int> occurrences;
        int n = text.length(), m = pattern.length();
        
        if (m > n) return occurrences;
        
        // Calculate hash of pattern
        long long patternHash = 0;
        for (char c : pattern) {
            patternHash = (patternHash * BASE + c - 'a') % MOD;
        }
        
        // Calculate hash of first window
        long long textHash = 0;
        long long power = 1;
        for (int i = 0; i < m; i++) {
            textHash = (textHash * BASE + text[i] - 'a') % MOD;
            if (i > 0) power = (power * BASE) % MOD;
        }
        
        // Check first window
        if (textHash == patternHash && text.substr(0, m) == pattern) {
            occurrences.push_back(0);
        }
        
        // Slide window and check
        for (int i = m; i < n; i++) {
            textHash = (textHash - (text[i - m] - 'a') * power % MOD + MOD) % MOD;
            textHash = (textHash * BASE + text[i] - 'a') % MOD;
            
            if (textHash == patternHash && text.substr(i - m + 1, m) == pattern) {
                occurrences.push_back(i - m + 1);
            }
        }
        
        return occurrences;
    }
    
    // Find longest common substring of two strings
    string longestCommonSubstring(const string& s1, const string& s2) {
        int n = s1.length(), m = s2.length();
        int low = 1, high = min(n, m);
        string result = "";
        
        while (low <= high) {
            int mid = (low + high) / 2;
            bool found = false;
            
            // Hash all substrings of length mid in s1
            unordered_set<long long> hashes;
            long long hash = 0, power = 1;
            
            for (int i = 0; i < mid; i++) {
                hash = (hash * BASE + s1[i] - 'a') % MOD;
                if (i > 0) power = (power * BASE) % MOD;
            }
            hashes.insert(hash);
            
            for (int i = mid; i < n; i++) {
                hash = (hash - (s1[i - mid] - 'a') * power % MOD + MOD) % MOD;
                hash = (hash * BASE + s1[i] - 'a') % MOD;
                hashes.insert(hash);
            }
            
            // Check if any substring of length mid in s2 matches
            hash = 0;
            for (int i = 0; i < mid; i++) {
                hash = (hash * BASE + s2[i] - 'a') % MOD;
            }
            
            if (hashes.count(hash)) {
                found = true;
                result = s2.substr(0, mid);
            }
            
            for (int i = mid; i < m; i++) {
                hash = (hash - (s2[i - mid] - 'a') * power % MOD + MOD) % MOD;
                hash = (hash * BASE + s2[i] - 'a') % MOD;
                if (hashes.count(hash)) {
                    found = true;
                    result = s2.substr(i - mid + 1, mid);
                }
            }
            
            if (found) low = mid + 1;
            else high = mid - 1;
        }
        
        return result;
    }
};
```

### Suffix Array
```cpp
class SuffixArray {
    string s;
    int n;
    vector<int> sa, lcp;
    
    void buildSuffixArray() {
        vector<int> c(n), cnt(n);
        sa.resize(n);
        
        // Initial sorting by first character
        for (int i = 0; i < n; i++) cnt[s[i]]++;
        for (int i = 1; i < 256; i++) cnt[i] += cnt[i-1];
        for (int i = n-1; i >= 0; i--) sa[--cnt[s[i]]] = i;
        
        c[sa[0]] = 0;
        int classes = 1;
        for (int i = 1; i < n; i++) {
            if (s[sa[i]] != s[sa[i-1]]) classes++;
            c[sa[i]] = classes - 1;
        }
        
        // Iterative doubling
        vector<int> pn(n), cn(n);
        for (int h = 0; (1 << h) < n; h++) {
            for (int i = 0; i < n; i++) {
                pn[i] = sa[i] - (1 << h);
                if (pn[i] < 0) pn[i] += n;
            }
            
            fill(cnt.begin(), cnt.begin() + classes, 0);
            for (int i = 0; i < n; i++) cnt[c[pn[i]]]++;
            for (int i = 1; i < classes; i++) cnt[i] += cnt[i-1];
            for (int i = n-1; i >= 0; i--) sa[--cnt[c[pn[i]]]] = pn[i];
            
            cn[sa[0]] = 0;
            classes = 1;
            for (int i = 1; i < n; i++) {
                pair<int, int> cur = {c[sa[i]], c[(sa[i] + (1 << h)) % n]};
                pair<int, int> prev = {c[sa[i-1]], c[(sa[i-1] + (1 << h)) % n]};
                if (cur != prev) classes++;
                cn[sa[i]] = classes - 1;
            }
            c.swap(cn);
        }
    }
    
    void buildLCP() {
        vector<int> rank(n);
        for (int i = 0; i < n; i++) rank[sa[i]] = i;
        
        lcp.resize(n);
        int k = 0;
        for (int i = 0; i < n; i++) {
            if (rank[i] == n - 1) {
                k = 0;
                continue;
            }
            int j = sa[rank[i] + 1];
            while (i + k < n && j + k < n && s[i + k] == s[j + k]) k++;
            lcp[rank[i]] = k;
            if (k) k--;
        }
    }
    
public:
    SuffixArray(const string& str) : s(str + '$'), n(s.length()) {
        buildSuffixArray();
        buildLCP();
    }
    
    // Find all occurrences of pattern
    vector<int> findPattern(const string& pattern) {
        vector<int> occurrences;
        int m = pattern.length();
        
        // Binary search for lower bound
        int l = 0, r = n - 1;
        while (l < r) {
            int mid = (l + r) / 2;
            if (s.substr(sa[mid], min(m, n - sa[mid])) < pattern) {
                l = mid + 1;
            } else {
                r = mid;
            }
        }
        int start = l;
        
        // Binary search for upper bound
        l = 0, r = n - 1;
        while (l < r) {
            int mid = (l + r + 1) / 2;
            if (s.substr(sa[mid], min(m, n - sa[mid])) <= pattern) {
                l = mid;
            } else {
                r = mid - 1;
            }
        }
        int end = l;
        
        for (int i = start; i <= end; i++) {
            occurrences.push_back(sa[i]);
        }
        
        return occurrences;
    }
    
    // Get longest common substring of two strings
    string longestCommonSubstring(const string& s1, const string& s2) {
        string combined = s1 + '#' + s2;
        SuffixArray sa(combined);
        
        int n1 = s1.length();
        string result = "";
        int maxLen = 0;
        
        for (int i = 0; i < sa.lcp.size() - 1; i++) {
            int pos1 = sa.sa[i], pos2 = sa.sa[i + 1];
            
            // Check if suffixes belong to different strings
            if ((pos1 < n1) != (pos2 < n1)) {
                if (sa.lcp[i] > maxLen) {
                    maxLen = sa.lcp[i];
                    result = combined.substr(sa.sa[i], maxLen);
                }
            }
        }
        
        return result;
    }
    
    const vector<int>& getSuffixArray() const { return sa; }
    const vector<int>& getLCP() const { return lcp; }
};
```

---

## 5. Concurrency & Parallelism Basics

### Thread Pool Implementation
```cpp
class ThreadPool {
    vector<thread> workers;
    queue<function<void()>> tasks;
    mutex queueMutex;
    condition_variable condition;
    bool stop;
    
public:
    ThreadPool(size_t threads) : stop(false) {
        for (size_t i = 0; i < threads; ++i) {
            workers.emplace_back([this] {
                while (true) {
                    function<void()> task;
                    {
                        unique_lock<mutex> lock(queueMutex);
                        condition.wait(lock, [this] { return stop || !tasks.empty(); });
                        if (stop && tasks.empty()) return;
                        task = move(tasks.front());
                        tasks.pop();
                    }
                    task();
                }
            });
        }
    }
    
    template<class F, class... Args>
    auto enqueue(F&& f, Args&&... args) -> future<typename result_of<F(Args...)>::type> {
        using return_type = typename result_of<F(Args...)>::type;
        
        auto task = make_shared<packaged_task<return_type()>>(bind(forward<F>(f), forward<Args>(args)...));
        future<return_type> res = task->get_future();
        
        {
            unique_lock<mutex> lock(queueMutex);
            if (stop) throw runtime_error("enqueue on stopped ThreadPool");
            tasks.emplace([task]() { (*task)(); });
        }
        condition.notify_one();
        return res;
    }
    
    ~ThreadPool() {
        {
            unique_lock<mutex> lock(queueMutex);
            stop = true;
        }
        condition.notify_all();
        for (thread& worker : workers) {
            worker.join();
        }
    }
};
```

### Producer-Consumer Pattern
```cpp
template<typename T>
class ProducerConsumer {
    queue<T> buffer;
    mutex mtx;
    condition_variable notFull, notEmpty;
    size_t maxSize;
    
public:
    ProducerConsumer(size_t size) : maxSize(size) {}
    
    void produce(T item) {
        unique_lock<mutex> lock(mtx);
        notFull.wait(lock, [this] { return buffer.size() < maxSize; });
        buffer.push(item);
        notEmpty.notify_one();
    }
    
    T consume() {
        unique_lock<mutex> lock(mtx);
        notEmpty.wait(lock, [this] { return !buffer.empty(); });
        T item = buffer.front();
        buffer.pop();
        notFull.notify_one();
        return item;
    }
};
```

### Atomic Operations and Lock-Free Data Structures
```cpp
class LockFreeStack {
    struct Node {
        int data;
        Node* next;
        Node(int d) : data(d), next(nullptr) {}
    };
    
    atomic<Node*> head;
    
public:
    void push(int data) {
        Node* new_node = new Node(data);
        Node* old_head = head.load();
        do {
            new_node->next = old_head;
        } while (!head.compare_exchange_weak(old_head, new_node));
    }
    
    bool pop(int& data) {
        Node* old_head = head.load();
        while (old_head) {
            if (head.compare_exchange_weak(old_head, old_head->next)) {
                data = old_head->data;
                delete old_head;
                return true;
            }
        }
        return false;
    }
};

// Atomic Counter
class AtomicCounter {
    atomic<int> value;
    
public:
    AtomicCounter() : value(0) {}
    
    void increment() { value.fetch_add(1); }
    void decrement() { value.fetch_sub(1); }
    int get() const { return value.load(); }
};
```

### Parallel Algorithms
```cpp
class ParallelAlgorithms {
public:
    // Parallel Merge Sort
    template<typename Iterator>
    void parallelMergeSort(Iterator begin, Iterator end, int depth = 0) {
        if (end - begin <= 1) return;
        
        Iterator mid = begin + (end - begin) / 2;
        
        if (depth < 3) { // Limit recursion depth for threads
            auto future = async(launch::async, [&]() {
                parallelMergeSort(begin, mid, depth + 1);
            });
            parallelMergeSort(mid, end, depth + 1);
            future.wait();
        } else {
            parallelMergeSort(begin, mid, depth + 1);
            parallelMergeSort(mid, end, depth + 1);
        }
        
        inplace_merge(begin, mid, end);
    }
    
    // Parallel Reduce
    template<typename Iterator, typename T, typename BinaryOp>
    T parallelReduce(Iterator begin, Iterator end, T init, BinaryOp op) {
        size_t size = end - begin;
        if (size <= 1000) {
            return accumulate(begin, end, init, op);
        }
        
        Iterator mid = begin + size / 2;
        auto future = async(launch::async, [&]() {
            return parallelReduce(begin, mid, init, op);
        });
        
        T right = parallelReduce(mid, end, init, op);
        T left = future.get();
        
        return op(left, right);
    }
};
```

---

## 6. Real-World Backend DSA Problems

### Consistent Hashing
```cpp
class ConsistentHashing {
    map<size_t, string> ring;
    hash<string> hasher;
    int virtualNodes;
    
public:
    ConsistentHashing(int virtualNodes = 150) : virtualNodes(virtualNodes) {}
    
    void addNode(const string& node) {
        for (int i = 0; i < virtualNodes; i++) {
            string virtualNode = node + "#" + to_string(i);
            size_t hash = hasher(virtualNode);
            ring[hash] = node;
        }
    }
    
    void removeNode(const string& node) {
        for (int i = 0; i < virtualNodes; i++) {
            string virtualNode = node + "#" + to_string(i);
            size_t hash = hasher(virtualNode);
            ring.erase(hash);
        }
    }
    
    string getNode(const string& key) {
        if (ring.empty()) return "";
        
        size_t hash = hasher(key);
        auto it = ring.lower_bound(hash);
        
        if (it == ring.end()) {
            it = ring.begin();
        }
        
        return it->second;
    }
};
```

### Rate Limiter (Token Bucket)
```cpp
class TokenBucketRateLimiter {
    double tokens;
    double maxTokens;
    double refillRate;
    chrono::steady_clock::time_point lastRefill;
    mutex mtx;
    
public:
    TokenBucketRateLimiter(double maxTokens, double refillRate) 
        : tokens(maxTokens), maxTokens(maxTokens), refillRate(refillRate), 
          lastRefill(chrono::steady_clock::now()) {}
    
    bool allowRequest(int tokensRequired = 1) {
        lock_guard<mutex> lock(mtx);
        
        auto now = chrono::steady_clock::now();
        double timePassed = chrono::duration<double>(now - lastRefill).count();
        tokens = min(maxTokens, tokens + timePassed * refillRate);
        lastRefill = now;
        
        if (tokens >= tokensRequired) {
            tokens -= tokensRequired;
            return true;
        }
        return false;
    }
};
```

### Sharded Counter
```cpp
class ShardedCounter {
    vector<atomic<int>> shards;
    int numShards;
    
public:
    ShardedCounter(int numShards = 16) : numShards(numShards) {
        shards.resize(numShards);
        for (auto& shard : shards) {
            shard.store(0);
        }
    }
    
    void increment() {
        int shard = hash<thread::id>{}(this_thread::get_id()) % numShards;
        shards[shard].fetch_add(1);
    }
    
    int getTotal() {
        int total = 0;
        for (auto& shard : shards) {
            total += shard.load();
        }
        return total;
    }
    
    void reset() {
        for (auto& shard : shards) {
            shard.store(0);
        }
    }
};
```

### Top-K Elements (Leaderboard)
```cpp
class TopKLeaderboard {
    priority_queue<pair<int, string>, vector<pair<int, string>>, greater<>> pq;
    unordered_map<string, int> scores;
    int k;
    
public:
    TopKLeaderboard(int k) : k(k) {}
    
    void addScore(const string& player, int score) {
        scores[player] = score;
        
        pq.push({score, player});
        if (pq.size() > k) {
            pq.pop();
        }
    }
    
    vector<pair<string, int>> getTopK() {
        vector<pair<string, int>> result;
        priority_queue<pair<int, string>, vector<pair<int, string>>, greater<>> temp = pq;
        
        while (!temp.empty()) {
            auto [score, player] = temp.top();
            temp.pop();
            result.push_back({player, score});
        }
        
        reverse(result.begin(), result.end());
        return result;
    }
    
    int getRank(const string& player) {
        if (scores.find(player) == scores.end()) return -1;
        
        int playerScore = scores[player];
        int rank = 1;
        
        for (const auto& [p, s] : scores) {
            if (s > playerScore) rank++;
        }
        
        return rank;
    }
};
```

### Sliding Window Rate Limiter
```cpp
class SlidingWindowRateLimiter {
    queue<chrono::steady_clock::time_point> requests;
    int maxRequests;
    chrono::seconds window;
    mutex mtx;
    
public:
    SlidingWindowRateLimiter(int maxRequests, chrono::seconds window) 
        : maxRequests(maxRequests), window(window) {}
    
    bool allowRequest() {
        lock_guard<mutex> lock(mtx);
        
        auto now = chrono::steady_clock::now();
        
        // Remove expired requests
        while (!requests.empty() && 
               now - requests.front() > window) {
            requests.pop();
        }
        
        if (requests.size() < maxRequests) {
            requests.push(now);
            return true;
        }
        
        return false;
    }
};
```

### Distributed Cache with Consistent Hashing
```cpp
class DistributedCache {
    ConsistentHashing hashRing;
    unordered_map<string, string> localCache;
    mutex cacheMtx;
    
public:
    DistributedCache() {
        // Add cache nodes
        hashRing.addNode("cache-node-1");
        hashRing.addNode("cache-node-2");
        hashRing.addNode("cache-node-3");
    }
    
    void put(const string& key, const string& value) {
        string node = hashRing.getNode(key);
        if (node == "cache-node-1") { // This node
            lock_guard<mutex> lock(cacheMtx);
            localCache[key] = value;
        } else {
            // Send to other node via network
            // Implementation depends on network layer
        }
    }
    
    string get(const string& key) {
        string node = hashRing.getNode(key);
        if (node == "cache-node-1") { // This node
            lock_guard<mutex> lock(cacheMtx);
            auto it = localCache.find(key);
            return it != localCache.end() ? it->second : "";
        } else {
            // Get from other node via network
            return "";
        }
    }
};
```

---

## 7. Practice Problems

1. Implement a thread-safe LRU cache in C++.
2. Design a consistent hashing ring for distributed cache nodes.
3. Find strongly connected components in a directed graph.
4. Implement a rate limiter using token bucket in C++.
5. Solve TSP using DP with bitmask.
6. Build a suffix array for a given string.
7. Implement a thread pool and demonstrate parallel map-reduce.
8. Design a sharded counter for high-concurrency updates.
9. Implement a real-time leaderboard (top-K) with frequent updates.
10. Use KMP to find all occurrences of a pattern in a large text.

*Hints for each problem: Think about thread safety, memory usage, and real-world constraints.*

---

*This document is designed for experienced backend developers and covers advanced DSA concepts in C++ relevant for SDE2 interviews and real-world backend systems.* 