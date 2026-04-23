# AVL Rotations

> [!summary]
> Rotation 是 AVL 恢复平衡的核心操作。  
> 它只改变局部父子关系，不改变 inorder 顺序，因此不会破坏 BST property。

> [!tip] Visual Legend
> Red = 原局部 root，Teal = 旋转后的局部 root，Yellow = 被移动到另一侧的中间子树，Blue = 不受影响的普通子树。

## rotateLeft(T)

前提：`T.right != null`。

```mermaid
flowchart LR
    subgraph Before["Before: T is right-heavy"]
        direction TB
        T1(("T"))
        W1(("W"))
        C1["C<br/>subtree"]
        E1["E<br/>subtree"]

        T1 -->|right| W1
        W1 -->|left| C1
        W1 -->|right| E1
    end

    T1 -.->|rotateLeft T| W2

    subgraph After["After: W becomes local root"]
        direction TB
        W2(("W"))
        T2(("T"))
        C2["C<br/>subtree"]
        E2["E<br/>subtree"]

        W2 -->|left| T2
        W2 -->|right| E2
        T2 -->|right| C2
    end

    classDef oldRoot fill:#ef4444,stroke:#7f1d1d,color:#fff,stroke-width:2px;
    classDef newRoot fill:#14b8a6,stroke:#134e4a,color:#fff,stroke-width:2px;
    classDef moved fill:#facc15,stroke:#a16207,color:#111827,stroke-width:2px;
    classDef subtree fill:#e0f2fe,stroke:#0284c7,color:#0c4a6e,stroke-width:1px,stroke-dasharray: 5 4;

    class T1,T2 oldRoot;
    class W1,W2 newRoot;
    class C1,C2 moved;
    class E1,E2 subtree;
```

`C` 会从 `W.left` 变成 `T.right`。由于原本有 `T < C < W`，旋转后顺序仍然合法。

> [!tip] Implementation Jump
> 完整代码看 [[09-4 Cpp AVL Rotations and Rebalance#rotateLeft|C++ rotateLeft]]。

## rotateRight(T)

前提：`T.left != null`。

```mermaid
flowchart LR
    subgraph Before["Before: T is left-heavy"]
        direction TB
        T1(("T"))
        W1(("W"))
        A1["A<br/>subtree"]
        C1["C<br/>subtree"]

        T1 -->|left| W1
        W1 -->|left| A1
        W1 -->|right| C1
    end

    T1 -.->|rotateRight T| W2

    subgraph After["After: W becomes local root"]
        direction TB
        W2(("W"))
        A2["A<br/>subtree"]
        T2(("T"))
        C2["C<br/>subtree"]

        W2 -->|left| A2
        W2 -->|right| T2
        T2 -->|left| C2
    end

    classDef oldRoot fill:#ef4444,stroke:#7f1d1d,color:#fff,stroke-width:2px;
    classDef newRoot fill:#14b8a6,stroke:#134e4a,color:#fff,stroke-width:2px;
    classDef moved fill:#facc15,stroke:#a16207,color:#111827,stroke-width:2px;
    classDef subtree fill:#e0f2fe,stroke:#0284c7,color:#0c4a6e,stroke-width:1px,stroke-dasharray: 5 4;

    class T1,T2 oldRoot;
    class W1,W2 newRoot;
    class C1,C2 moved;
    class A1,A2 subtree;
```

这是 `rotateLeft` 的镜像。

> [!tip] Implementation Jump
> 完整代码看 [[09-4 Cpp AVL Rotations and Rebalance#rotateRight|C++ rotateRight]]。

## Four AVL Cases

| Case | Condition | Fix |
|---|---|---|
| Left Left | `bf(x) = +2` and `bf(x.left) >= 0` | `rotateRight(x)` |
| Left Right | `bf(x) = +2` and `bf(x.left) < 0` | `rotateLeft(x.left)`, then `rotateRight(x)` |
| Right Right | `bf(x) = -2` and `bf(x.right) <= 0` | `rotateLeft(x)` |
| Right Left | `bf(x) = -2` and `bf(x.right) > 0` | `rotateRight(x.right)`, then `rotateLeft(x)` |

插入时常见教材会写成 `+1/-1` 判断；实现中用 `>= 0`、`<= 0` 可以同时覆盖删除后的边界情况。

```mermaid
flowchart TB
    subgraph LL["Left Left"]
        direction TB
        Z1(("z<br/>bf = +2"))
        Y1(("y<br/>left-heavy"))
        X1(("x"))
        F1["Fix:<br/>rotateRight z"]

        Z1 -->|left| Y1
        Y1 -->|left| X1
        X1 -.-> F1
    end

    subgraph LRCase["Left Right"]
        direction TB
        Z2(("z<br/>bf = +2"))
        Y2(("y<br/>right-heavy"))
        X2(("x"))
        F2["Fix:<br/>rotateLeft y<br/>then rotateRight z"]

        Z2 -->|left| Y2
        Y2 -->|right| X2
        X2 -.-> F2
    end

    subgraph RR["Right Right"]
        direction TB
        Z3(("z<br/>bf = -2"))
        Y3(("y<br/>right-heavy"))
        X3(("x"))
        F3["Fix:<br/>rotateLeft z"]

        Z3 -->|right| Y3
        Y3 -->|right| X3
        X3 -.-> F3
    end

    subgraph RL["Right Left"]
        direction TB
        Z4(("z<br/>bf = -2"))
        Y4(("y<br/>left-heavy"))
        X4(("x"))
        F4["Fix:<br/>rotateRight y<br/>then rotateLeft z"]

        Z4 -->|right| Y4
        Y4 -->|left| X4
        X4 -.-> F4
    end

    classDef bad fill:#ef4444,stroke:#7f1d1d,color:#fff,stroke-width:2px;
    classDef child fill:#f97316,stroke:#9a3412,color:#fff,stroke-width:2px;
    classDef pivot fill:#facc15,stroke:#a16207,color:#111827,stroke-width:2px;
    classDef fix fill:#dcfce7,stroke:#16a34a,color:#14532d,stroke-width:1px;

    class Z1,Z2,Z3,Z4 bad;
    class Y1,Y2,Y3,Y4 child;
    class X1,X2,X3,X4 pivot;
    class F1,F2,F3,F4 fix;
```

> [!tip] Implementation Jump
> 四种 case 的统一处理看 [[09-4 Cpp AVL Rotations and Rebalance#Rebalance|C++ Rebalance]]。

## Rotation Pseudocode

```cpp
Node* rotateLeft(Node* x) {
    Node* y = x->right;
    Node* beta = y->left;

    y->parent = x->parent;
    x->right = beta;
    if (beta != nullptr) beta->parent = x;

    y->left = x;
    x->parent = y;

    refresh(x);
    refresh(y);
    return y;
}
```

真实代码还必须处理：

- `x` 原来是不是 root。
- `x` 是 parent 的 left child 还是 right child。
- rotation 后更新 height 和 size。

完整实现见 [[09-4 Cpp AVL Rotations and Rebalance#rotateLeft|rotateLeft]]、[[09-4 Cpp AVL Rotations and Rebalance#rotateRight|rotateRight]] 和 [[09-4 Cpp AVL Rotations and Rebalance#Rebalance|rebalanceFrom]]。

## Insert vs Remove

- AVL insert: 第一个失衡点修好后，整棵树通常就恢复平衡。
- AVL remove: 删除可能让多个祖先连续变矮，所以可能一路触发多个 rotation。

## Links

- Back to [[Binary Search Tree]]
- Previous: [[06 AVL Tree and Balance]]
- Next: [[08 Traversal Rank Select]]
- Related: [[05 Complexity and Height]]
- Related: [[BST AVL Cheat Sheet]]
