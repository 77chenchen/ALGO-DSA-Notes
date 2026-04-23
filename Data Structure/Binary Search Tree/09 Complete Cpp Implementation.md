# Complete Cpp Implementation

> [!summary]
> 这一组笔记把完整 C++ 实现拆成适合 Obsidian 阅读的 Markdown 代码块。  
> 不再依赖单独 `.cpp` 文件；每一页只讲一个模块，方便浏览、复习和回链。

## Table of Contents

1. [[09-1 Cpp Node and Utilities|Cpp Node and Utilities]]
2. [[09-2 Cpp BST Query Operations|Cpp BST Query Operations]]
3. [[09-3 Cpp BST Insert and Remove|Cpp BST Insert and Remove]]
4. [[09-4 Cpp AVL Rotations and Rebalance|Cpp AVL Rotations and Rebalance]]
5. [[09-5 Cpp Example and Notes|Cpp Example and Notes]]

## Implementation Scope

- `BinarySearchTree`: 普通 BST 的主体结构。
- `AVLTree`: 基于 BST 增加 rotation 和 rebalance。
- `freq`: 重复 key 只增加频率，不创建重复节点。
- `height`: AVL 平衡维护。
- `size`: 支持 `rank`、`select` 和 multiset size。
- `parent`: 支持删除、向上回溯和 rotation。
- `optional<int>`: 表示 `min/max/successor` 这类可能不存在的答案。

## Design Notes

- `lowerBound(v)` 返回第一个 `>= v` 的 key。
- `successor(v)` 返回严格大于 `v` 的最小 key。
- `predecessor(v)` 返回严格小于 `v` 的最大 key。
- `rank(v)` 返回 `v` 第一次出现的 1-based rank；不存在则返回 `-1`。
- `select(k)` 使用 1-based `k`，并把重复频率计入排名。
- 删除两个孩子的节点时，代码会把 successor 的 `key/freq` 整体搬到当前节点，然后物理删除 successor 节点，保持「每个 distinct key 只对应一个节点」。

## Reading Order

推荐按顺序读：

1. 先看 [[09-1 Cpp Node and Utilities]]，理解节点字段和维护函数。
2. 再看 [[09-2 Cpp BST Query Operations]]，理解所有只读操作。
3. 然后看 [[09-3 Cpp BST Insert and Remove]]，理解树结构如何变化。
4. 最后看 [[09-4 Cpp AVL Rotations and Rebalance]]，理解 AVL 如何恢复平衡。

## Links

- Back to [[Binary Search Tree]]
- Previous: [[08 Traversal Rank Select]]
- Next: [[09-1 Cpp Node and Utilities]]
- Related: [[04 BST Update Operations]]
- Related: [[06 AVL Tree and Balance]]
- Related: [[07 AVL Rotations]]
