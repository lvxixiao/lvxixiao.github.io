# 读写二进制数据

## DataInput和DataOutput接口（P57-59）

DataOutput接口定义了以二进制格式写数组、字符、boolean值和字符串的方法。在Java中，所有的值都是按照高位在前的模式写出，不管使用何种处理器，这使得Java数据文件可以独立于平台。

为了读回数据，可以使用在DataInput接口定义的方法。DataInputStream类实现了DataInput接口，为了从文件读入二进制数据，可以将DataInputStream与某个字节源组合。

```text
DataInputStream in = new DataInputStream(new FileInputStream("employee.dat"));
```

与此类似，想要写出二进制数据，你可以使用实现了DataOutput接口的DataOutputStream类：

```text
DataOutputStream out = new DataOutputStream(new FileOutputStream("employee.dat"));
```

* java.io.DataInput 1.0
  * boolean readBoolean\(\)
  * byte readByte\(\)
  * char eadChar\(\)
  * double readDouble\(\)
  * float readFloat\(\)
  * int readInt\(\)
  * long readLong\(\)
  * short readShort\(\)
    * 读入一个给定类型的值
  * void readFully\(byte\[\] b\)
    * 将字节读入到数组b中，其间阻塞直至所有字节都读入。
  * void readFully\(byte\[\] b, int off, int len\)
    * 将字节读入到数组b中，其间阻塞直至所有字节都读入
      * b：数据读入的缓冲区
      * off：数据起始位置的偏移量
      * len：读入字节的最大数量
  * String readUTF\(\)
    * 读入由“修订过的UTF-8”格式的字符构成的字符串
  * int skipBytes\(int n\)
    * 跳过n个字节，其间阻塞直至所有字节都被跳过
* java.io.DataOutput 1.0
  * void writeBoolean\(boolean b\)
  * void writeByte\(int b\)
  * void writeChar\(int c\)
  * void writeDouble\(double d\)
  * void writeFloat\(float f\)
  * void writeInt\(int i\)
  * void writeLong\(long l\)
  * void writeShort\(int s\)
    * 写出一个给定类型的值。
  * void writeChars\(String s\)
    * 写出字符串中的所有字符
  * void writeUTF\(String s\)
    * 写出由“修订过的UTF-8”格式的字符构成的字符串

