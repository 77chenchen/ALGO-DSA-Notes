
# Bipartite Graph Check

> [!summary]
> Bipartite Graph Check 判断一个无向图能否被分成两个集合，使每条边都跨集合连接。  
> 等价地，它判断图能否被 2-coloring，且任意相邻点颜色不同。

Related: [[Graph Traversal]] | [[Cycle Detection in Directed Graphs]]

## 1. Definition

A **bipartite graph** is a graph whose vertices can be divided into two disjoint sets `A` and `B` such that:

- every edge connects a vertex in `A` to a vertex in `B`
- there is no edge connecting two vertices within the same set

Equivalent view:

> A graph is bipartite **iff** it can be colored using **two colors** so that adjacent vertices always have different colors.

## 2. Goal of Bipartite Graph Check

We want to determine whether a graph is bipartite.

That means checking whether we can assign two colors to all vertices such that:

- for every edge `(u, v)`, `color[u] != color[v]`

## 3. Core Idea: Two-Coloring

Use a color array:

- `0` = uncolored
- `1` = color 1
- `-1` = color 2

Rule:

- if `u` has color `1`, then all its neighbors must have color `-1`
- if `u` has color `-1`, then all its neighbors must have color `1`

If we ever find two adjacent vertices with the **same** color, the graph is **not bipartite**.

---

## 4. Why This Works

A bipartite graph separates vertices into two groups.

So if a graph is bipartite:

- all edges must go across the two groups
- equivalently, all adjacent vertices must have different colors

Therefore:

- **successful 2-coloring** -> bipartite
- **conflict during coloring** -> not bipartite

---

## 5. DFS Approach

### Idea

For each unvisited vertex:

1. assign it a color
2. DFS through its neighbors
3. give each uncolored neighbor the opposite color
4. if a neighbor already has the same color as the current vertex, return `false`

### DFS Template

```cpp
vector<vector<int>> adj;
vector<int> color; // 0 = uncolored, 1 = one color, -1 = the other

bool dfs(int u, int c) {
    color[u] = c;

    for (int v : adj[u]) {
        if (color[v] == 0) {
            if (!dfs(v, -c)) return false;
        } else if (color[v] == c) {
            return false;
        }
    }
    return true;
}

bool isBipartite(int n) {
    color.assign(n, 0);

    for (int i = 0; i < n; i++) {
        if (color[i] == 0) {
            if (!dfs(i, 1)) return false;
        }
    }
    return true;
}
```

---

## 6. BFS Approach

### Idea

Same logic as DFS, but use a queue:

1. choose an uncolored start vertex
2. color it and push it into the queue
3. pop one vertex at a time
4. color its uncolored neighbors with the opposite color
5. if any adjacent vertices have the same color, return `false`

### BFS Template

```cpp
vector<vector<int>> adj;
vector<int> color; // 0 = uncolored, 1 = one color, -1 = the other

bool isBipartite(int n) {
    color.assign(n, 0);

    for (int i = 0; i < n; i++) {
        if (color[i] != 0) continue;

        queue<int> q;
        q.push(i);
        color[i] = 1;

        while (!q.empty()) {
            int u = q.front();
            q.pop();

            for (int v : adj[u]) {
                if (color[v] == 0) {
                    color[v] = -color[u];
                    q.push(v);
                } else if (color[v] == color[u]) {
                    return false;
                }
            }
        }
    }
    return true;
}
```

---

## 8. Important Theorem

> An undirected graph is bipartite **iff** it contains **no odd cycle**.

---

## 9. Why Odd Cycles Break Bipartiteness

Example: triangle

```text
0 -- 1
 \  /
  2
```

Try coloring:

- `0` -> red
- `1` -> blue
- `2` must be red because it is adjacent to `1`

But `2` is also adjacent to `0`, so now two adjacent vertices are both red.

Conflict.

So:

- **odd cycle exists** -> not bipartite
- **no odd cycle** -> bipartite

---

## 10. Examples

### Example 1: Bipartite

```text
0 -- 1
|    |
3 -- 2
```

Possible partition:

- `A = {0, 2}`
- `B = {1, 3}`

All edges go across the two sets.

---

### Example 2: Not Bipartite

```text
0 -- 1
 \  /
  2
```

This is a cycle of length `3`, which is odd.

So it is **not bipartite**.

---

## 11. Time Complexity

For both DFS and BFS:

- **Time:** `O(V + E)`
- **Space:** `O(V)`

where:

- `V` = number of vertices
- `E` = number of edges


---

## Links

- Back to [[Graph Traversal]]
- Previous: [[Count Paths in DAG by Topological Order + DP]]
- Next: [[Articulation Points and Bridges]]
- Related: [[Common Pitfalls and Confusions]]
