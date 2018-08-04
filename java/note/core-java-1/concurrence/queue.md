# 阻塞队列

## 阻塞队列（P668-673）

java.util.concurrent包提供了阻塞队列的几个变种。

LinkedBlockingQueue的容量是没有上边界的，但是，也可以选择指定最大容量。

LinkedBlockingDeque是一个双端的版本。ArrayBlockingQueue在构造时需要指定容量，并且有一个可选的参数来指定是否需要公平性。

PriorityBlockingQueue是一个带优先级的队列，而不是先进先出队列。元素按照它们的优先级顺序被移出。该队列是没有容量上限的。

**阻塞队列方法**

| 方法 | 正常动作 | 特殊情况下的动作 |
| :--- | :--- | :--- |
| add | 添加一个元素 | 如果队列满，则抛出IllegalStateException异常 |
| element | 返回队列的头元素 | 如果队列空，抛出NoSuchElementException异常 |
| offer | 添加一个元素并返回true | 如果队列满，返回false |
| peek | 返回队列的头元素 | 如果队列空，则返回null |
| poll | 移出并返回队列的头元素 | 如果队列空，则返回null |
| put | 添加一个元素 | 如果队列满，则阻塞 |
| remove | 移出并返回头元素 | 如果队列空，则抛出NoSuchElementException异常 |
| take | 移出并返回头元素 | 如果队列空，则阻塞 |

* java.util.concurrent.ArrayBlockingQueue 5.0
  * ArrayBlockingQueue\(int capacity\)
  * ArrayBlockingQueue\(int capacity, boolean fair\)
    * 构造一个带有指定的容量和公平性设置的阻塞队列。该队列用循环数组实现。
* java.util.concurrent.LinkedBlockingQueue 5.0
  * LinkedBlockingQueue\(\)
    * 构造一个无上限的阻塞队列，用链表实现
  * LinkedBlockingQueue\(int capacity\)
    * 根据指定容量构造一个有限的阻塞队列，用链表实现
* java.util.concurrent.LinkedBlockingDeque 6
  * LinkedBlockingDeque\(\)
    * 构造一个无上限的阻塞双向队列，用链表实现
  * LinkedBlockingDeque\(int capacity\)
    * 根据指定容量构造一个有限的阻塞双向队列，用链表实现
* java.util.concurrent.DelayQueue 5.0
  * DelayQueue\(\)
    * 构造一个包含Delayed元素的无界的阻塞时间有限的阻塞队列。只有那些延迟已经超过时间的元素可以从队列移出。
* java.util.concurrent.Delayed 5.0
  * long getDelay\(TimeUnit unit\)
    * 得到该对象的延迟，用给定的时间单位进行度量 
* java.util.concurrent.PriorityBlockingQueue 5.0
  * PriorityBlockingQueue\(\)
  * PriorityBlockingQueue\(int initialCapacity\)
  * PriorityBlockingQueue\(int initialCapacity, Comparator&lt;? super E&gt; comparator\)
    * 构造一个无边界阻塞优先队列，用堆实现
    * initialCapacity：优先队列的初始容量。默认值是11。
    * comparator：用来对元素进行的比较器，如果没有指定，则元素必须实现Comparable接口。
* java.util.concurrent.TransferQueue 7
  * void transfer\(E element\)
  * boolean tryTransfer\(E element, long time , TimeUnit unit\)
    * 传输一个值，或者尝试在给定的超时时间内传输这个值，这个调用将阻塞，直到另一个线程将元素删除。第二个方法会在调用成功时返回true。

