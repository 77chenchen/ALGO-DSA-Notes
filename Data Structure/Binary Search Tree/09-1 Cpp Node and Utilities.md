# Cpp Node and Utilities

> [!summary]
> 这一页是 C++ 实现的基础部分：头文件、节点结构、`height/size` 维护、查找节点、最小最大节点，以及析构辅助函数。

## Headers

```cpp
#include <algorithm>
#include <climits>
#include <cmath>
#include <iostream>
#include <optional>
#include <string>
#include <utility>
#include <vector>

using namespace std;
```

## Class Skeleton and Node

```cpp
class BinarySearchTree {
protected:
    struct Node {
        int key;
        int freq;
        int height;
        int size;
        Node* left;
        Node* right;
        Node* parent;

        Node(int value, Node* p)
            : key(value),
              freq(1),
              height(0),
              size(1),
              left(nullptr),
              right(nullptr),
              parent(p) {}
    };

    Node* root = nullptr;
```

字段含义：

- `key`: 当前节点保存的值。
- `freq`: 这个 key 出现的次数，用于 multiset。
- `height`: AVL 需要的高度。
- `size`: 子树元素总数，包含重复频率，用于 `rank/select`。
- `left/right/parent`: 树结构指针。

## Height and Size Helpers

```cpp
    static int h(Node* node) {
        return node == nullptr ? -1 : node->height;
    }

    static int sz(Node* node) {
        return node == nullptr ? 0 : node->size;
    }

    static void refresh(Node* node) {
        if (node == nullptr) return;
        node->height = std::max(h(node->left), h(node->right)) + 1;
        node->size = sz(node->left) + node->freq + sz(node->right);
    }

    void refreshPath(Node* node) {
        while (node != nullptr) {
            refresh(node);
            node = node->parent;
        }
    }
```

这里 `height(null) = -1`，所以 leaf 的高度会自然变成 `0`。

## Internal Search Helpers

```cpp
    Node* findNode(int key) const {
        Node* cur = root;
        while (cur != nullptr) {
            if (key == cur->key) return cur;
            cur = key < cur->key ? cur->left : cur->right;
        }
        return nullptr;
    }

    Node* minNode(Node* node) const {
        if (node == nullptr) return nullptr;
        while (node->left != nullptr) node = node->left;
        return node;
    }

    Node* maxNode(Node* node) const {
        if (node == nullptr) return nullptr;
        while (node->right != nullptr) node = node->right;
        return node;
    }
```

`findNode` 是很多操作的基础。`minNode` 和 `maxNode` 分别一路向左、一路向右。

## Transplant

```cpp
    void transplant(Node* oldRoot, Node* newRoot) {
        if (oldRoot->parent == nullptr) {
            root = newRoot;
        } else if (oldRoot == oldRoot->parent->left) {
            oldRoot->parent->left = newRoot;
        } else {
            oldRoot->parent->right = newRoot;
        }

        if (newRoot != nullptr) newRoot->parent = oldRoot->parent;
    }
```

`transplant` 的作用是用 `newRoot` 替换 `oldRoot` 在树中的位置，删除 Case 2 和 Case 3 都会用到。

## Hooks for AVL

```cpp
    virtual void fixAfterInsert(Node* node) {
        refreshPath(node);
    }

    virtual void fixAfterErase(Node* node) {
        refreshPath(node);
    }

    virtual void fixAfterFreqChange(Node* node) {
        refreshPath(node);
    }
```

普通 BST 只需要更新路径上的 `height/size`。AVL 会 override 这些 hook，加入 rebalance。

## Private Utility Functions

```cpp
private:
    static void destroy(Node* node) {
        if (node == nullptr) return;
        destroy(node->left);
        destroy(node->right);
        delete node;
    }

    static int countDistinct(Node* node) {
        if (node == nullptr) return 0;
        return 1 + countDistinct(node->left) + countDistinct(node->right);
    }
```

`destroy` 使用 postorder，先删子树，再删当前节点。

## Validation Helper

```cpp
    bool validate(Node* node,
                  long long low,
                  long long high,
                  Node* expectedParent,
                  int& computedHeight,
                  int& computedSize) const {
        if (node == nullptr) {
            computedHeight = -1;
            computedSize = 0;
            return true;
        }

        if (node->key <= low || node->key >= high) return false;
        if (node->parent != expectedParent || node->freq <= 0) return false;

        int leftHeight = -1;
        int rightHeight = -1;
        int leftSize = 0;
        int rightSize = 0;

        if (!validate(node->left, low, node->key, node, leftHeight, leftSize)) {
            return false;
        }
        if (!validate(node->right, node->key, high, node, rightHeight, rightSize)) {
            return false;
        }

        computedHeight = std::max(leftHeight, rightHeight) + 1;
        computedSize = leftSize + node->freq + rightSize;
        return node->height == computedHeight && node->size == computedSize;
    }
```

这个函数同时检查：

- BST ordering 是否正确。
- parent pointer 是否正确。
- `height` 是否维护正确。
- `size` 是否维护正确。

## Constructor and Basic APIs

```cpp
public:
    BinarySearchTree() = default;

    virtual ~BinarySearchTree() {
        clear();
    }

    BinarySearchTree(const BinarySearchTree&) = delete;
    BinarySearchTree& operator=(const BinarySearchTree&) = delete;

    bool empty() const {
        return root == nullptr;
    }

    int height() const {
        return h(root);
    }

    int totalSize() const {
        return sz(root);
    }

    int distinctSize() const {
        return countDistinct(root);
    }

    void clear() {
        destroy(root);
        root = nullptr;
    }
```

## Links

- Back to [[09 Complete Cpp Implementation]]
- Previous: [[09 Complete Cpp Implementation]]
- Next: [[09-2 Cpp BST Query Operations]]
- Related: [[02 BST Core Concepts]]
- Related: [[06 AVL Tree and Balance]]
