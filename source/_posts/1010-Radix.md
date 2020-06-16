---
title: 1010 Radix
date: 2020-06-16 21:34:00
tags: 
    - algorithm
    - pat
    - binary search
categories: algorithm
---

## 题目内容
Given a pair of positive integers, for example, 6 and 110, can this equation 6 = 110 be true? The answer is yes, if 6 is a decimal number and 110 is a binary number.

Now for any pair of positive integers N1 and N2, your task is to find the radix of one number while that of the other is given.

<!-- more -->

### Input Specification:
Each input file contains one test case. Each case occupies a line which contains 4 positive integers:

```
N1 N2 tag radix
```
Here N1 and N2 each has no more than 10 digits. A digit is less than its radix and is chosen from the set { 0-9, a-z } where 0-9 represent the decimal numbers 0-9, and a-z represent the decimal numbers 10-35. The last number radix is the radix of N1 if tag is 1, or of N2 if tag is 2.

### Output Specification:
For each test case, print in one line the radix of the other number so that the equation N1 = N2 is true. If the equation is impossible, print Impossible. If the solution is not unique, output the smallest possible radix.

### Sample Input 1:
```
6 110 1 10
```

### Sample Output 1:
```
2
```

### Sample Input 2:
```
1 ab 1 2
```

### Sample Output 2:
```
Impossible
```

## 题目大意
给出一个n进制的数，问另一个数是几进制时可以和它相等，如果不能相等，输出 impossible。

## 解题思路
令已知进制的数为n1，未知的为n2，循环判断n2在不同进制是否能和n1相等。

应注意的点：
- **输入字符范围是```0-z```不代表最高是36进制**，正确的进制范围应该是n2中最大的数字+1 到 n2本身；
- **使用二分查找节省时间**；
- n2的最大值是```zzzzzzzzzz```，按照36进制转换成10进制是```3656158440062975```，已经超过long，因此**所有进制和结果的类型都至少为long long**；
- 此时，**使用二分查找中间值计算出的10进制数已经超过long long的范围，判断二分的条件时应该附加溢出判断**

## 代码
``` cpp
#include <iostream>
#include <string>
#include <cmath>
#include <algorithm>

using namespace std;

int toInt(char ch) {
    return isdigit(ch) ? ch - 48 : ch - 87;
}

long long toDecimal(string num, long long radix) {
    long long res = 0;
    for (int i = 0; i < num.length(); i++) {
        int n = toInt(num[i]);
        res += n * powl(radix, num.length() - i - 1);
    }
    return res;
}

int main() {
    string n1, n2;
    int tag, radix;

    cin >> n1 >> n2 >> tag >> radix;
    if (tag == 2) {
        string t = n1;
        n1 = n2;
        n2 = t;
    }

    long long target = toDecimal(n1, radix);
    long long l = toInt(*max_element(n2.begin(), n2.end())) + 1, r = max(l, target);
    while (l <= r) {
        long long mid = l + (r - l) / 2, test = toDecimal(n2, mid);
        if (test == target) {
            cout << mid;
            return 0;
        }
        else if (test > target || test < 0) {
            r = mid - 1;
        }
        else {
            l = mid + 1;
        }
    }
    cout << "Impossible";
    return 0;
}
```

## 小结
**解题时正数判断溢出可以直接用小于0。**

各种翻车，几乎摸清了20个测试点的作用。。。  
第一遍没有想到进制超过36，  
后改用二分，没有注意到转换出的值超过long long，  
最后左边界忘记加1。

## 参考链接
[柳婼 の blog](https://www.liuchuo.net/archives/2458)