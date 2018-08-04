# 标准注解

#### 标准注解\(P385-386\)

| 注解接口 | 应用场合 | 目的 |
| :--- | :--- | :--- |
| SuppressWarnings | 除了包和注解之外的所有情况 | 阻止某个给定类型的警告信息 |
| SafeVarargs | 方法和构造器 | 断言varargs参数可安全使用 |
| Override | 方法 | 检查该方法是否覆盖了某一个超类方法 |
| FunctionalInterface | 接口 | 将接口标记为只有一个抽象方法的函数式接口 |
| PostConstruct、PreDestory | 方法 | 被标记的方法应该在构造之后或移除之前立即被调用 |
| Resource | 类、接口、方法、域 | 在类或接口上：标记为在其他地方要用到的资源。在方法或域上：为“注入”标记 |
| Resources | 类、接口 | 一个资源数组 |
| Generated | 全部 |  |
| Target | 注解 | 指明可以应用这个注解的那些项 |
| Retention | 注解 | 指明这个注解可以保留多久 |
| Documented | 注解 | 指明这个注解应该包含在注解项的文档中 |
| Inherited | 注解 | 指明当这个注解应用于一个类的时候，能够自动被它的子类继承 |
| Repeatable | 注解 | 指明这个注解可以在同一个项目上应用多次 |

#### 用于编译的注解\(P386\)

* @Deprecated
  * 可以被添加到任何不再鼓励使用的项上。所以，当你使用一个已过时的项时，编译器将会发出警告。这个注解与Javadoc标签@deprecated具有同等功效。
* @SuppressWarnings 
  * 告知编译器阻止特定类型的警告信息
* @Override
  * 只能注解在方法上，编译器会检查具有这种注解的方法是否真正覆盖了一个来自于超类的方法。
* @Generated
  * 供代码生成工具使用，任何生成的源代码都可以被注解，从而与程序员提供的代码区分开。

#### 用于管理资源的注解\(P386\)

* @PostConstruct和@PreDestroy
  * 用于控制对象生命周期的环境中
* @Resource
  * 用于资源注入

#### 元注解

**@Target\(P387\)**

@Target元注解可以应用于一个注解，以限制该注解可以应用到哪些项上。一条没有@Target限制的注解可以应用于任何项上。

```text
@Target({ElementType.TYPE,ElementType.METHOD})
public @interface BugReport
```

@Target注解的元素类型

| 元素类型 | 注解适用场合 |
| :--- | :--- |
| ANNOTATION\_TYPE | 注解类型声明 |
| PACKAGE | 包 |
| TYPE | 类\(包括enum\)及接口\(包括注解类型\) |
| METHOD | 方法 |
| CONSTRUCTOR | 构造器 |
| FIELD | 成员域\(包括enum常量\) |
| PARAMETER | 方法或构造器参数 |
| LOCAL\_VARIABLE | 局部变量 |
| TYPE\_PARAMETER | 类型参数 |
| TYPE\_USE | 类型用法 |

**@Retention\(P387\)**

@Retention元注解用于指定一条注解应该保留多长时间。其值只能从下表中选择，默认值是RetentionPolicy.CLASS.

| 保留规则 | 描述 |
| :--- | :--- |
| SOURCE | 不包括在类文件中的注解 |
| CLASS | 包括在类文件中的注解，但是虚拟机不需要将它们载入 |
| RUNTIME | 包括在类文件中的注解，并由虚拟机载入。通过反射API可获得它们 |

**@Ducumented\(P387\)**

@Documented元注解为像Javadoc这样的归档工具提供了一些提示。应该像处理其他修饰符\(例如protected和static\)一样来处理归档注解，以实现其归档。

**@Inherited\(P388\)**

@Inherited

@Inherited元注解只能应用于对类的注解。如果一个类具有继承注解，那么它的所有子类都自动具有同样的注解

