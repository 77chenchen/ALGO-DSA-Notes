
# Bellman-Ford Algorithm

> [!summary]
> **Bellman-Ford Algorithm** 用于解决带负权边的 Single-Source Shortest Path，并且可以检测从源点可达的负权环。  
> 它通过重复 `V - 1` 轮 relax 所有边，把最短路信息逐步传播出去。

Related: [[SSSP Problem]] | [[Dijkstra Algorithm]] | [[Modified Dijkstra]]

## 1. Overview

Bellman-Ford 是一种用于解决 **Single-Source Shortest Path (SSSP, 单源最短路径)** 的经典算法。

它的特点是：

- 可以处理 **负权边 (negative edge weights)**
- 可以 **检测从源点可达的负权环 (negative cycle)**
- 时间复杂度较慢，为 **O(VE)**

---

## 2. Problem Definition

给定一个带权有向图 $G = (V, E)$ 和源点 $s$，Bellman-Ford 的目标是：

1. 求出从 $s$ 到每个顶点 $u$ 的最短距离
2. 记录实际最短路径
3. 判断是否存在 **从源点可达的负权环**

---

## 3. When to Use Bellman-Ford

适用情况：

- 图中可能有 **负权边**
- 需要判断是否存在 **负权环**
- 需要一个“通用型”的最短路算法

不太适合：

- 所有边权都非负且追求效率时  
  此时一般优先用 [[Dijkstra Algorithm]]

---

## 4. Core Idea

Bellman-Ford 的核心思想是：

> 反复对图中的每一条边做 relaxation，逐步更新最短距离。

它不是像 Dijkstra 那样“每次选当前最近的点”，而是：

- 一轮一轮扫描所有边
- 每轮尽量把更短的路径信息传播出去

---

## 5. Relaxation

对于一条边 $(u, v)$，边权为 $w(u,v)$：

如果当前经过 $u$ 再走到 $v$ 更优，即：

$$
D[u] + w(u,v) < D[v]
$$

那么就更新：

$$
D[v] = D[u] + w(u,v)
$$

并记录前驱：

$$
p[v] = u
$$

这一步就叫做 **relax(u, v)**。

---

## 6. Data Structures

### 6.1 Distance Array `D`

`D[u]` 表示：

> 当前已知的从源点 $s$ 到顶点 $u$ 的最短距离估计

初始化：

- `D[s] = 0`
- `D[u] = +∞` for all `u ≠ s`

---

### 6.2 Parent Array `p`

`p[u]` 表示：

> 从源点到 `u` 的当前最优路径中，`u` 的前驱结点

初始化：

- `p[u] = -1` 或 `NULL`

它用于恢复最短路径。

---

## 7. Why Repeat `V-1` Times?


原因：

- 一个 **合法的最短简单路径** 最多只包含 `|V|-1` 条边
- 如果路径用了 `|V|` 条边或更多，就一定重复顶点，形成环
- 在没有相关负权环的情况下，最短路不会需要重复绕环

所以：

> 最多经过 `|V|-1` 条边的路径就已经覆盖所有合法最短路

---

## 8. Key Property

Bellman-Ford 的一个重要性质：

> 第 `i` 轮结束后，所有“最多经过 `i` 条边”的最短路径都会被正确算出。

因此：

- 第 1 轮：求出最多 1 条边的最短路
- 第 2 轮：求出最多 2 条边的最短路
- ...
- 第 `|V|-1` 轮：求出最多 `|V|-1` 条边的最短路

---

## 9. Why This Property Is True

任意一条用了 `i` 条边的路径，都可以拆成：

- 前面一条用了 `i-1` 条边的路径
- 再加最后一条边

也就是说，如果第 `i-1` 轮后，所有最多 `i-1` 条边的最短路都正确了，  
那么第 `i` 轮扫描边 `(u,v)` 时，就可以把这些正确答案再往后推进一条边。

这就是 Bellman-Ford 一轮一轮传播最短路信息的本质。

---

## 10. Standard Pseudocode

```text
Initialize:
    D[s] = 0
    D[u] = ∞ for all u ≠ s
    p[u] = -1 for all u

for i = 1 to |V|-1:
    for each edge (u, v) in E:
        if D[u] != ∞ and D[u] + w(u,v) < D[v]:
            D[v] = D[u] + w(u,v)
            p[v] = u
```

---

## 11. Negative Cycle Detection

在完成 `|V|-1` 轮后，再额外扫描一遍所有边：

如果仍存在某条边满足：

$$
D[u] + w(u,v) < D[v]
$$

则说明：

> 存在从源点可达的负权环

伪代码：

```text
for each edge (u, v) in E:
    if D[u] != ∞ and D[u] + w(u,v) < D[v]:
        report negative cycle
```

---

## 12. Complete Pseudocode

```text
BellmanFord(s):
    for each vertex u:
        D[u] = ∞
        p[u] = -1
    D[s] = 0

    for i = 1 to |V|-1:
        for each edge (u, v) in E:
            if D[u] != ∞ and D[u] + w(u,v) < D[v]:
                D[v] = D[u] + w(u,v)
                p[v] = u

    for each edge (u, v) in E:
        if D[u] != ∞ and D[u] + w(u,v) < D[v]:
            report "reachable negative cycle exists"
```

---

## 13. Early Stopping Optimization

Bellman-Ford 可以优化：

如果某一整轮扫描所有边后，**没有任何更新**，就可以提前停止。

因为这意味着距离已经稳定。

### Optimized Pseudocode

```text
for i = 1 to |V|-1:
    changed = false
    for each edge (u, v) in E:
        if D[u] != ∞ and D[u] + w(u,v) < D[v]:
            D[v] = D[u] + w(u,v)
            p[v] = u
            changed = true
    if changed == false:
        break
```

### Complexity After Optimization

- 最坏情况仍然是 `O(VE)`
- 但实际可能只需 `O(kE)`，其中 `k` 是实际进行了多少轮

---

## 14. Time Complexity

### Standard Version

- 外层循环：`|V|-1`
- 内层扫描所有边：`|E|`
- 每次 relax：`O(1)`

总复杂度：

$$
O(VE)
$$

### Space Complexity

- `D` 数组：`O(V)`
- `p` 数组：`O(V)`
- 图存储：通常 `O(E)`

---

## 15. Why Negative Edge Is Fine but Negative Cycle Is Not

### Negative Edge

负权边本身不是问题。

例如：

- `0 -> 1` weight = 5
- `1 -> 2` weight = -8

那么路径 `0 -> 1 -> 2` 的总权就是 `-3`，这是完全可以定义的。

---

### Negative Cycle

如果存在一个环，其总权重小于 0，那么可以无限绕这个环使路径权值不断减小。

例如：

- `1 -> 2` weight = 15
- `2 -> 1` weight = -42

环总权重：

$$
15 + (-42) = -27
$$

每绕一圈，路径总权值就减少 27。

如果源点可以到达这个负权环，那么相关顶点的最短路就不再是有限值，而是：

$$
-\infty
$$

---

## 16. Important Refinement About Negative Cycles

并不是说图里只要有负权环，整个 SSSP 就全毁了。

更准确地说：

> 对某个目标点 `v`，只有当某个负权环既能从源点 `s` 到达，又能从该负权环继续到达 `v` 时，`δ(s,v)` 才会变成 `-∞`。

所以：

- 负权环 **可达** 很重要
- 负权环是否还能影响目标点，也很重要

---

## 17. Step-by-Step Example

考虑如下图，源点是 `0`：

- `0 -> 1` weight = 4
- `0 -> 2` weight = 5
- `1 -> 2` weight = -2
- `1 -> 3` weight = 6
- `2 -> 3` weight = 1

---

### Initial State

```text
D = [0, ∞, ∞, ∞]
p = [-1, -1, -1, -1]
```

---

### Pass 1

#### Edge `0 -> 1` (4)

`0 + 4 < ∞`，更新：

```text
D[1] = 4
p[1] = 0
```

#### Edge `0 -> 2` (5)

`0 + 5 < ∞`，更新：

```text
D[2] = 5
p[2] = 0
```

#### Edge `1 -> 2` (-2)

`4 + (-2) = 2 < 5`，更新：

```text
D[2] = 2
p[2] = 1
```

#### Edge `1 -> 3` (6)

`4 + 6 = 10 < ∞`，更新：

```text
D[3] = 10
p[3] = 1
```

#### Edge `2 -> 3` (1)

`2 + 1 = 3 < 10`，更新：

```text
D[3] = 3
p[3] = 2
```

Pass 1 结束：

```text
D = [0, 4, 2, 3]
p = [-1, 0, 1, 2]
```

---

### Pass 2

再次扫描所有边，没有任何更新。

说明已经收敛。

---

### Final Result

```text
D = [0, 4, 2, 3]
p = [-1, 0, 1, 2]
```

最短路：

- 到 `1`：`0 -> 1`
- 到 `2`：`0 -> 1 -> 2`
- 到 `3`：`0 -> 1 -> 2 -> 3`

---

## 18. Path Reconstruction

如果要恢复从 `s` 到某个点 `t` 的路径：

1. 从 `t` 开始
2. 不断看 `p[t]`
3. 一直回溯到 `s`
4. 最后 reverse

例如：

```text
p[3] = 2
p[2] = 1
p[1] = 0
```

则路径是反向记录为：

```text
3 <- 2 <- 1 <- 0
```

反转后得到：

```text
0 -> 1 -> 2 -> 3
```


---

## Links

- Back to [[SSSP Problem]]
- Previous: [[Modified Dijkstra]]
- Related: [[Dijkstra Algorithm]]

