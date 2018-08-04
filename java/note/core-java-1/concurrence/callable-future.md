# Callable与Future

## Callable（P681）

Runnable封装一个异步运行的任务，可以把它想象成为一个没有参数和返回值的异步方法。Callable与Runnable类似，但是有返回值。Callable接口是一个参数化的类型，只有一个方法call。

```text
public interface Callable<V>{
    v call() throws Exception;
}
```

类型参数是返回值的类型。例如，Callable表示一个最终返回Integer对象的异步计算。

## Future（P681）

Future保存异步计算的结果。可以启动一个计算，将Future对象交给某个线程，然后忘掉它。Future对象的所有者在结果计算好之后就可以获得它。

Future接口具有以下方法

```text
public interface Future<V>{
    V get() throws ...;
    V get(long timeout,TimeUnit unit) throws ...;
    void cancel(boolean mayInterrupt);
    boolean isCancelled();
    boolean isDone();
}
```

第一个get方法的调用被阻塞，直到计算完成。如果在计算完成之前，第二个get方法的调用超时，抛出一个TimeoutException异常。如果运行该计算的线程被中断，两个方法都将抛出InterruptedException。如果计算已经完成，那么get方法立即返回。

如果计算还在进行，isDone方法返回false；如果完成了，则返回true。

可以用cancel方法取消计算。如果计算还没有开始，它被取消且不再开始。如果计算处于运行之中，那么如果mayInterrupt参数为true，它就被中断。

## FutureTask包装器\(P681\)

FutureTask包装器是一种非常便利的机制，可将Callable转换成Future和Runnable，它同时实现二者的接口。

```text
Callable<Integer> myComputation = ...;
FutureTask<Integer> task = new FutureTsk<Integer>(myComputation);
Thread t = new Thread(task); // it's a Runnable
t.start();
...
Integer result = task.get();// it's  Future
```

## 相关API

* java.util.concurrent.Callable 5.0
  * V call\(\)
    * 运行一个将产生结果的任务。
* java.util.concurrent.Future 5.0
  * V get\(\)
  * V get\(long time, TimeUnit unit\)
    * 获取结果，如果没有结果可用。则阻塞直到真正得到结果超过指定的时间为止。如果不成功，第二个方法会抛出TimeoutException异常。
  * boolean cancel\(boolean mayInterrupt\)
    * 尝试取消这一任务的运行。如果任务已经开始，并且mayInterrupt参数值为true，它就会被中断。如果成功执行了取消操作，返回true。
  * boolean isCancelled\(\)
    * 如果任务在完成前被取消了，则返回true。
  * boolean isDone\(\)
    * 如果任务结束，无论是正常结束、中途取消或发生异常，都返回true。
* java.util.concurrent.FutureTask 5.0
  * FutureTask\(Callable task\)
  * FutureTask\(Runnable task,V result\)
    * 构造一个即是Future又是Runnable的对象

