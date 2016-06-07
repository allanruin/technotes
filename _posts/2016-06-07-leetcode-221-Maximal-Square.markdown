---
layout: post
title:  "Leetcode 221 Maximal Square"
subtitle:   ""  
date:       2016-06-07
author:     "allanruin"
header-img: "img/posthead/post-bg-js-module.jpg"
<!-- header-mask: 0.5 -->
catalog: false
tags:
    - leetcode
    - matrix
---

# 题目

Given a 2D binary matrix filled with 0's and 1's, find the largest square containing all 1's and return its area.

For example, given the following matrix:

```
1 0 1 0 0
1 0 1 1 1
1 1 1 1 1
1 0 0 1 0
```

Return 4.


# 思路

首先是暴力遍历，每个都查找每个点作为正方形的左上角，能构成的最大正方形。这样复杂度显然很高。能不能优化呢，容易就想到进行某种缓存/memorization。我们缓存的时候换一个方向，记录的是一个点作为正方形的右下角，最大构成的正方形边长。这样一个点(i,j)构成的最大正方形长度就跟(i-1,j-1)左上角的点能构成的最大正方形长度相关了。![](/img/in-post/MAXIMAL-square-01.png)
如图所示，一开始我觉得只要如果(i-1,j-1)是长度k的正方形，那么只要判断A、B两块区域是否全为1就可以了。
![](/img/in-post/MAXIMAL-square-02.png)
其实如果A、B区域不是全为1，但橙色部分全为1，则(i,j)还是可以构成一个小一点的正方形的。于是我就想到遍历A，B区域看从(i,j)出发最多多长，取短的那个加1作为(i,j)处的正方形边长。

``` cpp
int maximalSquare(vector<vector<char>>& matrix) {
    if (matrix.empty())return 0;
    int m = matrix.size();
    int n = matrix[0].size();
    int maxlen = 0;
    vector<vector<int>> c(m, vector<int>(n,0));
    for (int i = 0;i < m;i++) {
        for (int j = 0;j < n;j++) {
            if (matrix[i][j]=='1') { c[i][j] = 1; }
        }
    }

    for (int i = 0;i < m;i++) {
        for (int j = 0;j < n;j++) {
            if (i - 1 > -1 && j - 1 > -1) {
                if (c[i - 1][j - 1]==0|| c[i][j]==0) { continue; }

                int k = c[i - 1][j - 1];
                int yc = 0;int xc = 0;
                for (int y = j - 1;y >= j-k;y--) {
                    if (matrix[i][y] == '0') { break; }
                    else {
                        yc += 1;
                    }
                }
                for (int x = i-1;x>=i-k;x--) {
                    // in my testcase I use 0 instead of '0', so should change
                    if (matrix[x][j] == '0') { break; }
                    else{
                        xc += 1;
                    }
                }

                c[i][j] = 1 + min(xc,yc);
                maxlen = max(c[i][j], maxlen);

            }
            else {
                // 这里是边界点
                if (maxlen==0 && c[i][j]) { maxlen = 1; }
            }
        }
    }
    return maxlen*maxlen;
}
```
我的效果也不算特别差吧。![](/img/in-post/MAXIMAL-square-03.png),16ms感觉还行。不过上面那个修正是我自己用例子调试出来的。后来看到其他答案才反应过来，A、B区域是无需遍历的。仍然可以利用缓存矩阵来判断。如果橙色部分全是1，且(i-1,j-1)的也是一个正方形，则(i-1,j)和(i,j-1)也是构成正方形的！！

于是有了下面这个方法

``` cpp
int maximalSquare(vector<vector<char>>& matrix) {
    if (matrix.empty())return 0;
    int m = matrix.size();
    int n = matrix[0].size();
    int maxlen = 0;
    vector<vector<int>> c(m, vector<int>(n, 0));
    for (int i = 0;i < m;i++) {
        for (int j = 0;j < n;j++) {
            if (matrix[i][j] == 1) { c[i][j] = 1; }
        }
    }

    for (int i = 0;i < m;i++) {
        for (int j = 0;j < n;j++) {
            if (i - 1 > -1 && j - 1 > -1 ) {
                // 这里的条件之所以不放到上一个if里，是为了让上一个if的
                // else处理边界点
                if (c[i - 1][j - 1] == 0 || c[i][j] == 0) { continue; }

                c[i][j] = 1 + min(c[i-1][j-1], min(c[i-1][j],c[i][j-1]));
                maxlen = max(c[i][j], maxlen);

            }
            else {
                // 这里是边界点
                if (maxlen == 0 && c[i][j]) { maxlen = 1; }
            }
        }
    }
    return maxlen*maxlen;
}
```

然而![](/img/in-post/MAXIMAL-square-04.png)
反而变成了20ms了（=_,=）


# 别人的代码

```cpp
class Solution {
public:
int maximalSquare(vector<vector<char>>& matrix) {
    int m = matrix.size();
    if(m==0) return 0;
    int n = matrix[0].size();

    vector<vector<int>> dp(m+1, vector<int>(n+1, 0));

    int ret = 0;
    for(int i = 1; i <= m; i ++){
        for(int j = 1; j <= n; j ++){
            if(matrix[i-1][j-1] == '0'){
                dp[i][j] = 0;
            }else{
                dp[i][j] = 1 + min(dp[i-1][j], min(dp[i][j-1], dp[i-1][j-1]));
            }
            ret = max(ret, dp[i][j]);
        }
    }
    return ret*ret;
}
```