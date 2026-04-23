# Binary Indexed Tree (Fenwick Tree)

> [!summary]
> **Binary Indexed Tree (Fenwick Tree)** 是一种用数组维护前缀信息的数据结构。  
> 它常用于动态维护 prefix sum，支持单点更新和前缀查询，复杂度都是 `O(log N)`。

## Core Operations

| Operation | Meaning | Complexity |
|---|---|---:|
| `update(i, delta)` | 把位置 `i` 增加 `delta` | `O(log N)` |
| `prefixSum(i)` | 查询 `[1..i]` 的和 | `O(log N)` |
| `rangeSum(l, r)` | 查询 `[l..r]` 的和 | `O(log N)` |

## Key Idea

Fenwick Tree 使用 `lowbit(x)` 找到当前下标负责的区间长度：

```cpp
int lowbit(int x) {
    return x & -x;
}
```

更新时向后跳：

```text
i += lowbit(i)
```

查询时向前跳：

```text
i -= lowbit(i)
```

## C++ Template

```cpp
class FenwickTree {
private:
    vector<long long> bit;

public:
    FenwickTree(int n) : bit(n + 1, 0) {}

    void update(int idx, long long delta) {
        while (idx < (int)bit.size()) {
            bit[idx] += delta;
            idx += idx & -idx;
        }
    }

    long long prefixSum(int idx) const {
        long long result = 0;
        while (idx > 0) {
            result += bit[idx];
            idx -= idx & -idx;
        }
        return result;
    }

    long long rangeSum(int left, int right) const {
        return prefixSum(right) - prefixSum(left - 1);
    }
};
```

## Links

- Related: [[Binary Search Tree]]
- Related: [[Union Find Disjoint Set]]
