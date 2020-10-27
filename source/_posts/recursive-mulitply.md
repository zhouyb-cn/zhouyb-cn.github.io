---
title: 有趣的乘法
date: 2019-01-08 11:14:35
tags: algorithm
---

>leetcode算法思路

leetcode地址：[递归乘法](https://leetcode-cn.com/problems/recursive-mulitply-lcci/) 

该题让用递归的方法求乘法的结果，要求不要用 * 运算符，可以使用加、减、位移

题目比较简单，把乘法换成加法去做

解法1:

```java
class Solution {
    public int multiply(int A, int B) {
        // A * B = (A<<1)(b>>1) + A(B%2)
        if (B == 0) {
            return 0;
        }
        if (B == 1) {
            return A;
        }
        return multiply(A, B-1) + A;
    }
}
```

![image-20200909112413655](/Users/yanbo/Library/Application Support/typora-user-images/image-20200909112413655.png)

在题解中看到一种换算成位运算的操作A * B = (A<<1)(b>>1) + A(B%2)，结果内存消耗稍稍少了一点

解法2:

```java
class Solution {
    public int multiply(int A, int B) {
        // A * B = (A<<1)(b>>1) + A(B%2)
        if (B == 0) {
            return 0;
        }
        if (B == 1) {
            return A;
        }
        if (B % 2 == 1) {
            return multiply(A<<1, B>>1) + A;
        } else {
            return multiply(A<<1, B>>1);
        }
    }
}
```

![image-20200909112245826](/Users/yanbo/Library/Application Support/typora-user-images/image-20200909112245826.png)