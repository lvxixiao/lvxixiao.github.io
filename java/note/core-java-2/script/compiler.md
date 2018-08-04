# 编译器

#### 调用编译器

```text
JavaCompiler compiler = ToolProvider.getSystemJavaCompiler();
OutputStream outStream =...;
OutputStream errStream = ...;
int result = compiler.run(null,outStream,errStream,"-sourcepath","src","Test.java");
```

返回值为0表示编译成功。

编译器会向提供它的流发送输出和错误消息。如果将这些参数设置为null，就会使用System.out和System.err。run方法的第一个参数是输入流，由于编译器不会接受任何控制台输入，因此总是应该让其保持为null。

如果在命令行调用javac,那么run方法其余的参数就会作为变量传递给javac。这些变量是一些选项或文件名。

#### 使用编译工具

可以使用CompilationTask对象来对编译过程进行更多的控制。比如:

* 控制程序代码的来源，例如，在字符串构建器而不是文件中提供代码。
* 控制类文件的放置位置，例如，存储在数据库中。
* 监听在编译过程中产生的错误和警告信息。
* 在后台运行编译器。

源代码和类文件的位置是由JavaFileManager控制的，它负责确定源代码和类文件的JavaFileObject实例。JavaFileObject可以对应于磁盘文件，或者可以提供读写其内容的其他机制。

为了监听错误消息，需要安装一个DiagnosticListener。这个监听器在编译器报告警告或错误消息时会收到一个Diagnostic对象。DiagnosticCollector类实现了这个接口，它将收集所有的诊断信息，使得你可以在编译完成之后遍历这些信息。

Diagnostic对象包含有关问题位置的信息（包括文件名、行号和列号）以及人类可阅读的描述。

可以通过调用JavaCompiler类的getTask方法来获得CompilationTask对象。这时需要指定：

* 一个用于所有编译器输出的Writer，它不会将输出作为Diagnostic报告。如果是null，则使用System.err。
* 一个JavaFileManager，如果为null，则使用编译器的标准文件管理器。
* 一个DiagnosticListener。
* 选项字符串，如果没有选项，则为null。
* 用于注解处理的类名字，如果没有指定类名字，则为null。
* 用于源文件的JavaFileObject实例。

如果希望编译器从磁盘读取源文件，那么可以让StandardJavaFileManager将文件名字符串或File对象转译成JavaObject实例。

```text
StandardJavaFileManager fileManager = compiler.getStandardFileManager(null,null,null);

Iterable<JavaFileObject> fileObjects = fileManager.getJavaFileObjectsFromStrings(fileNames);
```

如果希望编译器从磁盘文件之外的其他地方读取源代码，那么可以提供自己的JavaFileObject的子类。

