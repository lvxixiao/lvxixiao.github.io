# 内存映射文件

## 内存映射文件（P96）

大多数操作系统都可以利用虚拟内存实现来将一个文件或者文件的一部分“映射”到内存中。然后，这个文件就可以当作是内存数组一样地访问，这比传统的文件操作要快得多。

## 内存映射文件的性能（P96-98）

内存映射比使用带缓冲的顺序输入要稍微快一点，但是比使用RandomAccessFile快很多。对于中等尺寸文件的顺序读入没有必要使用内存映射。

java.nio包使内存映射变得十分简单。首先，从文件中获得一个通道（channel），通道是用于磁盘文件的一种抽象，它使我们可以访问诸如内存映射、文件枷锁机制以及文件间快速数据传递等操作系统特性。

```text
FileChannel channel = FileChannel.open(path, options);
```

然后，通过调用FileChannel类的map方法从这个通道中获得一个ByteBuffer。可以指定想要映射的文件区域与映射模式。

一旦有了缓冲区，就可以使用ByteBuffer类和Buffer超类的方法读写数据了。

缓冲区支持顺序和随机数据访问，它有一个可以通过get和put操作移动的位置。例如，可以像下面这样顺序遍历缓冲区中的所有字节

```text
while(buffer.hasRemaining()) {
    byte b = buffer.get();
    ...
}
```

或者，像下面这样进行随机访问

```text
for(int i = 0; i < buffer.limit(); i++) {
    byte b = buffer.get(i);
    ...
}
```

Java对二进制数据使用高位在前的排序机制，但是，如果需要以低位在前的排序方式处理包含二进制数字的文件，那么只需调用

```text
buffer.order(ByteOrder.LITTLE_ENDIAN);
```

要查询缓冲区内当前的字节顺序，可以调用

```text
ByteOrder b = buffer.order()
```

## 缓冲区数据结构（P103-104）

缓冲区是由具有相同类型的数值构成的数组，Buffer类是一个抽象类，它有众多的具体子类，包括ByteBuffer、CharBuffer、DoubleBuffer、IntBuufer、LongBuffer和ShortBuffer。

每个缓冲区都具有：

* 一个容量，它永远不能改变
* 一个读写位置，下一个值将在此进行读写。
* 一个界限，超过它进行读写是没有意义的。
* 一个可选的标记，用于重复一个读入或写出操作

这些值满足下面的条件

0 ≤ 标记 ≤ 位置 ≤ 界限 ≤ 容量

使用缓冲区的主要目的是执行“写，然后读入”循环。假设我们有一个缓冲区，在一开始，它的位置为0，界限等于容量。我们不断地调用out将值添加到这个缓冲区中，当我们耗尽所有的数据或者写出的数据量达到容量大小时，就该切换到读入操作了。

这时调用flip方法将界限设置到当前位置，并把位置复位到0。现在在remaining方法返回正数时，不断调用get。在我们将缓冲区中所有的值都读入之后，调用clear使缓冲区为下一次写循环做好准备。clear方法将位置复位到0并将界限复位到容量。

要获得缓冲区，可以调用诸如ByteBuffer.allocate或ByteBuffer.wrap这些的静态方法。然后，可以用来自某个通道的数据填充缓冲区，或者将缓冲区的内容写出通道中。

```text
ByteBuffer buffer = ByteBuffer.allocate(RECORD_SIZE);
channel.read(buffer);
channel.position(newpos);
buffer.flip();
channel.write(buffer);
```

这是一种非常有用的方法，可以代替随机访问文件。

## 文件枷锁机制（P105-106）

要锁定一个文件，可以调用FileChannel类的lock或tryLock方法

```text
FileChannel = FileChannel.open(path)
File lock = channel.lock();

或

FileLock lock = channel.tryLock();
```

第一个调用会阻塞直至可获得锁，而第二个调用将立即返回，要么返回锁，要么在锁不可获得的情况下返回null。这个文件将保持锁定状态，直至这个通道关闭，或者在锁上调用了release方法。

你还可以通过下面的调用锁定文件的一部分

```text
FileLock lock(long start, long size, boolean shared);

或

fILElock tryLock(long start, long size, boolean shared);
```

如果shared标志位false，则锁定文件的目的是读写，而如果为true，则这是一个共享锁，它允许多个进程从文件中读入，并阻止任何进程获得独占的锁。并非所有的操作系统都支持共享锁，因此你可能会在请求共享锁的时候得到的是独占的锁。调用FileLock类的isShared方法可以查询你所持有的锁类型。

