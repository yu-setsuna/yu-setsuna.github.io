---
title: 1007 Maximum Subsequence Sum
date: 2020-06-14 07:17:14
tags: 
    - algorithm
    - maximum subsequence sum
    - dp
categories: algorithm
---

## 题目内容
Given a sequence of K integers { N1, N2, …, NK }. A continuous subsequence is defined to be { Ni, Ni+1, …, Nj } where 1 <= i <= j <= K. The Maximum Subsequence is the continuous subsequence which has the largest sum of its elements. For example, given sequence { -2, 11, -4, 13, -5, -2 }, its maximum subsequence is { 11, -4, 13 } with the largest sum being 20.

Now you are supposed to find the largest sum, together with the first and the last numbers of the maximum subsequence.

### Input Specification:
Each input file contains one test case. Each case occupies two lines. The first line contains a positive integer K (≤10000). The second line contains K numbers, separated by a space.

### Output Specification:
For each test case, output in one line the largest sum, together with the first and the last numbers of the maximum subsequence. The numbers must be separated by one space, but there must be no extra space at the end of a line. In case that the maximum subsequence is not unique, output the one with the smallest indices i and j (as shown by the sample case). If all the K numbers are negative, then its maximum sum is defined to be 0, and you are supposed to output the first and the last numbers of the whole sequence.

### Sample Input:
``` 
10
-10 1 2 3 4 -5 -23 3 7 -21
```
### Sample Output:
```
10 1 4
```

## 题目大意
求给出序列的最大子序列和。若结果不唯一，输出最前面的；若全为负值，输出0和整个范围；


## 解题思路
### 法一 朴素算法
直接用二重循环判断子序列的起始和结束位置，计算子序列和是否为最大；对子序列和计算的过程进行优化，使用 「前j个数的和 - 前i个数的和」 表示 「第i+1位到第j位的和」。

算法复杂度 O(N^2)。

``` cpp
#include <cstdio>
#include <algorithm>

using namespace std;

const int MAXN = 1e4 + 1;

int main() {
    int n;
    scanf("%d", &n);

    int seq[MAXN], acc[MAXN] = {0};
    bool allnegative = true;
    for (int i = 1; i <= n; i++) {
        scanf("%d", &seq[i]);
        if (seq[i] >= 0) allnegative = false;
        acc[i] = acc[i - 1] + seq[i];
    }
    if (allnegative) {
        printf("0 %d %d", seq[1], seq[n]);
        return 0;
    }

    int maxv = 0, start = 0, end = 0;
    for (int i = 1; i <= n; i++) {
        for (int j = i; j <= n; j++) {
            if (acc[j] - acc[i - 1] > maxv) {
                maxv = acc[j] - acc[i - 1];
                start = i;
                end = j;
            }
        }
    }

    printf("%d %d %d", maxv, seq[start], seq[end]);
    return 0;
}
```

### 法二 动态规划
由于最大的连续子序列的位置可能在序列的任何位置，「前i项最大子序列和」和 「前i-1项最大子序列和」是没有直接关系的。所以这里将问题改为 「先求以第i项为结尾的最大子序列和（从哪项开始没有限制），再取所有结果中的最大值」。

容易看出，求以第i项为结尾的最大子序列 可以根据第i-1项求出：  
如果第i-1项结果为负，那么以第i项结果必然等于序列第i项的值（相当于去掉 负数+a 中的负数后，结果比原来大）；  
否则等于前i-1项结果加序列第i项（相当于 非负数+a, 结果也会比a大）。

这样就可以得出递推关系：
``` 
以第i项为结尾的最大子序列和 = max(以第i-1项为结尾的最大子序列和 + 序列第i项, 序列第i项) 
```
初始值「以第0项为结尾的最大子序列和」自然是等于0的。
此时，已经可以根据初始值和递推关系求出任意一项。再求出所有结果中最大的一项，即题目中要求的最大子序列和。

时间复杂度 O(N)。


``` cpp
#include <cstdio>
#include <algorithm>

using namespace std;

const int MAXN = 1e4 + 1;

int main() {
    int n;
    scanf("%d", &n);

    int seq[MAXN];
    bool allnegative = true;
    for (int i = 1; i <= n; i++) {
        scanf("%d", &seq[i]);
        if (seq[i] >= 0) allnegative = false;
    }
    if (allnegative) {
        printf("0 %d %d", seq[1], seq[n]);
        return 0;
    }

    int sum[MAXN] = {0};
    int maxv = 0, start = 0, end = 0, t = 0;
    for (int i = 1; i <= n; i++) {
        sum[i] = sum[i - 1] < 0 ? seq[i] : sum[i - 1] + seq[i];
        if (sum[i - 1] <= 0) t = i;
        if (sum[i] > maxv) {
            maxv = sum[i];
            start = t; end = i;
        }
    }

    printf("%d %d %d", maxv, seq[start], seq[end]);
    return 0;
}
```
