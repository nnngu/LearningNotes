# Java中的String类能否被继承？为什么？
作者：[nnngu](https://github.com/nnngu)  
GitHub：[https://github.com/nnngu](https://github.com/nnngu)  
博客园：[http://www.cnblogs.com/nnngu](http://www.cnblogs.com/nnngu/)  
简书：[https://www.jianshu.com/users/1df20d76ea5c](https://www.jianshu.com/users/1df20d76ea5c)  
知乎：[https://www.zhihu.com/people/nnngu/posts](https://www.zhihu.com/people/nnngu/posts)  

---

不能被继承，因为String类有final修饰符，而final修饰的类是不能被继承的。

## **Java对String类的定义：**

```
public final class String implements java.io.Serializable, Comparable<String>, CharSequence {
    // 省略...　
}
```

## final修饰符的用法：

1.修饰类

　　当用final修饰一个类时，表明这个类不能被继承。final类中的成员变量可以根据需要设为final，但是要注意final类中的所有成员方法都会被隐式地指定为final方法。

![][1]

2.修饰方法

　　使用final修饰方法的原因有两个。第一个原因是把方法锁定，以防任何继承类修改它的含义；第二个原因是效率。在早期的Java实现版本中，会将final方法转为内嵌调用。但是如果方法过于庞大，可能看不到内嵌调用带来的任何性能提升。在最近的Java版本中，不需要使用final方法进行这些优化了。

　　因此，只有在想明确禁止该方法在子类中被覆盖的情况下才将方法设置为final。

　　注：一个类中的private方法会隐式地被指定为final方法。

3.修饰变量

　　对于被final修饰的变量，如果是基本数据类型的变量，则其数值一旦在初始化之后便不能更改；如果是引用类型的变量，则在对其初始化之后便不能再让其指向另一个对象。虽然不能再指向其他对象，但是它指向的对象的内容是可变的。

## final和static的区别：

很多时候会容易把static和final关键字混淆，static作用于成员变量用来表示只保存一份副本，而final的作用是用来保证变量不可变。看下面这个例子：

```
 1 public class Demo1 {
 2     public static void main(String[] args)  {
 3         MyClass myClass1 = new MyClass();
 4         MyClass myClass2 = new MyClass();
 5         System.out.println(myClass1.i);
 6         System.out.println(myClass2.i);
 7         System.out.println(myClass1.j);
 8         System.out.println(myClass2.j);
 9 
10     }
11 }
12 
13 class MyClass {
14     public final double i = Math.random();
15     public static double j = Math.random();
16 }
```

 运行结果：

```
0.3222977275463088
0.2565532218939688
0.36856868882926397
0.36856868882926397
```

每次打印的两个j值都是一样的，而i的值却是不同的。从这里就可以知道final和static变量的区别了。


  [1]: https://www.github.com/nnngu/FigureBed/raw/master/2018/1/21/1516470278278.jpg