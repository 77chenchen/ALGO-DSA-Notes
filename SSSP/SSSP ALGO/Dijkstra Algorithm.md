# Dijkstra's Algorithm

## 1. 核心思想

Dijkstra 算法用于求解：

- **单源最短路径问题**（SSSP, Single-Source Shortest Path）
- 适用于 **边权非负** 的图

它维护一个集合：

- **R (Resolved)**：已经确定最终最短路的点集合

初始时：

- `R = {}`

同时维护距离数组：

- `D[u]` 表示从源点 `s` 到点 `u` 的当前最短路估计值

初始化：

- `D[s] = 0`
- 对所有 `u ≠ s`，`D[u] = ∞`

---

## 2. 算法流程

Dijkstra 每一轮做的事情：

1. 在所有还没有被解决的点 `V \ R` 中
2. 选出当前 `D[u]` 最小的点 `u`
3. 把 `u` 加入 `R`
4. 对 `u` 的所有出边进行 **relax（松弛）**

可概括为：

    重复：
        从 V \ R 中选 D[u] 最小的点 u
        把 u 加入 R
        对所有边 (u, v) 做 relax
    直到所有点处理完毕

---

## 3. 什么是 Relax（松弛）

对于一条边 `(u, v)`，权重为 `w(u, v)`：

如果

    D[u] + w(u, v) < D[v]

那么说明经过 `u` 再到 `v` 更短，于是更新：

    D[v] = D[u] + w(u, v)

这一步就叫 **relax edge (u, v)**。

---

## 4. 为什么它是 Greedy（贪心）

Dijkstra 的贪心策略是：

- 每次优先处理当前距离估计值最小的点

也就是“先处理目前看起来最优的那个”。

这个策略能成立的关键前提是：

- **图中没有负权边**

因为当所有边权都非负时：

- 一旦某个点 `u` 是当前最小的 `D[u]`
- 以后不可能再通过其他路径把它变得更小

所以此时 `D[u]` 就可以被认为已经是最终答案。

---

## 5. Priority Queue（优先队列）优化

因为每次都要找：

- 当前 `D[u]` 最小的未解决点

所以通常使用：

- **Priority Queue (PQ)**

常见实现：

- **Binary Min Heap**
- **Balanced BST / AVL / Red-Black Tree**

---

## 6. 时间复杂度分析

### 6.1 ExtractMin

每个顶点最多被“最终确定”一次。

共有 `V` 个顶点，因此：

- `ExtractMin()` 最多执行 `O(V)` 次
- 每次复杂度 `O(log V)`

所以这一部分是：

    O(V log V)

---

### 6.2 Relax + DecreaseKey

整个过程中一共会检查所有边，因此：

- 总共处理 `E` 条边

若某条边松弛成功，就要更新优先队列中的键值：

- Binary Heap 中对应 `DecreaseKey()`
- BST 中通常是删除旧值再插入新值

每次更新代价：

    O(log V)

所以这一部分是：

    O(E log V)

---

### 6.3 总复杂度

总时间复杂度为：

    O(V log V + E log V)
    = O((V + E) log V)

相比 Bellman-Ford 的：

    O(VE)

要快很多。

---

## 7. 正确性证明思路：Loop Invariant

为了证明 Dijkstra 在**非负权图**上正确，通常使用 **循环不变式（loop invariant）**。

需要证明三部分：

### 7.1 Initialization

第一次循环开始之前，不变式成立。

### 7.2 Maintenance

如果第 `x` 轮开始时不变式成立，
那么第 `x+1` 轮开始时仍然成立。

### 7.3 Termination

当算法结束时，由不变式推出算法正确。

---

## 8. 常见循环不变式表述

一个典型写法是：

> 在每一轮循环开始时，所有已经在集合 `R` 中的顶点 `u`，
> 都满足 `D[u] = δ(s, u)`。

其中：

- `δ(s, u)` 表示从源点 `s` 到顶点 `u` 的真实最短路径长度

这就是 Dijkstra 正确性的核心。

---

## 9. 为什么不能有负权边

Dijkstra **不能处理负权边**。

注意：

- 即使图中**没有负权环**
- 只要存在**负权边**
- Dijkstra 就可能出错

---

## 10. 负权边为什么会让它失败

Dijkstra 的核心假设是：

- 当前最小的 `D[u]` 一旦被取出，就不会再变小

但如果存在负权边，就可能发生：

- 某条路径前面看起来比较差
- 但后面经过一条负边之后，总长度反而变得更优

这样就会导致：

- 某个点被“过早确定”
- 后来虽然发现了更短路径，但已经来不及正确传播

这就破坏了贪心策略。

---

## 11. 例子说明

假设源点是 `0`，有两条路径到 `3`：

- `0 → 1 → 3`，权重为 `1 + 2 = 3`
- `0 → 2 → 3`，权重为 `10 + (-10) = 0`

Dijkstra 一开始会觉得：

- 经过 `1` 到 `3` 的路径更优，因为 `3 < 10`

于是它可能先把 `3` 以距离 `3` 的状态处理掉，
并继续更新后继点，例如：

- `D[4] = 6`

但后来才发现：

- 其实 `0 → 2 → 3 = 0` 更短

也就是说：

- `D[3]` 本该从 `3` 变成 `0`

然而点 `3` 已经被处理过了，
这个更优结果无法继续正确传播下去，
所以后面的点（如 `4`）也会保持错误答案。

---

## C++实现
```cpp
#include <bits/stdc++.h>
using namespace std;

using ll = long long;
const ll INF = 4e18;

struct Edge {
    int to;
    ll w;
};

vector<ll> dijkstra(int n, int start, const vector<vector<Edge>>& graph) {
    vector<ll> dist(n + 1, INF);
    priority_queue<pair<ll, int>, vector<pair<ll, int>>, greater<pair<ll, int>>> pq;

    dist[start] = 0;
    pq.push({0, start});

    while (!pq.empty()) {
        auto [d, u] = pq.top();
        pq.pop();

        // 如果这个状态已经过期，就跳过
        if (d != dist[u]) continue;

        for (const auto& e : graph[u]) {
            int v = e.to;
            ll w = e.w;

            if (dist[u] + w < dist[v]) {
                dist[v] = dist[u] + w;
                pq.push({dist[v], v});
            }
        }
    }

    return dist;
}

int main() {
    ios::sync_with_stdio(false);
    cin.tie(nullptr);

    int n, m;
    cin >> n >> m;

    vector<vector<Edge>> graph(n + 1);

    // 输入 m 条边：u v w
    for (int i = 0; i < m; i++) {
        int u, v;
        ll w;
        cin >> u >> v >> w;

        graph[u].push_back({v, w});

        // 如果是无向图，把下面这行也打开
        // graph[v].push_back({u, w});
    }

    int start;
    cin >> start;

    vector<ll> dist = dijkstra(n, start, graph);

    for (int i = 1; i <= n; i++) {
        if (dist[i] == INF) cout << "INF\n";
        else cout << dist[i] << "\n";
    }

    return 0;
}
```
