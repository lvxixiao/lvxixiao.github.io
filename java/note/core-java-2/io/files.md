# Files

## 读写文件（P85-86）

Files类可以使得普通文件操作变得快捷，可以用下面的方式读取文件的所有内容

```text
byte[] bytes = Files.readAllBytes(path);
```

如果想要将文件当作字符串读入，那么可以在调用readAllBytes之后执行下面的代码：

```text
String content = new String(bytes, charset);
```

如果希望将文件当作行序列读入，那么可以调用

```text
List<String> lines = Files.readAllLines(path,charset);
```

相反地，如果希望写出一个字符串到文件中，可以调用

```text
File.write(path,content.getBytes(charset), StandardOpenOption.APPEND);
```

还可以用下面的语句将一个行的集合写出到文件中

```text
Files.write(path.lines);
```

这些简便方法适用于处理中等长度的文本文件，如果要处理的文件长度比较大，或者是二进制文件，那么还是应该使用熟知的输入/输出流或者读入器/写出器

```text
InputStream in = Files.newInputStream(path);
OutputStrea out = Files.newOutputStream(path);
Reader in = Files.newBufferedReader(path, charset);
Writer out = Files.newBufferedWriter(path, charset);
```

## 读写文件相关API（P86）

* java.nio.file.Files 7
  * static byte\[\] readAllBytes\(Path path\)
  * static List readAllLines\(Path path, Charset charset\)
    * 读入文件的内容
  * static Path write\(Path path, byte\[\] contents, OpenOption... options\)
  * static Path write\(Path path, Iterable&lt;? extends CharSequence&gt; contents, OpenOption options\)
    * 将给定内容写出到文件中，并返回path
  * static InputStream newInputStream\(Path path, OpenOption... options\)
  * static OutputStream newOutputStream\(Path path, OpenOption... options\)
  * static BufferedReader newBufferedReader\(Path path, Charset charset\)
  * static BufferedWriter newBufferedWriter\(Path path , Charset charset, OpenOption... options\)
    * 打开一个文件，用于读入或写出

## 创建文件和目录（P87）

创建新目录可以调用

```text
Files.creaeDirectory(path);
```

其中，路径中除了最后一个部件外，其他部分都必须是已存在的。要创建路径中的中间目录，应该使用

```text
Files.createDirectories(path);
```

可以使用下面的语句创建一个空文件

```text
Files.createFile(path);
```

如果文件已经存在了，那么这个调用就会抛出异常。检查文件是否存在和创建文件是原子性的，如果文件不存在，该文件就会被创建，并且其他程序在此过程中是无法执行文件创建操作的。

有些便捷方法可以用来在给定位置或者系统指定位置创建临时文件或临时目录

```text
Path newPath = Files.createTempFile(dir,prefix,suffix);
Path newPath = Files.createTempFile(prefix, suffix);
Path newPath = Files.createTempDirectory(dir,prefix);
Path newPath = Files.createTempDirectory(prefix);
```

其中，dir是一个Path对象，prefix和suffix是可以为null的字符串。

在创建文件或目录时，可以指定属性，例如文件的拥有者和权限。但是，指定属性的细节取决于文件系统。

## 复制、移动和删除文件（P88-89）

将文件从一个位置复制到另一个位置可以直接调用

```text
Files.copy(fromPath, toPath);
```

移动文件（即复制并删除原文件）可以调用

```text
Files.move(fromPath, toPath);
```

如果目标路径已经存在，那么复制或移动将失败。如果想要覆盖已有的目标路径，可以使用REPLACE\_EXISTING选型。如果想要复制所有的文件属性，可以使用COPY\_ATTRIBUTES选型，具体参考\(P88表2-3\)。

你还可以将一个输入流复制到Path中，这表示你想要将该输入流存储到硬盘上。类似地，你可以将一个Path复制到输出流中。

```text
Files.copy(inputStream, toPath);
Files.copy(fromPath, outputStream);
```

删除文件可以调用

```text
Files.delete(path);
```

如果要删除的文件不存在，这个方法就会抛出异常。因为，可以用下面的方法

```text
boolean deleted = Files.deleteIfExists(path);
```

该删除方法还可以用来移除空目录。

## 复制、移动和删除相关API（P89）

* java.nio.file.Files 7
  * static Path copy\(Path from, Path to, CopyOption... options\)
  * static Path move\(Path from, Path to, CopyOption... options\)
    * 将from复制或移动到给定位置，并返回to。
  * static long copy\(InputStream from, Path to, CopyOption... options\)
  * static long copy\(Path from, OutputStream to, CopyOption... options\)
    * 从输入流复制到文件中，或者从文件复制到输出流中，返回复制的字节数。
  * static void delete\(Path path\)
  * static boolean deleteIfExists\(Path path\)
    * 删除给定文件或空目录。第一个方法在文件或目录不存在情况下抛出异常，而第二个方法在这种情况下会返回false。

## 获取文件信息（P89-91）

* java.nio.file.Files 7
  * static boolean exists\(Path path\)
  * static boolean isHidden\(Path path\)
  * static boolean isReadable\(Path path\)
  * static boolean isWritable\(Path path\)
  * static boolean isExecutable\(Path path\)
  * static boolean isRegularFile\(Path path\)
  * static boolean isDirectory\(Path path\)
  * static boolean isSymbolicLink\(Path path\)
    * 检查由路径指定的文件的给定属性
  * static long size\(Path path\)
    * 获取文件按字节数量度量的尺寸
  * A readAttributes\(Path path, Class type, LinkOption... options\)
    * 读取类型为A的文件属性
* java.nio.file.attribute.BasicFileAttributes 7
  * FileTime creationTime\(\)
  * FileTime lastAccessTime\(\)
  * FileTime lastModifiedTime\(\)
  * boolean isRegularFile\(\)
  * boolean isDirectory\(\)
  * boolean isSymbolicLink\(\)
  * long size\(\)
  * Object fileKey\(\)
    * 获取所请求的属性

