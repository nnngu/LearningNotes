# 如何用java语言实现C#中的ref关键字(按引用传递参数)的效果

笔记仓库：[https://github.com/nnngu/LearningNotes](https://github.com/nnngu/LearningNotes)    

---

在上一篇文章中（[Java的参数传递是值传递还是引用传递](http://www.cnblogs.com/nnngu/p/8299724.html)），主要分析了java语言的参数传递只有按值传递而没有按引用传递。

先看一下微软的C#文档对按引用传递的定义（如下截图）：<https://docs.microsoft.com/zh-cn/dotnet/csharp/language-reference/keywords/ref#passing-an-argument-by-reference>

![][1]

**那么java语言如何实现C#中ref关键字(按引用传递参数)的效果呢？**

## 思路

我们可以把需要传递的参数再封装一层，即定义一个新的类，使得需要传递的参数成为新类的成员变量，传递参数时就传递这个新类的实例。以此达到ref关键字的效果。

## 代码演示

RefDemo.java

<pre>public class RefDemo {
    public static void main(String[] args) {
        Person person1 = new Person();
        PersonPack personPack = new PersonPack();
        personPack.person = person1;

        // 打印person
        System.out.println(personPack.person);

        change(personPack);

        // 再打印person
        System.out.println(personPack.person);
    }

    public static void change(PersonPack personPack) {
        Person person2 = new Person();
        personPack.person = person2;
    }
}

/**
 * Person类
 */
class Person {

}

/**
 * 包装类：PersonPack
 */
class PersonPack {
    public Person person;
}</pre>

运行结果：

![][2]

可以看出两次打印person的地址值不一样，即调用完change() 方法之后，person引用(指向) 了另一个对象！


  [1]: https://www.github.com/nnngu/FigureBed/raw/master/2018/1/21/1516472077285.jpg
  [2]: https://www.github.com/nnngu/FigureBed/raw/master/2018/1/21/1516472129252.jpg