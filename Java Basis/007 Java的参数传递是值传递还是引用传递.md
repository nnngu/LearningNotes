# Java的参数传递是值传递还是引用传递

笔记仓库：[https://github.com/nnngu/LearningNotes](https://github.com/nnngu/LearningNotes)    

---

**当一个对象被当作参数传递到一个方法后，在此方法内可以改变这个对象的属性，那么这里到底是值传递还是引用传递?**

**答：是值传递。**  Java 语言的参数传递只有值传递。 **当一个实例对象作为参数被传递到方法中时，参数的值就是该对象的引用的一个副本。指向同一个对象，对象的内容可以在被调用的方法内改变，但对象的引用(不是引用的副本) 是永远不会改变的。**

Java的参数传递，不管是基本数据类型还是引用类型的参数，**都是按值传递，没有按引用传递！**

 我们可以看一下microsoft的文档中对按引用传递参数的定义（如下截图）：<https://docs.microsoft.com/zh-cn/dotnet/csharp/language-reference/keywords/ref#passing-an-argument-by-reference>  

![][1]

### 1、基本数据类型的参数

先来看一下基本数据类型的参数按值传递的例子：

TransferTest.java

<pre>public class TransferTest {
    public static void main(String[] args) {
        int num = 1;
        System.out.println("changeNum()方法调用之前：num = " + num);
        changeNum(num);
        System.out.println("changeNum()方法调用之后：num = " + num);
    }

    public static void changeNum(int x) {
        x = 2;
    }
}</pre>

运行结果：

![][2]

这个传递过程的示意图如下：

![][3]

num作为参数传递给changeNum()方法时，是将内存空间中num所指向的那个存储单元中存放的值1传递给了changeNum()方法中的x变量，而这个x变量也在内存空间中分配了一个存储单元，这个时候，就把num的值1传递给了x的这个存储单元中。此后，在changeNum()方法中对x的一切操作都是针对x所指向的这个存储单元，与num所指向的那个存储单元没有关系了！

所以，在changeNum()方法调用之后，num所指向的存储单元的值还是没有发生变化，**这就是所谓的“值传递”！** 值传递的精髓是：传递的是存储单元中的内容，而不是存储单元的引用！

### 2、引用类型的参数

同样，先看一个例子：

TransferTest2.java  

<pre> 1 public class TransferTest2 {
 2     public static void main(String[] args) {
 3         Person person = new Person();
 4         System.out.println(person);
 5         change(person);
 6         System.out.println(person);
 7     }
 8 
 9     public static void change(Person p) {
10         p = new Person();
11     }
12 }
13 
14 /**
15  * Person类
16  */
17 class Person {
18 
19 }</pre>

运行结果：

![][4]

可以看出两次打印person的地址值是一样的，即调用完change() 方法之后，person变量并没有发生改变。

这个传递过程的示意图如下：

![][5]

当执行到第3行代码时，程序在堆内存中开辟了一块内存空间用来存储Person类的实例对象，同时在栈内存中开辟了一个存储单元用来存储该实例对象的引用，即上图中person指向的存储单元。

当执行到第5行代码时，person作为参数传递给change()方法，**需要注意的是：person将自己存储单元的内容传递给了change()方法的p变量！** 此后，在change()方法中对p的一切操作都是针对p所指向的存储单元，与person所指向的那个存储单元没有关系了！

下一篇文章会分析[如何用java语言实现按引用传递参数的效果](http://www.cnblogs.com/nnngu/p/8300164.html)


  [1]: https://www.github.com/nnngu/FigureBed/raw/master/2018/1/21/1516471639380.jpg
  [2]: https://www.github.com/nnngu/FigureBed/raw/master/2018/1/21/1516471699788.jpg
  [3]: https://www.github.com/nnngu/FigureBed/raw/master/2018/1/21/1516471736135.jpg
  [4]: https://www.github.com/nnngu/FigureBed/raw/master/2018/1/21/1516471785596.jpg
  [5]: https://www.github.com/nnngu/FigureBed/raw/master/2018/1/21/1516471828124.jpg