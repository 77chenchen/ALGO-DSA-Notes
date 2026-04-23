# Cpp BST Query Operations

> [!summary]
> 这一页实现 BST 的只读查询：`contains/count/min/max/lowerBound/successor/predecessor/rank/select` 和三种 traversal。

## Contains and Count

```cpp
    bool contains(int key) const {
        return findNode(key) != nullptr;
    }

    int count(int key) const {
        Node* node = findNode(key);
        return node == nullptr ? 0 : node->freq;
    }
```

`contains` 只看 key 是否存在，`count` 返回这个 key 的频率。

## Min and Max

```cpp
    optional<int> min() const {
        Node* node = minNode(root);
        if (node == nullptr) return nullopt;
        return node->key;
    }

    optional<int> max() const {
        Node* node = maxNode(root);
        if (node == nullptr) return nullopt;
        return node->key;
    }
```

空树没有最小值和最大值，所以返回 `optional<int>`。

## lowerBound

```cpp
    optional<int> lowerBound(int key) const {
        Node* cur = root;
        optional<int> answer;

        while (cur != nullptr) {
            if (cur->key >= key) {
                answer = cur->key;
                cur = cur->left;
            } else {
                cur = cur->right;
            }
        }

        return answer;
    }
```

遇到 `cur->key >= key` 时，当前节点可能是答案，但左子树里可能有更小的合法答案。

## Successor and Predecessor

```cpp
    optional<int> successor(int key) const {
        Node* cur = root;
        optional<int> answer;

        while (cur != nullptr) {
            if (cur->key > key) {
                answer = cur->key;
                cur = cur->left;
            } else {
                cur = cur->right;
            }
        }

        return answer;
    }

    optional<int> predecessor(int key) const {
        Node* cur = root;
        optional<int> answer;

        while (cur != nullptr) {
            if (cur->key < key) {
                answer = cur->key;
                cur = cur->right;
            } else {
                cur = cur->left;
            }
        }

        return answer;
    }
```

这里没有依赖 parent pointer，而是从 root 出发一路维护候选答案。

## Rank and orderOfKey

```cpp
    int orderOfKey(int key) const {
        int result = 0;
        Node* cur = root;

        while (cur != nullptr) {
            if (key <= cur->key) {
                cur = cur->left;
            } else {
                result += sz(cur->left) + cur->freq;
                cur = cur->right;
            }
        }

        return result;
    }

    int rank(int key) const {
        Node* cur = root;
        int before = 0;

        while (cur != nullptr) {
            if (key < cur->key) {
                cur = cur->left;
            } else if (key > cur->key) {
                before += sz(cur->left) + cur->freq;
                cur = cur->right;
            } else {
                return before + sz(cur->left) + 1;
            }
        }

        return -1;
    }
```

区别：

- `orderOfKey(key)` 返回严格小于 `key` 的元素数量。
- `rank(key)` 返回 `key` 第一次出现的位置，不存在则返回 `-1`。

## Select

```cpp
    optional<int> select(int kth) const {
        if (kth < 1 || kth > totalSize()) return nullopt;

        Node* cur = root;
        while (cur != nullptr) {
            int leftSize = sz(cur->left);

            if (kth <= leftSize) {
                cur = cur->left;
            } else if (kth <= leftSize + cur->freq) {
                return cur->key;
            } else {
                kth -= leftSize + cur->freq;
                cur = cur->right;
            }
        }

        return nullopt;
    }
```

`select(k)` 返回第 `k` 小元素，`k` 是 1-based，并且重复元素会计入排名。

## Traversal Helpers

```cpp
private:
    static void collectInorder(Node* node, vector<int>& out) {
        if (node == nullptr) return;
        collectInorder(node->left, out);
        for (int i = 0; i < node->freq; ++i) out.push_back(node->key);
        collectInorder(node->right, out);
    }

    static void collectPreorder(Node* node, vector<int>& out) {
        if (node == nullptr) return;
        for (int i = 0; i < node->freq; ++i) out.push_back(node->key);
        collectPreorder(node->left, out);
        collectPreorder(node->right, out);
    }

    static void collectPostorder(Node* node, vector<int>& out) {
        if (node == nullptr) return;
        collectPostorder(node->left, out);
        collectPostorder(node->right, out);
        for (int i = 0; i < node->freq; ++i) out.push_back(node->key);
    }

    static void collectItems(Node* node, vector<pair<int, int>>& out) {
        if (node == nullptr) return;
        collectItems(node->left, out);
        out.push_back({node->key, node->freq});
        collectItems(node->right, out);
    }
```

`items()` 只输出 distinct key 和频率，适合浏览 multiset 结构。

## Public Traversal APIs

```cpp
public:
    vector<int> inorder() const {
        vector<int> result;
        collectInorder(root, result);
        return result;
    }

    vector<int> preorder() const {
        vector<int> result;
        collectPreorder(root, result);
        return result;
    }

    vector<int> postorder() const {
        vector<int> result;
        collectPostorder(root, result);
        return result;
    }

    vector<pair<int, int>> items() const {
        vector<pair<int, int>> result;
        collectItems(root, result);
        return result;
    }

    bool isValidBST() const {
        int computedHeight = -1;
        int computedSize = 0;
        return validate(root, LLONG_MIN, LLONG_MAX, nullptr, computedHeight, computedSize);
    }
};
```

在真实代码里，查询函数和 [[09-3 Cpp BST Insert and Remove]] 的更新函数同属 `BinarySearchTree`。这里按阅读主题拆开，避免一页代码太长。

## Links

- Back to [[09 Complete Cpp Implementation]]
- Previous: [[09-1 Cpp Node and Utilities]]
- Next: [[09-3 Cpp BST Insert and Remove]]
- Related: [[03 BST Query Operations]]
- Related: [[08 Traversal Rank Select]]
