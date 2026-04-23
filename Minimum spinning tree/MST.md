# Minimum Spanning Tree

> [!summary]
> **Minimum Spanning Tree (MST)** 是在连通无向带权图中选择 `V - 1` 条边，使图保持连通、无环，并让总边权最小。  
> 这个专题主要包含两种经典贪心算法：[[Kruskal]] 和 [[Prim Algorithm]]。

## Table of Contents

1. [[Kruskal|Kruskal Algorithm]]
2. [[Prim Algorithm|Prim Algorithm]]

## Quick Comparison

| Algorithm | Core Idea | Data Structure | Typical Complexity |
|---|---|---|---|
| [[Kruskal]] | 按边权从小到大合并森林 | Edge List + [[Union Find Disjoint Set]] | `O(E log E)` |
| [[Prim Algorithm]] | 从一个起点不断扩展一棵树 | Adjacency List + Priority Queue | `O(E log V)` |

## Links

- Related: [[Union Find Disjoint Set]]
- Related: [[Graph Traversal]]
