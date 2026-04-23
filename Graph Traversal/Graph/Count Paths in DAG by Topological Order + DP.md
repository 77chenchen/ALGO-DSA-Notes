# Count Paths in DAG by Topological Order + DP

> [!summary]
> 在 DAG 中统计路径数时，可以先做 [[Topological Sort (DFS Kahn)]]，再按拓扑序做 DP。  
> 因为 DAG 没有环，每条边 `u -> v` 都能保证 `u` 在 `v` 前面被处理。

Related: [[Graph Traversal]] | [[Topological Sort (DFS Kahn)]]

## Core Idea

如果要统计从源点 `s` 到每个点的路径数量，定义：

```text
dp[u] = number of paths from s to u
```

初始化：

```text
dp[s] = 1
```

然后按拓扑序遍历每个点 `u`，对所有边 `u -> v` 做转移：

```text
dp[v] += dp[u]
```

## Why Topological Order Works

拓扑序保证所有进入 `v` 的前驱节点都会在 `v` 之前被处理。

所以当我们处理到 `v` 时：

- 所有能贡献到 `v` 的路径数都已经累计完成。
- 不会出现之后又绕回来更新前面节点的情况。

这正是 DAG DP 的关键。

## Template

```cpp
vector<long long> countPathsInDAG(
    int n,
    int source,
    const vector<vector<int>>& adj,
    const vector<int>& topo
) {
    vector<long long> dp(n, 0);
    dp[source] = 1;

    for (int u : topo) {
        for (int v : adj[u]) {
            dp[v] += dp[u];
        }
    }

    return dp;
}
```

## Complexity

| Step | Complexity |
|---|---:|
| Topological sort | `O(V + E)` |
| DP transition | `O(V + E)` |
| Total | `O(V + E)` |

## Links

- Back to [[Graph Traversal]]
- Previous: [[Topological Sort (DFS Kahn)]]
- Next: [[Bipartite Graph Check]]
- Related: [[Comparison Summary]]
