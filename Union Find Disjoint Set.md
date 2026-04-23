

> [!summary]
> **Union-Find Disjoint Sets（并查集）** 是一种维护“若干个互不相交集合”的数据结构。  
> 它支持三类核心操作：
> - `Find`：查询某个元素属于哪个集合
> - `Union`：合并两个集合
> - `IsSameSet`：判断两个元素是否属于同一个集合  
>
> 它常用于：
> - 无向图连通块判断
> - Kruskal 最小生成树
> - 动态连通性问题  
>
> 它的经典优化有：
> - **Path Compression（路径压缩）**
> - **Union by Rank / Size（按秩合并 / 按大小合并）**  
>
> 使用优化后，单次操作的均摊复杂度接近常数，为 **O(α(N))**。

---

## 1. 核心思想

并查集把每个集合表示成一棵树。

- 每个点有一个父节点 `parent[i]`
- 如果 `parent[i] == i`，说明 `i` 是所在集合的根
- 根节点就是该集合的**代表元**

例如：

```text
0 -> 1 -> 3
2 -> 3
3 -> 3
```

这里：
- `3` 是根
- `Find(0) = 3`
- `Find(2) = 3`
- 所以 `0` 和 `2` 属于同一个集合

本质上，并查集维护的是一片森林。

---

## 2. 并查集支持的操作

### 2.1 Initialize

初始化 `N` 个元素，每个元素各自单独成一个集合：

```cpp
for (int i = 0; i < n; i++) parent[i] = i;
```

此时一共有 `N` 个集合。初始化复杂度是 **O(N)**。

---

### 2.2 FindSet(i)

查找元素 `i` 所在集合的代表元，也就是一路沿着父指针向上找根。

朴素版：

```cpp
int findSet(int i) {
    if (parent[i] == i) return i;
    return findSet(parent[i]);
}
```

如果树很高，这个操作会变慢。

---

### 2.3 IsSameSet(i, j)

判断两个元素是否属于同一个集合：

```cpp
bool isSameSet(int i, int j) {
    return findSet(i) == findSet(j);
}
```

也就是看它们的根是否相同。

---

### 2.4 UnionSet(i, j)

把两个集合合并。

基本思路：

1. 先分别找到 `i` 和 `j` 的根
2. 如果根相同，说明已经在一个集合里，不用合并
3. 否则把一个根挂到另一个根下面

朴素版：

```cpp
void unionSet(int i, int j) {
    int x = findSet(i);
    int y = findSet(j);
    if (x != y) parent[x] = y;
}
```

但这种写法可能把树搞得很高，所以需要优化。

---

## 3. 两个关键优化

---

### 3.1 路径压缩 Path Compression

在执行 `findSet(i)` 的过程中，把路径上的点直接连到根上。

优化版：

```cpp
int findSet(int i) {
    if (parent[i] == i) return i;
    return parent[i] = findSet(parent[i]);
}
```

例如原来是：

```text
0 -> 1 -> 3
```

调用一次 `findSet(0)` 后会变成：

```text
0 -> 3
1 -> 3
```

这样以后查找就更快了。  
资料中也明确说明，`FindSet(i)` 会在查找后执行路径压缩。{index=4}

---

### 3.2 按秩合并 Union by Rank

合并时，不是随便挂，而是把“矮树”挂到“高树”下面。

这里的 `rank` 表示树高的上界，用来指导合并。

规则：

- 若 `rank[x] < rank[y]`，把 `x` 挂到 `y`
- 若 `rank[x] > rank[y]`，把 `y` 挂到 `x`
- 若相等，任选一个挂上去，并让新根的 `rank + 1`

这样树不会长得太高。

---

## 4. 为什么 rank 不一定等于真实高度？

这是一个很容易考的点。

资料里提到：

- 一开始 `rank` 的确反映树高
- 但做了**路径压缩**后，树会被“压扁”
- 此时某些节点的 `rank` 不再等于真实高度
- 但没关系，因为 `rank` 只是一个**合并启发式信息**，不需要精确维护。

也就是说：

> `rank` 是“历史上树高的一个上界提示”，不是实时真实高度。

---

## 5. C++ 标准实现（推荐背）

### 5.1 按 rank 合并版本

```cpp
#include <bits/stdc++.h>
using namespace std;

class UnionFind {
private:
    vector<int> parent, rankv;
    int numSets;

public:
    UnionFind(int n) {
        parent.resize(n);
        rankv.assign(n, 0);
        numSets = n;
        for (int i = 0; i < n; i++) parent[i] = i;
    }

    int findSet(int i) {
        if (parent[i] == i) return i;
        return parent[i] = findSet(parent[i]); // 路径压缩
    }

    bool isSameSet(int i, int j) {
        return findSet(i) == findSet(j);
    }

    void unionSet(int i, int j) {
        int x = findSet(i);
        int y = findSet(j);
        if (x == y) return;

        if (rankv[x] < rankv[y]) {
            parent[x] = y;
        } else if (rankv[x] > rankv[y]) {
            parent[y] = x;
        } else {
            parent[y] = x;
            rankv[x]++;
        }
        numSets--;
    }

    int countSets() {
        return numSets;
    }
};
```

---

### 5.2 按 size 合并版本

很多题里更常用的是 **union by size**：

```cpp
#include <bits/stdc++.h>
using namespace std;

class DSU {
private:
    vector<int> parent, sz; //ith parent is parent[i], sz refers to size

public:
    DSU(int n) {
        parent.resize(n);
        sz.assign(n, 1);
        for (int i = 0; i < n; i++) parent[i] = i;
    }

    int find(int x) {
        if (parent[x] == x) return x;
        return parent[x] = find(parent[x]);
    }

    bool unite(int a, int b) {
        a = find(a);
        b = find(b);
        if (a == b) return false;

        if (sz[a] < sz[b]) swap(a, b);
        parent[b] = a;
        sz[a] += sz[b];
        return true;
    }

    bool same(int a, int b) {
        return find(a) == find(b);
    }

    int size(int x) {
        return sz[find(x)];
    }
};
```

优点：

- 可以方便维护集合大小
- 写法很自然
- 很多竞赛题更喜欢这一版

---

## 6. 复杂度

如果同时使用：

- 路径压缩
- 按秩合并 / 按大小合并

那么：

- `find`
- `union`
- `same`

的均摊复杂度都是：

```text
O(α(N))
```

其中 `α(N)` 是 **反 Ackermann 函数**，增长极慢，在实际范围内几乎可以看成常数。

所以并查集通常被视为“近似 O(1)”的数据结构。

---

## 7. 典型应用

### 7.1 连通块判断

给很多无向边 `(u, v)`，每读到一条边就执行：

```cpp
dsu.unite(u, v);
```

最后：

- `same(a, b)` 可以判断是否连通
- 或统计有多少个不同根，就知道有多少个连通块

---

### 7.2 Kruskal 最小生成树
[[Kruskal]]

Kruskal 的核心思想：

1. 按边权从小到大排序
2. 依次尝试加入边 `(u, v)`
3. 如果 `u` 和 `v` 不在同一个集合，就加入这条边并合并
4. 如果已经在同一个集合，说明会成环，跳过

代码模板：

```cpp
struct Edge {
    int u, v, w;
    bool operator<(const Edge& other) const {
        return w < other.w;
    }
};

int kruskal(int n, vector<Edge>& edges) {
    sort(edges.begin(), edges.end());
    DSU dsu(n);
    int mstWeight = 0;

    for (auto &e : edges) {
        if (dsu.unite(e.u, e.v)) {
            mstWeight += e.w;
        }
    }
    return mstWeight;
}
```

并查集在这里就是用来快速判断“是否成环”的。  
资料中也明确指出 UFDS 可用于无向图连通块和 Kruskal MST。

---

## 8. 例题模板

### 8.1 Number of Provinces 类型

给邻接矩阵，求省份数量：

```cpp
class Solution {
public:
    class DSU {
    public:
        vector<int> p, sz;
        DSU(int n) {
            p.resize(n);
            sz.assign(n, 1);
            for (int i = 0; i < n; i++) p[i] = i;
        }

        int find(int x) {
            if (p[x] == x) return x;
            return p[x] = find(p[x]);
        }

        void unite(int a, int b) {
            a = find(a);
            b = find(b);
            if (a == b) return;
            if (sz[a] < sz[b]) swap(a, b);
            p[b] = a;
            sz[a] += sz[b];
        }
    };

    int findCircleNum(vector<vector<int>>& isConnected) {
        int n = isConnected.size();
        DSU dsu(n);

        for (int i = 0; i < n; i++) {
            for (int j = i + 1; j < n; j++) {
                if (isConnected[i][j]) {
                    dsu.unite(i, j);
                }
            }
        }

        int ans = 0;
        for (int i = 0; i < n; i++) {
            if (dsu.find(i) == i) ans++;
        }
        return ans;
    }
};
```

---
