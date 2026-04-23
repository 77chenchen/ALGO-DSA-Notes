# Binary Search Tree

> [!summary]
> **Binary Search Tree (BST)** 用二叉树维护有序 key。  
> 对任意节点 `x`，左子树所有 key 都 `< x.key`，右子树所有 key 都 `> x.key`。  
> 这组笔记同时覆盖普通 BST、带频率的 multiset BST、AVL Tree、常见操作复杂度，以及一份完整 C++ 实现。

## Table of Contents

1. [[01 Table ADT and Motivation|Table ADT and Motivation]]
2. [[02 BST Core Concepts|BST Core Concepts]]
3. [[03 BST Query Operations|BST Query Operations]]
4. [[04 BST Update Operations|BST Update Operations]]
5. [[05 Complexity and Height|Complexity and Height]]
6. [[06 AVL Tree and Balance|AVL Tree and Balance]]
7. [[07 AVL Rotations|AVL Rotations]]
8. [[08 Traversal Rank Select|Traversal Rank Select]]
9. [[09 Complete Cpp Implementation|Complete Cpp Implementation]]
10. [[BST AVL Cheat Sheet|BST AVL Cheat Sheet]]

## C++ Implementation Notes

- [[09-1 Cpp Node and Utilities]]
- [[09-2 Cpp BST Query Operations]]
- [[09-3 Cpp BST Insert and Remove]]
- [[09-4 Cpp AVL Rotations and Rebalance]]
- [[09-5 Cpp Example and Notes]]

## Learning Map

- 如果目标是理解为什么要学 BST，先看 [[01 Table ADT and Motivation]]。
- 如果目标是会写普通 BST，重点看 [[02 BST Core Concepts]]、[[03 BST Query Operations]]、[[04 BST Update Operations]]。
- 如果目标是掌握复杂度边界，重点看 [[05 Complexity and Height]]。
- 如果目标是掌握 AVL，重点看 [[06 AVL Tree and Balance]]、[[07 AVL Rotations]]。
- 如果目标是做竞赛题，最后看 [[08 Traversal Rank Select]] 和 [[09 Complete Cpp Implementation]]。

## Core Invariants

- **BST property**: left subtree `< root <` right subtree。
- **Multiset frequency**: 重复 key 不新建节点，而是在对应节点上维护 `freq`。
- **Height**: `height(null) = -1`，`height(x) = max(height(left), height(right)) + 1`。
- **AVL balance factor**: `bf(x) = height(left) - height(right)`，AVL 要求每个节点的 `bf` 都在 `[-1, 1]`。
- **Order statistics**: 如果要支持 `rank` 和 `select`，需要额外维护 `size = left.size + freq + right.size`。

## Related

- [[01 Table ADT and Motivation]]
- [[BST AVL Cheat Sheet]]
- [[09 Complete Cpp Implementation]]
