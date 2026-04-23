
# Prim Algorithm

> [!summary]
> **Prim's Algorithm** 是求 Minimum Spanning Tree 的经典贪心算法。  
> 它从一个起点开始，每次选择当前树连接到外部的最小边，逐步把树扩展到所有顶点。

Related: [[MST]] | [[Kruskal]]

## 1. Prim 是什么

**Prim's algorithm** 是另一个求 **Minimum Spanning Tree (MST)** 的贪心算法。

它和 Kruskal 一样，目标都是：

> 在一个 **连通、无向、带权图** 中，找一棵总权重最小的生成树。

Prim 的特点是：

> **从一个起点出发，逐步把一棵树往外扩展，直到覆盖所有顶点。**

---

## 2. Prim 的核心思想

Prim 不是先看全图所有边排序后再挑，而是：

- 先选一个起点 \(s\)
- 把当前树与外部相连的候选边放进优先队列
- 每次取出其中 **最小的合法边**
- 把新顶点加入树中
- 再把这个新顶点连出去的边加入候选集合
- 重复直到整张图都被覆盖

所以你可以把 Prim 理解成：

> **从一个点开始，始终用当前最便宜的方式，把树扩大一圈。**

---

## 3. Prim 和 Kruskal 的区别

### Prim
- 从 **一个起点** 开始
- 始终维护 **一棵正在生长的树**
- 每次选择“**当前树到外部**”的最小边

### Kruskal
- 不从某个点开始
- 一开始是很多棵小树
- 每次选择全局最小且不成环的边，逐步合并森林

一句话：

- **Prim = 长树**
- **Kruskal = 合并森林**

---

## 4. Prim 需要的数据结构

Prim 常用下面三个东西：

### 4.1 Priority Queue（优先队列）
用来动态维护：

> 当前所有可能把树扩展出去的边中，哪条最小

通常用：

- Binary Heap（二叉堆）
- 或 Balanced BST

---

### 4.2 Adjacency List（邻接表）
因为每次加入一个新顶点后，要快速遍历它的邻边，所以一般用邻接表存图。

---

### 4.3 Boolean array / visited array
记录一个顶点是否已经在当前生成树里面。

例如：

- `visited[u] = true` 表示顶点 \(u\) 已经被纳入树中
- `visited[u] = false` 表示还没纳入

这个数组的作用本质上就是：

> 防止重复加入顶点，从而防止成环

---

## 5. Prim 的标准流程

设起点为 \(s\)。

### Step 1：初始化
- 把 \(s\) 标记为已访问
- 把所有与 \(s\) 相连的边加入优先队列

---

### Step 2：反复取最小边
从优先队列取出当前权值最小的候选边。

如果这条边通向的顶点 \(v\) **还没有访问过**：

- 说明它可以把树扩展出去
- 把这条边加入 MST
- 把 \(v\) 标记为已访问
- 再把 \(v\) 的所有邻边加入优先队列

如果 \(v\) **已经访问过**：

- 说明这条边会连向树内部
- 会形成环
- 直接丢弃

---

### Step 3：直到包含所有顶点
当所有顶点都已经被纳入树中时，Prim 结束。

最终得到一棵 MST。

---

## 6. Prim 为什么不会成环

Prim 始终维持这样一个结构：

- 当前已访问顶点构成一棵树
- 新加入的边必须连接：
  - 一个已经在树中的点
  - 一个还不在树中的点

这样每次都会多加入一个新顶点，而不是把树内部两个点再连起来。

所以：

> Prim 每次都是“树内连树外”，因此不会形成环。

如果某条边的终点已经访问过了，说明这条边连的是“树内到树内”，会成环，因此要丢弃。

---

## 7. Prim 的贪心策略

Prim 是贪心算法，因为它每一步都做：

> 在所有当前可用的扩展边中，选最小的那条。

注意这里的“当前可用”很重要。

Prim 不是在所有边中选最小，而是在：

> **当前树和外部之间的所有边** 中选最小。

这称为 **cut-based greedy choice（割上的贪心选择）**。

---

## 8. Prim 为什么正确

下面用更容易理解的方式说明。

---

### 8.1 设定

- \(T\)：Prim 最终生成的树
- \(T^*\)：某棵真正的 MST

如果 \(T = T^*\)，那当然没问题。

难点在于：如果 \(T \ne T^*\)，为什么 Prim 生成的树仍然是 MST？

---

### 8.2 交换论证（Exchange Argument）

设 \(e_k = (u, v)\) 是 Prim 第一次选到、但不属于 \(T^*\) 的边。

因为 \(T^*\) 是一棵树，所以在 \(T^*\) 中，\(u\) 和 \(v\) 一定有一条唯一路径 \(P\)。

而在 Prim 选这条边时：

- \(u\) 在当前树里
- \(v\) 还不在当前树里

所以在路径 \(P\) 上，一定存在某条边 \(e^*\)，它刚好跨越这个边界：

- 一个端点在当前树里
- 另一个端点不在当前树里

这条 \(e^*\) 也是一个合法候选边。

但 Prim 没选 \(e^*\)，却选了 \(e_k\)，说明：

\[
w(e_k) \le w(e^*)
\]

也就是说，Prim 选的边不会比 \(T^*\) 中对应跨边界的那条边更贵。

于是我们可以：

- 从 \(T^*\) 中删去 \(e^*\)
- 加入 \(e_k\)

这样得到的新树仍然连通、无环，而且总权重不变或更小。  
因为 \(T^*\) 已经是 MST，所以这里只能是 **总权重相同**。

因此：

> 我们可以把 \(T^*\) 一步步“变成” Prim 的结果，而不增加总权重。

最终说明：

> Prim 生成的树也是 MST。

---

## 9. 一个简单例子

假设图中有这些边：

- \(0-1 = 1\)
- \(0-3 = 1\)
- \(1-3 = 1\)
- \(0-2 = 2\)

从顶点 \(1\) 开始做 Prim。

### 初始
树中只有顶点 \(1\)

候选边：
- \(1-0\)
- \(1-3\)

---

### 第一步
取最小边，比如 \(1-0\)

树变成：
- \(1, 0\)

加入新候选边：
- \(0-3\)
- \(0-2\)

---

### 第二步
当前最小边可能是 \(1-3\) 或 \(0-3\)，它们权值相同

选哪条都可以，只要合法就行。

这说明：

> Prim 得到的 MST **不一定唯一**
> 但总权重一样

---

## 10. Prim 的伪代码

这里给出常见写法：优先队列里存 `(weight, vertex)`。

```cpp
#include <bits/stdc++.h>
using namespace std;

struct Edge {
    int to, weight;
};

int main() {

    int n, m;
    cin >> n >> m;

    vector<vector<Edge>> adj(n);

    for (int i = 0; i < m; i++) {
        int u, v, w;
        cin >> u >> v >> w;
        adj[u].push_back({v, w});
        adj[v].push_back({u, w});
    }

    // visited[i] 表示点 i 是否已经加入 MST
    vector<bool> visited(n, false);

    // parent[i] 表示 MST 中 i 的父节点
    vector<int> parent(n, -1);

    // min_w[i] 表示当前把 i 接到 MST 的最小边权
    vector<int> min_w(n, INT_MAX);

    // 小根堆: {边权, 点编号}
    priority_queue<pair<int,int>, vector<pair<int,int>>, greater<pair<int,int>>> pq;

    // 从 0 开始
    min_w[0] = 0;
    pq.push({0, 0});

    int totalWeight = 0;
    int count = 0;

    while (!pq.empty()) {
        auto [w, u] = pq.top();
        pq.pop();

        if (visited[u]) continue;

        visited[u] = true;
        totalWeight += w;
        count++;

        for (auto &e : adj[u]) {
            int v = e.to;
            int weight = e.weight;

            if (!visited[v] && weight < min_w[v]) {
                min_w[v] = weight;
                parent[v] = u;
                pq.push({weight, v});
            }
        }
    }

    // 如果不是连通图，就不存在最小生成树
    if (count != n) {
        cout << "Graph is not connected, no MST exists.\n";
        return 0;
    }

    cout << "Edges in MST:\n";
    for (int i = 1; i < n; i++) {
        cout << parent[i] << " - " << i << " (weight = " << min_w[i] << ")\n";
    }

    cout << "Total weight = " << totalWeight << "\n";

    return 0;
}
```

## Links

- Back to [[MST]]
- Previous: [[Kruskal]]
- Related: [[Graph Traversal]]
