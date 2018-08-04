# 深入分析JavaIO的工作机制

## Java的I/O类库的基本架构

Java的I/O操作类在包java.io下，大概有将近80个类，这些类大概可以分成如下4组

* 基于字节操作的I/O接口：InputStream和OutputStream。
* 基于字符操作的I/O接口：Writer和Reader。
* 基于磁盘操作的I/O接口：File。
* 基于网络操作的I/O接口：Socket。

前两组主要是传输数据的数据格式，后两组主要是传输数据的方式。I/O的核心问题要么是数据格式影响I/O操作，要么是传输方式影响I/O操作。数据格式和传输方式是影响效率最关键的因素。

## 基于字节的I/O操作接口

* InputStream
  * ByteArrayInputStream
  * FileInputStream
    * SoketInputStream
  * FilterInputStream
    * InflaterInputStream
      * ZipInputStream
    * BufferedInputStream
    * DataInputStream
  * ObjectInputStream
  * PipedInputStream
* OutputStream
  * ByteArrayOutputStream
  * FileOutputStream
    * SocketOutputStream
  * FilterOutputStream
    * DataOutputStream
    * PrintStream
    * ZipOutputStream
  * ObjectOutputStream
  * PipedOutputStream

额外说明，操作数据的方式是可以组合使用的，必须要指定流最终写到什么地方。

```text
OutputStream out = new BufferedOutputStream(new ObjectOutputStream(new FileOutputStream("fileName")));
```

## 基于字符的I/O操作接口

* Writer
  * OutputStreamWriter
    * FileWriter
  * BufferedWriter
  * StringWriter
  * PipedWriter
  * PrintWriter
  * CharArrayWriter
* Reader
  * InputStreamReader
    * FileReader
  * BufferedReader
  * CharArrayReader
  * FilterReader
  * PipedReader
  * StringReader

## 字节与字符的转化接口

数据持久化或网络传输都是以字节进行的，所以必须要有从字符到字节或从字节到字符的转化。

InputStreamReader类是从字节到字符的转化桥梁，从InputStream到Reader的过程要指定编码字符集，否则将采用操作系统默认的字符集，很可能会出现乱码问题。StreamDecoder正是完成从字节到字符的解码的实现类。

FileReader继承了InputStreamReader类，实际上是读取文件流，然后通过StreamDecoder解码成char，只不过这里的解码字符集是默认字符集。

写入的过程也是类似，通过OutputStreamWriter类完成了从字节到字符的编码过程，由StreamEncoder完成编码过程。

## 几种访问文件的方式

读取和写入文件I/O操作都调用操作系统提供的接口，因为磁盘设备是由操作系统管理的，应用程序要访问物理设备只能通过系统调用的方式来工作。读和写分别对应rread\(\)和write\(\)两个系统调用。而只要是系统调用就可能存在内核空间地址和用户空间地址切换的问题，这是操作系统为了保护系统本身的运行安全，而将内核程序运行使用的内存空间和用户程序运行的内存空间进行隔离造成的。这种行为必然存在数据可能需要从内核空间向用户空间复制的问题。

磁盘I/O操作，数据从磁盘复制到内核空间，然后又从内核空间复制到用户空间，将会非常缓慢。这时操作系统为了加速I/O访问，在内核空间使用缓存机制。如果用户程序访问的是同一段磁盘地址的空间数据，那么操作系统将从内核缓存中直接取出返回给用户程序，这样可以减小I/O的响应时间。

**标准访问文件的方式**

标准访问文件的方式就是当应用程序调用read\(\)接口时，操作系统检查在内核的高速缓存中有没有需要的数据，如果已经缓存了，那么就直接从缓存中返回，如果没有，则从磁盘中读取，然后缓存在操作系统的缓存中。

写入的方式是，用户的应用程序调用write\(\)接口将数据从用户地址空间复制到内核地址空间的缓存中。什么时候再写到磁盘中由操作系统决定，除非显式地调用了sync同步命令。

**直接I/O的方式**

所谓的直接I/O的方式就是应用程序直接访问磁盘数据，而不经过操作系统内核数据缓冲区。

但是直接I/O也有负面影响，如果访问的数据不在应用程序缓存中，那么每次数据都会直接从磁盘进行加载，这种直接加载会非常缓慢。通常直接I/O与异步I/O结合使用会得到比较好的性能。

**同步访问文件的方式**

数据的读取和写入都是同步操作的，它与标准访问文件的方式不同的是，只有当数据被成功写到磁盘时才返回给应用程序成功的标志。这种访问文件的方式性能比较差，只有在一些对数据安全性要求比较高的场景中才会使用，而且通常这种操作方式的硬件都是定制的。

**异步访问文件的方式**

异步访问文件的方式就是当访问数据的线程发出请求之后，线程会接着去处理其他事情，而不是阻塞等待，当请求的数据返回后继续处理下面的操作。这种访问文件的方式可以明显地提高应用程序的效率，但是不会改变访问文件的效率。

**内存映射的方式**

内存映射的方式是指操作系统将内存中的某一块区域与磁盘中的文件关联起来，当要访问内存中的一段数据时，转换为访问文件的某一段数据。这种方式的目的同样是减少数据从内核空间缓存到用户空间缓存的数据复制操作，因为这两个空间的数据是共享的。

## Java访问磁盘文件

在Java中通常的File并不代表一个真实存在的文件对象，当你指定一个路径描述符时，它就会返回一个代表这个路径的虚拟对象，这个可能是一个真实存在的文件或者是一个包含多个文件的目录。

为何要这样设计呢？因为在大多数情况下，我们并不关心这个文件是否真的存在，而是关心对这个文件到底如何操作。

何时会真正检查一个文件存不存在？就是在真正要读取这个文件时。

**下面以读取文件的程序为例介绍如何从磁盘读取一段文本字符**

```text
try{
    StringBuffer str = new StringBuffer();
    char[] buf = new char[1024];
    FileReader f = new FileReader("file");
    while(f.read(buf) > 0){
        str.append(buf);
    }
} catch(IOException e){

}
```

当传入一个文件路径时，将会根据这个路径创建一个File对象来标识这个文件，然后根据这个File对象创建真正读取文件的操作对象，这时将会真正创建一个关联真实存在的磁盘文件的文件描述符FileDescriptor，通过这个对象可以直接控制这个磁盘文件。由于我们需要读取的是字符格式，所以需要StreamDecoder类将byte解码为char格式。至于如何从磁盘驱动器上读取一段数据，操作系统会帮我们完成。至于操作系统如何将数据持久化到磁盘及如何建立数据结构的，需要根据当前操作系统使用何种文件系统来回答。

## Java序列化技术

Java序列化就是将一个对象转化成一串二进制表示的字节数组，通过保存或转移这些字节数据来达到持久化的目的。需要持久化，对象必须继承java.io.Serializable接口。反序列化则是相反的过程，将这个字节数组再重新构造成对象。

反序列化时，必须有原始类作为模板，才能将这个对象还原，从这个过程可以猜测，序列化的数据并不像class文件那样保存类的完整的结构信息。

复杂对象的序列化情况

* 当父类继承Serializable接口时，所有子类都可以被序列化
* 子类实现了Serializable接口，父类没有，父类中的属性不能序列化，但是在子类中属性仍能正确序列化。
* 如果序列化的对属性是对象，则这个对象也必须实现Seriablizable接口，否则会报错。
* 在反序列化时，如果对象的属性有修改或删减，则修改的部分属性会丢失，但不会报错。
* 在反序列化时，如果serialVersionUID被修改，则反序列化时会失败。

在纯Java环境下，Java序列化能够很好地工作，但是在多语言环境下，用Java序列化存储后，很难用其他语言还原出结构。在这种情况下，还是要尽量存储通用的数据结构，如JSON或者XML结构数据。

