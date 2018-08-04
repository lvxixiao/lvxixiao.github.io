# 同步

## 竞争条件详解（P644）

假定两个线程同时执行指令

accounts\[to\] += amount;

问题在于这不是原子操作。该指令可能被处理如下： 1. 将accounts\[to\]加载到寄存器 2. 增加amount 3. 将结果写回accounts\[to\]

现在，假定第1个线程执行步骤1和2，然后，它被剥夺了运行权。假定第2个线程被唤醒并修改了accounts数组中的同一项。然后，第1个线程被唤醒并完成其第3步。

这样，这一动作擦去了第二个线程所做的更新。于是，总金额不再正确。

所谓原子操作，是指不会被线程调度机制打断的操作。原子操作不需要synchronized。

## 锁对象（P646-648）

Java提供一个synchronized关键字以及在JavaSE 5.0引入了ReentrantLock类，来防止代码块受并发访问的干扰。synchronized关键字自动提供一个锁以及相关的“条件”，对于大多数需要显式锁的情况，这是很便利的。

用ReentrantLock保护代码块的基本结构如下：

```text
myLock.lock();// a ReentrantLock object
try{
    critical section
}
finally {
    myLock.unlock();// make sure the lock is unlocked even if an exception is thrown
}
```

这一结构保证任何时刻只有一个线程进入临界区。一旦一个线程封锁了锁对象，其他任何线程都无法通过lock语句。当其他线程调用lock时，它们被阻塞，直到第一个线程释放锁对象。

如果使用锁，就不能使用带资源的try语句。首先，解锁方法名不是close。不过，即使将它重命名，带资源的try语句也无法正常工作。它的首部希望声明一个新变量。但是如果使用一个锁，你可能想使用多个线程共享的那个变量（而不是新变量）。

```text
带资源的try语句
try(BufferedReader br = new BufferedReader(new FileReader(path))){
    //do some thing
}
//br会在try结束后自动关闭。
```

如果两个线程试图访问同一个锁对象，那么锁以串行方式提供服务。如果两个线程访问不同的锁对象，两个线程不会发生阻塞。

锁是可重入的，因为线程可以重复地获得已经持有的锁。锁保持一个持有计数来跟踪对lock方法的嵌套调用。线程在每一次调用lock都要调用unlock来释放锁。被一个锁保护的代码可以调用另一个使用相同锁的方法。

* java.util.concurrentlocks.Lock 5.0
  * void lock\(\)
    * 获取这个锁；如果锁同时被另一个线程拥有则发生阻塞。
  * void unlock\(\)
    * 释放这个锁
* java.util.concurrent.locks.ReentrantLock 5.0
  * ReentrantLock\(\)
    * 构建一个可以被用来保护临界区的可重入锁
  * ReentrantLock\(boolean fair\)
    * 构建一个带有公平策略的锁。一个公平锁偏爱等待时间最长的线程。但是，这一公平的保证将大大降低性能。

      **条件对象（P648-652）**

      通常，线程进入临界区，却发现在某一条件满足之后它才能执行。要使用一个条件对象来管理那些已经获得了一个锁但是却不能做有用工作的线程。一个锁对象可以有一个或多个相关的条件对象。你可以用newCondition方法获得一个条件对象

      ```text
      class Bank {
      private Condition sufficientFunds;
      ...
      public Bank() {
      ...
      sufficientFunds = bankLock.newCondition();
      }
      }
      ```

      如果Bnak实例中的对象调用方法发现条件不满足，它调用

sufficientFunds.await\(\);

当前线程现在被阻塞了，并放弃了锁。

等待获得锁的线程和调用await方法的线程存在本质上的不同。一旦一个线程调用await方法，它进入该条件的等待集。当锁可用时，该线程不能马上解除阻塞。相反，它处于阻塞状态，直到另一个线程调用同一条件对象上的signalAll方法时为止。

这一调用重新激活因为这一条件而等待的所有线程。当这些线程从等待集当中移出时，它们再次称为可运行的，调度器将再次激活它们。同时，它们将试图重新进入该对象。一旦锁成为可用的，它们中的某个将从await调用返回，获得该锁并从阻塞的地方继续执行。

signalAll方法仅仅是通知正在等待的线程：此时可能已经满足条件，值得再去检测该条件。

如果没有其他线程来重新激活等待的线程，它就永远不再运行了。

```text
通常，对await的调用应该在如下形式的循环体中
public void transfer(int from,int to,int amount) {
    bankLock.lock();
    try{
        while(accounts[from] < amount)
            sufficientFunds.await();
        ...
        sufficientFunds.signalAll();
    } finally {
        bankLock.unlock();
    }
}
```

注意调用signalAll不会立即激活一个等待线程。它仅仅解除等待线程的阻塞，以便这些线程可以在当前线程退出同步方法之后，通过竞争实现对对象的访问。另一个方法signal，则是随机解除等待集中某个线程的阻塞状态。这比解除所有线程的阻塞更加有效，但也存在危险。如果随机选择的线程发现自己仍然不能运行，那么它再次被阻塞。

* java.util.concurrent.locks.Lock 5.0
  * Condition newCondition\(\)
    * 返回一个与该锁相关的条件对象
* java.util.concurrent.locks.Condition 5.0
  * void await\(\)
    * 将该线程放到条件的等待集中
  * void signalAll\(\)
    * 解除该条件的等待集中的所有线程的阻塞状态
  * void signal\(\)
    * 从该条件的等待集中随机地选择一个线程，解除其阻塞状态

      **synchronized关键字（P653 -656）**

      如果一个方法用synchronized关键字声明，那么对象的锁将保护整个方法。也就是说，要调用该方法，线程必须获得内部的对象锁。

      ```text
      public synchronized void method() {
      method body
      }
      等价于
      public void method() {
      this.intrinsicLock.lock();
      try{ 
      method body
      } finally {
      this.intrinsicLock.unlock();
      }
      }
      ```

      内部对象锁只有一个相关条件。wait方法添加一个线程到等待集中，notifyAll/notify方法解除等待线程的阻塞状态。换句话说，调用wait或notifyAll等价于

      ```text
      intrinsicCondition.await();
      intrinsicCondition.signalAll();
      ```

      每一个对象有一个内部锁，并且该锁有一个内部条件。由锁来管理那些试图进入synchronized方法的线程，由条件来管理那些调用wait的线程。将静态方法声明为synchronized也是合法的。如果调用这种方法，该方法获得相关的类对象的内部锁。类锁与对象锁互不干扰。

内部锁和条件存在一些局限，包括：

* 不能中断一个正在试图获得锁的线程
* 试图获得锁时不能设定超时
* 每个锁仅有单一的条件，可能是不够的
* java.lang.Object 1.0
  * void notifyAll\(\)
    * 解除那些在该对象上调用wait方法的线程的阻塞状态。该方法只能在同步方法或同步块内部调用。如果当前线程不是对象锁的持有者，该方法抛出一个IllegalMonitorStateException异常。
  * void notify\(\)
    * 随机选择一个在该对象上调用wait方法的线程，解除其阻塞状态。该方法只能在一个同步方法或同步块中调用。如果当前线程不是对象锁的持有者，该方法抛出一个IllegalMonitorStateException异常。
  * void wait\(\)
    * 导致线程进入等待状态直到它被通知。该方法只能在一个同步方法中调用。如果当前线程不是对象锁的持有者，该方法抛出一个IllegalMonitorStateException异常。
  * void wait\(long millis\)
  * void wait\(long millis,int nanos\)
    * millis：毫秒数
    * nanos：纳秒数，&lt; 1 000 000
    * 导致线程进入等待状态直到它被通知或者经过指定的时间。这些方法只能在一个同步方法中调用。如果当前线程不是对象锁的持有者该方法抛出一个IllegalMonitorStateException异常。
* java.lang.Thread
  * static native void sleep\(long millis\)
  * static native void sleep\(long millis,int nanos\)
    * millis：毫秒数
    * nanos：纳秒数，&lt; 1 000 000
    * 导致当前线程停止执行，直到经过特定的时间。服从于系统定时器与调度器，无法保证时间精确。

**wait\(\)与sleep\(\)的区别**

* Thread.sleep不会导致锁行为的变化，如果当前线程是拥有锁的，那么Thread.sleep不会让线程释放锁。
* Object.wait会使线程进入等待状态，并且释放锁。直到有其他线程调用notify\(\)或者notifyAll\(\)才能重新获得CPU执行时间。
* Thread.sleep和Object.wait都会暂停当前线程，对于CPU资源来说，不管是哪种方式暂停的线程，都表示它暂时不再需要CPU的执行时间。OS会将执行时间分配给其他线程。

  **同步阻塞（P656-657）**

  每一个Java对象有一个锁。线程可以通过调用同步方法获得锁。还有另一种机制可以获得锁，通过进入一个同步阻塞。

  ```text
  synchronized(obj) {// this is the syntax for a synchronized block
  critical section
  }
  ```

  **Volatile域（P658）**

  有时仅仅为了读写一个或者两个实例域就使用同步的话，显得开销过大，volatile关键字为实例域的同步访问提供了免锁的机制。如果声明一个域为volatile,那么编译器和虚拟机就知道该域是可能被另一个线程并发更新的。

一旦一个共享变量（类的成员变量、类的静态成员变量）被volatile修饰之后，那么就具备了两层语义：

* 保证了不同线程对这个变量进行操作时的可见性，即一个线程修改了某个变量的值，这新值对其他线程来说是立即可见的。
* 禁止进行指令重排序。

volatile变量不能提供原子性。通常来说，使用volatile必须具备以下两个条件：

* 对变量的写操作不依赖于当前值
* 该变量没有包含在具有其他变量的不变式中

volatile使用的主要两个场景

```text
//状态标志
volatile boolean shutdownRequested;
...
public void shutdown()
 { 
 shutdownRequested = true;
  }
public void doWork() { 
    while (!shutdownRequested) { 
        // do stuff
    }
}
```

```text
//双重检查模式（DCL）
public class Singleton {  
    private volatile static Singleton instance = null;  
    public static Singleton getInstance() {  
        if (instance == null) {  
            synchronized(this) {  
                if (instance == null) {  
                    instance = new Singleton();  
                }  
            }  
        }  
        return instance;  
    }  
}
```

DCL优点是资源利用率高，第一次执行getInstance时单例对象才被实例化，效率高。缺点是第一次加载时反应稍慢一些，在高并发环境下也有一定的缺陷，虽然发生的概率很小。 DCL虽然在一定程度解决了资源的消耗和多余的同步，线程安全等问题，但是他还是在某些情况会出现失效的问题，也就是DCL失效，在《java并发编程实践》一书建议用以下的代码\(静态内部类单例模式）来替代DCL：

```text
public class Singleton { 
    private Singleton(){
    }
      public static Singleton getInstance(){  
        return SingletonHolder.sInstance;  
    }  
    private static class SingletonHolder {  
        private static final Singleton sInstance = new Singleton();  
    }  
}
```

与锁相比，Volatile 变量是一种非常简单但同时又非常脆弱的同步机制，它在某些情况下将提供优于锁的性能和伸缩性。如果严格遵循 volatile 的使用条件即变量真正独立于其他变量和自己以前的值 ，在某些情况下可以使用 volatile 代替 synchronized 来简化代码。然而，使用 volatile 的代码往往比使用锁的代码更加容易出错。

## final变量（P659）

除了使用volatile修饰符，还有一种情况可以安全地访问一个共享域。

final Map accounts = new HashMap&lt;&gt;\(\);

其他线程会在构造函数完成构造后才看到这个accounts变量。如果不使用final，就不能保证其他线程看到的是accounts更新后的值，它们可能都只看到null，而不是新构造的HasmhMap。

当然，对这个映射表的操作并不是线程安全的。如果多个线程在读写这个映射表，仍然需要进行同步。

## 原子性（P659-661）

java.util.concurrent.atomic包中有很多类使用了很高效的机器级指令（而不是使用锁）来保证其他操作的原子性。

AtomicInteger类提供了方法incrementAndGet和decrementAndGet，它们分别以原子方式将一个整数自增或自减。

```text
//可以安全地生成一个数值序列
public static AtomicLong nextNumber = new ActomicLong();
//In some thread...
long id = nextNumber.incrementAndGet();
```

incrementAndGet方法以原子方式将AtomicLong自增，并返回自增后的值。也就是说，获得值、增1并设置然后生成新值的操作不会中断。可以保证即使是多个线程并发地访问同一个实例，也会计算并返回正确的值。

如果有大量线程要访问相同的原子值，性能会大幅下降，因为乐观更新需要太多次重试。Java SE 8 提供了LongAdder和LongAccumulator类来解决这个问题。

如果认为可能存在的大量竞争，只需要使用LongAdder而不是AtomicLong。

## 线程局部变量（P663-664）

使用ThreadLocal辅助类为各个线程提供各自的实例

* java,lang.ThreadLocal 1.2
  * T get\(\)
    * 得到这个线程的当前值。如果是首次调用set，会调用initialize来得到这个值。
  * protected initialize\(\)
    * 应覆盖这个方法来提供一个初始值。默认情况下，这个方法返回null。
  * void set\(T t\)
    * 为这个线程设置一个新值
  * void remove\(\)
    * 删除对应这个线程的值
  * static \ ThreadLocal\ withInitial\(Supplier&lt;? extends S&gt; supplier\) 8
    * 创建一个线程局部变量，其初始值通过调用给定的supplier生成。
* java.util.concurrent.ThreadLocalRandom 7
  * static ThreadLocalRandom current\(\)
    * 返回特定于当前线程的Random类实例

      **锁测试与超时（P665-666）**

      线程在调用lock方法来获得另一个线程所持有的锁的时候，很可能发生阻塞。应该更加谨慎地申请锁。tryLock方法试图申请一个锁，在成功获得锁后返回true，否则，立即返回false，而且线程可以立即离开去做其他事情。

      ```text
      if(myLock.tryLock()){
      //now the thread owns the lock
      try{
      ...
      } finally {
      myLock.unlock();
      }
      }
      ```

      lock方法不能被中断，如果一个线程在等待获得一个锁时被中断，中断线程在获得锁之前一直处于阻塞状态。如果出现死锁，那么lock方法就无法终止。

如果调用带有用超时参数的tryLock，那么如果线程在等待期间被中断，将抛出InterruptedException异常。这是一个非常有用的特性，因为允许程序打破死锁。也可以调用lockInterruptibly方法，它就相当于一个超时设为无限的tryLock方法。

如果一个线程被另一个线程通过调用signalAll或signal激活，或者超时时限已达到，或者线程被中断，那么await方法将返回。

如果等待的线程被中断，await方法将抛出一个InterrupteException异常。在你希望出现这种情况时线程继续等待（可能不太合理），可以使用awaitUninterruptibly方法代替await。

* java.util.concurrent.locks.Lock 5.0
  * boolean tryLock\(\)
    * 尝试获得锁而没有发生阻塞；如果成功返回真，这个方法会抢夺可用的锁，即使该锁有公平枷锁策略，即便其他线程已经等待很久也是如此。
  * boolean tryLock\(long time, TimeUnit unit\)
    * 尝试获得锁，阻塞时间不会超过给定的值；如果成功返回true。
  * void lockInterruptibly\(\)
    * 获得锁，但是会不确定发生阻塞。如果线程被中断，抛出一个InterrupteException异常。
* java.util.concurrent.locks.Condition 5.0
  * boolean await\(long time, TimeUnit unit\)
    * 进入该条件的等待集，直到线程从等待集中移出或等待了指定的时间之后才解除阻塞。
  * void awaitUninterruptibly\(\)
    * 进入该条件的等待集，直到线程从等待集移出才解除阻塞。如果线程被中断，该方法不会抛出InterruptedException异常。

      **读/写锁（P666）**

      如果很多线程从一个数据结构读取数据而很少线程修改其中数据的话，后者是十分有用的。在这种情况下，允许对读者线程共享访问是合适的。当然，写者线程依然必须是互斥访问的。

下面是使用读/写锁的必要步骤： 1. 构造一个ReentrantReadWriteLock对象。 2. 抽取读锁和写锁。 3. 对所有的获取方法加读锁。 4. 对所有的修改方法加写锁。

```text
//步骤1
private ReentrantReadWriteLock rw1 = new ReentrantReadWriteLock();
//步骤2
private Lock readLock = rw1.readLock();
private Lock writeLock = rw1.writeLock();
//步骤3
public double getTotalBalance(){
    readLock.lock();
    try{...}
    finally{ readLock.unlock();}
}
//步骤4
public void transfer(...){
    writeLock.lock();
    try{}
    finally{writeLock.unlock();}
}
```

* java.util.concurrent.locks.ReentranReadWriteLock 5.0
  * Lock readLock\(\)
    * 得到一个可以被多个读操作共用的读锁，但会排斥所有写操作。
  * Lock writeLock\(\)
    * 得到一个写锁，排斥所有其他的读操作和写操作。

