# 注解

#### 注解

注解是那些插入到源代码中使用其他工具可以对其进行处理的标签。

注解不会改变程序的编译方式。Java编译器对于包含注解和不包含注解的代码会生成相同的虚拟机指令。

#### 注解简介

在Java中，注解是当作一个修饰符来使用的，它被置于被注解项之前，中间没有分号。每一个注解的名称前面都加上了@符号，注解是代码的一部分。

注解可以出现的地方

* 包
* 类\(包括enum\)
* 接口\(包括注解接口\)
* 方法
* 构造器
* 实例域\(包含enum常量\)
* 局部变量
* 参数变量
* 类型参数

对局部变量的注解只能在源码级别上进行处理。类文件并不描述局部变量。因此，所有的局部变量注解在编译完一个类的时候就会被遗弃掉。同样地，对包的注解不能在源码级别之外存在。

每个注解都必须通过一个注解接口进行定义。这些接口中的方法与注解中的元素相对应。

```text
@Target(ElementType.METHOD)
@Retention(RetentionPolicy.RUNTIME)
public @interface Test(){
    long timeout() default 0L;
}
```

@interface声明创建了一个真正的Java接口。处理注解的工具将接收那些实现了这个注解接口的对象。这类工具可以调用timeout方法来检索某个特定Test注解的timeout元素。

注解Target和Retention是元注解。它们是注解了Test注解，即将Test注解标识成一个只能运用到方法上的注解，并且当类文件载入到虚拟机的时候，仍可以保留下来。

#### 注解语法

注解是由注解接口定义的

```text
modifiers @interface AnnotationName {
    elementDeclaration1
    elementDeclaration2
}
```

每个元素声明都具有下面这种形式

```text
type elementName();
```

或者

```text
type elementName() default value;
```

例子如下:

```text
public @interfcace BugReport{
    String assignedTo() default "[none]";
    int severity();
}
```

所有的注解接口都隐式地扩展自java.lang.annotation.Annotation接口。这个接口是一个常规接口，不是一个注解接口。

#### 注解元素的类型

* 基本类型
* String 
* Class\(具有一个可选的类型参数，例如Class&lt;? extends MyClass&gt;\)
* enum类型
* 注解类型
* 由前面所述类型组成的数组\(由数组组成的数组不是合法的元素类型\)

例子

```text
public @interface BugReport{
    enum Statis{ UNCONFIRMED,CONFIRMED,FIXED,NOTABUG};
    boolean showStopper() default false;
    String assignedTo() default "[none]";
    Class<?> testCase() default Void.class;
    Status status() default Status.UNCONFIRMED;
    Reference ref() default @Reference(); //an annotation type
    String[] reportedBy();
}
```

默认值并不是和注解存储在一起的;相反地，它们是动态计算而来的。例如，如果你将元素assignedTo的默认值更改为"\[\]"，然后重新编译BugReport接口，那么注解@BugRePORT\(serverity=10\)将使用这个新的默认值，甚至在那些在默认值修改之前就已经编译过的类文件中也是如此。

注解是由编译器计算而来的，因此，所有元素值必须是编译器常量。

一个注解元素永远不能设置为null,甚至不允许其默认值为null。

#### 单值注解

如果一个元素具有特殊的名字value，并且没有指定其他元素，那么你就可以忽略掉这个元素名以及等号。

```text
public @interface ActionListenerFor{
    String value();
}
```

我们可以将这个注解书写成如下形式

```text
@ActionListenerFor("yellowButton")
```

#### 注解类型用法

类型用法注解可以放置到类型引元之前

* 与泛化类型引元一起使用：List&lt;@NonNull String&gt;,Comparator.&lt;@NonNull String&gt; reverseOrder\(\).
* 数组中的任何位置: @NonNull String\[\]\[\] words\(words\[i\]\[j\]部位null\)，String @NonNull \[\]\[\] words\(words不为null\)，String\[\] @NonNull \[\] words\(words\[i\]不为null\)。
* 与超累和实现接口一起使用：class Warning extends @Localized Message
* 与构造器调用一起使用：new @Localized String\(...\)
* 与强制转型和instanceof检查一起使用:\(@Localized String\) text,if\(text instanceof @Localized String\)。\(这些注解只供外部工具使用，它们对强制转型和instanceof检查不会产生任何影响\)
* 与异常规约一起使用：public String read\(\) throws @Localized IOException
* 与通配符和类型边界一起使用：List&lt;@Localized ? extends Message&gt;, List&lt;? extends @Localized Message&gt;
* 与方法和构造器引用一起使用：@Localized Message::getText

#### 注解this

假设想要将参数注解为在方法中不会被修改。

```text
public class Point{
    public boolean equals(@ReadOnly Object other){...}
}
```

那么，处理这个注解的工具在看到下面的调用时

```text
p.equals(q)
```

就会推理出q没有被修改过。

但是p呢？

当该方法被调用时，this变量是绑定到p的。虽然this从来都没有被声明过，但是你可以用下面的方法来声明它

```text
public class Point{
    public boolean equals(@ReadOnly Point this,@ReadOnly Object other){...}
}
```

第一个参数被称为接收器参数，它必须被命名为this，而它的类型就是要构建的类。

你只能为方法而不能为构造器提供接收器参数。从概念上讲，构造器中的this引用在构造器没有执行完之前还不是给定类型的对象。所以，放置在构造器上的注解描述的是被构建的对象的属性。

