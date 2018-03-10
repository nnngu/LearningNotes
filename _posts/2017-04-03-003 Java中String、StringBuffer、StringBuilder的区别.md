---
categories: JavaBasis
description: 1.从是否可变的角度
---

笔记仓库：[https://github.com/nnngu/LearningNotes](https://github.com/nnngu/LearningNotes)    

---

## 1.从是否可变的角度

　　String类中使用字符数组保存字符串，因为有“final”修饰符，所以String对象是不可变的。

```
/** The value is used for character storage. */
    private final char value[];
``` 

　　**StringBuffer和StringBuilder**都继承自AbstractStringBuilder类，在AbstractStringBuilder中也是使用字符数组保存字符串，但没有“final”修饰符，所以两种对象都是可变的。

```
/**
     * The value is used for character storage.
     */
    char[] value;
```

## 2.是否多线程安全

　　String中的对象是不可变的，也就可以理解为常量，所以**是线程安全的**。

　　AbstractStringBuilder是StringBuffer和StringBuilder的公共父类，定义了一些字符串的基本操作，如append、insert、indexOf等公共方法。

　　StringBuffer对方法加了同步锁(synchronized) ，所以是**线程安全的**。看如下源码：

```
1     public synchronized StringBuffer append(String str) {
2         toStringCache = null;
3         super.append(str);
4         return this;
5     }
```

　　StringBuilder并没有对方法进行加同步锁，所以是**非线程安全的**。如下源码：

```
1     public StringBuilder append(String str) {
2         super.append(str);
3         return this;
4     }
```

## 3.StringBuffer和StringBuilder的共同点

　　StringBuffer和StringBuilder有公共父类AbstractStringBuilder(**抽象类**)。

　　StringBuffer、StringBuilder的方法都会调用AbstractStringBuilder中的公共方法，如上面的两段源码中都调用了super.append(str);  只是StringBuffer会在方法上加synchronized关键字，进行同步。

　　**最后，如果程序不是多线程的，那么使用StringBuilder效率高于StringBuffer。**

