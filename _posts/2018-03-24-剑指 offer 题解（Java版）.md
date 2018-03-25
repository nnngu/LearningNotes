---
categories: Sword2offer
description: 用 Java 语言实现的《剑指 offer 》题解
---

本文永久更新地址：[https://github.com/nnngu/LearningNotes/blob/master/_posts/2018-03-24-%E5%89%91%E6%8C%87%20offer%20%E9%A2%98%E8%A7%A3%EF%BC%88Java%E7%89%88%EF%BC%89.md](https://github.com/nnngu/LearningNotes/blob/master/_posts/2018-03-24-%E5%89%91%E6%8C%87%20offer%20%E9%A2%98%E8%A7%A3%EF%BC%88Java%E7%89%88%EF%BC%89.md)

---

## 本文的约定

### 变量命名约定

* nums 表示数字数组，array 表示通用数组，matrix 表示矩阵；
* n 表示数组长度、字符串长度、树节点个数，以及其它具有一维性质的数据结构的元素个数；
* m, n 表示矩阵的总行数和总列数；
* first, last 表示闭区间，在需要作为函数参数时使用：\[first, last]；
* l, h 也表示闭区间，在只作为局部变量时使用：\[l, h]；
* begin, end 表示左闭右开区间：\[begin, end)；
* ret 表示结果相关的变量；
* dp 表示动态规划保存子问题的数组；

### 复杂度简写说明

O(nlog<sub>n</sub>) + O(n<sup>2</sup>)，+ 号前面的表示时间复杂度，+ 号后面的表示空间复杂度。

## 实现单例模式

> [单例模式的5种写法](https://github.com/nnngu/LearningNotes/blob/master/_posts/2017-04-19-019%20%E5%8D%95%E4%BE%8B%E6%A8%A1%E5%BC%8F%E7%9A%845%E7%A7%8D%E5%86%99%E6%B3%95.md)

## 数组中重复的数字

### 题目描述

在一个长度为 n 的数组里的所有数字都在 0 到 n-1 的范围内。数组中某些数字是重复的，但不知道有几个数字是重复的。也不知道每个数字重复几次。请找出数组中任意一个重复的数字。例如，如果输入长度为 7 的数组 {2, 3, 1, 0, 2, 5, 3}，那么对应的输出是第一个重复的数字 2。

### 解题思路

这种数组元素在 \[0, n-1] 范围内的问题，可以将值为 i 的元素放到第 i 个位置上。

以 (2, 3, 1, 0, 2, 5) 为例，代码的运行过程为：

```
position-0 : (2,3,1,0,2,5) // 2 <-> 1
             (1,3,2,0,2,5) // 1 <-> 3
             (3,1,1,0,2,5) // 3 <-> 0
             (0,1,1,3,2,5) // already in position
position-1 : (0,1,1,3,2,5) // already in position
position-2 : (0,1,1,3,2,5) // nums[i] == nums[nums[i]], exit
```

遍历到位置 2 时，该位置上的数为 1，但是第 1 个位置上已经有一个 1 的值了，因此可以知道 1 重复。

复杂度：O(n) + O(1)

```java
public boolean duplicate(int[] nums, int length, int[] duplication) {
    if (nums == null || length <= 0) return false;
    for (int i = 0; i < length; i++) {
        while (nums[i] != i && nums[i] != nums[nums[i]]) {
            swap(nums, i, nums[i]);
        }
        if (nums[i] != i && nums[i] == nums[nums[i]]) {
            duplication[0] = nums[i];
            return true;
        }
    }
    return false;
}

private void swap(int[] nums, int i, int j) {
    int t = nums[i]; nums[i] = nums[j]; nums[j] = t;
}
```

## 二维数组中的查找

### 题目描述

在一个二维数组中，每一行都按照从左到右递增的顺序排序，每一列都按照从上到下递增的顺序排序。请完成一个函数，输入这样的一个二维数组和一个整数，判断数组中是否含有该整数。

```
Consider the following matrix:
[
  [1,   4,  7, 11, 15],
  [2,   5,  8, 12, 19],
  [3,   6,  9, 16, 22],
  [10, 13, 14, 17, 24],
  [18, 21, 23, 26, 30]
]

Given target = 5, return true.
Given target = 20, return false.
```

### 解题思路

从右上角开始查找。因为矩阵中的一个数，它左边的数都比它小，下边的数都比它大。因此，从右上角开始查找，就可以根据 target 和当前元素的大小关系来缩小查找区间。

复杂度：O(m + n) + O(1)

```java
public boolean Find(int target, int[][] matrix) {
    if (matrix == null || matrix.length == 0 || matrix[0].length == 0) return false;
    int m = matrix.length, n = matrix[0].length;
    int r = 0, c = n - 1; // 从右上角开始
    while (r <= m - 1 && c >= 0) {
        if (target == matrix[r][c]) return true;
        else if (target > matrix[r][c]) r++;
        else c--;
    }
    return false;
}
```

## 替换空格

### 题目描述

请实现一个函数，将一个字符串中的空格替换成“%20”。例如，当字符串为 We Are Happy. 则经过替换之后的字符串为 We%20Are%20Happy。

### 解题思路

在字符串尾部填充任意字符，使得字符串的长度等于字符串替换之后的长度。因为一个空格要替换成三个字符（%20），因此当遍历到一个空格时，需要在尾部填充两个任意字符。

令 P1 指向字符串原来的末尾位置，P2 指向字符串现在的末尾位置。P1 和 P2 从后向前遍历，当 P1 遍历到一个空格时，就需要令 P2 指向的位置依次填充 02%（注意是逆序的），否则就填充上 P1 指向字符的值。

从后向前遍是为了在改变 P2 所指向的内容时，不会影响到 P1 遍历原来字符串的内容。

复杂度：O(n) + O(1)

![][1]

```java
public String replaceSpace(StringBuffer str) {
    int oldLen = str.length();
    for (int i = 0; i < oldLen; i++) {
        if (str.charAt(i) == ' ') {
            str.append("  ");
        }
    }
    int idxOfOld = oldLen - 1;
    int idxOfNew = str.length() - 1;
    while (idxOfOld >= 0 && idxOfNew > idxOfOld) {
        char c = str.charAt(idxOfOld--);
        if (c == ' ') {
            str.setCharAt(idxOfNew--, '0');
            str.setCharAt(idxOfNew--, '2');
            str.setCharAt(idxOfNew--, '%');
        } else {
            str.setCharAt(idxOfNew--, c);
        }
    }
    return str.toString();
}
```

## 从尾到头打印链表







  [1]: https://www.github.com/nnngu/FigureBed/raw/master/2018/3/25/1521955019752.jpg