# Table ADT and Motivation

> [!summary]
> BST 常用于实现一种 **key 必须有序** 的 Table / Map ADT。  
> 它的目标是让 `Search(v)`、`Insert(v)`、`Remove(v)` 以及 ordered operations 尽量高效。

## Table ADT Operations

基础操作：

- `Search(v)`: 判断 `v` 是否存在。
- `Insert(v)`: 插入 `v`。
- `Remove(v)`: 删除 `v`。

有序 Table ADT 还经常需要：

- `Min()` / `Max()`
- `Successor(v)` / `Predecessor(v)`
- sorted order traversal
- `Rank(v)` / `Select(k)`

这些 ordered operations 是 BST 相比 Hash Table 仍然重要的核心原因。

## Array / Vector Choices

| Implementation | Search | Insert | Remove | Ordered operations |
|---|---:|---:|---:|---|
| unsorted array | `O(N)` | `O(1)` append | `O(N)` | `Min/Max O(N)`, sorted listing needs sorting |
| sorted array | `O(log N)` | `O(N)` shift | `O(N)` shift | `Min/Max O(1)`, successor via binary search `O(log N)`, sorted listing `O(N)` |
| linked list | `O(N)` | usually `O(1)` if unordered | `O(N)` search first | random access is bad, binary search is not practical |
| hash table | expected `O(1)` | expected `O(1)` | expected `O(1)` | ordered operations are not naturally supported |
| balanced BST | `O(log N)` | `O(log N)` | `O(log N)` | ordered operations are naturally supported |

## Why Not Just Use Hash Table?

Hash Table 对基础三操作非常快，但它不保留 key 的顺序，所以以下操作不自然：

- 找最小值、最大值
- 找前驱、后继
- 按升序列出全部元素
- 查询第 `k` 小或某个 key 的 rank

如果题目只问 membership，Hash Table 通常很好；如果题目要求顺序信息，balanced BST 更合适。

## Static vs Dynamic

- **Static data structure**: 更新很少时才高效，例如排序数组适合查询多、插入删除少的场景。
- **Dynamic data structure**: 面对大量插入删除仍然高效，例如 BST，尤其是 AVL / Red-Black Tree。

## Links

- Back to [[Binary Search Tree]]
- Next: [[02 BST Core Concepts]]
- Related: [[05 Complexity and Height]]
- Related: [[08 Traversal Rank Select]]
