# Graph Traversal Comparison Summary

> [!summary]
> 这一页把 Graph Traversal 相关主题放在一起对比，方便复习时快速选择算法。

## Algorithm Comparison

| Problem | Graph Type | Main Tool | Complexity |
|---|---|---|---:|
| Directed cycle detection | Directed graph | DFS three colors | `O(V + E)` |
| Topological sort | DAG | DFS finish order or Kahn | `O(V + E)` |
| Count paths in DAG | DAG | Topological order + DP | `O(V + E)` |
| Bipartite check | Undirected graph | BFS/DFS 2-coloring | `O(V + E)` |
| Articulation points | Undirected graph | DFS low-link | `O(V + E)` |
| Bridges | Undirected graph | DFS low-link | `O(V + E)` |
| SCC | Directed graph | Kosaraju / Tarjan | `O(V + E)` |
| 2-SAT | Implication graph | SCC | `O(N + M)` |

## Key Distinctions

- Directed cycle detection uses `visiting` state; undirected DFS must exclude the parent edge.
- Topological sort exists iff the directed graph is a DAG.
- DAG DP depends on topological order, because dependencies must be processed first.
- Bipartite check fails exactly when an odd cycle exists in an undirected graph.
- Bridge uses `low[v] > num[u]`; articulation point uses `low[v] >= num[u]`.
- SCC is for directed mutual reachability, not undirected connected components.

## Links

- Back to [[Graph Traversal]]
- Previous: [[2-SAT]]
- Next: [[Common Pitfalls and Confusions]]
- Related: [[Common Pitfalls and Confusions]]
