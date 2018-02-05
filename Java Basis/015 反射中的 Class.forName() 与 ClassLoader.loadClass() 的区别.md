# 015 反射中的 Class.forName() 与 ClassLoader.loadClass() 的区别

笔记仓库：[https://github.com/nnngu/LearningNotes](https://github.com/nnngu/LearningNotes)    

---

Class.forName() 与 ClassLoader.loadClass() 大家都知道是反射用来构造类的方法，但是他们的用法还是有一定区别的。

 在讲区别之前，我觉得很有必要把类的加载过程在此整理一下。

 在Java中，类加载器把一个类加载进Java虚拟机中，要经过三个步骤来完成：加载、链接和初始化，其中链接又可以分成验证、准备和解析三步，除了解析外，其它步骤是严格按照顺序完成的，各个步骤的主要工作如下：
 
* 加载：查找和导入类或接口的二进制数据； 

* 链接：执行下面的校验、准备和解析步骤，其中解析步骤是可以选择的； 

  * 验证：检查导入类或接口的二进制数据的正确性； 

  * 准备：给类的静态变量分配并初始化存储空间； 

  * 解析：将符号引用转成直接引用； 

* 初始化：激活类的静态变量的初始化Java代码和静态Java代码块。

![][1]

 于是乎我们可以开始看2者的区别了。
 
Class.forName(className) 方法，其实调用的方法是Class.forName(className,true,classloader); 注意看第2个boolean参数，它表示的意思，在加载之后必须初始化。在执行过此方法后，目标对象的静态块代码已经被执行，静态参数也已经被初始化。

再看ClassLoader.loadClass(className) 方法，其实他调用的方法是ClassLoader.loadClass(className,false); 注意看第2个 boolean 参数，该参数表示目标对象被加载后不进行链接，这就意味着不会去执行该类静态块中的内容。因此两者的区别就显而易见了。




  [1]: https://www.github.com/nnngu/FigureBed/raw/master/2018/1/26/1516908236033.jpg