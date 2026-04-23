# Cpp AVL Rotations and Rebalance

> [!summary]
> 这一页实现 AVL Tree：继承 `BinarySearchTree`，重写插入删除后的修复逻辑，并通过 rotation 保持平衡。

## Class Header

```cpp
class AVLTree : public BinarySearchTree {
protected:
    using Node = BinarySearchTree::Node;

    void fixAfterInsert(Node* node) override {
        rebalanceFrom(node);
    }

    void fixAfterErase(Node* node) override {
        rebalanceFrom(node);
    }

    void fixAfterFreqChange(Node* node) override {
        refreshPath(node);
    }
```

重复 key 只改变 `freq/size`，不会改变树形，所以不需要 rotation。

## Balance Factor

Concept: [[06 AVL Tree and Balance#Balance Factor|Balance Factor]]

```cpp
private:
    int balanceFactor(Node* node) const {
        return node == nullptr ? 0 : h(node->left) - h(node->right);
    }
```

AVL 要求每个节点的 balance factor 都在 `[-1, 1]`。

## rotateLeft

Concept: [[07 AVL Rotations#rotateLeft(T)|rotateLeft(T)]]

```cpp
    Node* rotateLeft(Node* node) {
        Node* pivot = node->right;
        Node* movedSubtree = pivot->left;

        pivot->parent = node->parent;
        if (node->parent == nullptr) {
            root = pivot;
        } else if (node == node->parent->left) {
            node->parent->left = pivot;
        } else {
            node->parent->right = pivot;
        }

        pivot->left = node;
        node->parent = pivot;
        node->right = movedSubtree;
        if (movedSubtree != nullptr) movedSubtree->parent = node;

        refresh(node);
        refresh(pivot);
        return pivot;
    }
```

先重连 parent，再移动中间子树，最后更新高度和大小。

## rotateRight

Concept: [[07 AVL Rotations#rotateRight(T)|rotateRight(T)]]

```cpp
    Node* rotateRight(Node* node) {
        Node* pivot = node->left;
        Node* movedSubtree = pivot->right;

        pivot->parent = node->parent;
        if (node->parent == nullptr) {
            root = pivot;
        } else if (node == node->parent->left) {
            node->parent->left = pivot;
        } else {
            node->parent->right = pivot;
        }

        pivot->right = node;
        node->parent = pivot;
        node->left = movedSubtree;
        if (movedSubtree != nullptr) movedSubtree->parent = node;

        refresh(node);
        refresh(pivot);
        return pivot;
    }
```

`rotateRight` 是 `rotateLeft` 的镜像。

## Rebalance

Concept: [[07 AVL Rotations#Four AVL Cases|Four AVL Cases]] and [[06 AVL Tree and Balance#Insert in AVL|Insert in AVL]]

```cpp
    void rebalanceFrom(Node* node) {
        while (node != nullptr) {
            refresh(node);

            if (balanceFactor(node) > 1) {
                if (balanceFactor(node->left) < 0) {
                    rotateLeft(node->left);
                }
                node = rotateRight(node);
            } else if (balanceFactor(node) < -1) {
                if (balanceFactor(node->right) > 0) {
                    rotateRight(node->right);
                }
                node = rotateLeft(node);
            }

            node = node->parent;
        }
    }
```

对应四种情况：

- LL: `balanceFactor(node) > 1` 且左孩子不右重，直接 `rotateRight(node)`。
- LR: 左孩子右重，先 `rotateLeft(node->left)`，再 `rotateRight(node)`。
- RR: `balanceFactor(node) < -1` 且右孩子不左重，直接 `rotateLeft(node)`。
- RL: 右孩子左重，先 `rotateRight(node->right)`，再 `rotateLeft(node)`。

## AVL Validation

```cpp
    bool isBalanced(Node* node) const {
        if (node == nullptr) return true;
        if (abs(balanceFactor(node)) > 1) return false;
        return isBalanced(node->left) && isBalanced(node->right);
    }

public:
    bool isAVL() const {
        return isValidBST() && isBalanced(root);
    }
};
```

`isAVL()` 同时检查：

- BST property。
- parent pointer。
- height 和 size。
- AVL balance invariant。

## Links

- Back to [[09 Complete Cpp Implementation]]
- Previous: [[09-3 Cpp BST Insert and Remove]]
- Next: [[09-5 Cpp Example and Notes]]
- Related: [[07 AVL Rotations]]
- Related: [[06 AVL Tree and Balance]]
