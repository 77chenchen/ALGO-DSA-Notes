# Common Pitfalls and Confusions

> [!summary]
> 这一页集中记录 Graph Traversal 里最容易混淆的点：有向图和无向图的差异、DFS 状态含义、拓扑排序和判环的关系、以及割点桥的条件。

## Common Pitfalls

- Directed cycle detection 的三色 DFS 中，只有遇到 `visiting` 节点才说明有 back edge。
- Undirected graph DFS 判环时，要排除 parent edge。
- Topological sort 只适用于 DAG；如果图有环，就不存在完整拓扑序。
- Bipartite check 的本质是 2-coloring；odd cycle 会破坏二分性。
- Bridge 条件是 `low[v] > num[u]`，articulation point 条件是 `low[v] >= num[u]`。
- DFS root 的 articulation point 判断必须单独处理：root 至少有两个 DFS children。

## Quick Links

- [[Cycle Detection in Directed Graphs]]
- [[Topological Sort (DFS Kahn)]]
- [[Bipartite Graph Check]]
- [[Articulation Points and Bridges]]
- [[Strongly Connected Components (Kosaraju Tarjan)]]

## Links

- Back to [[Graph Traversal]]
- Related: [[Comparison Summary]]
