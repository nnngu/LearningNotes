# 03 JVM的垃圾回收机制

## 前言

理解JVM的垃圾回收机制（简称GC）有什么好处呢？作为一名软件开发者，满足自己的好奇心将是一个很好的理由，不过更重要的是，理解GC工作机制可以帮助你写出更好的Java程序。

在学习GC前，你应该知道一个技术名词：“stop-the-world” ，无论你选择哪种GC算法，“stop-the-world”都会发生。“stop-the-world”意味着JVM停止应用程序，而去进行垃圾回收。当“stop-the-world”发生时，除了进行垃圾回收的线程，其他所有线程都将停止运行。被中断的任务将在GC任务完成后恢复执行。GC调优往往意味着减少“stop-the-world”的时间。

## 分代垃圾收集机制

**在HotSpot虚拟机中，将内存分为 年轻代(young generation)、老年代(old generation) 和 永久代(permanent generation)**

我们看一下这幅图：

![][1]

**年轻代：** 新创建的对象都存放在这里。因为大多数对象很快变得不可达，所以大多数对象在年轻代中创建，然后消失。当对象从这块内存区域消失时，我们说发生了一次“minor GC”。

**老年代：** 没有变得不可达，存活下来的年轻代对象被复制到这里。这块内存区域一般大于年轻代。因为它更大的规模，GC发生的次数比在年轻代的少。对象从老年代消失时，我们说“major GC”（或“full GC”）发生了。

**永久代：** 上图中的**永久代(permanent generation)** 也称为“方法区(method area)”，它存储class对象和字符串常量。所以这块内存区域绝对不是永久的存放从老年代存活下来的对象的。发生在这里的垃圾回收也被称为major GC。

## 垃圾收集算法

## 5种垃圾收集器

在JDK7中，有5种垃圾收集器:

* Serial收集器
* Parallel收集器
* Parallel Old收集器 (Parallel Compacting GC)收集器
* Concurrent Mark & Sweep GC  (or “CMS”)收集器
* Garbage First (G1) 收集器

其中，Serial 收集器一定不能用于服务器端。这个收集器类型仅应用于单核CPU桌面电脑。使用Serial收集器会显着降低应用程序的性能。












---

本文永久更新地址：[https://github.com/nnngu/LearningNotes/blob/master/JVM/03%20JVM%E7%9A%84%E5%9E%83%E5%9C%BE%E5%9B%9E%E6%94%B6%E6%9C%BA%E5%88%B6.md](https://github.com/nnngu/LearningNotes/blob/master/JVM/03%20JVM%E7%9A%84%E5%9E%83%E5%9C%BE%E5%9B%9E%E6%94%B6%E6%9C%BA%E5%88%B6.md)


  [1]: https://www.github.com/nnngu/FigureBed/raw/master/2018/3/1/1519900668092.jpg