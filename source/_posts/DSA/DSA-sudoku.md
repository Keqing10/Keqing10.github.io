---
title: 求解数独
category_bar: true
math: false
tags: [算法]
category: [数据结构与算法]
date: 2025-09-01 09:53:01
---

### 思路
利用简单的深度优先搜索策略求解。
先根据输入确定每个空格不能填入的数字，将所有的空格保存在一个队列中，然后依次弹出队列首项，判断是否有数字可以填入。如果存在当前合法解，就填入该数字，将该位置保存到一个栈中，继续判断队列的下一项。直到某一项可能不存在可填入的数字，就说明前面有数字填入错误，将栈内的位置依次弹出重新保存在队列中，判断应填入的下一个数字。
在整个过程中，队列和栈中的元素只发生了移动并不会改变，因此可以合并使用一个数组来表示，直接使用下标来标示当前尝试填入的位置。
链接：[力扣37.解数独](https://leetcode.cn/problems/sudoku-solver)

### 代码

```cpp
// Solution.h
#include <stack>
#include <vector>

class Solution
{
private:
    constexpr static int SIZE = 9;
    // 下标1~9表示二进制的1~9位分别为1；1022 = 11 1111 1110B = 2 + 4 + 8 + ... + 512
    constexpr static unsigned NUMBERS[SIZE + 1] = { 1022, 1 << 1, 1 << 2, 1 << 3, 1 << 4, 1 << 5, 1 << 6, 1 << 7, 1 << 8, 1 << 9 };
    unsigned map[SIZE][SIZE]{ 0 };  // 记录每格填入的数字：数字本身表示，未填充时为0
    unsigned choosenNum[SIZE][SIZE]{ 0 }, originChoosen[SIZE][SIZE]{ 0 };  // 记录每格不能选的数字：低位起1~SIZE位表示
    std::vector<std::pair<int, int>> q;  // 模拟队列，记录待填充的空格坐标，方便回溯，等效于已填坐标栈+未填坐标栈/队列

    // 初始化map[][]，choosenNum[][]，originChoosen[][]，q
    void init();

    // 检查num是否可放入[row][col]
    bool check(int row, int col, int num);
public:
    Solution() = default;
    ~Solution() = default;

    // 输入数据，必须为9*9二维数组
    void inputBoard(std::vector<std::vector<char>>& board);
    void inputBoard(std::vector<std::vector<int>>& board);

    // 求解数独，按照行-列依次填入
    void solveSudoku();

    // 输出当前的数据
    void printBoard();
};
```

```cpp
// Solution.cpp
#include "Solution.h"

#include <iostream>

using namespace std;

void Solution::init()
{
    // update map[][], choosenNum[][], q
    q.reserve(SIZE * SIZE);  // 预分配空间
    for (int i = 0; i < SIZE; ++i) {
        for (int j = 0; j < SIZE; ++j) {
            if (map[i][j] == 0) {  // 是需要填充的情况
                q.push_back(make_pair(i, j));  // 加入待填充队列
            }
            else {  // 是已填充的情况，更新choosenNum[][]
                for (int k = 0; k < SIZE; ++k) {
                    choosenNum[i][k] |= NUMBERS[map[i][j]];  // row
                    choosenNum[k][j] |= NUMBERS[map[i][j]];  // column
                    int boxRow = i / 3, boxCol = j / 3;
                    int ii = boxRow * 3 + k / 3, jj = boxCol * 3 + k % 3;
                    choosenNum[ii][jj] |= NUMBERS[map[i][j]];  // box
                }
            }
        }
    }
    // update originChoosen[][]
    for (int i = 0; i < SIZE; ++i) {
        for (int j = 0; j < SIZE; ++j) {
            originChoosen[i][j] = choosenNum[i][j];
        }
    }
}

bool Solution::check(int row, int col, int num)
{
    // check row
    for (int i = 0; i < SIZE; ++i) {
        if (i != col && map[row][i] == num) return false;
    }
    // check column
    for (int i = 0; i < SIZE; ++i) {
        if (i != row && map[i][col] == num) return false;
    }
    // check box
    int boxRow = row / 3, boxCol = col / 3;
    for (int i = 0; i < SIZE; ++i) {
        int ii = boxRow * 3 + i / 3, jj = boxCol * 3 + i % 3;
        if ((ii != row || jj != col) && map[ii][jj] == num) return false;
    }
    map[row][col] = num;
    return true;
}

void Solution::inputBoard(std::vector<std::vector<char>>& board)
{
    for (int i = 0; i < SIZE; ++i) {
        for (int j = 0; j < SIZE; ++j) {
            if (board[i][j] >= '1' && board[i][j] <= '9') map[i][j] = board[i][j] - '0';
            else map[i][j] = 0;
        }
    }
}

void Solution::inputBoard(std::vector<std::vector<int>>& board) {
    for (int i = 0; i < SIZE; ++i) {
        for (int j = 0; j < SIZE; ++j) {
            map[i][j] = board[i][j];
        }
    }
}

void Solution::solveSudoku()
{
    init();
    for (auto it = q.begin(); it != q.end(); ) {
        int row = it->first, col = it->second;
        if (map[row][col] == 0 && choosenNum[row][col] == NUMBERS[0]) {
            choosenNum[row][col] = originChoosen[row][col];  // 重置可选数字
            --it;
            continue;  // 没有可以填充的数字，直接跳过，在下一轮循环重新填充上一个空格
        }

        map[row][col] = 0;  // 先将该位置置为0，防止重新填充失败时下面判断错误
        // 尝试填充所有可能的数字 NUMBERS[i]
        for (int i = 1; i <= SIZE; ++i) {
            if (choosenNum[row][col] & NUMBERS[i]) {  // 该数字不可能填充
                continue;
            }
            choosenNum[row][col] |= NUMBERS[i];
            if (check(row, col, i)) {  // 该数字可以填充
                break;  // 进入外层的下一轮循环
            }
        }
        if (map[row][col] != 0) {  // 该位置填充了数字
            ++it;
        }
    }
}

void Solution::printBoard()
{
    for (int i = 0; i < SIZE; ++i) {
        for (int j = 0; j < SIZE; ++j) {
            cout << map[i][j] << " \n"[j == SIZE - 1];
        }
    }
}
```

