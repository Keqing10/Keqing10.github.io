---
title: 单调栈、单调队列
category_bar: true
math: true
tags: [数据结构]
category: [数据结构与算法]
date: 2025-04-06 20:44:07
---

## 单调栈
栈中的元素保持单调递增或单调递减的顺序。单调栈通常用于解决一些需要找到某个元素左边或右边第一个比它大或小的元素的问题。
### 下一个（严格）更大元素
注意栈中存储的是元素的下标。
```cpp
vector<int> nextGreaterElement(vector<int>& nums) {
    int n = nums.size();
    vector<int> result(n, -1);  // 初始化结果数组，默认值为-1
    stack<int> stk;  // 单调栈，保存索引
    for (int i = 0; i < n; ++i) {
        // 当栈不为空且当前元素大于栈顶元素时，更新结果
        while (!stk.empty() && nums[i] > nums[stk.top()]) {
            result[stk.top()] = nums[i];
            stk.pop();
        }
        // 将当前元素的索引压入栈中
        stk.push(i);
    }
    return result;
}
```

## 单调队列
队列中的元素保持单调递增或递减的顺序（可以是非严格）。通常用于解决滑动窗口问题，在 $O(1)$ 的时间内获取窗口内最小值或最大值。
例题：[洛谷1886](https://www.luogu.com.cn/problem/P1886)
### 思路
使用双端队列实现。为了保持队列的单调性，每次`push`元素时，检查队尾元素是否能与被插入元素满足单调性，若不满足，持续弹出队尾元素，最后再插入该元素。
队首始终保存着队列的最值。
在解决滑动窗口问题时，已知窗口大小为 $k$，可以知道窗口外的上一个元素数值，然后检查队首元素，若相等则弹出。队列中始终最多保存着 $k$ 个元素。
### 单调递减队列与滑动窗口最大值
```cpp
class MonotonicQueue {
private:
    deque<int> dq;
public:
    void push(int value) {
        // 维护单调递减队列
        while (!dq.empty() && dq.back() < value) dq.pop_back();
        dq.push_back(value);
    }
    void pop(int value) {
        // 只有当队首元素等于当前要弹出的元素时才弹出
        if (!dq.empty() && dq.front() == value) dq.pop_front();
    }
    int max() { return dq.front(); }
};

vector<int> maxSlidingWindow(vector<int>& nums, int k) {
    MonotonicQueue mq;
    vector<int> result;
    for (int i = 0; i < nums.size(); ++i) {
        if (i < k - 1) {
            mq.push(nums[i]);
        } else {
            mq.push(nums[i]);
            result.push_back(mq.max());
            mq.pop(nums[i - k + 1]);
        }
    }
    return result;
}
```
在每次`push`元素并读取最大值后后直接调用`pop`，使得队列中最多保存着 $k-1$ 个元素，下一次循环时直接`push`。
### 单调递增队列与滑动窗口最小值
```cpp
class MonotonicQueue {
private:
    deque<int> dq;
public:
    void push(int value) {
        // 维护单调递增队列
        while (!dq.empty() && dq.back() > value) dq.pop_back();
        dq.push_back(value);
    }
    void pop(int value) {
        // 只有当队首元素等于当前要弹出的元素时才弹出
        if (!dq.empty() && dq.front() == value) dq.pop_front();
    }
    int min() { return dq.front(); }
};

vector<int> minSlidingWindow(vector<int>& nums, int k) {
    MonotonicQueue mq;
    vector<int> result;
    for (int i = 0; i < nums.size(); ++i) {
        if (i < k - 1) {
            mq.push(nums[i]);
        } else {
            mq.push(nums[i]);
            result.push_back(mq.min());
            mq.pop(nums[i - k + 1]);
        }
    }
    return result;
}
```

