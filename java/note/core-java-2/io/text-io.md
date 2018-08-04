# 文本输入与输出

## 文本保存（P48-49）

在保存数据时，可以选择二进制格式或文本格式。例如，整数1234存储成二进制数时，它被写为由字节00 00 04 D2构成的序列，而存储成文本格式时，它被存成了字符串"1234"。

在存储文本字符串时，需要考虑字符编码（character encoding）方式。在Java内部使用UTF-16编码方式中，字符串"1234"编码为00 31 00 32 00 33 00 34。在UTF-8这种在互联网上最常用的编码方式中，这个字符串将写出4A 6F 73 C3 A9。

OutputStremWriter类将使用选定的字符编码方式，把Unicode码元的输出流转换为字节流。而InputStreamReader类将包含字节（用某种字符编码方式表示的字节）的输入流转换为可以产生Unicode码元的读入器。

## 文本输出（P49-51）

对于文本输出，可以使用PrintWriter。这个类拥有以文本格式打印字符串和数字的方法，它还有一个将PrintWriter链接到FileWriter的便捷方法：

```text
PrintWriter out = new PrintWriter("employee.dat","UTF-8");
```

为了输出到打印写出器，需要使用与使用System.out时相同的print、println、和printf方法。你可以用这些方法来打印数字、字符和boolean值、字符串和对象。

println方法在行中添加了对目标系统来说恰当的行结束符（windows系统是"\r\n"，UNIX系统是"\n"），也就是通过调用System.getProperty\("line.separator"\)而获得的字符串。

如果写出器设置为自动冲刷模式，那么只要println被调用，缓冲区中的所有字符都会被发送到它们的目的地\(打印写出器总是带缓冲区的\)。默认情况下，自动冲刷机制是禁用的，你可以通过使用PrintWriter\(Writer out, Boolean autoFlush\)来启用或禁用自动冲刷机制。

```text
PrintWriter out = new PrintWriter(
    new OutputStreamWriter(
        new FileOutputStream("employee,txt"),"UTF-8"),
    true);//aotuflush
```

print方法不抛出异常，你可以调用checkError方法来查看输出流是否出现了某些错误。

* java.io.PrintWriter 1.1
  * PrintWriter\(Writer out\)
  * PrintWriter\(Writer writer\)
    * 创建一个向给定的写出器写出的新的PrintWriter。
  * PrintWriter\(String filename,String encoding\)
  * PrintWriter\(File file, String encoding\)
    * 创建一个使用给定的编码方式向给定的文件写出的新的PrintWriter。
  * void print\(Object obj\)
    * 通过打印从toString产生的字符串来打印一个对象。
  * void print\(String s\)
    * 打印一个包含Unicode码元的字符串
  * void println\(String s\)
    * 打印一个字符串，后面紧跟着一个行终止符。如果这个流处于自动冲刷模式，那么就会冲刷这个流。
  * void print\(char\[\] s\)
    * 打印在给定的字符串中的所有Unicode码元。
  * void print\(char c\)
    * 打印一个Unicode码元。
  * void print\(int i\)
  * void print\(long l\)
  * void print\(float f\)
  * void print\(double d\)
  * void printf\(boolean b\)
    * 以文本格式打印给定的值。 
  * void printf\(String format, Object... args\)
    * 按照格式字符串指定的方式打印给定的值。
  * boolean checkError\(\)
    * 如果产生格式化或输出错误，则返回true。一旦这个流碰到了错误，它就受到了污染，并且所有对checkError的调用都将返回true。

## 文本输入（P51-52）

我们可以从任何输出流中构建Scanner对象，或者可以将短小的文本文件像下面这样读入到一个字符串中：

```text
String content = new String(Files.readAllBytes(path), charset);
```

如果想要将这个文件一行行地读入，那么可以调用：

```text
List<String> lines = Files.readAllLines(path,charset);
```

如果这个文件太大，那么可以将行惰性处理为一个Stream对象：

```text
try(Stream<String> lines = Files.lines(path,charset)) {
    ...
}
```

在早期的Java版本中，处理文本输入的唯一方式就是通过BufferedReader类。它的readLine方法会产生一行文本，或者在无法获得更多的输入时返回null。典型的输入循环看起来像下面这样：

```text
InputStream inputStream = ...;
try(BufferedReader in = new BufferedReader(new InputStreamReader(inputStream, SandardCharsets.UTF_8))) {
    String line;
    while((line = in.readLine()) != null) {
        do something with line
    }
}
```

如今，BufferedReader类又有了一个lines方法，可以产生一个Stream对象。但是，与Scanner不同，BufferedReader没有用于任何读入数字的方法。

