# 字符编码方式

## 字符编码方式（P55-56）

Java针对字符使用的是Unicode标准。每个字符或“编码点”都具有一个21位的整数。有多种不同的字符编码方式，也就是说，将这些21位数字包装成字节的方法有很多种。

最常见的编码方式是UTF-8，它会将每个Unicode编码点编码为1到4个字节的序列。UTF-8的好处是传统的包含了英语中用到的所有字符的ASCII字符集中的每个字符都只会占用一个字节。

另一种常见的编码方式是UTF-16，它会将每个Unicode编码点编码为1个或2个16位值。这是一种在Java字符串中使用的编码方式。实际上，有两种形式的UTF-16，被称为“高位优先”和“低位优先”。为了表示使用的是哪一种格式，文件可以以“字节顺序标记”开头，这个标记为16位数值0xFEFF。读入器可以使用这个值来确定字节顺序，然后丢弃它。

## StandardCharsets（P56-57）

StandardCharsets类具有类型为Charset的静态变量，用于表示每种Java虚拟机都必须支持的字符编码方式：

* StandardCharsets.UTF\_8
* StandardCharsets.UTF\_16
* StandardCharsets.UTF\_16BE
* StandardCharsets.UTF\_16LB
* StandardCharsets.ISO\_8859\_1
* StandardCharsets.US\_ASCII

为了获得另一种编码方式的Charset，可以使用静态的forName方法：

```text
Charset shiftJIS = Charset.forName("Shift-JIS");
```

在读入或写出文本时，应该使用Charset对象。例如，我们可以像下面这样将一个字节数组转换为字符串：

```text
String str = new String(bytes, StandardCharsets.UTF_8)
```

在不制定任何编码方式时，有些方法（例如String\(byte\[\]\)构造器）会使用默认的平台编码方式，而其他方法（例如Files.readAllLines）会使用UTF-8。

