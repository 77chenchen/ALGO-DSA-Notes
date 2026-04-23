# AVL Tree and Balance

> [!summary]
> AVL Tree 是一种 self-balancing BST。  
> 它维护每个节点左右子树高度差不超过 `1`，因此高度是 `O(log N)`，大多数 BST 操作也变成 `O(log N)`。

## Height Definition

AVL 需要在每个节点上维护 `height`：

```text
height(null) = -1
height(leaf) = 0
height(x) = max(height(x.left), height(x.right)) + 1
```

更新节点时，只需要从插入或删除位置一路向 root 回溯，并重新计算路径上的 height。

## Balance Factor

```text
bf(x) = height(x.left) - height(x.right)
```

AVL invariant：

```text
abs(bf(x)) <= 1 for every node x
```

如果某个节点出现：

- `bf(x) = 2`: 左边太高。
- `bf(x) = -2`: 右边太高。

就需要通过 rotation 恢复平衡。

## Why AVL Is O(log N)

令 `N_h` 表示高度为 `h` 的 AVL Tree 至少需要多少节点。最小规模 AVL 的根必须有两个尽量矮但仍满足平衡的子树：

```text
N_h = 1 + N_(h-1) + N_(h-2)
```

因此 `N_h` 按 Fibonacci-like 速度增长。VisuAlgo 的简化证明给出：

```text
h < 2 * log2(N)
```

所以所有 `O(h)` 的 BST 操作，在 AVL 中都是 `O(log N)`。

## Insert in AVL

步骤：

1. 先按普通 BST 插入。
2. 从插入点一路向 root 回溯。
3. 更新每个经过节点的 height。
4. 找到第一个失衡节点。
5. 用 [[07 AVL Rotations]] 中的四种情况之一恢复平衡。

一次 AVL 插入最多需要常数次 rotation，但仍需要 `O(log N)` 回溯。

## Remove in AVL

步骤：

1. 先按普通 BST 删除。
2. 从删除影响的位置一路向 root 回溯。
3. 每个失衡节点都要修复。

和插入不同，删除可能触发多次 rebalance，但最多沿高度走 `O(log N)` 层。

## Links

- Back to [[Binary Search Tree]]
- Previous: [[05 Complexity and Height]]
- Next: [[07 AVL Rotations]]
- Related: [[04 BST Update Operations]]
- Related: [[09 Complete Cpp Implementation]]
