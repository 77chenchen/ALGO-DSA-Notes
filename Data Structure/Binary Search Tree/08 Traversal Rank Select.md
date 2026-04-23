# Traversal Rank Select

> [!summary]
> Traversal 用来按不同顺序访问整棵树。  
> 如果额外维护 subtree size，BST / AVL 还能支持 `Rank(v)` 和 `Select(k)`。

## Inorder Traversal

访问顺序：

```text
left subtree -> root -> right subtree
```

对 BST 做 inorder traversal 会得到升序序列。

复杂度：

- 按 distinct key 输出：`O(number of nodes)`。
- 按 multiset 全量输出：`O(total frequency)`。

## Preorder Traversal

访问顺序：

```text
root -> left subtree -> right subtree
```

Preorder 常用于序列化树结构。对于 key distinct 的 BST，只给 preorder 也可以恢复原 BST：

- 方法 1: 按 preorder 顺序重新插入空 BST。
- 方法 2: 用上下界递归重建，整体 `O(N)`。

## Postorder Traversal

访问顺序：

```text
left subtree -> right subtree -> root
```

Postorder 适合先处理孩子、再处理当前节点的场景，例如释放整棵树。

对于 key distinct 的 BST，也可以用 postorder 加上下界恢复原树。

## Rank(v)

`Rank(v)` 返回 `v` 在升序序列中的 1-based 位置。

如果维护：

```text
size(x) = size(x.left) + freq(x) + size(x.right)
```

则可以在 `O(h)` 内求 rank：

- 如果 `v < x.key`，去左子树。
- 如果 `v > x.key`，跳过 `x.left` 和 `x.freq`，去右子树。
- 如果 `v == x.key`，答案是已跳过数量加 `size(x.left) + 1`。

如果 key 有重复，`Rank(v)` 通常返回第一个 `v` 的位置。

## Select(k)

`Select(k)` 返回第 `k` 小的 key。

假设当前节点为 `x`：

- 如果 `k <= size(x.left)`，去左子树。
- 如果 `k <= size(x.left) + freq(x)`，答案是 `x.key`。
- 否则跳过左子树和当前节点频率，去右子树。

这也是 `O(h)`；在 AVL 中就是 `O(log N)`。

## Links

- Back to [[Binary Search Tree]]
- Previous: [[07 AVL Rotations]]
- Next: [[09 Complete Cpp Implementation]]
- Related: [[03 BST Query Operations]]
- Related: [[BST AVL Cheat Sheet]]
