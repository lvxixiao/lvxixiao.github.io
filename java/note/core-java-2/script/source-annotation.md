# 源码级注解处理

注解的另一种用法是自动处理源代码以产生更多的源代码、配置文件、脚本或其他任何我们想要生成的东西。

#### 注解处理\(P389-390\)

注解处理已经被集成到了Java编译器中。在编译过程中，你可以通过运行下面的命令来调用注解处理器。

```text
javac -processor ProcessorClassName1,ProcessorClassName2,...sourceFiles
```

编译器会定位源文件中的注解。每个注解处理器会依次执行，并得到它表示感兴趣的注解。如果某个注解处理器创建了一个新的源文件，那么将重复执行这个处理过程。如果某次处理循环没有再产生任何新的源文件，那么就编译所有的源文件。

注解处理器只能产生新的源文件，它无法修改已有的源文件。

**注解处理器**

注解处理器通常通过扩展AbstractProcessor类而实现Processor接口。

```text
@SupportedAnnotationType("com.horstmann.annotations.ToString")
@SupportedSourceVersion(SourceVersion.RELEASE_8)
public class ToStringAnnotationProcessor extends AbstractProcessor{
    public boolean process(Set<? extends TypeElement> annotations,RoundEnvironment currentRound){
        ...
    }
}
```

以上例子中，处理器可以声明具体的注解类型或注诸如“com.horstmann\*”这样的通配符\(com.horstmann包及其所有子包中的注解\)，甚至是“\*”\(所有注解\)。

在每一轮中，process方法都会被调用一次，调用时会传递给由这一轮在所有文件中发现的所有注解构成的集，以及包含了有关当前处理轮次的信息的RoundEnvironment引用。

#### 语言模型API\(P390\)

与呈现类和方法的虚拟机表示形式的反射API不同，语言模型API让我们可以根据Java语言的规则去分析Java程序。

编译器会产生一棵树，其节点是实现了javax.lang.model.element.Element接口及其TypeElement、VariableElement、ExecutableElement等子接口的类的实例。这些节点可以类比于编译时的Class、Field/Parament和Method/Constructor反射类。

**语言模型处理注解流程**

RoundEnvironment通过调用下面的方法交给你一个由特定注解标注过的所有元素构成的集。

```text
Set<? extends Element> getElementsAnnotatedWith(Class<? extends Annotation> a)
```

在源码级别上等价于AnnotatedElement接口的是AnnotatedConstruct。使用下面的方法就可以获得属于给定注解类的单挑注解或重复的注解。

```text
A getAnnotation<Class<A> annotationType)
A[] getAnnotationByType(Class<A> annotationType)
```

TypeElement表示一个类或接口，而getEnclosedElements方法会产生一个由它的域和方法构成的列表。

在Element上调用getSimpleName或在TypeElement上调用geQualifiedName会产生一个Name对象，它可以用toString方法转换为一个字符串。

