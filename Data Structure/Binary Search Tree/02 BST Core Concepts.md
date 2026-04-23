# BST Core Concepts

> [!summary]
> BST 是一棵二叉树，每个节点最多两个孩子，并通过大小关系组织 key。  
> 它把「比较大小」这件事编码进树结构，因此可以像 binary search 一样沿树向下搜索。

## BST Property

对任意节点 `x`：

- 左子树所有 key 都严格小于 `x.key`。
- 右子树所有 key 都严格大于 `x.key`。
- 左右子树本身也必须是 BST。

如果允许重复值，常见做法有两种：

- 把重复值统一放到某一侧，例如 `<=` 放左边。
- 更推荐在节点中维护 `freq`，也就是这组笔记采用的 multiset 写法。

## Node Attributes

普通 BST 节点通常包含：

```cpp
key
freq
left
right
parent
height
size
```

其中：

- `key`: 用来排序的值。
- `freq`: 当前 key 出现次数，只有重复插入时大于 `1`。
- `left/right`: 左右孩子。
- `parent`: 父节点，方便向上找祖先和做旋转。
- `height`: AVL 需要，用于维护平衡。
- `size`: order statistics 需要，用于支持 `Rank` 和 `Select`。

## Parent Pointer Can Be Omitted

`parent` 不是必须的。可以用以下方式替代：

- 递归调用栈天然保存了从 root 到当前节点的路径。
- 插入、删除时额外传入父节点参数。
- 找 successor / predecessor 时，从 root 出发一路维护候选答案。

不过 AVL 旋转需要频繁重连父子关系，保留 `parent` 会让实现更直接。

## Root, Leaf, Internal Node

- `root`: 唯一没有 parent 的节点。
- `leaf`: 没有左右孩子的节点。
- `internal node`: 至少有一个孩子的节点。

特殊情况：只有一个节点的 BST 中，root 也可以被看成 leaf。

## Links

- Back to [[Binary Search Tree]]
- Previous: [[01 Table ADT and Motivation]]
- Next: [[03 BST Query Operations]]
- Related: [[09 Complete Cpp Implementation]]
