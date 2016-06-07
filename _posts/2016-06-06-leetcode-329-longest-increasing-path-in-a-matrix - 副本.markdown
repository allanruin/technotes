---
layout: post
title:  "Leetcode 329 矩阵中的最长路径长度"
subtitle:   ""  
date:       2016-06-06
author:     "allanruin"
header-img: "img/posthead/post-bg-js-module.jpg"
<!-- header-mask: 0.5 -->
catalog: false
tags:
    - leetcode
    - matrix
---

# 题目

Given an integer matrix, find the length of the longest increasing path.

From each cell, you can either move to four directions: left, right, up or down. You may NOT move diagonally or move outside of the boundary (i.e. wrap-around is not allowed).

Example 1:

```
nums = [
  [9,9,4],
  [6,6,8],
  [2,1,1]
]
```

Return 4, The longest increasing path is [1, 2, 6, 9].

Example 2:

```
nums = [
  [3,4,5],
  [3,2,6],
  [2,2,1]
]
```

Return 4,The longest increasing path is [3, 4, 5, 6]. Moving diagonally is not allowed.


# 思路

我自己想到的是，诸葛个元素遍历暴力计算最长路径，最多给个cache咯。不过还有一点我没想到的是，可以不需要记录一个点是否被访问过的数组，这样因为递归产生的内存开销就小很多了。为什么可以不需要记录访问情况的visit/mark数组呢，因为要求是递增序列，所以一旦是已经访问的（路径上的）元素，那么其值必然的大于当前元素。

过程中有一点小（大）优化是对记录方向的结构，用数组不要用双重vector模拟。用vector会慢到200ms，改成int longestIncreasingPathDir[4][2]直接变成85ms。这里每次dfs都需要四个方向做遍历，访问时非常非常频繁的。所以这么做速度提升显著。

当然提交的时候被空矩阵坑了一下，我用的老方法判断元素数量的方式识别空矩阵，看到别人有个非常棒的方法是用`matrix.empty()`

我的代码如下：

``` cpp
int longestIncreasingPath(vector<vector<int>>& matrix) {
	int n = matrix.size();
	if (n == 0) { return 0; }

	int m = matrix[0].size();
	vector<vector<int>> cache(n, vector<int>(m, -1));
	int maxlen = 0;
	for (int i = 0;i < n;i++) {
		for (int j = 0;j < m;j++) {
			maxlen = max(dfs(matrix, cache, n, m, i, j), maxlen);
		}
	}
	return maxlen+1;
}
int longestIncreasingPathDir[4][2] = { {1,0},{0,1},{-1,0},{0,-1} };
int dfs(vector<vector<int>>& matrix, vector<vector<int>>& cache,
	int n, int m, int x,int y) {
	if (cache[x][y] != -1) {
		return cache[x][y];
	}
	int  maxlen = 0;
	for (int d = 0;d < 4;d++) {
		auto ds = longestIncreasingPathDir[d];
		int nx = x + ds[0];
		int ny = y + ds[1];
		if (nx > -1 && nx<n && ny>-1 && ny<m &&
		 matrix[x][y]<matrix[nx][ny]) {
			maxlen = max( maxlen
				,dfs(matrix, cache, n, m, nx, ny) + 1);
		}
	}
	cache[x][y] = maxlen;
	return maxlen;
}
```

下面这个是号称CPP最快的代码，60ms，超过100%。

``` cpp
class Solution {
    vector<vector<int>> visited;
    int height = 0, width = 0;
    int floodfill(vector<vector<int>>& matrix, int cur, int i, int j)
    {
        if (i < 0 || i >= height || j < 0 || j >= width )
            return 0;
        if (matrix[i][j] <= cur)
            return 0;
        if (visited[i][j] > 0)
            return visited[i][j];
        int r = floodfill(matrix, matrix[i][j], i + 1, j);
        int l = floodfill(matrix, matrix[i][j], i - 1, j);
        int u = floodfill(matrix, matrix[i][j], i, j + 1);
        int d = floodfill(matrix, matrix[i][j], i, j - 1);
        visited[i][j] = max(r, max(l, max(u, d))) + 1;
        return visited[i][j];
    }
public:
    int longestIncreasingPath(vector<vector<int>>& matrix) {
        if (matrix.empty())
            return 0;
        height = matrix.size(), width = matrix[0].size();
        visited.resize(height, vector<int>(width));
        int max_len = 0;
        for (int i = 0; i < height; ++i)
            for (int j = 0; j < width; ++j)
                max_len = max(max_len, floodfill(matrix, INT_MIN, i, j));
        return max_len;
    }
};
```