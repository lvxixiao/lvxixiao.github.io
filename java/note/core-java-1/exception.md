# 异常

## 异常分类（P265-267）

在Java程序设计中，所有的异常都是由Throwable继承而来，但在下一层立即分解为两个分支：Error和Exception。

Error类层次结构描述了Java运行时系统的内部错误和资源耗尽错误。应用程序不应该抛出这种类型的对象。如果出现了这样的内部错误，除了通告给用户，并尽力使程序安全地终止之外，再也无能为力了。

Exception层次结构又分解为两个分支：一个分支派生于RuntimeException。另一个分支包含其他异常。划分两个分支的规则是：由程序错误导致的异常属于RuntimeException；而程序本身没有问题，但由于像I/O错误这类问题导致的异常属于其他异常。

派生于RuntimeException的异常包含下面几种情况：

* 错误的类型转换
* 数组访问越界
* 访问null指针

不是派生于RuntimeException的异常包括：

* 试图在文件尾部后面读取数据
* 试图打开一个不存在的文件
* 试图根据给定的字符串查找Class对象，而这个字符串表示的类并不存在

**Java语言规范将派生于Error类或RuntimeException类的所有异常称为非受查\(unchecked\)异常，所有其他的异常称为受查\(checked\)异常。编译器将核查是否为所有的受查异常提供了异常处理器。**

## throw 和 throws

* throws
  * 声明这个方法会抛出这种类型的异常，使其他地方调用它时知道要捕获这个异常
* throw
  * try/catch模块中抛出一个异常对象，表示异常已经发生，会终止程序运行\(finally依然会运行\)。

## 带资源的try语句（P278-279）

假设资源属于一个实现了AutoCloseable接口的类，Java SE 7 为这种代码模式提供了一个很有用的快捷方式。AutoCloseable接口有一个方法：

```text
void close() throws Exception
```

另外，还有一个Closeable接口。这是AutoCloseable的子接口，也包含一个close方法。不过，这个方法声明为抛出一个IOException。

带资源的try语句\(try-with-resources\)的最简形式为：

```text
try(Resource res = ...) {
    work with res
}
```

try块退出时，会自动调用res.close\(\)，catch和finally子句会在关闭资源之后执行。

不带资源的try语句如果try块抛出一个异常，而且close方法也抛出一个异常，这就会带来一个难题：try块抛出的异常会被close方法抛出的异常覆盖。带资源的try语句可以很好地处理这种情况。原来的异常会重新抛出，而close方法抛出的异常会“被抑制”。这些异常将自动捕获，并由addSuppressed方法增加到原来的异常。如果对这些异常感兴趣，可以调用getSuppressed方法，它会得到从close方法抛出并被抑制的异常列表。

## 分析堆栈轨迹元素（P280-281）

堆栈轨迹\(stack trace\)是一个方法调用过程的列表，它包含了程序执行过程中方法调用的特定位置。当Java程序正常终止，而没有捕获异常时，这个表就会显示出来。

可以调用Throwable类的printStackTrace方法访问堆栈轨迹的文本描述信息。

```text
Throwable t = new Throwable();
StringWriter out = new StringWriter();
t.printStackTrace(new PrintWriter(out));
String description = out.toString();
```

一种更灵活的方法是使用getStackTrace方法，它会得到StackTraceElement对象的一个数组。StackTraceElement类含有能够获得文件名和当前执行的代码行号的方法，同时，还含有能够获得类名和方法名的方法。

```text
Throwable t = new Throwable();
StackTraceElement[] frames = t.getStackTrace();
for(StackTraceElement frame : frames)
    analyze frame
```

静态的Thread.getAllStackTrace方法，它可以产生所有线程的堆栈轨迹。

```text
Map<Thread,StackTraceElement[]> map = Thread.getAllStackTraces();
for(Thread t : map.keySet()) {
    StackTraceElement[] frames = map.get(t);
    analyze frames
}
```

## 相关API

* java.lang.Throwable 1.0
  * Throwable\(Throwable cause\) 1.4
  * Throwable\(String message , Throwable cause\) 1.4
    * 用给定的“原因”构造一个Throwable对象。
  * Throwable initCause\(Throwable cause\) 1.4
    * 将这个对象设置为“原因”。如果这个对象已经被设置为“原因”，则抛出一个异常。返回this引用。
  * Throwable getCause\(\) 1.4
    * 获得设置为这个对象的“原因”的异常对象。如果没有设置“原因”，则返回null。
  * StackTraceElement\[\] getStackTrace\(\) 1.4
    * 获得构造这个对象时调用堆栈的跟踪。
  * void addSuppressed\(Throwable t\) 7
    * 为这个异常增加一个“抑制”异常。这出现在带资源的try语句中，其中t是close方法抛出的一个异常。
  * Throwable\[\] getSuppressed\(\) 7
    * 得到这个异常的所有“抑制”异常。一般来说，这些是带资源的try语句中close方法抛出的异常。
* java.langh.Exception 1.0
  * Exception\(Throwable cause\) 1.4
  * Exception\(String message , Throwable cause\)
    * 用给定的“原因”构造一个异常对象。
* java.lang.RuntimeException 1.0
  * RuntimeException\(Throwable cause\) 1.4
  * RuntimeException\(String message, Throwable cause\) 1.4
    * 用给定的“原因”构造一个RuntimeException对象。
* java.lang.StackTraceElement 1.4
  * String getFileName\(\)
    * 返回这个元素运行时对应的源文件名。如果这个信息不存在，则返回null。
  * int getLineNumber\(\)
    * 返回这个元素运行时对应的源文件行数。如果这个信息不存在，则返回 -1。
  * String getClassName\(\)
    * 返回这个元素运行时对应的类的完全限定名。
  * String getMethodNmae\(\)
    * 返回这个元素运行时对应的方法名。构造器名是；静态初始化器名是。这里无法区分同名的重载方法。
  * boolean isNativeMethod\(\)
    * 如果这个元素运行时在一个native方法中，则返回true。
  * String toString\(\)
    * 如果存在的话，返回一个包含类名、方法名、文件名和行数的格式化字符串。

