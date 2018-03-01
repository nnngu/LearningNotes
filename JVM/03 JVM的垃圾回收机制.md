# 03 JVM的垃圾回收机制

## 前言

理解JVM的垃圾回收机制（简称GC）有什么好处呢？作为一名软件开发者，满足自己的好奇心将是一个很好的理由，不过更重要的是，理解GC工作机制可以帮助你写出更好的Java程序。

在学习GC前，你应该知道一个技术名词：“stop-the-world” ，无论你选择哪种GC算法，“stop-the-world”都会发生。“stop-the-world”意味着JVM停止应用程序，而去进行垃圾回收。当“stop-the-world”发生时，除了进行垃圾回收的线程，其他所有线程都将停止运行。被中断的任务将在GC任务完成后恢复执行。GC调优往往意味着减少“stop-the-world”的时间。

## 分代垃圾收集















---

本文永久更新地址：[https://github.com/nnngu/LearningNotes/blob/master/JVM/03%20JVM%E7%9A%84%E5%9E%83%E5%9C%BE%E5%9B%9E%E6%94%B6%E6%9C%BA%E5%88%B6.md](https://github.com/nnngu/LearningNotes/blob/master/JVM/03%20JVM%E7%9A%84%E5%9E%83%E5%9C%BE%E5%9B%9E%E6%94%B6%E6%9C%BA%E5%88%B6.md)