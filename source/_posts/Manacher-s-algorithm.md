---
title: Manacher's algorithm
date: 2020-04-25 18:40:48
tags:
    - algorithm
    - longest palindromic substring
categories: algorithm

---

求字符串的最长回文子串，线性时间复杂度。

传统思路为遍历每个字符，以该字符为中心向两边查找，时间复杂度为O(n^2)，  
Manachar算法充分利用回文的特点（对称性），减少对回文中心右侧的重复判断，可提升时间复杂度为O(n)。

## 步骤

### 预处理
由于回文分为偶回文和奇回文，为处理简便，将其全部转换为奇回文。
e.g. `abbc` 转换为 `^a#b#b#c$`

### 定义数组`int p[i]`，表示以i为中心的最长回文半径
e.g.  
![数组p](/images/Snipaste_2020-04-25_19-53-30.png)

### 维护回文中心和回文右边界
在此基础上对字符串进行遍历，若字符在右边界内，则可以利用对称性加快查找。

此时应注意，**对称位置上的最长回文半径长度可能超过当前字符到边界的距离**，所以p[i]应为`min(p[mirror_i], mx - i)`。

若当前字符已经在边界外，可暂时将p[i]赋为1。

``` cpp
if (i < boundary) {
    p[i] = min(p[mirror_i], mx - i);
}
else {
    p[i] = 1;
}
```

### 更新p[i]
以i为中心向两边扩展，更新p
``` cpp
while (str[i - p[i]] == str[i + p[i]]) {
    p[i]++;
}
```

## 代码
``` cpp
string longestPalindrome(string s) {
    // makes all palindromes odd by inserting #
    string converted = "^#";
    for (int i = 0; i < s.length(); i++) {
        converted += s[i];
        converted += "#";
    }
    converted += "$";
    
    int mcenter = 0, mlength = 0;
    int center = 0, boundary = 0;
    int *p = new int[converted.length()];
    for (int i = 1; i < converted.length() - 1; i++) {
        // update by the mirror data
        if (i < boundary) p[i] = min(p[2 * center - i], boundary - i);
        else p[i] = 1;
        // then extend using naive method
        while (converted[i - p[i]] == converted[i + p[i]]) p[i]++;
    
        // update the palindrome cneter
        if (i + p[i] > boundary) {
            center = i;
            boundary = i + p[i];
        }

        // maintain the max center and the max length
        if (p[i] - 1 > mlength) {
            mcenter = i;
            mlength = p[i] - 1;
        }
    }
    return s.substr((mcenter - mlength) / 2, mlength);
}
```

## 参考链接
[https://ethsonliu.com/2018/04/manacher.html](https://ethsonliu.com/2018/04/manacher.html)