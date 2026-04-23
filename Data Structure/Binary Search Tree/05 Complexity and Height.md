# Complexity and Height

> [!summary]
> BST 的效率由高度 `h` 决定。  
> 普通 BST 最坏可以退化成链，AVL 通过旋转把高度维持在 `O(log N)`。

## O(h) Operations

以下操作通常都是 `O(h)`：

- `Search(v)`
- `lower_bound(v)`
- `Min()` / `Max()`
- `Successor(v)` / `Predecessor(v)`
- `Insert(v)`
- `Remove(v)`

其中 `h` 是 root 到最深 leaf 的边数。

## Inorder Is O(N)

`Inorder Traversal` 是 `O(N)`，不取决于树高。

原因很简单：不管树形如何，要输出全部元素就必须访问每个节点一次。如果考虑重复频率并输出重复元素，则复杂度是 `O(total frequency)`。

## Lower Bound

如果 BST 完全平衡，高度接近：

```text
h >= log2(N + 1) - 1
```

推导来自满二叉树节点数：

```text
N <= 1 + 2 + 4 + ... + 2^h = 2^(h + 1) - 1
```

所以最理想情况下，BST 高度是 `Omega(log N)`。

## Upper Bound

如果按升序插入：

```text
1, 2, 3, 4, 5, ...
```

普通 BST 会退化成 skewed right，形状像链表，高度为：

```text
h = N - 1
```

如果按降序插入，则会得到 skewed left。

## Random BST

随机插入构造的 BST 期望高度通常是 `O(log N)`，但这不是严格保证。对需要稳定最坏复杂度的场景，应使用 AVL、Red-Black Tree 等 balanced BST。

## BST Sort

把 `N` 个元素插入 BST，再做 inorder traversal 可以得到有序序列：

- 随机或平衡情况下，插入总成本约 `O(N log N)`。
- 最坏情况下，普通 BST 插入总成本是 `O(N^2)`。

实际排序中通常更常用 quicksort、mergesort、heapsort 或语言内建排序。

## Links

- Back to [[Binary Search Tree]]
- Previous: [[04 BST Update Operations]]
- Next: [[06 AVL Tree and Balance]]
- Related: [[07 AVL Rotations]]
- Related: [[BST AVL Cheat Sheet]]
