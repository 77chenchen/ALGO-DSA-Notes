# 2-SAT

> [!summary]
> **2-SAT** 判断一组每个 clause 至多含两个 literal 的布尔公式是否可满足。  
> 它可以转化为 implication graph，并用 [[Strongly Connected Components (Kosaraju Tarjan)]] 在线性时间内求解。

Related: [[Graph Traversal]] | [[Strongly Connected Components (Kosaraju Tarjan)]]

## Problem Form

2-SAT 的每个 clause 形如：

```text
(a OR b)
```

其中 `a` 和 `b` 可以是变量，也可以是变量的取反。

目标是判断是否存在一种 true/false 赋值，使所有 clauses 都为 true。

## Implication Graph

关键转化：

```text
(a OR b) == (!a -> b) AND (!b -> a)
```

所以每个 clause 会变成两条有向边。

例如：

```text
(x OR !y)
```

对应：

```text
(!x -> !y)
(y -> x)
```

## SCC Criterion

构建 implication graph 后，对图求 SCC。

如果某个变量 `x` 和它的反面 `!x` 在同一个 SCC 中，那么公式不可满足：

```text
x reaches !x
!x reaches x
```

这意味着 `x` 和 `!x` 会互相强迫，产生矛盾。

否则公式可满足。

## Variable Encoding

常见编码：

```text
x_i false -> 2*i
x_i true  -> 2*i + 1
```

取反可以用：

```cpp
id ^ 1
```

## Complexity

如果有 `N` 个变量、`M` 个 clauses：

- 顶点数：`2N`
- 边数：`2M`
- 总复杂度：`O(N + M)`

## Links

- Back to [[Graph Traversal]]
- Previous: [[Strongly Connected Components (Kosaraju Tarjan)]]
- Next: [[Comparison Summary]]
- Related: [[Common Pitfalls and Confusions]]
