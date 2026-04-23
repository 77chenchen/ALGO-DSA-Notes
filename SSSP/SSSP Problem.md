# Single-Source Shortest Path

> [!summary]
> **SSSP (Single-Source Shortest Path)** 研究从一个源点 `s` 到图中所有其他点的最短距离。  
> 选择算法时，最重要的判断是：图是否无权、边权是否非负、是否需要处理负权边或负环。

## Table of Contents

1. [[BFS for SSSP|BFS for SSSP]]
2. [[Dijkstra Algorithm|Dijkstra Algorithm]]
3. [[Modified Dijkstra|Modified Dijkstra]]
4. [[Bellman-Ford algorithm|Bellman-Ford Algorithm]]

## Choosing the Right Algorithm

| Graph Type | Recommended Algorithm | Complexity |
|---|---|---:|
| Unweighted graph | [[BFS for SSSP]] | `O(V + E)` |
| Non-negative weighted graph | [[Dijkstra Algorithm]] | `O((V + E) log V)` with heap |
| Non-negative weighted graph with lazy heap | [[Modified Dijkstra]] | `O(E log E)` or `O(E log V)` in practice |
| Negative edges, no negative cycle | [[Bellman-Ford algorithm]] | `O(VE)` |
| Need negative-cycle detection | [[Bellman-Ford algorithm]] | `O(VE)` |

## Links

- Related: [[Graph Traversal]]
- Related: [[BFS for SSSP]]
