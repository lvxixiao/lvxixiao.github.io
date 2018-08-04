# 组合流过滤器

## 组合输入\输出流过滤器（P45-47）

FileInputStream和FileOutputStream可以提供附着在一个磁盘文件上的输入流和输出流，而你只需向其构造器提供文件名或文件的完整路径名。

```text
FileInputStream fin = new FileInputStream("employee.dat");
```

与抽象类InputStream和OutputStream一样，这些类只支持在字节级别上的读写。也就是说，我们只能从fin对象中读入字节和字节数组。

```text
byte b = (byte) fin.read();
```

如果我们只有DataInputStream，那么我们就只能读入数值类型：

```text
DataInputStream din = ...;
double x = din.readDouble();
```

但是正如FileInputStream没有任何读入数值类型的方法一样，DataInputStream也没有任何从文件中获取数据的方法。

Java使用了一种灵巧的机制来分离这两种职责。某些输入流（例如FileInputStream和由URL类的openStream方法返回的输入流）可以从文件和其他更外部的位置上获取字节，而其他的输入流（例如DataInputStream）可以将字节组装到更有用的数据类型中。Java程序员必须对二者进行组合。例如，为了从文件中读入数字，首先需要创建一个FileInputStrem，然后将其传递给DataInputStream的构造器：

```text
FileInputStream fin = new FileInputStream("employee.dat");
DataInputStream din = new DataInputStream(fin);
double x = din.readDouble();
```

你可以通过嵌套过滤器来添加多重功能。例如，输入流在默认情况下是不被缓冲区缓存的，也就是说，每个对read的调用都会请求操作系统再分发一个字节。相比之下，请求一个数据块并将其置于缓冲区会显得更加高效。如果我们想使用缓冲机制，以及用于文件的数据输入方法，那么就需要使用下面这种构造器序列：

```text
DataInputStream din = new DataInputStream(
    new BufferedInputStream(
        new FileInputStream("employee.dat")));
```

我们把DataInputStream置于构造器链的最后，这是因为我们希望使用DataInputStream的方法，并且希望它们能够使用带缓冲机制的read方法。

有时当多个输入流链接在一起时，你需要跟踪各个中介输入流（intermediate inputstream）。例如，当读入输入时，你经常需要预览下一个字节，以了解它是否是你想要的值。Java提供了用于此目的的PushbackInputStream：

```text
PushbackInputStream pbin = new PushbackInputStream(
    new BufferedInputStream(
        new FileInputStream("employee.dat")));
```

现在你可以预读下一个字节，并且在它并非你所期望的值时将其推回流中。

```text
int b = pbin.read();
if( b != '<')
    pbin.unread(b);
```

但是读入和推回是可应用于回推（pushback）输入流的仅有方法。如果你希望能够预先浏览并且还可以读入数字，那么你需要一个即是可回推输入流，又是一个数据输入流的引用。

```text
DataInputStream din = new DataInputStream(
    pbin = new PushbackInputStream(
        new BufferedInputStream(
            new FileInputStream("employee.dat"))));
```

当然，在其他编程语言的输入/输出流库中，诸如缓冲机制和预览等细节都是自动处理的。因此，相比较而言，Java就有一点麻烦，它必须将多个流过滤器组合起来。但是，这种混合并匹配过滤器类以构建真正有用的输入/输出流序列的能力，将带来极大的灵活性，例如，你可以从一个ZIP压缩文件中通过使用下面的输入流序列来读入数字。

```text
ZipInputStream zin = new ZipInputStream(new FileInputStream("employee.zip"));
DataInputStream din = new DataInputStream(zin);
```

## API（P48）

* java.io.FileInputStream 1.0
  * FileInputStream\(String name\)
  * FileInputStream\(File file\)
    * 使用由name字符串或file对象指定路径名的文件创建一个新的文件输入流。非绝对的路径名将按照相对于VM启动时所设置的工作目录来解析。
* java.io.FileOutputStream 1.0
  * FileOutputStream\(String name\)
  * FileOutputStream\(String name, boolean append\)
  * FileOutputStream\(File file\)
  * FileOutputStream\(File file, boolean append\)
    * 使用由name字符串或file对象指定路径名的文件创建一个新的文件输出流。如果append参数为true，那么数据将被添加到文件尾，而具有相同名字的已有文件不会被删除；否则，则这个方法会删除所有具有相同名字的已有文件。
* java.io.BufferedInpuStream 1.0
  * BufferedInputStream\(InputStream in\)
    * 创建一个带缓冲区的输入流。带缓冲区的输入流在从流中读入字符时，不会每次都对设备访问。当缓冲区为空时，会向缓冲区读入一个新的数据块。
* java.io.BufferedOutputStream 1.0
  * BufferedOutputStream\(OutputStream out\)
    * 创建一个带缓冲区的输出流。带缓冲区的输出流在收集要写出的字符时，不会每次都对设备访问。当缓冲区填满或当流被冲刷时，数据就被写出。
* java.io.PushbackInpuStream 1.0
  * PushbackInputStream\(InputStream in\)
  * PushbackInputStream\(InputStream in, int size\)
    * 构建一个可以预览一个字节或者具有指定尺寸的回推缓冲区的输入流。
  * void unread\(int b\)
    * 回推一个字节，它可以在下次调用read时被再次获取。
      * b：要再次读入的字节

