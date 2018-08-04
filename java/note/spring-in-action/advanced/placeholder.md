# 属性占位符

#### 属性占位符

直接从Environment中检索属性是非常方便的，尤其是在Java配置中装配bean的时候。但是，Spring也提供了通过占位符装配属性的方法，这些占位符的值会来源于一个属性源。

#### 解析属性占位符

Spring一直支持将属性定义到外部的属性文件中，并使用占位符值将其插入到Spring bean中。在Spring装配中，占位符的形式为使用"${...}"包装的属性名称。

为了使用占位符，我们必须要配置一个PropertyPlaceholderConfigurer bean或者 PropertySourcesPlacheholderConfiguer bean。

从Spring 3.1卡是，推荐使用PropertySourcesPlacholderConfigurer，因为它能够基于Spring Environment及其属性源来解析占位符。

```text
@Bean
public static PropertySourcesPlacheholderConfigurer placeholderConfigurer() {
    return new PropertySourcesPlaceholderConfigurer();
}
```

我们可以通过使用@Value注解，来获取属性文件的属性。

```text
public BlankDisc(@Value("${disc.title}") String title){
    this.title = title
}
```

解析外部属性能够将值的处理推迟到运行时，但是它的关注点在于根据名称解析来自于Spring Environment和属性源的属性。而Spring表达式语言提供了一种更通用的方式在运行时计算所要注入的值。

#### SpEL

Spring Expression Language,SpEL拥有很多特性，包括

* 使用bean的ID来引用bean
* 调用方法和访问对象的属性
* 对值进行算术、关系和逻辑运算
* 正则表达式匹配
* 集合操作

SpEL表达式要放到"\#{...}"之中。

**SqEL样例**

```text
#{T{System}.currentTimeMillis()}
#{T(java.lang.Math).PI}
```

T\(\)运算符，可以访问类作用域的方法或常量。

```text
#{sgtPeppers.artist}
#{sgtPeppers}
#{sgtPeppers.getArtist()}
#{sgtPeppers.getArtist()?.toUpperCase()}
```

上面这个表达式会计算得到ID为sgtPeppers的bean的artist属性、得到bean或者调用bean上面的方法。.?运算符能够在访问它右边的内容之前，确保它所对应的元素不是null。如果getArtist\(\)返回值是null的话，不会调用toUpperCase\(\)。

```text
#{systemProperties['disc.title']}
```

我们还可以通过systemProperties对象引用系统属性。

```text
#{3.14159}
#{9.87E4}
#{'Hello'}
#{false}
```

表示字面值。

```text
#{jukebox.songs[4].title}
#{'This is a test'[3]} //s
```

\[\]运算符用来从集合或数组中按照索引获取元素。

**SpEL运算符**

| 运算符类型 | 运算符 |
| :--- | :--- |
| 算术运算 | +、-、\*、/、%、^ |
| 比较运算 | &lt;、&gt;、==、&lt;=、&gt;=、lt、gt、eq、le、ge |
| 逻辑运算 | and、or、not、 |
| 条件运算 | ?:\(ternary\)、?: \(Elvis\) |
| 正则表达式 | matches |

