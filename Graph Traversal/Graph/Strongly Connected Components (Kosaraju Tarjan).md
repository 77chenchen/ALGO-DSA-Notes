# Strongly Connected Components (Kosaraju / Tarjan)

> [!summary]
> **Strongly Connected Component (SCC)** 是有向图中一组互相可达的顶点。  
> 常见算法有 Kosaraju 和 Tarjan，二者都能在线性时间 `O(V + E)` 内分解 SCC。

Related: [[Graph Traversal]] | [[Cycle Detection in Directed Graphs]]

## Definition

在有向图中，如果一组点里的任意两个点 `u` 和 `v` 都满足：

```text
u can reach v
v can reach u
```

那么它们属于同一个 SCC。

## Kosaraju Algorithm

核心步骤：

1. 在原图上 DFS，按完成时间把点压入 order。
2. 反转所有边，得到 reversed graph。
3. 按 order 的逆序，在 reversed graph 上 DFS。
4. 每次 DFS 访问到的一整组点就是一个 SCC。

为什么有效：

- 第一次 DFS 得到的完成时间顺序能定位 condensation DAG 的 source/sink 结构。
- 在反图上按逆完成时间 DFS，可以一次抓出一个 SCC。

## Tarjan Algorithm

Tarjan 使用一次 DFS，并维护：

- `disc[u]`: 第一次访问 `u` 的时间戳。
- `low[u]`: `u` 能回到的最早栈内祖先时间戳。
- `stack`: 当前 DFS 路径中尚未归类的点。
- `inStack[u]`: 判断点是否仍在栈中。

当发现：

```text
low[u] == disc[u]
```

说明 `u` 是一个 SCC 的根，可以从栈中不断弹出直到 `u`。

## Comparison

| Algorithm | DFS Count | Extra Graph Needed | Main Advantage |
|---|---:|---|---|
| Kosaraju | 2 passes | reversed graph | Conceptually simple |
| Tarjan | 1 pass | no reversed graph | More compact and online-like |

## Links

- Back to [[Graph Traversal]]
- Previous: [[Articulation Points and Bridges]]
- Next: [[2-SAT]]
- Related: [[Common Pitfalls and Confusions]]
