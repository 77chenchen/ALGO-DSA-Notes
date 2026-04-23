# Cpp BST Insert and Remove

> [!summary]
> 这一页实现 BST 的结构修改：插入、删除，以及删除的三种情况。  
> 为了便于阅读，这些函数属于 [[09-1 Cpp Node and Utilities]] 中的 `BinarySearchTree` 类。

## Insert

```cpp
    void insert(int key) {
        if (root == nullptr) {
            root = new Node(key, nullptr);
            return;
        }

        Node* cur = root;
        Node* parent = nullptr;
        while (cur != nullptr) {
            parent = cur;
            if (key == cur->key) {
                ++cur->freq;
                fixAfterFreqChange(cur);
                return;
            }
            cur = key < cur->key ? cur->left : cur->right;
        }

        Node* node = new Node(key, parent);
        if (key < parent->key) {
            parent->left = node;
        } else {
            parent->right = node;
        }
        fixAfterInsert(node);
    }
```

插入逻辑：

- 空树时，新节点直接成为 root。
- key 已存在时，只做 `freq++`。
- key 不存在时，沿搜索路径找到 `null` 位置并新建节点。
- 最后调用 `fixAfterInsert`，普通 BST 更新高度和大小，AVL 会进一步 rebalance。

## Remove Overview

```cpp
    bool erase(int key) {
        Node* node = findNode(key);
        if (node == nullptr) return false;

        if (node->freq > 1) {
            --node->freq;
            fixAfterFreqChange(node);
            return true;
        }

        Node* rebalanceStart = nullptr;
```

删除先处理两个简单情况：

- key 不存在：返回 `false`。
- `freq > 1`：只减少频率，不改变树结构。

## Remove Case 1 and Case 2

```cpp
        if (node->left == nullptr) {
            rebalanceStart = node->parent;
            transplant(node, node->right);
            delete node;
        } else if (node->right == nullptr) {
            rebalanceStart = node->parent;
            transplant(node, node->left);
            delete node;
        }
```

这里同时覆盖：

- Case 1 leaf: 左右孩子都是 `nullptr`，等价于用 `nullptr` 替换自己。
- Case 2 one child: 用唯一的 child 替换自己。

## Remove Case 3

```cpp
        else {
            Node* successor = minNode(node->right);

            node->key = successor->key;
            node->freq = successor->freq;

            rebalanceStart = successor->parent;
            transplant(successor, successor->right);
            delete successor;
        }

        fixAfterErase(rebalanceStart);
        return true;
    }
```

两个孩子时：

- 找右子树最小节点 `successor`。
- 把 successor 的 `key/freq` 搬到当前节点。
- 删除原 successor 节点。

注意 successor 最多只有一个右孩子，因此物理删除 successor 会落回 Case 1 或 Case 2。

## Why Move freq Together?

因为这份实现采用 multiset 语义：

```text
one distinct key = one tree node
```

如果只移动 `key` 不移动 `freq`，会导致两个节点代表同一个 key，破坏封装。

## Complexity

- 普通 BST: `O(h)`。
- AVL Tree: 因为 `h = O(log N)`，所以是 `O(log N)`。

## Links

- Back to [[09 Complete Cpp Implementation]]
- Previous: [[09-2 Cpp BST Query Operations]]
- Next: [[09-4 Cpp AVL Rotations and Rebalance]]
- Related: [[04 BST Update Operations]]
- Related: [[06 AVL Tree and Balance]]
