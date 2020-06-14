---
title: Floyd-Warshall algorithm
date: 2020-04-25 17:33:27
tags: 
    - algorithm
    - shortest path
categories: algorithm
---

适用于多源最短路径，也被用于计算有有向图的传递闭包，可以正确处理有向图和负权，时间复杂度O(n^3)、空间复杂度O(n^2)。


## 思想
在两个点AB之找到第三个点C，使 `AC + CB < AB`

<!-- more -->

## 代码
``` cpp
for (int i = 0; i < size; i++) {
    for (int j = 0; j < size; j++) {
        for (int k = 0; k < size; k++) {
            path[i][j] = min(path[i][j], path[i][k] + path[k][j]);
        }
    }
}
```