# 010 有顺序的Map的实现类：TreeMap和LinkedHashMap
作者：[nnngu](https://github.com/nnngu)  
GitHub：[https://github.com/nnngu](https://github.com/nnngu)  
博客园：[http://www.cnblogs.com/nnngu](http://www.cnblogs.com/nnngu/)  
简书：[https://www.jianshu.com/users/1df20d76ea5c](https://www.jianshu.com/users/1df20d76ea5c)  
知乎：[https://www.zhihu.com/people/nnngu/posts](https://www.zhihu.com/people/nnngu/posts)  

---

Map主要用于存储健值对，根据键得到值，因此不允许键重复，但允许值重复。

## HashMap

　　说到Map，首先能想起的是HashMap，它是一个最常用的Map，它根据键的HashCode 来存储数据，根据键可以直接获取它的值，具有很快的访问速度。**遍历时，取得数据的顺序是完全随机的。**
  
　　HashMap最多只允许一条记录的键为Null；允许多条记录的值为 Null。（不允许键重复，但允许值重复）
  
　　HashMap不支持线程的同步（任一时刻可以有多个线程同时写HashMap，即线程非安全），可能会导致数据的不一致。如果需要同步，可以用 Collections的synchronizedMap() 方法使HashMap具有同步的能力，或者使用ConcurrentHashMap。
  
　　Hashtable与 HashMap类似。不同的是：它不允许记录的键或者值为空；它支持线程的同步（任一时刻只有一个线程能写Hashtable，即线程安全），因此也导致了 Hashtable 在写入时会比较慢。

## TreeMap

　　TreeMap实现SortMap接口，能够把它保存的记录根据键排序。
  
　　**默认是按键的升序排序，也可以指定排序的比较器**，当用Iterator 遍历TreeMap时，得到的记录是排过序的。

## LinkedHashMap

**LinkedHashMap保存了记录的插入顺序，在用Iterator遍历LinkedHashMap时，先得到的记录肯定是先插入的**。

在遍历的时候会比HashMap慢，不过有种情况例外：当HashMap容量很大，实际数据较少时，遍历起来可能会比LinkedHashMap慢。因为LinkedHashMap的遍历速度只和实际数据有关，和容量无关，而HashMap的遍历速度和它的容量有关。

## 三种类型的Map分别在什么时候使用

　　1、一般情况下，我们用的最多的是HashMap。HashMap里面存入的值在取出的时候是随机的，它根据键的HashCode来存储数据，根据键可以直接获取它的值，具有很快的访问速度。在Map 中插入、删除和定位元素，HashMap 是最好的选择。
  
　　2、TreeMap取出来的是排序后的键值对。但如果您要按自然顺序或自定义顺序遍历键，那么TreeMap会更好。
  
　　3、LinkedHashMap 是HashMap的一个子类，如果需要输出的顺序和输入的顺序相同，那么用LinkedHashMap可以实现。

