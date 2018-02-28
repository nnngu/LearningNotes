# 01 深入理解JVM的内存区域

先来看看JVM运行时候的内存区域，如下图：

![][1]

　　大多数 JVM 将内存区域划分为 Heap（堆）、方法区、Stack（栈）、本地方法栈、程序计数器。其中 Heap 和 方法区 是线程共享的，Stack、本地方法栈 和 程序计数器 是非线程共享的。为什么分为线程共享和非线程共享的呢？请继续往下看。

　　首先我们熟悉一下一个 Java 程序的工作过程。一个 Java 源程序文件，会被编译为字节码文件（以 .class 为扩展名），每个 Java 程序都需要运行在自己的 JVM 上，被 JVM 通过字节码解释器加载运行。那么程序开始运行后，都是如何涉及到各内存区域的呢？

　　概括地说，JVM 初始运行的时候都会分配好 Heap 和 方法区，而 JVM 每遇到一个线程，就为其分配一个 Stack、本地方法栈 和 程序计数器。当线程终止时，三者所占用的内存空间也会被释放掉。这也是为什么我把内存区域分为线程共享和非线程共享的原因，非线程共享的那三个区域的生命周期与所属线程相同，而线程共享的区域与 Java 程序运行的生命周期相同，所以这也是系统垃圾回收的场所只发生在线程共享的区域（实际上对大部分虚拟机来说是发生在 Heap 上）的原因。
  
## 1、Heap

Heap（堆）是 JVM 的内存数据区。Heap 的管理很复杂，是被所有线程共享的内存区域，在 JVM 启动的时候创建，专门用来保存对象。在 Heap 中分配一定的内存来保存对象，实际上也只是保存对象的属性值、属性的类型 和 对象本身的类型标记等，并不保存对象的方法（方法以栈帧的形式保存在 Stack 中）。而对象在 Heap 中分配好内存以后，需要在 Stack 中保存一个4个字节的 Heap 内存地址，用来定位该对象在 Heap 中的位置，便于找到该对象。Heap 是垃圾回收的主要场所。Heap 处于物理不连续的内存空间中，只要逻辑上连续即可。

## 2、方法区

Object Class Data(加载类的类定义数据) 是存储在方法区的。除此之外，常量、静态变量、JIT(即时编译器)编译后的代码也都在方法区。正因为方法区所存储的数据与堆有一种类比关系，所以它还被称为 Non-Heap。方法区也可以是内存不连续的区域组成的，并且可设置为固定大小，也可以设置为可扩展的，这点与堆一样。

垃圾回收在这个区域会比较少出现，这个区域的内存回收主要针对常量池的回收和类的卸载。
  
## 3、Stack

先来了解下 Java 指令的构成：

Java 指令由 操作码（方法本身）和 操作数（方法内部变量）组成。　　　

1）方法本身是指令的操作码部分，保存在 Stack 中；

2）方法内部变量（局部变量）作为指令的操作数部分，跟在指令的操作码之后，保存在 Stack 中（实际上是简单类型（int, byte, short 等）保存在 Stack 中，对象类型在 Stack 中保存地址，在 Heap 中保存值）；

　　栈也叫栈内存，是在线程创建时创建，它的生命期是跟随线程的生命期，线程结束栈内存也就释放，对于栈来说不存在垃圾回收问题，只要线程一结束，该栈就 Over ，所以不存在垃圾回收。也有一些资料翻译成 Java 方法栈，大概是因为它所描述的是 Java 方法执行的内存模型，每个方法执行的同时创建栈帧（Strack Frame）用于存储局部变量表，操作栈（记录出栈、入栈的操作），动态链接，方法出口 等信息，每个方法被调用直到执行完毕的过程，对应着 栈帧 在 栈 的入栈和出栈的过程。

　　局部变量表 存放了编译期可知的各种基本数据类型（byte、short、int、long、float、double、boolean、char）、对象的引用（ reference 类型，不等同于对象本身）和 returnAdress 类型（指向下一条字节码指令的地址）。局部变量表 所需的内存空间在编译期间完成分配，在方法运行之前，该局部变量表所需要的内存空间是固定的，运行期间也不会改变。

　　栈帧是一个内存区块，是一个数据集，是一个有关方法( Method ) 和运行期数据的数据集，当一个方法 A 被调用时就产生了一个栈帧 F1 ，并被压入到栈中，A 方法又调用了 B 方法，于是产生栈帧 F2 也被压入栈，执行完毕后，先弹出 F2 栈帧，再弹出 F1 栈帧，遵循 “先进后出” 原则。
  
## 4、本地方法栈

与 Stack 相似，Stack 为 JVM 提供执行 Java 方法的服务，本地方法栈 则为 JVM 提供使用 native 方法的服务。
  
## 5、程序计数器

程序计数器是一块较小的内存区域，作用可以看做是当前线程执行的字节码的位置指示器。分支、循环、跳转、异常处理和线程恢复等基础功能都需要依赖这个计数器来完成。

## JVM 运行例子

我们来写一个简单的类，代码如下：

```java
public class JVMShowcase {  
	// 静态类常量
	public final static String ClASS_CONST = "I'm a Const";  

	// 私有实例变量  
	private int instanceVar = 15;  

	public static void main(String[] args) {  

		// 调用静态方法  
		runStaticMethod();  

		// 调用非静态方法  
		JVMShowcase showcase = new JVMShowcase();  
		showcase.runNonStaticMethod(100);  
	}  

	// 常规静态方法  
	public static String runStaticMethod() {  
		return ClASS_CONST;  
	}  

	// 非静态方法  
	public int runNonStaticMethod(int parameter) {  
		int methodVar = this.instanceVar * parameter;  
		return methodVar;  
	}  
}
```

这个类没有任何意义，不用猜测这个类是做什么用的，只是写一个比较典型的类，然后我们来看看 JVM 是如何运行的，也就是输入 java JVMShowcase 后，我们来看 JVM 是如何处理的：

* 第 1 步， 向操作系统申请空闲内存。JVM 对操作系统说 “给我 64M（随便模拟数据，并不是真实数据） 空闲内存” ，于是，操作系统就查找自己的内存分配表，找了段 64M 的内存写上 “Java 占用” 标签，然后把内存段的起始地址和终止地址给 JVM， JVM 准备加载类文件。

* 第 2 步，JVM 分配内存。JVM 获得 64M 内存，就开始得瑟了，首先给 Heap 分配内存，然后给 Stack 也分配好。

* 第 3 步，文件检查 和 分析 class 文件。若发现有错误即返回错误。

* 第 4 步，加载类。由于没有指定加载器，JVM 默认使用 bootstrap 加载器，就把 rt.jar 下的所有类都加载，JVMShowcase 也被加载到内存中。我们来看看方法区，如下图：（这时候包含了 main 方法和 runStaticMethod 方法的符号引用，因为它们都是静态方法，在类加载的时候就会加载）

![][2]

此时，Heap 是空，Stack 是空，因为还没有对象的新建和线程被执行。

* 第 5 步，执行方法。执行 main 方法。执行启动一个线程，开始执行 main 方法，在 main 执行完毕前，方法区如下图所示：
（public final static String ClASS_CONST = "I'm a Const";  ）

![][3]

在 方法区 加入了 CLASS_CONST 常量，它是在第一次被访问时产生的（runStaticMethod方法内部）。


Heap 内存中有两个对象 Object 和 showcase 对象，如下图所示：（执行了JVMShowcase showcase = new JVMShowcase();  ）

![][4]

为什么会有 Object 对象呢？因为它是 JVMShowcase 的父类，JVM 是先初始化父类，然后再初始化子类，不管有多少个父类都初始化。


在 Stack 内存中有三个栈帧，如下图所示：

![][5]

于此同时，还创建了一个程序计数器指向下一条要执行的语句。

* 第 6 步，释放内存。运行结束，JVM 向操作系统发送消息，说 “内存用完了，我还给你” ，运行结束。



















---

本文永久更新地址：[https://github.com/nnngu/LearningNotes/blob/master/JVM/01%20%E6%B7%B1%E5%85%A5%E7%90%86%E8%A7%A3JVM%E7%9A%84%E5%86%85%E5%AD%98%E5%8C%BA%E5%9F%9F.md](https://github.com/nnngu/LearningNotes/blob/master/JVM/01%20%E6%B7%B1%E5%85%A5%E7%90%86%E8%A7%A3JVM%E7%9A%84%E5%86%85%E5%AD%98%E5%8C%BA%E5%9F%9F.md)


  [1]: https://www.github.com/nnngu/FigureBed/raw/master/2018/2/28/1519799017324.jpg
  [2]: https://www.github.com/nnngu/FigureBed/raw/master/2018/2/28/1519800108931.jpg
  [3]: https://www.github.com/nnngu/FigureBed/raw/master/2018/2/28/1519800303143.jpg
  [4]: https://www.github.com/nnngu/FigureBed/raw/master/2018/2/28/1519800522688.jpg
  [5]: https://www.github.com/nnngu/FigureBed/raw/master/2018/2/28/1519800661395.jpg