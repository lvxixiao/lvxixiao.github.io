# 随机访问文件

## 随机访问文件（P59-63）

RandomAccessFile类可以在文件中的任何位置查找或写入数据。磁盘文件都是随机访问的，但是与网络套接字通信的输入/输出流缺不是。你可以打开一个随机访问文件，只用于读入或者同时用于读写，你可以通过使用字符串"r"或"rw"作为构造器的第二个参数来指定这个选项。

```text
RandomAccessFile in = new RandomAccessFile("employee.dat","r");

RandomAccessFile in = new RandomAccessFile("employee.dat","rw");
```

当你将已有文件作为RandomAccessFile打开时，这个文件并不会被删除。

随机访问文件有一个表示下一个将被读入或写出的字节所处位置的文件指针，seek方法可以用来将这个文件指针设置到文件中的任意字节位置，seek的参数是一个long类型的整数，它的值位于0到文件按照字节来度量的长度之间。

getFilePointer方法将返回文件指针的当前位置。

RandomAccesssFile类同时实现了DataInput和DataOutput接口。

要确定文件中的字节总数，可以使用length方法。

* java.io.RandomAccessFile 1.0
  * RandomAccessFile\(String file, String mode\)
  * RandomAccessFile\(File file, String mode\)
    * file：要打开的文件
    * mode："r"表示只读模式；"rw"表示读/写模式；"rws"表示每次更新时，都对数据和元数据的写磁盘操作进行同步的读/写模式；"rwd"表示每次更新时，只对数据的写磁盘操作进行同步的读/写模式
  * long getFilePointer\(\)
    * 返回文件指针的当前位置
  * void seek\(long pos\)
    * 将文件指针设置到距文件开头pos个字节处
  * long length\(\)
    * 返回文件按照字节来度量的长度

