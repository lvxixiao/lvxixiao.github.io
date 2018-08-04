# 线程

## 执行器（P685）

构建一个新的线程是有一定代价的，因为涉及与操作系统的交互。如果程序中创建了大量的生命周期很短的线程，应该使用线程池\(thread pool\)。一个线程池中包含许多准备运行的空闲线程。将Runnable对象交给线程池，就会有一个线程调用run方法。当run方法退出时，线程不会死亡，而是在池中准备为下一个请求提供服务。

另一个使用线程池的理由是减少并发线程的数目。创建大量线程会大大降低性能甚至使虚拟机崩溃。

执行器\(Exceutors\)类有许多静态工厂方法用来构建线程池，下表对这些方法进行了汇总。

| 方法 | 描述 |
| :--- | :--- |
| newCachedThreadPool | 必要时创建新线程；空闲线程会被保留60秒。 |
| newFixedThreadPool | 该池包含固定数量的线程；空闲线程会一直被保留 |
| newSingleThreadExecutor | 只有一个线程的“池”，该线程顺序执行每一个提交的任务 |
| newScheduledThreadPool | 用于预定执行而构建的固定线程池，替代java.util.Timer |
| newSingleThreadScheduledExecutor | 用于预定执行而构建的单线程“池” |

## 线程池（P685-689）

**newCachedThreadPool**方法构建了一个线程池，对于每个任务，如果有空闲线程可用，立即让它执行任务，如果没有可用的空闲线程，则创建一个新线程。

**newFixedThreadPool**方法构建一个具有固定大小的线程池。如果提交的任务数多余空闲的线程数，那么把得不到服务的任务放置到队列中。当其他任务完成以后再运行它们。

**newSingleThreadExecutor**是一个退化了的大小为1的线程池：由一个线程执行提交的任务，一个接着一个。

这3个方法返回实现了ExecutorService接口的ThreadPoolExecutorl类的对象。

可用下面的方法之一将一个Runnable对象或Callable对象提交给ExecutorService

```text
Future<?> submit(Runable task)
Future<T> submit(Runable task,T result)
Future<T> submit(Callable<T> task)
```

该池会在方便的时候尽早执行提交的任务。调用submit时，会得到一个Future对象，可用来查询该任务的状态。

第一个submit方法返回一个奇怪样子的Future&lt;?&gt;。可以使用这样一个对象来调用isDone、cancel或isCancelled。但是，get方法在完成的时候只是简单地返回null。

第二个版本的submit也提交一个Runnable，并且返回的Future对象将在计算结果准备好的时候得到它。

第三个版本的submit提交一个Callable，并且返回的Future对象将在计算结果准备好的时候得到它。

当用完一个线程池的时候，调用shutdown。该方法启动该池的关闭序列。被关闭的执行器不再接受新的任务。当所有任务都完成以后，线程池中的线程死亡。另一种方法是调用shutdownNow。该池取消尚未开始的所有任务并试图中断正在运行的线程。

下面总结了在使用连接池时应该做的事： 1. 调用Executors类中的静态方法newCachedThreadPool或newFixedThreadPool 2. 调用submit提交Runnable或Callable对象 3. 如果想要取消一个任务，或如果提交Callable对象，那就要保存好返回的Future对象

1. 当不再提交任何任务时，调用shutdown

* java.util.concurrent.Executors 5.0
  * ExecutorService newCachedThreadPool\(\)
    * 返回一个带缓存的线程池，该池在必要的时候创建线程，在线程空闲60秒之后终止线程。
  * ExecutorService newFixedThreadPool\(int threads\)
    * 返回一个线程池，该池中的线程数由参数指定。
  * ExecutorService newSingleThreadExecutor\(\)
    * 返回一个执行器，它在一个单个的线程中依次执行各个任务。
* java.util.concurrent.ExecutorService 5.0
  * Future&lt;?&gt; submit\(Runable task\)
  * Future submit\(Runable task,T result\)
  * Future submit\(Callable task\)
    * 提交指定的任务去执行。
  * void shutdown\(\)
    * 关闭服务，会先完成已经提交的任务而不再接受新的任务。
* java.util.concurrent.ThreadPoolExecutor 5.0
  * int getLargestPoolSize\(\)
    * 返回线程池在该执行器生命周期中的最大尺寸。

      **预定执行（P689-690）**

      ScheduledExecutorService接口具有为预定执行\(Scheduled Execution\)或重复执行任务而设计的方法。

Executors类的newScheduledThreadPool和newSingleThreadScheduleExecutor方法将返实现了ScheduledExecutorService接口的对象。可以预定Runnable或Callable在初始的延迟之后只运行一次。也可以预定一个Runnable对象周期性地运行。

* java.util.concurrent.Executors 5.0
  * ScheduledExecutorService newScheduledThreadPool\(int threads\)
    * 返回一个线程池，它使用给定的线程数来调度任务。
  * ScheduledExecutorService newSingleThreadScheduledExecutor\(\)
    * 返回一个执行器，它在一个单独线程中调度任务。
* java.util.concurrent.ScheduledExecutorService 5.0
  * ScheduledFuture schedule\(Callable task, long time, TimeUnit unit\)
  * ScheduledFuture&lt;?&gt; schedule\(Runnable task, long time, TimeUnit unit\)
    * 预定在指定的时间之后执行任务。
  * ScheduledFuture&lt;?&gt; scheduleAtFixedRate\(Runnable task, long initialDelay, long period, TimeUnit unit\)
    * 预定在初始的延迟结束后，周期性地运行给定的任务，周期长度是period。
  * ScheduledFuture&lt;?&gt; scheduleWithFixedDelay\(Runnable task, long initialDelay,long delay,TimeUnit unit\)
    * 预定在初始的延迟结束后周期性地运行给定的任务，在一次调用完成和下一次调用开始之间有长度为delay的延迟。

      **控制任务组\(P690-691\)**
* java.util.concurrent.ExecutorService 5.0
  * T invokeAny\(Collection&gt; tasks\)
  * T invokeAny\(Collection&gt; tasks,long timeout,TimeUnit unit\)
    * 执行给定的任务，返回其中一个任务的结果。第二个方法若发生超时，抛出一个TimeoutException异常。
  * List&gt; invokeAll\(Collection&gt; tasks\)
  * List&gt; invokeAll\(Collection&gt; tasks, long timeout,TimeUnit unit\)
    * 执行给定的任务，返回所有任务的结果。第二个方法若发生超时，抛出一个TimeoutException异常。
* java.util.concurrent.ExecutorCompletionService 5.0
  * ExecutorCompletionService\(Executor e\)
    * 构建一个执行器完成服务来收集给定执行器的结果。
  * Future submit\(Callable task\)
  * Future submit\(Runnable task, V result\)
    * 提交一个任务给底层的执行器
  * Future take\(\)
    * 移除下一个已完成的结果，如果没有任何已完成的结果可用则阻塞。
  * Future poll\(\)
  * Future poll\(long time,TimeUnit unit\)
    * 移除下一个已完成的结果，如果没有任何已完成结果可用则返回null。第二个方法将等待给定的时间。

