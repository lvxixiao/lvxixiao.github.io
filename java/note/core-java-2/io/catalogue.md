# 目录

## 访问目录中的项（P91-92）

静态的Files.list方法会返回一个可以读取目录中各个项的Stream对象。目录是被惰性读取的，这使得处理具有大量项的目录可以变得更高效。 因为读取目录涉及需要关闭的系统资源，所以应该使用try块

```text
 try(Stream<Path> entries = Files.list(pathToDirectory)) {
     ...
 }
```

list方法不会进入子目录。为了处理目录中的所有子目录，需要使用File.walk方法。

```text
 try(Stream<Path> entries = Files.walk(pathToRoot)) {
     // Contains all descendants, visited in depth-first order
 }
```

只要遍历的项是目录，那么在进入它之前，会继续访问它的兄弟项。

可以通过调用File.walk\(pathToPoo, depth\)来限制想要访问的树的深度。两种walk方法都具有FileVisitOption...的可变长参数，但是你只能提供一种选项：FOLLOW\_LINKS，即跟踪符号链接。

无法容易地使用Files.walk方法来删除目录树，因为你需要在删除目录之前必须先删除子目录。

## 使用目录流（P92-95）

File.newDirectoryStream对象会产生一个DirectoryStream。它不是java.util.stream.Stream的子接口，而是专门用于目录遍历的接口。是Iterable的子接口，因此你可以在增强的for循环中使用目录流。

```text
 try(DirectoryStream<Path> entries = Files.newDirectoryStream(dir)) {
     for(Path entry : entries)
        Process entries
 }
```

try语句块用来确保目录流可以被正常关闭。访问目录中的项并没有具体的顺序。

如果想要访问某个目录的所有子孙成员，可以转而调用walkFileTree方法，并向其传递一个FileVisitor类型的对象，这个对象会得到下列通知。

* 在遇到一个文件或目录时，调用visitFile方法
* 在一个目录被处理前，调用preVisitDirecory方法
* 在试图访问文件或目录时发生错误，调用visitFileFailed方法

便捷类SimpleFileVisitor实现了FileVisitor接口，但是其除visitFileFailed方法之外的所有方法并不做任何处理而是直接继续访问，而visitFileFailed方法会抛出失败导致的异常，进而终止访问。

