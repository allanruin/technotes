---
layout: post
title:  "Leetcode 289 Game of Life"
subtitle:   ""  
date:       2016-06-13
author:     "allanruin"
header-img: "img/posthead/post-bg-js-module.jpg"
<!-- header-mask: 0.5 -->
catalog: false
tags:
    - leetcode
    - matrix
---

# 题目

According to the Wikipedia's article: "The Game of Life, also known simply as Life, is a cellular automaton devised by the British mathematician John Horton Conway in 1970."

Given a board with m by n cells, each cell has an initial state live (1) or dead (0). Each cell interacts with its eight neighbors (horizontal, vertical, diagonal) using the following four rules (taken from the above Wikipedia article):

1. Any live cell with fewer than two live neighbors dies, as if caused by under-population.
2. Any live cell with two or three live neighbors lives on to the next generation.
3. Any live cell with more than three live neighbors dies, as if by over-population..
4. Any dead cell with exactly three live neighbors becomes a live cell, as if by reproduction.
Write a function to compute the next state (after one update) of the board given its current state.

Follow up: 
Could you solve it in-place? Remember that the board needs to be updated at the same time: You cannot update some cells first and then use their updated values to update other cells.
In this question, we represent the board using a 2D array. In principle, the board is infinite, which would cause problems when the active area encroaches the border of the array. How would you address these problems?


# 思路

死办法自然是很容易的。为了不判断边界，我把旧数组拷贝到新数组里去了。不过其实判断边界也没那么糟糕是不是。。

``` cpp
    void gameOfLife(vector<vector<int>>& board) {
        int m = board.size();
        if (m == 0)return;
        int n = board[0].size();

        vector<vector<int>> ob(m+2, vector<int>(n+2, 0));
        vector<vector<int>> nb(m, vector<int>(n, 0));

        // copy board into ob with offset vector (1,1)
        for (int i = 0;i < m;i++) {
            for (int j = 0;j < n;j++) {
                ob[i + 1][j + 1] = board[i][j];
            }
        }

        for (int i = 1;i < m+1;i++) {
            for (int j = 1;j < n+1;j++) {
                int c = countNeighbor(i, j, ob);
                if (ob[i][j]) {
                    if (c == 2 || c == 3) {
                        // keep live
                        nb[i - 1][j - 1] = 1;
                    }
                    else {
                        nb[i - 1][j - 1] = 0;
                    }
                }
                else {
                    if (c == 3) {
                        nb[i - 1][j - 1] = 1;
                    }
                }
            }
        }
        // copy back next phrase state to board
        for (int i = 0;i < m;i++) {
            for (int j = 0;j < n;j++) {
                board[i][j] = nb[i][j];
            }
        }
    }
    int countNeighbor(int i, int j, vector<vector<int>>& b) {
        return b[i - 1][j - 1] + b[i - 1][j] + b[i - 1][j + 1]
            + b[i][j - 1] + b[i][j + 1]
            + b[i + 1][j - 1] + b[i + 1][j] + b[i + 1][j + 1];
    }
}
```



在讨论区里看到了这个脑洞大开的做法：因为给定的数组元素时32位的整数，而储存是否存活只需要一个bit，所以我们可以把新的状态储存在第二位里。

``` cpp
// https://leetcode.com/discuss/103688/0ms-c-solution-easy-to-understand
void gameOfLife(vector<vector<int>>& board) {
    if (board.empty()) return;
    int m = board.size(), n = board[0].size();
    int around[8][2] = { 
        { -1, -1 }, { -1, 0 }, { -1, 1 },
        { 0, -1 },             { 0, 1 },
        { 1, -1 },  { 1, 0 },  { 1, 1 }
    };
    for (int i = 0; i < m; i++) {
        for (int j = 0; j < n; j++) {
            int liveCount = 0;
            for (int k = 0; k < 8; k++) {
                int x = i + around[k][0], y = j + around[k][1];
                // 注意这里需要取第一位才是真正的值
                if (x > -1 && x < m && y > -1 && y < n) liveCount += (0x1 & board[x][y]);
            }
            if (board[i][j] == 0) {
                // 如果符合存活条件，就储存到第二位
                if (liveCount == 3) board[i][j] |= 0x2;
            } else {
                if (liveCount == 2 || liveCount == 3) {
                    board[i][j] |= 0x2;
                }
            }
        }
    }

    for (auto& line : board) {
        for (auto& life : line) {
            life = life >> 1;   //右起第二位储存的是新的状态
        }
    }
}
```
