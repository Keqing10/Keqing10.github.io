---
title: 并查集
category_bar: true
math: true
tags: [数据结构]
category: [数据结构与算法]
date: 2025-04-06 19:34:58
---

## 并查集
并查集（Disjoint Set Union）是一种管理元素分组的数据结构，支持两种基本操作：查找元素属于哪个集合、合并两个集合。并查集通常使用**路径压缩**和**按秩合并**来优化性能。
### 实现思路
1. **初始化**：每个元素最初都是自己的父节点，秩（rank）为0。
2. **查找**：通过递归查找元素的根节点，并在查找过程中进行路径压缩。
3. **合并**：将两个集合的根节点合并，按秩合并以保持树的平衡。
### 使用数组的实现
已知有 $n$ 个元素并且用数 $0, 1, 2,\cdots, n-1$ 来表示。
```cpp
class DSU {
private:
    std::vector<int> parent;
    std::vector<int> rank;
public:
    DSU(int n) {
        parent.resize(n);
        rank.resize(n, 0);
        for (int i = 0; i < n; ++i) {
            parent[i] = i; // 每个元素的父节点初始化为自己
        }
    }
    // 查找操作，带路径压缩
    int find(int x) {
        if (parent[x] != x) {
            parent[x] = find(parent[x]); // 路径压缩
        }
        return parent[x];
    }
    // 合并操作，带按秩合并
    void unite(int x, int y) {
        int rootX = find(x);
        int rootY = find(y);
        if (rootX != rootY) {
            if (rank[rootX] < rank[rootY]) {
                parent[rootX] = rootY;
            } else if (rank[rootX] > rank[rootY]) {
                parent[rootY] = rootX;
            } else {
                parent[rootY] = rootX;
                rank[rootX]++;
            }
        }
    }
    // 检查两个元素是否属于同一集合
    bool isSameSet(int x, int y) {
        return find(x) == find(y);
    }
};
```
> *秩的意义*
> 秩表示树的深度（或高度）的一个**上界**，而不是精确的深度。秩的主要作用是指导合并操作，使得树尽量保持平衡。因此在代码中，查询并压缩路径时，即使树的高度可能减小，秩也不会更新。秩始终是不减小的。

路径压缩的过程也可以使用栈辅助实现。
```cpp
int find(int x) {
    std::stack<int> path; // 用于保存路径上的节点
    while (parent[x] != x) {
        path.push(x); // 将当前节点压入栈
        x = parent[x]; // 移动到父节点
    }
    while (!path.empty()) {
        parent[path.top()] = x; // 将栈中节点连接到根节点
        path.pop();
    }
    return x;
}
```
当路径较长时，递归调用可能使系统栈溢出，改用栈实现，从堆中申请空间，防止栈溢出。
### 使用哈希的模板实现
相对于数组，适用于元素范围未知或稀疏的场景。
```cpp
template <typename T>
class DSU {
private:
    std::unordered_map<T, T> parent; // 父节点映射
    std::unordered_map<T, int> rank; // 秩映射
public:
    // 查找操作，带路径压缩
    const T& find(const T& x) {
        if (parent.find(x) == parent.end()) {
            parent[x] = x; // 如果元素不存在，初始化父节点为自己
            rank[x] = 0;   // 初始化秩为0
        }
        if (parent[x] != x) {
            parent[x] = find(parent[x]); // 路径压缩
        }
        return parent[x];
    }
    // 合并操作，带按秩合并
    void unite(const T& x, const T& y) {
        const T& rootX = find(x);
        const T& rootY = find(y);
        if (rootX != rootY) {
            if (rank[rootX] < rank[rootY]) {
                parent[rootX] = rootY;
            } else if (rank[rootX] > rank[rootY]) {
                parent[rootY] = rootX;
            } else {
                parent[rootY] = rootX;
                rank[rootX]++;
            }
        }
    }
    // 检查两个元素是否属于同一集合
    bool isSameSet(const T& x, const T& y) {
        return find(x) == find(y);
    }
};
```