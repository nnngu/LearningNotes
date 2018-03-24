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



