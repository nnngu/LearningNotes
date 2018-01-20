# JAVA有哪些数据类型？基本数据类型各占多少个字节
作者：[nnngu](https://github.com/nnngu) 
转载本文请注明出处：[http://www.cnblogs.com/nnngu](http://www.cnblogs.com/nnngu/)

---

**java的数据类型分为：基本数据类型和引用数据类型。**

![](http://images2017.cnblogs.com/blog/1313428/201801/1313428-20180120182441428-1016525855.png)

**基本数据类型各占多少个字节：**

![][1]

关于boolean占几个字节，众说纷纭，虽然boolean表现出非0即1的“位”特性，但是存储空间的基本计量单位是字节，不是位。所以boolean至少占1个字节。 
JVM规范中，boolean变量当作int处理，也就是4字节；而boolean数组当做byte数组处理，即boolean类型的数组里面的每一个元素占1个字节。


  [1]: https://www.github.com/nnngu/FigureBed/raw/master/2018/1/20/1516444153746.jpg