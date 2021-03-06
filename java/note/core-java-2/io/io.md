# 输入、输出流

## 输入/输出流（P39）

在Java API中，可以从其中读入一个字节序列的对象称做输入流，而可以向其中写入一个字节的对象称做输出流。

抽象类InputStream和OutputStream构成了输入/输出\(I/O\)类层次结构的基础。

因为面向字节的流不便于处理以Unicode形式存储的信息，所以从抽象类Reader和Writer中继承出来了一个专门用于处理Unicode字符的单独的类层次结构。这些类拥有的读入和写入操作都是基于两字节的Char值\(即，Unicode码元\)，而不是基于byte值的。

## 读写字节（P39-40）

InputStream类有一个抽象方法

```text
abstract int read()
```

这个方法将读入一个字节，并返回读入的字节，或者在遇到输入源结尾时返回-1.在设计具体的输入流时，必须覆盖这个方法以提供适用的功能。

InputStream类还有若干个非抽象的方法，它们可以读入一个字节数组，或者跳过大量的字节。这些方法都要调用抽象的read方法，因此，各个子类都只需覆盖这一个方法。

OutputStream类定义了下面的抽象方法

```text
abstract void write(int b)
```

它可以向某个输出位置写出一个字节。

read和write方法在执行时都将阻塞，直至字节确实被读入或写出。这就意味着如果流不能被立即访问，那么当前的线程将被阻塞。这使得在这两个方法等待指定的流变为可用的这段时间里，其他的线程就有机会去执行有用的工作。

available方法使我们可以去检查当前可读入的字节数量，这意味着像下面这样的代码片段就不可能被阻塞。

```text
int bytesAvailable = in.available();
if(bytesAvailable > 0) {
    byte[] data = new byte[bytesAvailable];
    in.read(data);
}
```

当你完成对输入/输出流的读写时，应该通过调用close方法来关闭它，这个调用会释放掉十分有限的操作系统资源。如果一个应用程序打开了过多的输入/输出流而没有关闭，那么系统资源将被耗尽。关闭一个输出流的同时还会冲刷用于该输出流的缓冲区：所有被临时置于缓冲区中，以便用更大的包的形式传递的字节在关闭输出流时都将被送出。特别是，如果不关闭文件，那么写出字节的最后一个包可能将永远也得不到传递。当然，我们还可以用flush方法来人为地冲刷这些输出。

## API（P40-41）

* java.io.InputStream 1.0
  * abstract int read\(\)
    * 从数据中读入一个字节，并返回该字节。这个read方法在碰到输入流的结尾时返回-1。
  * int read\(byte\[\] b\)
    * 读入一个字节数组，并返回实际读入的字节数，或者在碰到输入流的结尾时返回-1。这个read方法最多读入b.lengths个字节
  * int read\(byte\[\] b , int off, int len\)
    * 读入一个字节数组。这个read方法返回实际读入的字节数，或者在碰到输入流的结尾时返回-1。
      * b：数据读入的数组
      * off：第一个读入字节应该被放置的位置在b中的偏移量。
      * len：读入字节的最大数量
  * lng skip\(long n\)
    * 在输入流中跳过n个字节，返回实际跳过的字节数（如果碰到输入流的结尾，则可能小于n）
  * int available\(\)
    * 返回在不阻塞的情况下可获取的字节数\(阻塞意味着当前线程将失去它对资源的占用\)。
  * void close\(\)
    * 关闭这个输入流
  * void mark\(int readlimit\)
    * 在输入流的当前位置打一个标记（并非所有的流都支持这个特性）。如果从输入流中已经读入的字节多于readlimit个，则这个流允许忽略这个标记。
  * void reset\(\)
    * 返回到最后一个标记，随后对read的调用将重新读入这些字节。如果当前没有任何标记，则这个流不被重置。
  * boolean markSupported\(\)
    * 如果这个流支持打标记，则返回true。
* java.io.OutputStream 1.0
  * abstract void write\(int n\)
    * 写出一个字节的数据
  * void write\(byte\[\] b\)
  * void write\(byte\[\] b, int off, int len\)
    * 写出数组b中的所有字节或者某个范围内的字节
      * b：数据写出的数组
      * off：第一个写出字节在b中的偏移量
      * len：写出字节的最大数量
  * void close\(\)
    * 冲刷并关闭输出流。
  * void flush\(\)
    * 冲刷输出流，也就是将所有缓冲的数据发送到目的地。

