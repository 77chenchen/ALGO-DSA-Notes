# BST AVL Cheat Sheet

> [!summary]
> 一页速查：BST 靠 ordering，AVL 靠 height balance。  
> 普通 BST 操作是 `O(h)`，AVL 把 `h` 控制在 `O(log N)`。

## BST Property

```text
all keys in left subtree < root.key < all keys in right subtree
```

重复 key 推荐用：

```text
node.freq += 1
```

## Operation Complexity

| Operation | BST | AVL |
|---|---:|---:|
| Search | `O(h)` | `O(log N)` |
| lower_bound | `O(h)` | `O(log N)` |
| Min / Max | `O(h)` | `O(log N)` |
| Successor / Predecessor | `O(h)` | `O(log N)` |
| Insert | `O(h)` | `O(log N)` |
| Remove | `O(h)` | `O(log N)` |
| Inorder traversal | `O(N)` | `O(N)` |
| Rank / Select with `size` | `O(h)` | `O(log N)` |

## Delete Cases

- leaf: 直接删除。
- one child: child 接到 parent 上。
- two children: 用 successor 或 predecessor 替换，再删除被搬走的节点。

## AVL Invariant

```text
height(null) = -1
height(leaf) = 0
bf(x) = height(x.left) - height(x.right)
abs(bf(x)) <= 1
```

## Rotation Cases

- LL: `rotateRight(x)`
- LR: `rotateLeft(x.left)`, then `rotateRight(x)`
- RR: `rotateLeft(x)`
- RL: `rotateRight(x.right)`, then `rotateLeft(x)`

## Common Pitfalls

- 删除有两个孩子的节点时，不要破坏 BST ordering。
- AVL rotation 后必须更新 parent pointer。
- rotation 后先更新较低节点的 height / size，再更新新的子树 root。
- 支持 duplicate 时，删除要先处理 `freq--`。
- `rank/select` 必须维护 subtree `size`，否则会退化成 traversal。

## Links

- Back to [[Binary Search Tree]]
- Related: [[03 BST Query Operations]]
- Related: [[04 BST Update Operations]]
- Related: [[07 AVL Rotations]]
- Related: [[09 Complete Cpp Implementation]]
