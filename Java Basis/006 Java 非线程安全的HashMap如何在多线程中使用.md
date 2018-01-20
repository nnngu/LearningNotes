# Java 非线程安全的HashMap如何在多线程中使用
作者：[nnngu](https://github.com/nnngu)  
GitHub：[https://github.com/nnngu](https://github.com/nnngu)  
博客园：[http://www.cnblogs.com/nnngu](http://www.cnblogs.com/nnngu/)  
简书：[https://www.jianshu.com/users/1df20d76ea5c](https://www.jianshu.com/users/1df20d76ea5c)  
知乎：[https://www.zhihu.com/people/nnngu/posts](https://www.zhihu.com/people/nnngu/posts)  

---


HashMap 是非线程安全的。在多线程条件下，容易导致死循环，具体表现为CPU使用率100%。因此多线程环境下保证 HashMap 的线程安全性，主要有如下几种方法：

1.  使用 java.util.Hashtable 类，此类是线程安全的。

2.  使用 java.util.concurrent.ConcurrentHashMap，此类是线程安全的。

3.  使用 java.util.Collections.synchronizedMap() 方法包装 HashMap object，得到线程安全的Map，并在此Map上进行操作。

4.  自己在程序的关键代码段加锁，保证多线程安全（不推荐）

### 接下来分析上面列举的几种方法实现并发安全的 HashMap 的原理：

（一）java.util.Hashtable类：

查看该类的源码

<pre>public synchronized V get(Object key) {  
    …… //具体的实现省略，请参考 jdk实现  
}  

public synchronized V put(K key, V value) {  
    …… //具体的实现省略，请参考 jdk实现  
}  

public synchronized V remove(Object key) {  
    …… //具体的实现省略，请参考 jdk实现  
}  </pre>

     上面是 Hashtable 类提供的几个主要方法，包括 get()，put()，remove() 等。注意到**每个方法本身都是 synchronized 的，不会出现两个线程同时对数据进行操作的情况，因此保证了线程安全性，但是也大大的降低了执行效率**。因此是不推荐的。

（二）使用 java.util.concurrent.ConcurrentHashMap 类：

该类是 HashMap 的线程安全版，与 Hashtable 相比， ConcurrentHashMap 不仅保证了访问的线程安全性，而且在效率上有较大的提高。

ConcurrentHashMap的数据结构如下：

![][1]

可以看出，相对 HashMap 和 Hashtable， ConcurrentHashMap 增加了Segment 层，每个Segment 原理上等同于一个 Hashtable， ConcurrentHashMap 等同于一个 Segment 的数组。下面是 ConcurrentHashMap 的 put 和 get 方法：

<pre>final Segment<K,V> segmentFor(int hash) {  
    return segments[(hash >>> segmentShift) & segmentMask];  
}  

public V put(K key, V value) {  
    if (value == null)  
        throw new NullPointerException();  
    int hash = hash(key.hashCode());  
    return segmentFor(hash).put(key, hash, value, false);  
}  

public V get(Object key) {  
    int hash = hash(key.hashCode());  
    return segmentFor(hash).get(key, hash);  
}  </pre>

向 ConcurrentHashMap 中插入数据(put) 或者 读取数据(get)，首先都要将相应的 Key 映射到对应的 Segment，**因此不用锁定整个类， 只要对单个的 Segment 操作进行上锁操作就可以了**。理论上如果有 n 个 Segment，那么最多可以同时支持 n 个线程的并发访问，从而大大提高了并发访问的效率。


  [1]: https://www.github.com/nnngu/FigureBed/raw/master/2018/1/21/1516471388763.jpg