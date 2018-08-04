# 线程安全的集合

## 高效的映射、集和队列（P673）

java.util.concurrent包提供了映射、有序集和队列的高效实现：**ConcurrentHashMap**、**ConcurrentSkipListMap**、**ConcurrentSkipListSet**和**ConcurrentLinkedQueue**。

这些集合使用复杂的算法，通过允许并发地访问数据结构的不同部分来使竞争极小化。与大多数集合不同，size方法不必在常量时间内操作。确定这样的集合当前的大小通常需要遍历。

集合返回弱一致性\(weakly consistent\)的迭代器。这意味着迭代器不一定能反映出它们被构造之后的所有的修改，但是，它们不会将同一个值返回两次，也不会抛出ConcurrentModificationException异常。

与之形成对照的是，集合如果在迭代器构造之后发生变化，java.util包中的迭代器将抛出一个ConcurrentModificationException异常。

* java.util.concurrent.ConcurrentLinkedQueue 5.0
  * ConcurrentLinkedQueue 
    * 构造一个可以被多线程安全访问的无边界非阻塞的队列
* java.util.concurrent.ConcurrentLinkedQueue 6
  * ConcurrentSkipListSet \(\)
  * ConcurrentSkipListSet \(Comparator&lt;? super E&gt; comp\)
    * 构造一个可以被多线程安全访问的有序集。第一个构造器要求元素实现Comparable接口。
* java.util.concurrent.ConcurrentHashMap 5.0
  * ConcurrentHashMap \(\)
  * ConcurrentHashMap \(int initialCapacity\)
  * ConcurrentHashMap \(int initialCapacity, float loadFactor , int concurrencyLevel\)
    * 构造一个可以被多线程安全访问的散列映射表
    * initialCapacity：集合的初始容量。默认值为16.
    * loadFactor：控制调整：如果每一个桶的平均负载超过这个因子，表的大小会被重新调整。默认值0.75
    * concurrencyLevel：并发写者线程的估计数目
* java.util.concurrent.ConcurrentSkipListMap 6
  * ConcurrentSkipListMap \(\)
  * ConcurrentSkipListMap \(Comparator&lt;? super K&gt; comp\)
    * 构造一个可以被多线程安全访问的有序的映像表。第二个构造函数要求键实现Comparable接口。

