# Path

## 操作文件（P83）

Path和Files类封装了在用户机器上处理文件系统所需的所有功能。例如，Files类可以用来移除或重命名文件，或者查询文件最后被修改的时间。换句话说，输入/输出流类关心的是文件的内容，而我们在此处要讨论的类关心的是在磁盘上如何存储文件。

## Path （P83-84）

Path表示的是一个目录名序列，其后还可以跟着一个文件名。路径中的第一个部件可以是根部件，例如/或C:\，而允许访问的根部件取决于文件系统。以根部件开始的路径是绝对路径；否则，就是相对路径。

```text
//假设文件系统为类Unix的文件系统
Path absolute = Paths.get("/home","harry");
Path relative = Paths.get("myprog","conf","user.properties");
```

静态的Paths.get方法接受一个或多个字符串，并将它们用默认文件系统的路径分隔符（类Unix文件系统是/，Windows是\）连接起来。然后它解析连接起来的结果，如果其表示的不是给定文件系统中的合法路径，那么就抛出InvalidPathException异常。这个连接起来的结果就是一个Path对象。

get方法可以获取包含多个部件构成的单个字符串，例如，可以像下面这样从配置文件中读取路径：

```text
String baseDir = props.getProperty("base.dir");
// May be a string such as /opt/myprog or c:\Program Files\myprog
Path basePath = Paths.get(baseDir);
```

**注意：路径不必对应着某个实际存在的文件，它仅仅只是一个抽象的名字序列。当你想要创建文件时，首先要创建一个路径，然后才调用方法去创建对应的文件**

组合或解析路径是经常有的操作，调用p.resolve\(q\)将按照下列规则返回一个路径

* 如果q是绝对路径，则结果就是q。
* 否则，根据文件系统的规则，将“p后面跟着q”作为结果

例如，假设你的应用系统需要查找相对于给定基目录的工作目录，其中基目录是从配置文件中读取的

```text
Path workRelative = Paths.get("work");
Path workPath = basePath.resolve(workRelative);
// or
Path workPath = basePath.resolve("work");
```

方法resolveSibling，它通过解析指定路径的父路径产生其兄弟路径。例如，如果workPath是/opt/myapp/work，那么下面调用

```text
Path tempPath = workPath.resolveSibling("temp");
```

将创建/opt/myapp/temp。

调用p.relativize\(r\)将产生路径q，而进行解析的结果正是r。例如，以“/home/cay”为目标对“/home/fred/myprog”进行相对化操作，会产生“../fred/myprog”，其中，我们假设..表示文件系统中的父目录。

normalize方法将移除所有冗余的.和..部件（或者文件系统认为冗余的所有部件）。如，规范化/home/cay/../fred/./myprog将产生/home/fred/myprog。

toAbsolutePath方法将产生给定路径的绝对路径，该绝对路径从根部件开始，例如/home/fred/input.txt或c:\User\fred\input.txt。

Path类还有许多有用的方法用来将路径断开

```text
Path p = Paths.get("home","fred","myprog.peoperties");
Path parent = p.getParent();//the path /home/fred
Path file = p.getFileName();//the path myprog.properties
Path roor = p.getRoot();//the path /
```

还可以从Path对象中构建Scanner对象

```text
Scanner in = new Scanner(Paths.get("/home/fred/input.txt"));
```

## API（P85）

* java.nio.file.Paths 7
  * static Path get\(String first, String... more\)
    * 通过连接给定的字符串创建一个路径
* java.nio.file.Path 7
  * Path resolve\(Path other\)
  * Path resolve\(String other\)
    * 如果other是绝对路径，那么就返回other；否则，通过连接this和other获得的路径。
  * Path resolveSibling\(Path other\)
  * Path resolveSibling\(String other\)
    * 如果Other是绝对路径，那么就返回other；否则，返回通过连接this和other获得的路径。
  * Path relativize\(Path other\)
    * 返回用this进行解析，相对于other的相对路径。
  * Path normalize\(\)
    * 移除诸如.和..等冗余的路径元素。
  * Path toAbsolutePath\(\)
    * 返回与该路径等价的绝对路径。
  * Path getParent\(\)
    * 返回父路径，或者在该路径没有父路径时，返回null。
  * Path getFileName\(\)
    * 返回该路径的根部件，或者在该路径没有任何根部时，返回null。
  * toFile\(\)
    * 从该路径中创建一个File对象。
* java.io.File 1.0
  * Path toPath\(\) 7
    * 从该文件中创建一个Path对象。

