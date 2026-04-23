# Cpp Example and Notes

> [!summary]
> 这一页给出辅助打印函数和一个使用示例。  
> 它主要用于理解 API 怎么调用，不要求真的在 Obsidian 里运行。

## Print Helpers

```cpp
void printOptional(const string& label, optional<int> value) {
    cout << label << ": ";
    if (value.has_value()) {
        cout << *value << '\n';
    } else {
        cout << "none\n";
    }
}

void printVector(const string& label, const vector<int>& values) {
    cout << label << ":";
    for (int value : values) cout << ' ' << value;
    cout << '\n';
}

void printItems(const string& label, const vector<pair<int, int>>& items) {
    cout << label << ":";
    for (auto [key, freq] : items) {
        cout << ' ' << key;
        if (freq > 1) cout << '-' << freq;
    }
    cout << '\n';
}
```

`items()` 的展示方式和 VisuAlgo 类似：重复 key 写成 `key-frequency`。

## Example Usage

```cpp
int main() {
    AVLTree tree;
    vector<int> values = {15, 6, 23, 4, 7, 71, 5, 50, 7, 15, 15, 60, 37};

    for (int value : values) tree.insert(value);

    cout << boolalpha;
    cout << "valid AVL: " << tree.isAVL() << '\n';
    cout << "height: " << tree.height() << '\n';
    cout << "total size including duplicates: " << tree.totalSize() << '\n';
    cout << "distinct size: " << tree.distinctSize() << '\n';

    printItems("items as key-frequency", tree.items());
    printVector("inorder", tree.inorder());
    printVector("preorder", tree.preorder());
    printVector("postorder", tree.postorder());

    cout << "contains 23: " << tree.contains(23) << '\n';
    cout << "frequency of 15: " << tree.count(15) << '\n';

    printOptional("min", tree.min());
    printOptional("max", tree.max());
    printOptional("lower_bound(21)", tree.lowerBound(21));
    printOptional("successor(23)", tree.successor(23));
    printOptional("predecessor(23)", tree.predecessor(23));

    cout << "rank(15): " << tree.rank(15) << '\n';
    cout << "orderOfKey(23): " << tree.orderOfKey(23) << '\n';
    printOptional("select(5)", tree.select(5));

    tree.erase(15);
    tree.erase(7);
    tree.erase(23);

    cout << "\nafter erase 15, 7, 23\n";
    cout << "valid AVL: " << tree.isAVL() << '\n';
    printItems("items as key-frequency", tree.items());
    printVector("inorder", tree.inorder());

    return 0;
}
```

## Expected Mental Output

你不需要运行它，只需要知道这些 API 会展示：

- 树是否仍然是合法 AVL。
- 当前高度、总元素数、distinct key 数。
- inorder 是否有序。
- 重复 key 是否通过 `freq` 体现。
- 删除后结构是否仍然平衡。

## Reading Checklist

读完这一组实现笔记后，建议确认自己能回答：

- 为什么重复 key 用 `freq`，而不是新建一个相同 key 的节点。
- 为什么 `rank/select` 需要维护 subtree `size`。
- 为什么删除两个孩子的节点可以用 successor 替换。
- 为什么 AVL rotation 不会破坏 inorder 顺序。
- 为什么 AVL 删除可能一路向上触发多次 rebalance。

## Links

- Back to [[09 Complete Cpp Implementation]]
- Previous: [[09-4 Cpp AVL Rotations and Rebalance]]
- Related: [[08 Traversal Rank Select]]
- Related: [[BST AVL Cheat Sheet]]
