# Java中ArrayList与LinkedList的区别
作者：[nnngu](https://github.com/nnngu)  
GitHub：[https://github.com/nnngu](https://github.com/nnngu)  
博客园：[http://www.cnblogs.com/nnngu](http://www.cnblogs.com/nnngu/)  
简书：[https://www.jianshu.com/users/1df20d76ea5c](https://www.jianshu.com/users/1df20d76ea5c)  
知乎：[https://www.zhihu.com/people/nnngu/posts](https://www.zhihu.com/people/nnngu/posts)  

---

**一般大家都知道ArrayList和LinkedList的区别：**

      1、ArrayList的实现是基于数组，LinkedList的实现是基于双向链表。
	  
      2、对于随机访问，ArrayList优于LinkedList

      3、对于插入和删除操作，LinkedList优于ArrayList

      4、LinkedList比ArrayList更占内存，因为LinkedList的节点除了存储数据，还存储了两个引用，一个指向前一个元素，一个指向后一个元素。

##  **一．在时间复杂度上的区别**

假设我们有两个很大的列表，它们里面的元素已经排好序了，这两个列表分别是ArrayList类型和LinkedList类型的，现在我们对这两个列表来进行二分查找(binary search)，比较它们的查找速度。

代码如下：

<pre> 1 package com.demo;
 2 
 3 import java.util.ArrayList;
 4 import java.util.Collections;
 5 import java.util.LinkedList;
 6 import java.util.List;
 7 
 8 public class Demo1 {
 9     static List<Integer> array = new ArrayList<Integer>();
10     static List<Integer> linked = new LinkedList<Integer>();
11 
12     public static void main(String[] args) {
13 
14         for (int i = 0; i < 10000; i++) {
15             array.add(i);
16             linked.add(i);
17         }
18         System.out.println("ArrayList访问消耗的时间：" + getTime(array));
19         System.out.println("LinkedList访问消耗的时间：" + getTime(linked));
20     }
21 
22     public static long getTime(List list) {
23         long time = System.currentTimeMillis();
24         for (int i = 0; i < 10000; i++) {
25             int index = Collections.binarySearch(list, list.get(i));
26             if (index != i) {
27                 System.out.println("ERROR!");
28             }
29         }
30         return System.currentTimeMillis() - time;
31     }
32 
33 }</pre>

运行结果：

<pre>ArrayList访问消耗的时间：10
LinkedList访问消耗的时间：383</pre>

可以看出，对于随机访问，ArrayList的访问速度更快。 

ArrayList和LinkedList的插入数据耗时：

<pre> 1 package com.demo;
 2 
 3 import java.util.ArrayList; 
 4 import java.util.LinkedList;
 5 import java.util.List;
 6 
 7 public class Demo2 {
 8     static List<Integer> array = new ArrayList<Integer>();
 9     static List<Integer> linked = new LinkedList<Integer>();
10 
11     public static void main(String[] args) {
12 
13         for (int i = 0; i < 10000; i++) {
14             array.add(i);
15             linked.add(i);
16         }
17         System.out.println("ArrayList插入消耗的时间：" + insertTime(array));
18         System.out.println("LinkedList插入消耗的时间：" + insertTime(linked));
19     }
20 
21     public static long insertTime(List list) {
22         long time = System.currentTimeMillis();
23         for (int i = 100; i < 10000; i++) {
24             list.add(10, i); // 在索引为10的位置插入i
25         }
26         return System.currentTimeMillis() - time;
27     }
28 }</pre>

运行结果：

<pre>ArrayList插入消耗的时间：31
LinkedList插入消耗的时间：4</pre>

可以看出，对于插入操作，LinkedList 的速度更快。

##  二．在空间复杂度上的区别

在LinkedList中有一个私有的内部类，定义如下：

<pre>private static class Entry {   
         Object element;   
         Entry next;   
         Entry previous;   
     }   </pre>

LinkedList中的每一个元素中还存储了它的前一个元素的索引和后一个元素的索引。

ArrayList使用一个内置的数组来存储元素，这个数组的起始容量是10，当数组需要增长时，新的容量按如下公式获得：新容量 = 旧容量&times;1.5 + 1，也就是说每一次容量大概会增长50% 

## 总结：

**ArrayList和LinkedList的区别如下：**

      1\. ArrayList的实现是基于数组，LinkedList的实现是基于双向链表。 
	  
      2\. 对于随机访问，ArrayList优于LinkedList，ArrayList可以根据下标以O(1)时间复杂度对元素进行随机访问。而LinkedList的每一个元素都依靠地址指针和它后一个元素连接在一起，在这种情况下，查找某个元素的时间复杂度是O(n) 

      3\. 对于插入和删除操作，LinkedList优于ArrayList，因为当元素被添加到LinkedList任意位置的时候，不需要像ArrayList那样重新计算大小或者是更新索引。 

　　4\. LinkedList比ArrayList更占内存，因为LinkedList的节点除了存储数据，还存储了两个引用，一个指向前一个元素，一个指向后一个元素。

