# JAVA有哪些数据类型？基本数据类型各占多少个字节
作者：[nnngu](https://github.com/nnngu)  
GitHub：[https://github.com/nnngu](https://github.com/nnngu)  
博客园：[http://www.cnblogs.com/nnngu](http://www.cnblogs.com/nnngu/)  
简书：[https://www.jianshu.com/users/1df20d76ea5c](https://www.jianshu.com/users/1df20d76ea5c)  
知乎：[https://www.zhihu.com/people/nnngu/posts](https://www.zhihu.com/people/nnngu/posts)  

---

**java的数据类型分为：基本数据类型和引用数据类型。**

![][1]

**基本数据类型各占多少个字节：**

![][2]

关于boolean占几个字节，众说纷纭，虽然boolean表现出非0即1的“位”特性，但是存储空间的基本计量单位是字节，不是位。所以boolean至少占1个字节。 
JVM规范中，boolean变量当作int处理，也就是4字节；而boolean数组当做byte数组处理，即boolean类型的数组里面的每一个元素占1个字节。


  [1]: https://www.github.com/nnngu/FigureBed/raw/master/2018/1/21/1516469967556.jpg
  [2]: https://www.github.com/nnngu/FigureBed/raw/master/2018/1/20/1516444153746.jpg